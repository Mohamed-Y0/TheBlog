# Error Handling Architecture - Technical Specification

This document details the centralized error handling architecture implemented in this project. The system is designed to be robust, environment-aware, and developer-friendly by minimizing repetitive `try-catch` blocks and providing clear, actionable feedback to clients.

---

## 1. Centralized Error Handling Pattern

The project follows a **Global Error Handling Middleware** pattern. Instead of handling errors locally in every controller, all errors are passed to a single, centralized middleware.

### Flow of an Error:

1.  **Occurrence:** An error occurs in a Route, Controller, or Model.
2.  **Capture:** The error is captured by the `catchAsync` wrapper or manually via `next(new AppError(...))`.
3.  **Propagation:** Express automatically passes the error to the global error handling middleware.
4.  **Transformation:** The middleware identifies the error type (e.g., Mongoose `ValidationError`) and transforms it into a clean `AppError`.
5.  **Response:** The middleware sends an environment-specific response (`Development` vs. `Production`) to the client.

---

## 2. Operational vs. Programming Errors

The system distinguishes between two primary types of errors to ensure security and clarity.

### Operational Errors

These are expected errors that occur during the normal operation of the application. They are marked with `isOperational: true` via the `AppError` class.

- **Examples:** Invalid user input, failed DB validation, resource not found (404), unauthorized access.
- **Handling:** Safe to show a descriptive message to the client.

### Programming Errors

These are bugs or unexpected issues in the code.

- **Examples:** Reading a property of `undefined`, syntax errors, failed third-party library calls.
- **Handling:** These should _not_ leak technical details to the client. In production, we send a generic "Something went wrong!" message and log the details for developers.

---

## 3. Core Components

### `AppError` Utility Class

Located in `utils/appError.js`, this class extends the native `Error` object. It adds properties like `statusCode`, `status` (fail/error), and explicitly marks the error as **operational**.

```javascript
class AppError extends Error {
  constructor(message, statusCode) {
    super(message);
    this.statusCode = statusCode;
    this.status = `${statusCode}`.startsWith('4') ? 'fail' : 'error';
    this.isOperational = true;

    Error.captureStackTrace(this, this.constructor);
  }
}
```

### `catchAsync` Wrapper

Located in `utils/catchAsync.js`, this higher-order function eliminates the need for `try-catch` blocks in asynchronous controllers. It wraps the async function and automatically catches any rejected promise, passing it to `next(err)`.

```javascript
export const catchAsync = fn => {
  return (req, res, next) => {
    fn(req, res, next).catch(next);
  };
};
```

---

## 4. Deep Dive: The Global Error Controller

The `controllers/errorController.js` is the heart of the system. It acts as a "filter" between your backend logic and the end user.

### Behind the Scenes: The Middleware Signature

Express distinguishes error-handling middleware from regular middleware by the number of arguments. By providing **four** arguments `(err, req, res, next)`, Express skips all other middleware and jumps straight to this function whenever `next(err)` is called.

```javascript
export default (err, req, res, next) => {
  err.statusCode = err.statusCode || 500;
  err.status = err.status || 'error';
  // ... branching logic
};
```

---

## 5. Database Error Transformation Logic

In `Production`, the controller intercepts cryptic Mongoose/MongoDB errors and translates them into readable `AppError` instances.

| Error Type           | Detection Logic                    | Refactored Result                                         |
| :------------------- | :--------------------------------- | :-------------------------------------------------------- |
| **Cast Error**       | `error.name === 'CastError'`       | "Invalid [path]: [value]" (e.g., Invalid ID)              |
| **Duplicate Field**  | `error.code === 11000`             | Extracts the duplicate value and asks for a new one.      |
| **Validation Error** | `error.name === 'ValidationError'` | Aggregates all field validation messages into one string. |

**Example: Handling Validation Errors**

```javascript
const handleValidationErrorDB = err => {
  // Extracts messages from the Mongoose 'errors' object
  const errors = Object.values(err.errors).map(el => el.message);
  const message = `Invalid input data. ${errors.join('. ')}`;

  // Returns a marked Operational Error
  return new AppError(message, 400);
};
```

---

## 6. Catch-All for Undefined Routes

To ensure the error controller handles everything, a "catch-all" route is placed at the very end of the `app.js` file, after all valid routes are defined.

```javascript
app.get('/{*splat}', (req, res, next) => {
  next(
    new AppError(`The requested path ${req.originalUrl} does not exist.`, 404),
  );
});
```

This ensures that any request to an undefined URL is caught and funneled through the same centralized error logic.

---

## 7. Global Safety Net (Process Level)

In `server.js`, we handle errors that occur outside the Express application context.

### Uncaught Exceptions

Synchronous bugs (e.g., `console.log(x)` where `x` is not defined). These are fatal and requires an immediate process exit.

### Unhandled Rejections

Asynchronous failures (e.g., Database connection timeout). We gracefully shut down the server before exiting.

```javascript
process.on('unhandledRejection', err => {
  server.close(() => {
    process.exit(1);
  });
});
```
