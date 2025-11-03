# AuthGuardAPI - Beginner's Learning Guide

## 📚 What is AuthGuardAPI?

AuthGuardAPI is a **complete authentication and user management system**. Think of it as the "security guard" for your application - it handles who can log in, what they can do, and keeps everything secure.

---

## 🎯 What Does This Project Do?

Imagine you're building a website like Facebook or Gmail. You need:
- Users to create accounts (registration)
- Users to log in (authentication)
- Keep passwords safe (security)
- Remember who's logged in (sessions)
- Extra security like 2FA (two-factor authentication)
- Let users log in with Google/GitHub (OAuth)

**This project does ALL of that!**

---

## 🏗️ Project Structure (How Files Are Organized)

```
AuthGuardAPI/
├── src/                          # All source code lives here
│   ├── controllers/              # Handle requests (the "brain")
│   │   └── auth.ts              # 825 lines! Handles all auth logic
│   ├── models/                   # Database structure (what data looks like)
│   │   ├── User.ts              # User information
│   │   ├── Session.ts           # Login sessions
│   │   ├── AuditLog.ts          # Security tracking
│   │   └── AdminAction.ts       # Admin activities
│   ├── routes/                   # URL paths (like /login, /register)
│   │   └── auth.ts              # Defines all API endpoints
│   ├── middleware/               # Code that runs before controllers
│   │   ├── auth.ts              # Checks if user is logged in
│   │   └── rate-limiter.ts      # Prevents spam/attacks
│   ├── services/                 # Business logic (complex operations)
│   │   └── email.service.ts     # Sends emails
│   ├── utils/                    # Helper functions
│   │   └── logger.ts            # Records what happens
│   ├── config/                   # Settings
│   │   └── index.ts             # Configuration values
│   └── app.ts                    # Main application setup
├── .env                          # Secret keys (never share!)
├── package.json                  # Project dependencies
└── tsconfig.json                 # TypeScript settings
```

---

## 🔑 Key Concepts Explained (For Absolute Beginners)

### 1. **Authentication vs Authorization**

**Authentication** = "Who are you?"
- Like showing your ID card
- Example: Login with email and password

**Authorization** = "What can you do?"
- Like having a VIP pass
- Example: Only admins can delete users

### 2. **JWT (JSON Web Token)**

Think of JWT as a **digital ticket**:
- When you log in, you get a ticket (JWT)
- Every time you make a request, you show your ticket
- The server checks if your ticket is valid
- No need to log in again and again!

**Example JWT**:
```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VySWQiOiIxMjM0NSJ9.signature
```

### 3. **Password Hashing**

**Never store passwords directly!**

```javascript
// ❌ BAD - Never do this!
password: "mypassword123"

// ✅ GOOD - Store hashed version
password: "$2a$10$N9qo8uLOickgx2ZMRZoMyeIjZAgcfl7p92ldGxad68LJZdL17lhWy"
```

**How it works**:
1. User enters password: "mypassword123"
2. System hashes it: "$2a$10$N9qo8..."
3. Store the hash in database
4. When user logs in, hash their input and compare

### 4. **Two-Factor Authentication (2FA)**

**Extra security layer**:
1. User logs in with password (first factor)
2. System sends code to phone (second factor)
3. User enters code to complete login

Like having two locks on your door!

### 5. **OAuth (Login with Google/GitHub)**

Instead of creating a new account:
- Click "Login with Google"
- Google confirms who you are
- You're logged in!

No need to remember another password!

---

## 📖 How the Code Works (Step by Step)

### Example 1: User Registration

**What happens when someone creates an account?**

```typescript
// 1. User sends data to /api/v1/auth/register
POST /api/v1/auth/register
{
  "name": "John Doe",
  "email": "john@example.com",
  "password": "SecurePass123!"
}

// 2. Code in auth.ts controller runs:
export const register = async (req: Request, res: Response) => {
  // Get data from request
  const { name, email, password } = req.body;
  
  // Check if user already exists
  const existingUser = await User.findOne({ email });
  if (existingUser) {
    throw new BadRequestError('Email already registered');
  }
  
  // Hash the password (make it secure)
  const hashedPassword = await bcrypt.hash(password, 10);
  
  // Create new user in database
  const user = await User.create({
    name,
    email,
    password: hashedPassword
  });
  
  // Send verification email
  await sendVerificationEmail(user.email, user.verificationToken);
  
  // Return success
  res.status(201).json({
    success: true,
    message: 'Registration successful! Check your email.'
  });
};
```

