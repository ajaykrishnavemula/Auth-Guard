# 🔐 Auth-Guard-API - Enterprise Authentication System

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Node.js](https://img.shields.io/badge/Node.js-43853D?style=flat-square&logo=node.js&logoColor=white)](https://nodejs.org/)
[![TypeScript](https://img.shields.io/badge/TypeScript-007ACC?style=flat-square&logo=typescript&logoColor=white)](https://www.typescriptlang.org/)
[![Express](https://img.shields.io/badge/Express-000000?style=flat-square&logo=express&logoColor=white)](https://expressjs.com/)
[![MongoDB](https://img.shields.io/badge/MongoDB-4EA94B?style=flat-square&logo=mongodb&logoColor=white)](https://www.mongodb.com/)

> 🔐 Enterprise authentication system with JWT, OAuth (Google/GitHub), 2FA, and role-based access control. Features email verification, password reset, and session management. Built with Express & MongoDB. Production-ready security! 🛡️

A comprehensive authentication and user management service built with Node.js, Express, TypeScript, and MongoDB. This project has been enhanced through three phases of development to include advanced security features, OAuth integration, and an admin dashboard.

## Development Phases

This project was enhanced through three major development phases:

### Phase 1: Foundation Upgrade
- Migrated the codebase to TypeScript
- Implemented proper user data storage in MongoDB
- Added comprehensive error handling and logging
- Set up testing infrastructure

### Phase 2: Core Feature Enhancement
- Added password reset and email verification
- Implemented OAuth providers (Google, GitHub)
- Added basic security features
- Created user profiles with customizable fields

### Phase 3: Advanced Features
- Implemented Two-Factor Authentication (2FA)
- Added advanced security features (rate limiting, account locking)
- Created admin dashboard for user management
- Implemented user activity tracking and analytics
- Added session management and security logs

## Features

### Core Authentication

- **User Registration & Login**: Secure registration and login with JWT authentication
- **Email Verification**: Email verification flow with token generation and verification
- **Password Management**: Secure password reset flow and password change functionality
- **JWT Authentication**: Secure token-based authentication with refresh tokens
- **Role-Based Access Control**: User and admin roles with appropriate permissions

### Enhanced Security

- **Two-Factor Authentication**: Optional 2FA using TOTP (Time-based One-Time Password)
- **Account Locking**: Automatic account locking after multiple failed login attempts
- **Rate Limiting**: Protection against brute force attacks with rate limiting
- **XSS Protection**: Protection against cross-site scripting attacks
- **CSRF Protection**: Protection against cross-site request forgery
- **Secure Headers**: Helmet middleware for secure HTTP headers
- **Security Logs**: Comprehensive logging of security events and login attempts
- **IP-based Blocking**: Blocking suspicious IP addresses

### OAuth Integration

- **Google OAuth**: Sign in with Google account
- **GitHub OAuth**: Sign in with GitHub account
- **OAuth Profile Linking**: Link multiple OAuth providers to a single account

### User Profile Management

- **Enhanced User Profiles**: Comprehensive user profile with personal and professional information
- **Profile Customization**: User-defined profile fields including bio, location, skills, and more
- **User Preferences**: Customizable user preferences for notifications, theme, and more
- **Social Links**: Connect multiple social media profiles
- **Activity History**: Track user login history and account activity
- **Data Export**: GDPR-compliant user data export functionality
- **Account Deletion**: Secure account deletion process

### Admin Dashboard

- **User Management**: Comprehensive user management interface for administrators
- **User Analytics**: Insights on user registrations, logins, and activity
- **User Search**: Advanced search and filtering of user accounts
- **Bulk Actions**: Perform actions on multiple user accounts
- **Role Management**: Assign and manage user roles and permissions
- **System Monitoring**: Monitor system health and security events

## API Endpoints

### Authentication

- `POST /api/v1/auth/register`: Register a new user
- `POST /api/v1/auth/login`: Login user
- `POST /api/v1/auth/verify-email`: Verify user email
- `POST /api/v1/auth/resend-verification`: Resend verification email
- `POST /api/v1/auth/forgot-password`: Request password reset
- `POST /api/v1/auth/reset-password`: Reset password with token
- `POST /api/v1/auth/refresh-token`: Refresh JWT token

### OAuth

- `GET /api/v1/auth/google`: Initiate Google OAuth flow
- `GET /api/v1/auth/google/callback`: Google OAuth callback
- `GET /api/v1/auth/github`: Initiate GitHub OAuth flow
- `GET /api/v1/auth/github/callback`: GitHub OAuth callback
- `POST /api/v1/auth/link-oauth`: Link OAuth provider to existing account
- `POST /api/v1/auth/unlink-oauth`: Unlink OAuth provider from account

### Two-Factor Authentication

- `POST /api/v1/auth/setup-2fa`: Set up two-factor authentication
- `POST /api/v1/auth/verify-2fa`: Verify two-factor authentication token
- `POST /api/v1/auth/disable-2fa`: Disable two-factor authentication
- `POST /api/v1/auth/generate-backup-codes`: Generate 2FA backup codes
- `POST /api/v1/auth/verify-backup-code`: Verify 2FA backup code

### User Management

- `GET /api/v1/auth/me`: Get current user information
- `PATCH /api/v1/auth/update-profile`: Update user profile
- `PATCH /api/v1/auth/update-preferences`: Update user preferences
- `PATCH /api/v1/auth/change-password`: Change user password
- `GET /api/v1/auth/activity`: Get user activity history
- `POST /api/v1/auth/export-data`: Export user data (GDPR)
- `DELETE /api/v1/auth/delete-account`: Delete user account

### Session Management

- `GET /api/v1/auth/sessions`: Get all active sessions
- `DELETE /api/v1/auth/sessions/:id`: Revoke a specific session
- `DELETE /api/v1/auth/sessions`: Revoke all sessions except current

### Admin Routes

- `GET /api/v1/admin/users`: Get all users with filtering and pagination
- `GET /api/v1/admin/users/:id`: Get specific user details
- `PATCH /api/v1/admin/users/:id`: Update user information
- `DELETE /api/v1/admin/users/:id`: Delete a user
- `PATCH /api/v1/admin/users/:id/role`: Change user role
- `POST /api/v1/admin/users/:id/lock`: Lock a user account
- `POST /api/v1/admin/users/:id/unlock`: Unlock a user account
- `GET /api/v1/admin/analytics/registrations`: Get user registration analytics
- `GET /api/v1/admin/analytics/logins`: Get login analytics
- `GET /api/v1/admin/security/logs`: Get security logs
- `GET /api/v1/admin/security/blocked-ips`: Get blocked IP addresses
- `POST /api/v1/admin/security/block-ip`: Block an IP address
- `DELETE /api/v1/admin/security/unblock-ip`: Unblock an IP address

## Setup and Configuration

### Environment Variables

Create a `.env` file in the root directory with the following variables:

```
# Server
PORT=5000
NODE_ENV=development

# MongoDB
MONGO_URI=mongodb://localhost:27017/auth-service

# JWT
JWT_SECRET=your_jwt_secret
JWT_EXPIRES_IN=15m
REFRESH_TOKEN_SECRET=your_refresh_token_secret
REFRESH_TOKEN_EXPIRES_IN=7d

# Email
EMAIL_FROM=noreply@auth-service.com
SMTP_HOST=smtp.example.com
SMTP_PORT=587
SMTP_USER=your_smtp_username
SMTP_PASS=your_smtp_password
FRONTEND_URL=http://localhost:3000

# OAuth
GOOGLE_CLIENT_ID=your_google_client_id
GOOGLE_CLIENT_SECRET=your_google_client_secret
GITHUB_CLIENT_ID=your_github_client_id
GITHUB_CLIENT_SECRET=your_github_client_secret

# Security
MAX_LOGIN_ATTEMPTS=5
LOCK_TIME=3600000
RATE_LIMIT_WINDOW_MS=900000
RATE_LIMIT_MAX=100
ENABLE_IP_BLOCKING=true
CAPTCHA_SECRET=your_captcha_secret

# Admin Dashboard
ADMIN_DASHBOARD_URL=http://localhost:3001
```

### Installation

```bash
# Install dependencies
npm install

# Build the project
npm run build

# Start the server
npm start

# Start in development mode
npm run dev
```

## Project Structure

```
User_Register_Login/
├── src/                  # TypeScript source files
│   ├── config/           # Configuration files
│   ├── controllers/      # Request handlers
│   ├── db/               # Database connection
│   ├── errors/           # Custom error classes
│   ├── interfaces/       # TypeScript interfaces
│   ├── middleware/       # Express middleware
│   ├── models/           # Mongoose models
│   ├── routes/           # API routes
│   ├── services/         # Business logic services
│   ├── utils/            # Utility functions
│   └── app.ts            # Main application file
├── admin-dashboard/      # Admin dashboard frontend
├── public/               # Static files
├── dist/                 # Compiled JavaScript files
├── logs/                 # Application logs
├── .env.example          # Example environment variables
├── tsconfig.json         # TypeScript configuration
└── package.json          # Project dependencies
```

## Security Best Practices

- All passwords are hashed using bcrypt
- JWT tokens have short expiration times
- Refresh tokens are used for obtaining new access tokens
- Two-factor authentication adds an extra layer of security
- Rate limiting prevents brute force attacks
- Account locking after multiple failed login attempts
- XSS and CSRF protection
- Secure HTTP headers with Helmet
- Input validation and sanitization
- IP-based blocking for suspicious activity
- Comprehensive security logs and audit trails

## TypeScript Types and Interfaces

The project uses TypeScript for type safety. Key interfaces include:

- `IUser`: User document interface
- `JwtPayload`: JWT token payload interface
- `UserProfile`: User profile interface
- `EmailOptions`: Email sending options interface
- `SecurityLog`: Security event logging interface
- `UserActivity`: User activity tracking interface
- `AdminDashboardStats`: Admin dashboard statistics interface

## Error Handling

Custom error classes for different types of errors:

- `BadRequestError`: For invalid request data
- `UnauthenticatedError`: For authentication failures
- `NotFoundError`: For resources not found
- `TooManyRequestsError`: For rate limiting
- `ForbiddenError`: For permission issues
- `ConflictError`: For resource conflicts

## Logging

Winston logger for application logging with different log levels based on the environment. Security events are logged separately for audit purposes.

## Testing

Comprehensive test suite using Jest and Supertest:

- Unit tests for utilities and services
- Integration tests for API endpoints
- Authentication flow tests
- Security feature tests

## Admin Dashboard

The admin dashboard provides a comprehensive interface for managing users and monitoring system activity:

- User management with filtering and search
- User analytics and statistics
- Security logs and monitoring
- System health metrics
- Role and permission management

---

## 📄 License

This project is licensed under the MIT License.

```
MIT License

Copyright (c) 2024 Ajay Krishna

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

---

**Built with ❤️ by Ajay Krishna**

*Securing applications, one authentication at a time.*