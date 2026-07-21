# Schedula Backend - Authentication System

A comprehensive healthcare appointment scheduling backend built with **NestJS**, **PostgreSQL**, and **JWT Authentication**.

---

## Table of Contents

- [Features](#features)
- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Environment Setup](#environment-setup)
- [Database Setup](#database-setup)
- [Running the Project](#running-the-project)
- [API Documentation](#api-documentation)
- [Testing with Postman](#testing-with-postman)
- [Authentication Flow](#authentication-flow)
- [Contributing](#contributing)

---

## Features

✅ **User Registration (Signup)**
- Hash passwords using bcryptjs
- Validate email uniqueness
- Support for DOCTOR and PATIENT roles

✅ **User Authentication (Login)**
- Email and password verification
- JWT token generation with 24-hour expiration
- Secure password comparison

✅ **JWT Authentication Guard**
- Middleware to verify JWT tokens
- Extract user information from token
- Automatic token validation on protected routes

✅ **Role-Based Authorization**
- Decorator-based role verification
- Separate protected routes for DOCTOR and PATIENT
- Return 403 Forbidden for unauthorized roles

✅ **Protected Routes**
- `/doctor/profile` - Only accessible to DOCTOR role
- `/patient/profile` - Only accessible to PATIENT role

✅ **Error Handling**
- Proper HTTP status codes
- Meaningful error messages
- Input validation via DTOs

---

## Tech Stack

- **Framework**: NestJS (Node.js framework)
- **Language**: TypeScript
- **Database**: PostgreSQL
- **ORM**: Prisma
- **Authentication**: JWT (JSON Web Tokens)
- **Password Hashing**: bcryptjs
- **Validation**: NestJS built-in validation

---

## Project Structure

```
src/
├── auth/
│   ├── dto/
│   │   ├── signup.dto.ts          # Signup request DTO
│   │   ├── login.dto.ts           # Login request DTO
│   │   └── index.ts
│   ├── guards/
│   │   └── auth.guard.ts          # JWT verification + role checking
│   ├── middleware/
│   │   ├── jwt-auth.middleware.ts # JWT validation middleware
│   │   └── role-auth.middleware.ts # Role authorization middleware
│   ├── auth.controller.ts         # /auth endpoints
│   ├── auth.service.ts            # Auth business logic
│   └── auth.module.ts             # Auth module
├── doctor/
│   ├── doctor.controller.ts       # /doctor endpoints
│   ├── doctor.service.ts          # Doctor business logic
│   └── doctor.module.ts
├── patient/
│   ├── patient.controller.ts      # /patient endpoints
│   ├── patient.service.ts         # Patient business logic
│   └── patient.module.ts
├── prisma/
│   ├── prisma.service.ts          # Prisma service (dependency injection)
│   └── prisma.module.ts           # Prisma module
├── prisma/
│   └── schema.prisma              # Database schema
├── app.module.ts                  # Root module
├── main.ts                        # Entry point
└── ...

Configuration Files:
├── .env                           # Environment variables
├── prisma.config.ts               # Prisma configuration
├── tsconfig.json                  # TypeScript configuration
├── nest-cli.json                  # NestJS CLI configuration
└── package.json                   # Dependencies
```

---

## Prerequisites

Before you begin, ensure you have the following installed:

- **Node.js** (v18 or higher) - [Download](https://nodejs.org/)
- **npm** (comes with Node.js)
- **PostgreSQL** (v12 or higher) - [Download](https://www.postgresql.org/download/)
- **Git** (for version control)

---

## Installation

### 1. Clone or Navigate to the Project

```bash
cd schedula-backend
```

### 2. Install Dependencies

```bash
npm install
```

This installs all required packages including:
- @nestjs/core, @nestjs/common
- @prisma/client, prisma
- bcryptjs, jsonwebtoken
- TypeScript, ESLint, Prettier

---

## Environment Setup

### 1. Create `.env` File

Create a `.env` file in the root directory with the following variables:

```env
# PostgreSQL Database URL
DATABASE_URL="postgresql://postgres:your_password@localhost:5432/schedula"

# JWT Secret Key (Change this in production!)
JWT_SECRET="your-secret-key-change-this-in-production"
```

**Example with actual values:**
```env
DATABASE_URL="postgresql://postgres:mypassword123@localhost:5432/schedula"
JWT_SECRET="my-super-secret-key-12345"
```

### 2. Update Variables

- Replace `postgres` with your PostgreSQL username
- Replace `your_password` with your PostgreSQL password
- Replace `localhost:5432` if your database is on a different server
- Update `JWT_SECRET` with a strong secret key

---

## Database Setup

### 1. Create PostgreSQL Database

Open PostgreSQL command line or pgAdmin and create the database:

```sql
CREATE DATABASE schedula;
```

### 2. Configure Prisma

Ensure your `.env` has the correct `DATABASE_URL` (see Environment Setup above).

### 3. Run Prisma Migration

This creates the User table in your database:

```bash
npx prisma migrate dev --name init
```

**Expected Output:**
```
✔ Prisma Migrate created the migration folder and a migration file.
✔ Prisma schema loaded from prisma\schema.prisma
✔ Datasource "db": PostgreSQL database "schedula"
✔ Executed migrations
```

### 4. Verify Database

View your database schema:

```bash
npx prisma studio
```

This opens a visual database explorer at http://localhost:5555

---

## Running the Project

## Project setup

```bash
$ npm install
```

## Compile and run the project

```bash
# development
$ npm run start

# watch mode
$ npm run start:dev

# production mode
$ npm run start:prod
```

---

## Running the Project

### 1. Start Development Server

```bash
npm run start:dev
```

The server will start on `http://localhost:3000`

Expected output:
```
[Nest] 1234  - 07/21/2026, 10:30:00 AM     LOG [NestFactory] Starting Nest application...
[Nest] 1234  - 07/21/2026, 10:30:00 AM     LOG [InstanceLoader] AuthModule dependencies initialized
[Nest] 1234  - 07/21/2026, 10:30:01 AM     LOG [RouterExplorer] Mapped /auth/signup (POST)
[Nest] 1234  - 07/21/2026, 10:30:01 AM     LOG [RouterExplorer] Mapped /auth/login (POST)
[Nest] 1234  - 07/21/2026, 10:30:01 AM     LOG [NestApplication] Nest application successfully started
```

### 2. Build for Production

```bash
npm run build
```

This compiles TypeScript to JavaScript in the `dist/` folder.

### 3. Run Production Build

```bash
npm run start:prod
```

---

## API Documentation

### Base URL
```
http://localhost:3000
```

### 1. User Signup

**Endpoint:** `POST /auth/signup`

**Request:**
```json
{
  "name": "Dr. Rahul",
  "email": "doctor@gmail.com",
  "password": "password123",
  "role": "DOCTOR"
}
```

**Responses:**

**Success (201 Created):**
```json
{
  "message": "User registered successfully",
  "user": {
    "id": 1,
    "name": "Dr. Rahul",
    "email": "doctor@gmail.com",
    "role": "DOCTOR"
  }
}
```

**Error (400 Bad Request - Email exists):**
```json
{
  "message": "Email already exists",
  "error": "Bad Request",
  "statusCode": 400
}
```

---

### 2. User Login

**Endpoint:** `POST /auth/login`

**Request:**
```json
{
  "email": "doctor@gmail.com",
  "password": "password123"
}
```

**Success (200 OK):**
```json
{
  "message": "Login successful",
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6MSwiZW...",
  "user": {
    "id": 1,
    "name": "Dr. Rahul",
    "email": "doctor@gmail.com",
    "role": "DOCTOR"
  }
}
```

**Error (401 Unauthorized):**
```json
{
  "message": "Invalid credentials",
  "error": "Unauthorized",
  "statusCode": 401
}
```

---

### 3. Get Doctor Profile

**Endpoint:** `GET /doctor/profile`

**Headers:**
```
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

**Allowed Roles:** DOCTOR only

**Success (200 OK):**
```json
{
  "message": "Doctor profile retrieved",
  "user": {
    "id": 1,
    "name": "Dr. Rahul",
    "email": "doctor@gmail.com",
    "role": "DOCTOR",
    "createdAt": "2024-01-15T10:30:00.000Z"
  }
}
```

**Error (401 Unauthorized - No token):**
```json
{
  "message": "Missing or invalid token",
  "error": "Unauthorized",
  "statusCode": 401
}
```

**Error (403 Forbidden - Wrong role):**
```json
{
  "message": "Access denied for this role",
  "error": "Forbidden",
  "statusCode": 403
}
```

---

### 4. Get Patient Profile

**Endpoint:** `GET /patient/profile`

**Headers:**
```
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

**Allowed Roles:** PATIENT only

**Success (200 OK):**
```json
{
  "message": "Patient profile retrieved",
  "user": {
    "id": 2,
    "name": "Priya",
    "email": "patient@gmail.com",
    "role": "PATIENT",
    "createdAt": "2024-01-15T10:35:00.000Z"
  }
}
```

---

## Testing with Postman

### Step 1: Import Collection

1. Open Postman
2. Click **Import** → Select `Schedula_Auth_Postman_Collection.json`
3. The collection loads with all pre-configured requests

### Step 2: Test Signup

**Signup Doctor:**
- Select request: **Signup Doctor**
- Click **Send**
- Expected: 201 Created

**Signup Patient:**
- Select request: **Signup Patient**
- Click **Send**
- Expected: 201 Created

### Step 3: Test Login & Save Tokens

**Login Doctor:**
- Select request: **Login Doctor**
- Click **Send**
- Copy the `token` from response
- In Postman, go to **Variables** tab
- Set `doctor_token = <copied_token>`

**Login Patient:**
- Select request: **Login Patient**
- Click **Send**
- Copy the `token` from response
- Set `patient_token = <copied_token>`

### Step 4: Test Protected Routes

**Doctor accessing Doctor Profile (Should succeed):**
- Select request: **Get Doctor Profile (Doctor Token)**
- Click **Send**
- Expected: 200 OK

**Doctor accessing Patient Profile (Should fail):**
- Select request: **Get Doctor Profile (Patient Token - Should Fail)**
- Click **Send**
- Expected: 403 Forbidden

**Patient accessing Patient Profile (Should succeed):**
- Select request: **Get Patient Profile (Patient Token)**
- Click **Send**
- Expected: 200 OK

**Patient accessing Doctor Profile (Should fail):**
- Select request: **Get Patient Profile (Doctor Token - Should Fail)**
- Click **Send**
- Expected: 403 Forbidden

---

## Authentication Flow

### Registration Flow
```
User fills signup form
        ↓
POST /auth/signup
        ↓
Validate input (email, password, role)
        ↓
Check if email already exists
        ↓
Hash password using bcrypt
        ↓
Store user in database
        ↓
Return 201 Created with user info
```

### Login Flow
```
User enters email & password
        ↓
POST /auth/login
        ↓
Find user by email in database
        ↓
Compare password using bcrypt
        ↓
If match: Generate JWT token
        ↓
Return 200 OK with token
        ↓
If no match: Return 401 Unauthorized
```

### Protected Route Access
```
Client sends request with Bearer token
        ↓
GET /doctor/profile
  Authorization: Bearer token
        ↓
AuthGuard extracts token from header
        ↓
Verify JWT signature and expiration
        ↓
Extract user ID and role from token
        ↓
Check if role is DOCTOR
        ↓
If authorized: Execute route handler
        ↓
Return 200 OK with profile data
        ↓
If unauthorized: Return 403 Forbidden
```

---

## JWT Token Structure

JWT tokens contain three parts separated by dots: `header.payload.signature`

**Payload Example:**
```json
{
  "id": 1,
  "role": "DOCTOR",
  "iat": 1705318200,
  "exp": 1705404600
}
```

**Token Properties:**
- `id` - User ID in database
- `role` - User role (DOCTOR or PATIENT)
- `iat` - Issued at (timestamp)
- `exp` - Expiration time (24 hours from creation)

**Decode tokens at:** [jwt.io](https://jwt.io)

---

## Database Schema

```prisma
enum UserRole {
  DOCTOR
  PATIENT
}

model User {
  id        Int     @id @default(autoincrement())
  name      String
  email     String  @unique
  password  String
  role      UserRole
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
}
```

---

## Common Issues & Solutions

### Issue: "Authentication failed against database server"
**Solution:**
- Verify PostgreSQL is running
- Check `.env` DATABASE_URL is correct
- Ensure database exists: `CREATE DATABASE schedula;`

### Issue: "Cannot find module '@prisma/client'"
**Solution:**
```bash
npm install @prisma/client
npx prisma generate
```

### Issue: "Port 3000 is already in use"
**Solution:**
```bash
# Find and kill process on port 3000
# Windows: netstat -ano | findstr :3000
# Mac/Linux: lsof -i :3000
```

### Issue: "Token is invalid or expired"
**Solution:**
- Generate new token by logging in again
- Check JWT_SECRET matches between `.env` and code
- Verify token hasn't been modified

---

## Available Scripts

```bash
# Install dependencies
npm install

# Run in development mode (with hot reload)
npm run start:dev

# Build for production
npm run build

# Run production build
npm run start:prod

# Format code with Prettier
npm run format

# Lint code with ESLint
npm run lint

# Run unit tests
npm test

# Run e2e tests
npm run test:e2e

# Run tests with coverage
npm run test:cov
```

---

## Contributing

1. Create feature branch: `git checkout -b feature/authentication`
2. Make your changes
3. Commit with meaningful messages:
   ```bash
   git commit -m "Add user signup API"
   ```
4. Push to branch: `git push origin feature/authentication`
5. Open Pull Request

---

## Next Steps

- [ ] Set up PostgreSQL database
- [ ] Configure `.env` with valid credentials
- [ ] Run `npx prisma migrate dev --name init`
- [ ] Start dev server: `npm run start:dev`
- [ ] Import Postman collection
- [ ] Test all API endpoints
- [ ] Record Loom walkthrough video
- [ ] Push to GitHub and create PR

---

## Resources

- [NestJS Documentation](https://docs.nestjs.com/)
- [Prisma Documentation](https://www.prisma.io/docs/)
- [JWT Introduction](https://jwt.io/introduction)
- [bcryptjs Documentation](https://github.com/dcodeIO/bcrypt.js)
- [PostgreSQL Documentation](https://www.postgresql.org/docs/)

---

## License

This project is licensed under the UNLICENSED license.

---

**Happy Coding!** 🚀
$ npm run test

# e2e tests
$ npm run test:e2e

# test coverage
$ npm run test:cov
```

## Deployment

When you're ready to deploy your NestJS application to production, there are some key steps you can take to ensure it runs as efficiently as possible. Check out the [deployment documentation](https://docs.nestjs.com/deployment) for more information.

If you are looking for a cloud-based platform to deploy your NestJS application, check out [Mau](https://mau.nestjs.com), our official platform for deploying NestJS applications on AWS. Mau makes deployment straightforward and fast, requiring just a few simple steps:

```bash
$ npm install -g @nestjs/mau
$ mau deploy
```

With Mau, you can deploy your application in just a few clicks, allowing you to focus on building features rather than managing infrastructure.

## Resources

Check out a few resources that may come in handy when working with NestJS:

- Visit the [NestJS Documentation](https://docs.nestjs.com) to learn more about the framework.
- For questions and support, please visit our [Discord channel](https://discord.gg/G7Qnnhy).
- To dive deeper and get more hands-on experience, check out our official video [courses](https://courses.nestjs.com/).
- Deploy your application to AWS with the help of [NestJS Mau](https://mau.nestjs.com) in just a few clicks.
- Visualize your application graph and interact with the NestJS application in real-time using [NestJS Devtools](https://devtools.nestjs.com).
- Need help with your project (part-time to full-time)? Check out our official [enterprise support](https://enterprise.nestjs.com).
- To stay in the loop and get updates, follow us on [X](https://x.com/nestframework) and [LinkedIn](https://linkedin.com/company/nestjs).
- Looking for a job, or have a job to offer? Check out our official [Jobs board](https://jobs.nestjs.com).

## Support

Nest is an MIT-licensed open source project. It can grow thanks to the sponsors and support by the amazing backers. If you'd like to join them, please [read more here](https://docs.nestjs.com/support).

## Stay in touch

- Author - [Kamil Myśliwiec](https://twitter.com/kammysliwiec)
- Website - [https://nestjs.com](https://nestjs.com/)
- Twitter - [@nestframework](https://twitter.com/nestframework)

## License

Nest is [MIT licensed](https://github.com/nestjs/nest/blob/master/LICENSE).