**Flow Diagram**:
```
User → Register Request → Check if exists → Hash password → 
Save to DB → Send email → Return success
```

### Example 2: User Login

**What happens when someone logs in?**

```typescript
// 1. User sends credentials
POST /api/v1/auth/login
{
  "email": "john@example.com",
  "password": "SecurePass123!"
}

// 2. Code verifies and creates JWT
export const login = async (req: Request, res: Response) => {
  const { email, password } = req.body;
  
  // Find user by email
  const user = await User.findOne({ email });
  if (!user) {
    throw new UnauthenticatedError('Invalid credentials');
  }
  
  // Check if password matches
  const isPasswordCorrect = await bcrypt.compare(password, user.password);
  if (!isPasswordCorrect) {
    throw new UnauthenticatedError('Invalid credentials');
  }
  
  // Check if 2FA is enabled
  if (user.twoFactorEnabled) {
    // Send 2FA code
    return res.json({ requiresTwoFactor: true });
  }
  
  // Create JWT token (the "ticket")
  const token = jwt.sign(
    { userId: user._id, role: user.role },
    process.env.JWT_SECRET,
    { expiresIn: '7d' }
  );
  
  // Return token
  res.json({
    success: true,
    token,
    user: { name: user.name, email: user.email }
  });
};
```

**Flow Diagram**:
```
User → Login Request → Find user → Check password → 
Check 2FA → Create JWT → Return token
```

### Example 3: Protected Route

**How to check if user is logged in?**

```typescript
// Middleware that runs before controller
export const authenticateUser = async (req: Request, res: Response, next: NextFunction) => {
  // Get token from header
  const authHeader = req.headers.authorization;
  if (!authHeader || !authHeader.startsWith('Bearer ')) {
    throw new UnauthenticatedError('No token provided');
  }
  
  const token = authHeader.split(' ')[1];
  
  // Verify token
  const decoded = jwt.verify(token, process.env.JWT_SECRET);
  
  // Add user info to request
  req.user = decoded;
  
  // Continue to next function
  next();
};

// Usage in routes
router.get('/profile', authenticateUser, getProfile);
//                     ↑ This runs first!
```

---

## 🎨 Features Implemented (What You Can Do)

### ✅ Basic Authentication
1. **Register** - Create new account
2. **Login** - Sign in with email/password
3. **Logout** - Sign out
4. **Email Verification** - Confirm email address
5. **Password Reset** - Forgot password flow
6. **Change Password** - Update password when logged in

### ✅ Two-Factor Authentication (2FA)
1. **Setup 2FA** - Enable extra security
2. **Verify 2FA** - Enter code to login
3. **Disable 2FA** - Turn off if needed

### ✅ OAuth Integration
1. **Google Login** - Sign in with Google
2. **GitHub Login** - Sign in with GitHub

### ✅ User Management
1. **Get Profile** - View your information
2. **Update Profile** - Change name, preferences
3. **Delete Account** - Remove account

### ✅ Admin Features
1. **List Users** - See all users
2. **Update User** - Modify user details
3. **Delete User** - Remove users
4. **Assign Roles** - Make someone admin

### ✅ Security Features
1. **Audit Logging** - Track all security events
2. **Session Management** - Track active logins
3. **Rate Limiting** - Prevent spam attacks
4. **IP Tracking** - Monitor login locations

---

## 🔐 Security Best Practices Used

### 1. **Password Security**
```typescript
// Passwords are hashed with bcrypt (10 rounds)
const hashedPassword = await bcrypt.hash(password, 10);

// Minimum requirements enforced
- At least 8 characters
- Contains uppercase and lowercase
- Contains numbers
- Contains special characters
```

### 2. **JWT Security**
```typescript
// Tokens expire after 7 days
expiresIn: '7d'

// Tokens are signed with secret key
jwt.sign(payload, process.env.JWT_SECRET)

// Refresh tokens for long sessions
refreshToken: jwt.sign(payload, secret, { expiresIn: '30d' })
```

### 3. **Rate Limiting**
```typescript
// Prevent brute force attacks
- Max 5 login attempts per 15 minutes
- Max 3 password reset requests per hour
- Max 100 API requests per 15 minutes
```

### 4. **Input Validation**
```typescript
// All inputs are validated
- Email format checked
- Password strength verified
- SQL injection prevented
- XSS attacks blocked
```

---

## 🚀 How to Use This API

### Setup

