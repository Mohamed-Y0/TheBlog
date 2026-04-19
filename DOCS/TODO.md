> _This file can be updated any time._

## Blogger / Owner Perspective

- [ ] **Create A New Article**
  - [ ] Create `POST /articles` (Protected route).
  - [ ] Automatic slug generation from title.
- [ ] **Update Existing Article**
  - [ ] Create `PUT /articles/:id` (Protected route).
- [ ] **Delete Existing Article**
  - [ ] Create `DELETE /articles/:id`.
  - [ ] Implementation of **Soft Delete** (`isDeleted: true`).
- [ ] **Update My Profile**
  - [ ] Create `PATCH /profile` to update bio and links.

### Owner: Nice-to-Have Features

- [ ] **Drafts System**
  - [ ] Add `status` field (Draft/Published) to the article logic.
- [ ] **Dashboard/Analytics**
  - [ ] Implement `viewCount` logic for each article.
  - [ ] Create an endpoint for overall stats.
- [ ] **Schedule The Articles**
  - [ ] Add `publishedAt` field and backend logic to handle future dates.

## User Perspective (Reader)

- [ ] **Browse All Articles**
  - [ ] Create `GET /articles` endpoint.
  - [ ] Implement logic to show only published posts.
- [ ] **Read Author Articles**
  - [ ] Create `GET /articles/author/:id` endpoint.
- [ ] **Author Profile**
  - [ ] Create `GET /users/profile/:username` to view "About the Author".
- [ ] **Sort Based On Time**
  - [ ] Implement sorting logic (Default: Newest first).
- [ ] **Filter By Category**
  - [ ] Add query filtering in the `GET /articles` endpoint.

### User: Nice-to-Have Features

- [ ] **Reading Time Estimate**
  - [ ] Backend logic to calculate minutes based on word count.
- [ ] **Engagement**
  - [ ] Implement Like system logic.
  - [ ] Implement Comment system logic.
- [ ] **Newsletter/Subscribe**
  - [ ] Create subscription endpoint to collect emails.
