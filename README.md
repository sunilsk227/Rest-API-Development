# 🎮 CodeGame — Gamified Coding Learning Platform

> A full-stack MERN application where students learn programming by solving interactive challenges, earning points, and unlocking levels like a video game.

---

## 📋 TABLE OF CONTENTS

1. [Project Overview](#1-project-overview)
2. [Folder Structure](#2-folder-structure)
3. [Setup Instructions](#3-setup-instructions)
4. [How to Run](#4-how-to-run)
5. [Database Schema](#5-database-schema)
6. [API Routes](#6-api-routes)
7. [Frontend ↔ Backend Connection](#7-frontend--backend-connection)
8. [Features Explained](#8-features-explained)
9. [Common Errors & Fixes](#9-common-errors--fixes)
10. [Improvements for Final Year](#10-improvements-for-final-year)
11. [Viva Questions & Answers](#11-viva-questions--answers)

---

## 1. Project Overview

**CodeGame** is a web application that teaches Python programming through an interactive, game-based approach. Instead of watching videos or reading text, users:

- 🎮 **Play through Levels** — Each level covers a programming concept (Variables, Loops, Functions, etc.)
- ✅ **Solve Challenges** — Multiple choice, fill-in-the-blank, and output prediction questions
- ⚡ **Earn Points** — Correct answers earn XP; speed bonuses reward fast solvers
- 🔓 **Unlock Levels** — Spend earned points to unlock harder levels
- 🏆 **Compete** — A global leaderboard ranks all players by total points
- 🔥 **Build Streaks** — Daily login tracking rewards consistent practice
- 🏅 **Collect Badges** — Special achievements unlock automatically

---

## 2. Folder Structure

```
codegame/
│
├── 📁 server/                    ← Node.js + Express Backend
│   ├── server.js                 ← Main server entry point
│   ├── .env                      ← Environment variables (SECRET)
│   ├── package.json
│   │
│   ├── 📁 models/                ← MongoDB Schemas
│   │   ├── User.js               ← User data, progress, streaks
│   │   ├── Level.js              ← Game level definitions
│   │   └── Challenge.js          ← Individual coding challenges
│   │
│   ├── 📁 routes/                ← API Endpoints
│   │   ├── auth.js               ← POST /signup, POST /login, GET /me
│   │   ├── levels.js             ← GET /levels, POST /unlock
│   │   ├── challenges.js         ← GET challenges, POST /submit, GET /hint
│   │   ├── leaderboard.js        ← GET /leaderboard
│   │   └── user.js               ← GET /profile, GET /stats
│   │
│   ├── 📁 middleware/
│   │   └── auth.js               ← JWT verification middleware
│   │
│   └── 📁 seed/
│       └── seedData.js           ← Populates DB with levels & challenges
│
└── 📁 client/                    ← React Frontend
    ├── index.html
    ├── vite.config.js
    ├── package.json
    │
    └── 📁 src/
        ├── main.jsx              ← React entry point
        ├── App.jsx               ← Routes & layout
        │
        ├── 📁 api/
        │   └── axios.js          ← Axios HTTP client (auto-attaches JWT)
        │
        ├── 📁 context/
        │   └── AuthContext.jsx   ← Global auth state (user, login, logout)
        │
        ├── 📁 styles/
        │   └── global.css        ← All global styles, CSS variables
        │
        ├── 📁 components/
        │   ├── Navbar.jsx        ← Top navigation bar
        │   └── Toast.jsx         ← Notification system
        │
        └── 📁 pages/
            ├── LandingPage.jsx   ← Home page (public)
            ├── LoginPage.jsx     ← Login + Signup forms
            ├── SignupPage.jsx    ← Re-exports from LoginPage
            ├── Dashboard.jsx     ← User dashboard with stats
            ├── LevelsPage.jsx    ← All game levels grid
            ├── ChallengePage.jsx ← Main game screen (THE CORE)
            ├── LeaderboardPage.jsx ← Global rankings
            └── ProfilePage.jsx   ← User profile & badges
```

---

## 3. Setup Instructions

### Prerequisites
Make sure you have these installed:
- **Node.js** v18+ → Download from https://nodejs.org
- **MongoDB** → Install from https://mongodb.com/try/download/community
  - OR use **MongoDB Atlas** (free cloud database) → https://cloud.mongodb.com
- **Git** (optional but recommended)

### Step 1: Check installations
```bash
node --version     # Should show v18.x.x or higher
npm --version      # Should show 9.x.x or higher
mongod --version   # Should show 6.x or 7.x
```

### Step 2: Clone / create the project
```bash
# If using git:
git clone <your-repo-url>
cd codegame

# Or just navigate to where you saved the files:
cd path/to/codegame
```

### Step 3: Install Backend Dependencies
```bash
cd server
npm install
```
This installs: express, mongoose, bcryptjs, jsonwebtoken, cors, dotenv, nodemon

### Step 4: Configure Environment Variables
Edit `server/.env`:
```env
PORT=5000
MONGO_URI=mongodb://localhost:27017/codegame
JWT_SECRET=super_secret_key_change_me_in_production_abc123
NODE_ENV=development
CLIENT_URL=http://localhost:5173
```

**If using MongoDB Atlas (cloud):**
```env
MONGO_URI=mongodb+srv://username:password@cluster.mongodb.net/codegame
```

### Step 5: Seed the Database
```bash
# Make sure you're in the server folder
cd server
node seed/seedData.js
```
You should see:
```
✅ Connected to MongoDB
🗑️  Cleared existing data
✅ Level "Variables & Data Types": 5 challenges
✅ Level "Conditionals (If/Else)": 4 challenges
...
🎮 Database seeded successfully!
```

### Step 6: Install Frontend Dependencies
```bash
# Open a new terminal
cd client
npm install
```
This installs: react, react-dom, react-router-dom, axios

---

## 4. How to Run

### Start the Backend
```bash
cd server
npm run dev       # Uses nodemon (auto-restarts on changes)
# OR:
npm start         # Regular start (no auto-restart)
```
✅ You should see: `🚀 Server running at http://localhost:5000`

### Start the Frontend (new terminal)
```bash
cd client
npm run dev
```
✅ You should see: `Local: http://localhost:5173`

### Open the App
Go to **http://localhost:5173** in your browser.

**Both terminals must be running simultaneously!**

---

## 5. Database Schema

### User Collection
```javascript
{
  username: "CoolCoder99",           // Unique display name
  email: "user@example.com",         // Unique email (lowercase)
  password: "$2b$12$hashed...",      // bcrypt hashed password
  totalPoints: 250,                  // Total XP earned
  currentLevel: 2,                   // Current level number
  rank: "Apprentice",                // Auto-calculated rank
  unlockedLevels: [ObjectId, ...],   // Array of level IDs
  completedChallenges: [{
    challengeId: ObjectId,
    pointsEarned: 15,
    timeTaken: 23,                   // seconds
    completedAt: Date
  }],
  badges: [{
    name: "First Steps",
    description: "Completed first challenge!",
    icon: "👣",
    earnedAt: Date
  }],
  currentStreak: 3,                  // Days in a row
  longestStreak: 7,
  lastActiveDate: Date,
  totalChallengesCompleted: 12,
  totalTimeSpent: 1440,              // seconds
  createdAt: Date,
  updatedAt: Date
}
```

### Level Collection
```javascript
{
  title: "Variables & Data Types",
  description: "Learn how to store data...",
  order: 1,                    // Sort order (1 = first)
  icon: "📦",
  color: "#6c63ff",
  requiredPoints: 0,           // Points needed to unlock
  totalChallenges: 5,
  difficulty: "Beginner",
  xpReward: 50                 // Bonus XP for completing level
}
```

### Challenge Collection
```javascript
{
  levelId: ObjectId,           // Which level this belongs to
  title: "What is a Variable?",
  description: "Test your understanding...",
  type: "multiple-choice",     // multiple-choice | fill-blank | output-predict | fix-bug
  question: "What is a variable?",
  codeSnippet: "x = 5\nprint(x)", // Optional code display
  options: ["A) ...", "B) ...", "C) ...", "D) ..."],
  correctAnswer: "B",          // NEVER sent to frontend
  explanation: "A variable is...",
  hints: ["Hint 1", "Hint 2", "Hint 3"],
  points: 10,
  bonusPoints: 5,
  timeLimit: 30,               // seconds (0 = no limit)
  difficulty: "Easy",
  order: 1                     // Sort order within level
}
```

---

## 6. API Routes

### Authentication Routes (`/api/auth`)
| Method | Route | Description | Auth Required |
|--------|-------|-------------|---------------|
| POST | `/api/auth/signup` | Create new account | ❌ No |
| POST | `/api/auth/login` | Login, get JWT token | ❌ No |
| GET | `/api/auth/me` | Get current user info | ✅ Yes |

**Signup Request Body:**
```json
{ "username": "CoolCoder", "email": "user@test.com", "password": "secret123" }
```

**Login Response:**
```json
{
  "success": true,
  "token": "eyJhbGci...",
  "user": { "id": "...", "username": "CoolCoder", "totalPoints": 0, "rank": "Novice" }
}
```

### Level Routes (`/api/levels`)
| Method | Route | Description |
|--------|-------|-------------|
| GET | `/api/levels` | Get all levels with user progress |
| GET | `/api/levels/:id` | Get specific level + its challenges |
| POST | `/api/levels/:id/unlock` | Unlock a level |

### Challenge Routes (`/api/challenges`)
| Method | Route | Description |
|--------|-------|-------------|
| GET | `/api/challenges/level/:levelId` | Get challenges for a level |
| GET | `/api/challenges/:id` | Get single challenge (no answer) |
| POST | `/api/challenges/:id/submit` | Submit answer, get result + points |
| GET | `/api/challenges/:id/hint` | Get a hint (costs points) |

**Submit Body:**
```json
{ "answer": "B", "timeTaken": 15, "usedHints": 0 }
```

**Submit Response (correct):**
```json
{
  "correct": true,
  "pointsEarned": 20,
  "bonusAwarded": ["+5 Speed Bonus ⚡"],
  "newBadges": [{ "name": "First Steps", "icon": "👣" }],
  "explanation": "A variable is a named storage location...",
  "levelCompleted": false,
  "totalPoints": 20
}
```

### Other Routes
| Method | Route | Description |
|--------|-------|-------------|
| GET | `/api/leaderboard` | Top 50 users by points |
| GET | `/api/user/stats` | Current user's statistics |
| GET | `/api/user/profile` | Full profile with populated levels |

---

## 7. Frontend ↔ Backend Connection

### How JWT Authentication Works
```
1. User logs in → Backend creates JWT token → Stored in localStorage
2. Every API request → Axios interceptor auto-attaches token as header:
   Authorization: Bearer eyJhbGciOiJIUzI1NiI...
3. Backend middleware verifies token → Extracts user → Attaches to req.user
4. Route handlers use req.user to personalize response
5. Token expires in 7 days → User must login again
```

### React State Flow
```
AuthContext (global state)
    ↓ provides: { user, login, logout, updateUser }
    ↓
App.jsx (routing)
    ↓ protects routes with <ProtectedRoute>
    ↓
Pages use: useAuth() hook to access user data
Pages use: API (axios instance) to call backend
Pages use: updateUser() to sync state after earning points
```

### Vite Proxy (Development Only)
In `vite.config.js`, requests to `/api/*` are forwarded to `http://localhost:5000`:
```javascript
proxy: {
  '/api': { target: 'http://localhost:5000', changeOrigin: true }
}
```
This means you write `API.get('/levels')` and it hits `localhost:5000/api/levels`. No CORS issues!

---

## 8. Features Explained

### 🔥 Streak System
- On every login, `user.updateStreak()` is called
- If `lastActiveDate` was yesterday → streak increases
- If more than 1 day gap → streak resets to 1
- Longest streak is always tracked

### 🏅 Badge System
Badges are automatically awarded when:
- **First Steps** → Complete first challenge
- **Speed Demon** → Solve in under 10 seconds
- **Century** → Reach 100 total points
- **High Scorer** → Reach 500 points
- **Week Warrior** → 7-day streak
- **Level Master** → Complete an entire level

### ⏱️ Timer-Based Challenges
- Each challenge has a `timeLimit` field (seconds)
- Frontend counts down; backend calculates `timeTaken` from submission timestamp
- Speed bonus awarded if solved in < 30% of time limit
- Timer turns red when < 25% remains

### 🔓 Level Unlocking
- Users start with Level 1 unlocked (done at signup)
- Each level has `requiredPoints` threshold
- When a level is completed, the next level auto-unlocks (via backend)
- Users can also manually unlock by clicking on a locked level

### 🤖 AI Hint System
- Up to 3 hints available per challenge
- First tries to fetch hints from database
- Falls back to AI-generated contextual hints based on challenge type
- Each hint deducts 2 points from the score

### 📊 Rank System
| Points | Rank |
|--------|------|
| 0 | Novice 🌱 |
| 200 | Apprentice 📚 |
| 500 | Coder 💻 |
| 1,000 | Developer 🛠️ |
| 2,000 | Engineer ⚙️ |
| 3,000 | Architect 🏛️ |
| 5,000 | Legend 🌟 |

---

## 9. Common Errors & Fixes

### ❌ `ECONNREFUSED` or MongoDB not connecting
**Cause:** MongoDB is not running
**Fix:**
```bash
# On Mac/Linux:
sudo service mongod start
# Or:
mongod

# On Windows (Run as Administrator):
net start MongoDB
```

### ❌ `Cannot find module 'express'`
**Cause:** npm install not run
**Fix:**
```bash
cd server
npm install
```

### ❌ `Invalid Token` or 401 errors
**Cause:** JWT secret mismatch or expired token
**Fix:**
1. Clear localStorage: In browser console → `localStorage.clear()`
2. Login again
3. Make sure `JWT_SECRET` in `.env` hasn't changed

### ❌ Frontend shows blank / API calls fail
**Cause:** Backend not running or Vite proxy not working
**Fix:**
1. Make sure backend is running on port 5000
2. Check `vite.config.js` has the proxy config
3. Try `http://localhost:5000/api/health` in browser - should return `{"status":"OK"}`

### ❌ `MongoServerError: E11000 duplicate key`
**Cause:** Trying to insert a user with email/username that already exists
**Fix:** This is handled - backend returns a friendly error message.

### ❌ Seed fails with `Cannot find module '../.env'`
**Cause:** Running seed from wrong directory
**Fix:**
```bash
# Must run from the server directory
cd server
node seed/seedData.js
```

### ❌ React: "useAuth must be used within AuthProvider"
**Cause:** Component used outside of AuthProvider wrapper
**Fix:** Make sure `<AuthProvider>` wraps everything in `main.jsx`

---

## 10. Improvements for Final Year Level

### 🚀 Technical Upgrades
1. **Real Code Execution** — Use Judge0 API or Piston API to actually run submitted Python code
   ```javascript
   // Instead of comparing strings, run the code:
   const result = await judge0.run({ language: 'python', code: userCode });
   ```

2. **WebSocket for Real-time** — Use Socket.io for live leaderboard updates
3. **Redis Caching** — Cache leaderboard results for performance
4. **Docker** — Containerize both frontend and backend
5. **CI/CD Pipeline** — GitHub Actions for automated testing and deployment
6. **Unit Testing** — Jest for backend, React Testing Library for frontend
7. **Rate Limiting** — Prevent cheating/spam with express-rate-limit
8. **OAuth** — Google/GitHub login with Passport.js

### 🎮 Feature Upgrades
1. **Real Code Editor** — Integrate Monaco Editor (VS Code's editor)
2. **Multiplayer Mode** — Challenge a friend in real-time
3. **Custom Challenges** — Let instructors create their own challenges
4. **Video Explanations** — Embed YouTube videos for each concept
5. **Discussion Forum** — Let students help each other with challenges
6. **Admin Dashboard** — Analytics: which challenges are hardest, where users drop off
7. **Mobile App** — React Native version of the platform
8. **Certificate System** — Generate PDF certificates on level completion
9. **Multiple Languages** — Support JavaScript, Java, C++ in addition to Python

### 📊 Data & Analytics
1. Track which challenges have the highest failure rates
2. A/B test different hint types
3. Recommendation engine ("You should try X challenge next")

---

## 11. Viva Questions & Answers

### 📌 Basic Questions

**Q1: What is the MERN stack?**
> MERN stands for MongoDB, Express.js, React, and Node.js. MongoDB is the database (NoSQL, stores JSON), Express.js is the web framework for Node.js that handles API routes, React is the frontend UI library, and Node.js is the JavaScript runtime that runs the backend server.

**Q2: What is JWT and why do you use it?**
> JWT (JSON Web Token) is a compact, self-contained way to securely transmit information between parties as a JSON object. We use it for authentication — after login, the server creates a token containing the user's ID and a signature. The client stores this and sends it with every request. The server verifies the signature without needing to look up the database each time, making it stateless and scalable.

**Q3: What is the difference between authentication and authorization?**
> Authentication verifies WHO you are (login with email/password). Authorization determines WHAT you can do (can you access this route?). In our app, JWT handles authentication. Our `protect` middleware handles authorization — it verifies the token before allowing access to protected routes.

**Q4: Why did you use bcrypt for passwords?**
> bcrypt is a password hashing function specifically designed to be slow, making brute-force attacks impractical. We never store plain-text passwords. When a user signs up, we hash their password with a salt (random data) using 12 rounds. When they login, we compare the entered password against the stored hash using `bcrypt.compare()`.

**Q5: What is the difference between SQL and NoSQL? Why MongoDB?**
> SQL databases use tables with fixed schemas and relationships (JOIN operations). NoSQL databases like MongoDB use flexible documents (JSON-like) that can have different structures. We chose MongoDB because our data (users with varying badges, challenges with different option counts) fits naturally into documents, and it pairs perfectly with JavaScript/Node.js.

### 📌 Intermediate Questions

**Q6: Explain the middleware pattern in Express.**
> Middleware functions in Express have access to `req`, `res`, and `next`. They can modify the request/response, end the cycle, or pass control to the next middleware via `next()`. Our `protect` middleware checks for a JWT token on protected routes — if valid, it attaches the user to `req.user` and calls `next()` to proceed; otherwise it sends a 401 error.

**Q7: How does React Router work in your app?**
> React Router allows client-side navigation without page reloads. We define `<Route>` components in `App.jsx`. When the URL changes, React Router renders the matching component. We have protected routes (require login) and public routes. The `useNavigate` hook lets components navigate programmatically.

**Q8: What is the Context API and why did you use it?**
> React's Context API provides a way to share state across components without prop drilling (passing props through many levels). We use `AuthContext` to make the user's login state, `login()`, and `logout()` functions available to any component. Without Context, we'd have to pass user data as props through every component level.

**Q9: How does the streak system work?**
> On each login, we compare `lastActiveDate` with today's date:
> - Same day → no change (already logged in today)
> - Yesterday → increment streak by 1 (consecutive day!)
> - More than 1 day ago → reset streak to 1 (broken)
> We store `currentStreak`, `longestStreak`, and `lastActiveDate` in the User model and update them via a model method `user.updateStreak()`.

**Q10: How do you prevent users from cheating (getting the answer from API response)?**
> The correct answer is never sent to the client. In the GET challenge routes, we use `.select('-correctAnswer')` in Mongoose to exclude the answer field from the response. The answer is only accessed server-side in the POST `/submit` route, where we compare the user's answer against the database value.

### 📌 Advanced Questions

**Q11: What is CORS and why do you need it?**
> CORS (Cross-Origin Resource Sharing) is a browser security mechanism that blocks HTTP requests from a different origin (domain/port) than the server. Our React app runs on port 5173 and our API on port 5000 — different ports = different origins. We use the `cors` package on the Express server to allow requests from our frontend URL. In production, you'd restrict this to your exact domain.

**Q12: How would you scale this application for 100,000 users?**
> Several strategies: (1) **Horizontal scaling** — run multiple Node.js instances with a load balancer like Nginx. (2) **MongoDB Atlas** — cloud-managed MongoDB with auto-scaling. (3) **Redis caching** — cache the leaderboard (doesn't need real-time accuracy). (4) **CDN** — serve the React build files from a CDN. (5) **Database indexing** — add indexes on `totalPoints` for leaderboard queries and `email`/`username` for lookups.

**Q13: What security vulnerabilities did you consider?**
> (1) **SQL/NoSQL Injection** — Mongoose sanitizes inputs by default. (2) **XSS** — React escapes HTML by default. (3) **CSRF** — JWT in Authorization header (not cookies) is immune to CSRF. (4) **Brute force** — Should add rate limiting on `/login`. (5) **Password security** — bcrypt hashing with 12 salt rounds. (6) **JWT expiry** — Tokens expire in 7 days, forcing re-authentication.

**Q14: How does the Vite proxy solve CORS in development?**
> In development, React runs on port 5173 and Express on 5000. Without the proxy, the browser would block the request due to CORS. Vite's dev server proxy intercepts requests matching `/api/*` and forwards them to `localhost:5000` — from the browser's perspective, it's a same-origin request (same port 5173), so no CORS issue. In production, you'd serve the React build from Express directly or configure proper CORS headers.

**Q15: What is the purpose of the pre-save hook in your User model?**
> The `pre('save')` hook in Mongoose runs before every `.save()` call. We use it to automatically hash the user's password before it's stored in the database. The key check is `if (!this.isModified('password')) return next()` — this ensures we only re-hash when the password field actually changed (not on every other update like changing the streak). Without this check, every `user.save()` call would re-hash an already-hashed password.

---

## 🎯 Quick Reference

### Development Commands
```bash
# Backend
cd server && npm run dev          # Start with auto-restart
cd server && node seed/seedData.js # Reset database with sample data

# Frontend
cd client && npm run dev          # Start development server
cd client && npm run build        # Build for production
```

### Test the API with curl
```bash
# Health check
curl http://localhost:5000/api/health

# Signup
curl -X POST http://localhost:5000/api/auth/signup \
  -H "Content-Type: application/json" \
  -d '{"username":"TestUser","email":"test@test.com","password":"test123"}'

# Login
curl -X POST http://localhost:5000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"test@test.com","password":"test123"}'
```

### Environment Variables Checklist
- [ ] `PORT` = 5000
- [ ] `MONGO_URI` = MongoDB connection string
- [ ] `JWT_SECRET` = Long random string (change in production!)
- [ ] `CLIENT_URL` = Frontend URL

---

*Built with ❤️ using MongoDB, Express, React, and Node.js*