```bash
# 1. Install dependencies
npm install

# 2. Create .env file
PORT=5000
MONGO_URI=mongodb://localhost:27017/authguard
JWT_SECRET=your-super-secret-key-change-this
JWT_EXPIRES_IN=7d
REFRESH_TOKEN_SECRET=another-secret-key
REFRESH_TOKEN_EXPIRES_IN=30d

# 3. Start server
npm run dev
```

### API Examples

#### Register a New User
```bash
curl -X POST http://localhost:5000/api/v1/auth/register \
  -H "Content-Type: application/json" \
  -d '{
    "name": "John Doe",
    "email": "john@example.com",
    "password": "SecurePass123!"
  }'
```

#### Login
```bash
curl -X POST http://localhost:5000/api/v1/auth/login \
  -H "Content-Type: application/json" \
  -d '{
    "email": "john@example.com",
    "password": "SecurePass123!"
  }'
```

#### Get Profile (Protected Route)
```bash
curl -X GET http://localhost:5000/api/v1/auth/profile \
  -H "Authorization: Bearer YOUR_JWT_TOKEN_HERE"
```

---

## 📊 Database Models Explained

### User Model
```typescript
{
  name: "John Doe",              // User's full name
  email: "john@example.com",     // Unique email
  password: "hashed_password",   // Never plain text!
  role: "user",                  // user, admin, moderator
  isEmailVerified: false,        // Email confirmed?
  twoFactorEnabled: false,       // 2FA active?
  twoFactorSecret: "secret",     // For 2FA codes
  lastLogin: Date,               // When last logged in
  loginAttempts: 0,              // Failed login count
  lockUntil: Date,               // Account locked until
  preferences: {                 // User settings
    language: "en",
    notifications: true
  }
}
```

### Session Model
```typescript
{
  userId: ObjectId,              // Which user
  token: "jwt_token",            // The JWT
  ipAddress: "192.168.1.1",      // Where from
  userAgent: "Chrome/...",       // What browser
  isActive: true,                // Still valid?
  expiresAt: Date,               // When expires
  lastActivity: Date             // Last used
}
```

### AuditLog Model
```typescript
{
  userId: ObjectId,              // Who did it
  action: "login",               // What happened
  ipAddress: "192.168.1.1",      // From where
  userAgent: "Chrome/...",       // What browser
  success: true,                 // Did it work?
  details: {},                   // Extra info
  timestamp: Date                // When
}
```

---

## 🎓 Learning Path

### Beginner Level
1. ✅ Understand what authentication is
2. ✅ Learn about HTTP requests (GET, POST, etc.)
3. ✅ Understand JSON format
4. ✅ Learn about databases (MongoDB)

### Intermediate Level
1. ✅ Understand JWT tokens
2. ✅ Learn password hashing
3. ✅ Understand middleware concept
4. ✅ Learn about async/await

### Advanced Level
1. ✅ Implement 2FA
2. ✅ Add OAuth integration
3. ✅ Build audit logging
4. ✅ Add rate limiting

---

## 🐛 Common Issues & Solutions

### Issue 1: "Invalid token"
**Problem**: JWT token expired or invalid
**Solution**: Login again to get new token

### Issue 2: "Email already exists"
**Problem**: Trying to register with existing email
**Solution**: Use different email or login instead

### Issue 3: "Too many requests"
**Problem**: Rate limit exceeded
**Solution**: Wait 15 minutes and try again

---

## 📚 Further Learning Resources

### Concepts to Study
1. **REST API** - How web APIs work
2. **MongoDB** - NoSQL database
3. **Express.js** - Web framework
4. **TypeScript** - Typed JavaScript
5. **JWT** - Token-based authentication
6. **bcrypt** - Password hashing
7. **OAuth 2.0** - Third-party login

### Recommended Reading
- [Express.js Documentation](https://expressjs.com/)
- [MongoDB Documentation](https://docs.mongodb.com/)
- [JWT.io](https://jwt.io/)
- [OWASP Security Guide](https://owasp.org/)

---

## 🎯 Next Steps

### To Learn This Project
1. Read this guide completely
2. Look at [`auth.ts`](src/controllers/auth.ts) controller
3. Understand the User model
4. Try the API endpoints with Postman
5. Modify and experiment!

### To Improve This Project
1. Add email templates
2. Add password strength meter
3. Add social media OAuth (Facebook, Twitter)
4. Add biometric authentication
5. Add account recovery questions

---

**Remember**: This is a production-ready authentication system. Take time to understand each part, and don't hesitate to experiment!

**Happy Learning! 🚀**