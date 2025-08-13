# 🎥 VideoTube - Backend

VideoTube is the complete backend for a **video-sharing platform** similar to YouTube.  
It follows a **professional-grade file structure** and includes **robust features** for user management, video handling, and social engagement.

---

## ✨ Features

- **User Authentication**
  
  Secure user registration, login, and session management using **JWT (Access & Refresh Tokens)**.

- **Video Management**  
  Upload, update, delete, and publish/unpublish videos.

- **Social Engagement**  
  - Like/unlike videos, comments, and tweets.  
  - Post, edit, and delete comments on videos.  
  - Create, update, and delete tweets.

- **Subscription System**  
  Subscribe/unsubscribe to channels.

- **Playlists**  
  Create playlists and add/remove videos.

- **User Dashboard**  
  View channel statistics (total views, subscribers, likes) and manage uploaded videos.

- **Cloud Media Management**  
  Video/image uploads handled with **Cloudinary**.

---

## 🛠️ Tech Stack

- **Backend:** Node.js, Express.js  
- **Database:** MongoDB with Mongoose  
- **Authentication:** JWT, Bcrypt  
- **File Uploads:** Multer, Cloudinary  
- **Middleware:** Cookie-Parser, CORS  

---

## 🚀 Getting Started

### 📋 Prerequisites
- Node.js **v18+**
- MongoDB (local or [MongoDB Atlas](https://www.mongodb.com/atlas))
- [Cloudinary](https://cloudinary.com/) account

### ⚙️ Installation & Setup

1. **Clone the repository**
   ```bash
   git clone https://github.com/satyajitpanda17/videotube.git
   cd videotube

2. **Install Dependencies**
   ```bash
   npm install

3. **Set up .env variables**
    At root directory
    ```bash
    PORT=8000
    MONGODB_URI=your_mongodb_connection_string
    CORS_ORIGIN=*
    ACCESS_TOKEN_SECRET=your_access_token_secret
    ACCESS_TOKEN_EXPIRY=1d
    REFRESH_TOKEN_SECRET=your_refresh_token_secret
    REFRESH_TOKEN_EXPIRY=10d
    CLOUDINARY_CLOUD_NAME=your_cloudinary_cloud_name
    CLOUDINARY_API_KEY=your_cloudinary_api_key
    CLOUDINARY_API_SECRET=your_cloudinary_api_secret

4. **Run Developement Server**
    ```bash
    npm run dev

## API Documentation
#### This documentation provides a detailed reference for all the API endpoints available in this project.

### Base URL: /api/v1

## 🔐 Authentication

### 1. Register a New User
**POST** `/users/register`  
**Content-Type:** `multipart/form-data`  

**Body Parameters:**
| Field      | Type   | Required | Description                |
|------------|--------|----------|----------------------------|
| fullName   | String | ✅        | Full name of the user      |
| username   | String | ✅        | Unique username            |
| email      | String | ✅        | Unique email address       |
| password   | String | ✅        | User password              |
| avatar     | File   | ✅        | Avatar image               |
| coverImage | File   | ❌        | Channel cover image        |

**Success Response (201):**

```json
{
  "success": true,
  "message": "User registered successfully",
  "data": {
    "_id": "60d5f1b2c3b4f2b1e8b9c4d2",
    "username": "testuser",
    "email": "test@example.com",
    "fullName": "Test User",
    "avatar": "http://cloudinary.com/path/to/avatar.jpg",
    "coverImage": "http://cloudinary.com/path/to/cover.jpg"
  }
}
```
---
### 2. Login User
**Endpoint: POST** `/users/login`

Description: Authenticates a user and returns JWT access and refresh tokens in secure, httpOnly cookies.

**Request Body (application/json):**

```json
{
  "email": "user@example.com",
  "password": "password123"
}
```

**Success Response (200 OK)**:

```json
{
    "success": true,
    "message": "User logged in successfully",
    "data": {
        "user": {"User Object"},
        "accessToken": "...",
        "refreshToken": "..."
    }
}
```
---
### 3. Logout User
**Endpoint: POST** `/users/logout`

Description: Clears the user's refresh token from the database and clears their access/refresh token cookies.

**Authentication: Required (Bearer Token)**

Success Response (200 OK):
```json
{
    "success": true,
    "message": "User logged out successfully"
}
```
---
### 4. Refresh Access Token
**Endpoint: POST** `/users/refresh-token`

Description: Generates a new access token using a valid refresh token from the request cookies or body.

**Request Body (application/json, optional):**
```json
{
  "refreshToken": "..."
}
```

**Success Response (200 OK):**
```json
{
    "success": true,
    "message": "Access Token refreshed",
    "data": {
        "accessToken": "...",
        "refreshToken": "..."
    }
}
```
---
## User Account Management 👤
Endpoints for managing authenticated user data. All routes in this section require authentication.

### 5. Change Current Password
**Endpoint: POST** `/users/change-password`

**Authentication: Required (Bearer Token**)

**Request Body (application/json):**
```json
{
  "oldPassword": "currentPassword123",
  "newPassword": "newSecurePassword456"
}
```

**Success Response (200 OK):**
```json
{
    "success": true,
    "message": "Password changed successfully"
}
```
---
### 6. Get Current User Details
**Endpoint: GET** `/users/current-user`

**Authentication: Required (Bearer Token)**

**Success Response (200 OK):**
```json
{
    "success": true,
    "message": "Current user fetched successfully",
    "data": { "User Object without password/refreshToken" }
}
```
---
### 7. Update Account Details
**Endpoint: PATCH** ``/users/update-account``

**Authentication: Required (Bearer Token)**

**Request Body (application/json):**
```json
{
  "fullName": "New Full Name",
  "email": "new.email@example.com"
}
```
**Success Response (200 OK):**
```json
{
    "success": true,
    "message": "Account details updated successfully",
    "data": { "Updated User Object" }
}
```
### 8. Update User Avatar
**Endpoint: PATCH** `/users/avatar`

**Authentication: Required (Bearer Token)**

**Request Body:**
| Field      | Type   | Required | Description                |
|------------|--------|----------|----------------------------|
| avatar   | File | ✅        | The new avatar image file.      |

**Success Response (200 OK):**
```json
{
    "success": true,
    "message": "Avatar updated successfully",
    "data": { "User Object with new avatar URL" }
}
```
---
### 9. Update User Cover Image
**Endpoint: PATCH** `/users/cover-image`

**Authentication: Required (Bearer Token)**

**Request Body):**

| Field      | Type   | Required | Description                |
|------------|--------|----------|----------------------------|
| coverImage   | File | ✅        | The cover image file.      |

**Success Response (200 OK):**
```json
{
    "success": true,
    "message": "Cover Image updated successfully",
    "data": { "User Object with new cover image URL" }
}
```
---

## Channel & History 📺
Endpoints for public channel data and user-specific history.

### 10. Get User Channel Profile
**Endpoint: GET** `/users/c/:username`

Description: Fetches a user's public channel profile, including subscriber counts.

**Authentication: Optional (If authenticated, the isSubscribed field will be accurate for the current user).**

**Success Response (200 OK):**
```json
{
    "success": true,
    "message": "User channel fetched successfully",
    "data": {
        "fullName": "Test User",
        "username": "testuser",
        "subscribersCount": 150,
        "channelsSubscribedToCount": 10,
        "isSubscribed": true,
        "avatar": "http://cloudinary.com/path/to/avatar.jpg",
        "coverImage": "http://cloudinary.com/path/to/cover.jpg"
    }
}
```
---
### 11. Get User Watch History
**Endpoint: GET** `/users/history`

Description: Retrieves the watch history for the currently authenticated user.

**Authentication: Required (Bearer Token)**

**Success Response (200 OK):**
```json
{
    "success": true,
    "message": "Watch History fetched successfully",
    "data": [
        { "Video Object 1" },
        { "Video Object 2" }
    ]
}
```
---
## Video Management
### Authentication: All routes require a valid JWT Bearer Token.

### 1. Get All Videos
**GET** : base url/

Description: Retrieves a paginated list of all published videos. Supports searching, sorting, and filtering by user.

Query Parameters:

| Parameter  | Type   | Default | Description                                           |
| ---------- | ------ | ------- | ----------------------------------------------------- |
| `page`     | Number | 1       | Page number for pagination.                           |
| `limit`    | Number | 10      | Number of videos per page.                            |
| `query`    | String | N/A     | Search term to filter videos by title or description. |
| `sortBy`   | String | N/A     | Field to sort by: `views`, `createdAt`, `duration`.   |
| `sortType` | String | N/A     | Sort order: `asc` or `desc`.                          |
| `userId`   | String | N/A     | Filter videos by a specific user's ID.                |

**Success Response (200 OK):**
```json
{
  "status": 200,
  "data": {
    "docs": [
      {
        "_id": "video_id",
        "title": "Video Title",
        "description": "Video Description",
        "duration": 120,
        "views": 100,
        "isPublished": true,
        "owner": {
          "_id": "user_id",
          "username": "username",
          "avatar": {
            "url": "avatar_url"
          }
        },
        "createdAt": "2025-08-13T12:00:00Z",
        "updatedAt": "2025-08-13T12:00:00Z"
      }
    ],
    "totalDocs": 1,
    "limit": 10,
    "totalPages": 1,
    "page": 1
  },
  "message": "Videos fetched successfully"
}
```
### 2. Publish a Video
**POST** : base url/

Description: Uploads a new video and its thumbnail to Cloudinary and creates a new video document.

**Request Body (multipart/form-data):**

| Parameter     | Type   | Description                       |
| ------------- | ------ | --------------------------------- |
| `videoFile`   | File   | The video file                    |
| `thumbnail`   | File   | The thumbnail image for the video |
| `title`       | String | The title of the video            |
| `description` | String | The description of the video      |


**Success Response (201 Created):**
```json
{
  "status": 200,
  "data": {
    "_id": "video_id",
    "title": "Video Title",
    "description": "Video Description",
    "duration": 120,
    "views": 0,
    "isPublished": false,
    "owner": "user_id",
    "createdAt": "2025-08-13T12:00:00Z",
    "updatedAt": "2025-08-13T12:00:00Z"
  },
  "message": "Video uploaded successfully"
}
```
### 3. Get Video by ID
**Endpoint: GET** /:videoId

Description: Retrieves detailed information for a single video, including owner details, likes, and subscription status. Increments the view count for the video.

**Request Body (multipart/form-data):**
| Parameter | Type   | Description         |
| --------- | ------ | ------------------- |
| `videoId` | String | The ID of the video |


**Success Response (200 OK):**
```json
{
  "status": 200,
  "data": {
    "_id": "video_id",
    "title": "Video Title",
    "description": "Video Description",
    "duration": 120,
    "views": 100,
    "isPublished": true,
    "owner": {
      "_id": "user_id",
      "username": "username",
      "avatar": {
        "url": "avatar_url"
      },
      "subscribersCount": 50,
      "isSubscribed": true
    },
    "comments": [
      {
        "_id": "comment_id",
        "text": "Great video!",
        "user": {
          "_id": "user_id",
          "username": "commenter_username"
        },
        "createdAt": "2025-08-13T12:00:00Z"
      }
    ],
    "likesCount": 10,
    "isLiked": true,
    "createdAt": "2025-08-13T12:00:00Z",
    "updatedAt": "2025-08-13T12:00:00Z"
  },
  "message": "Video details fetched successfully"
}
```

### 4. Update Video Details
**Endpoint: PATCH** /:videoId

Description: Updates the title, description, and/or thumbnail of a video. Only the owner of the video can perform this action.

**Request Body (multipart/form-data):**
| Parameter     | Type   | Description                           |
| ------------- | ------ | ------------------------------------- |
| `title`       | String | The title of the video                |
| `description` | String | The description of the video          |
| `thumbnail`   | File   | The new thumbnail image for the video |

**Success Response (200 OK):**
```json
{
  "status": 200,
  "data": {
    "_id": "video_id",
    "title": "Updated Video Title",
    "description": "Updated Video Description",
    "duration": 120,
    "views": 100,
    "isPublished": true,
    "owner": "user_id",
    "createdAt": "2025-08-13T12:00:00Z",
    "updatedAt": "2025-08-13T12:00:00Z"
  },
  "message": "Video updated successfully"
}
```
### 5. Delete a Video
**Endpoint: DELETE** /:videoId

Description: Deletes a video, its associated files from Cloudinary, and all related comments and likes. Only the owner of the video can perform this action.

**Path parameters**
| Parameter | Type   | Description         |
| --------- | ------ | ------------------- |
| `videoId` | String | The ID of the video |

**Success Response (200 OK):**
```json
{
  "status": 200,
  "data": {},
  "message": "Video deleted successfully"
}
```
### 6. Toggle Publish Status
**Endpoint: PATCH** /toggle/publish/:videoId

Description: Toggles the isPublished status of a video, making it public or private. Only the owner can change the status.

**Path parameters**
| Parameter | Type   | Description         |
| --------- | ------ | ------------------- |
| `videoId` | String | The ID of the video |

**Success Response (200 OK):**
```json
{
  "status": 200,
  "data": {
    "isPublished": true
  },
  "message": "Video publish status toggled successfully"
}
```


