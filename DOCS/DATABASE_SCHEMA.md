## Overview

This project uses **MongoDB** as a NoSQL database. We have two main collections: `Users` and `Articles`.

> _This file can be updated any time._

---

## 1. Users Collection

Stores information about the blogger (owner) and potentially other contributors or readers in the future.

| **Field**     | **Type** | **Description**                       | **Constraints**             |
| ------------- | -------- | ------------------------------------- | --------------------------- |
| `_id`         | ObjectId | Unique identifier                     | Primary Key                 |
| `username`    | String   | Unique name for profile URL           | Required, Unique, Trim      |
| `email`       | String   | User email for login/notifications    | Required, Unique, Lowercase |
| `password`    | String   | Hashed password                       | Required, MinLength: 8      |
| `bio`         | String   | Short description about the author    | MaxLength: 200              |
| `avatar`      | String   | URL to profile picture                | Optional                    |
| `socialLinks` | Object   | { linkedin: String, twitter: String } | Optional                    |
| `createdAt`   | Date     | Account creation timestamp            | Auto-generated              |

---

## 2. Articles Collection

Stores the blog posts content and metadata.

| **Field**    | **Type** | **Description**                           | **Constraints**        |
| ------------ | -------- | ----------------------------------------- | ---------------------- |
| `_id`        | ObjectId | Unique identifier                         | Primary Key            |
| `title`      | String   | Headline of the article                   | Required, Trim         |
| `slug`       | String   | SEO friendly URL (e.g., my-post-title)    | Unique, Indexed        |
| `content`    | String   | The actual body (Markdown/HTML)           | Required               |
| `author`     | ObjectId | Reference to the Users collection         | Required (Ref: 'User') |
| `category`   | String   | Main topic (e.g., Tech, Life, Coding)     | Required, Indexed      |
| `tags`       | Array    | List of strings for better filtering      | Optional               |
| `status`     | String   | Post state (draft / published / archived) | Default: 'draft'       |
| `coverImage` | String   | URL to the article header image           | Optional               |
| `readTime`   | Number   | Estimated reading time in minutes         | Calculated on Backend  |
| `viewCount`  | Number   | Number of unique visits                   | Default: 0             |
| `isDeleted`  | Boolean  | Soft delete flag                          | Default: false         |
| `updatedAt`  | Date     | Last modification timestamp               | Auto-updated           |
| `createdAt`  | Date     | Publication date                          | Auto-generated         |

---

## Relationships (ERD Logic)

- **One-to-Many:** One **User** can have multiple **Articles**.
- The `author` field in the `Articles` collection stores the `_id` of the user who created it (Population will be used in Mongoose).

## Indexes

- `slug`: For fast lookups by URL.
- `category`: To optimize filtering articles by category.
- `title`: Text index for basic search functionality.
