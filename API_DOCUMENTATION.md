# Authentication API Documentation

## Overview
This document outlines the authentication system implemented in the Schedula backend project.

---

## Database Setup (PostgreSQL)

**Current Status:** Awaiting database configuration

Before running migrations and tests, ensure:
1. PostgreSQL is installed and running on `localhost:5432`
2. Update `.env` with correct credentials:
   ```
   DATABASE_URL="postgresql://username:password@localhost:5432/schedula"
   JWT_SECRET="your-secret-key-change-this-in-production"
   ```
3. Create the `schedula` database (if it doesn't exist)
4. Run migration:
   ```bash
   npx prisma migrate dev --name init
   ```

---

## API Endpoints

### 1. Signup (Create User)
**Endpoint:** `POST /auth/signup`

**Request Body:**
```json
{
  "name": "Rahul",
  "email": "rahul@gmail.com",
  "password": "123456",
  "role": "DOCTOR"
}
```

**Successful Response (201):**
```json
{
  "message": "User registered successfully",
  "user": {
    "id": 1,
    "name": "Rahul",
    "email": "rahul@gmail.com",
    "role": "DOCTOR"
  }
}
```

**Error Responses:**
- `400 Bad Request` - If email already exists
- `400 Bad Request` - If required fields are missing

---

### 2. Login
**Endpoint:** `POST /auth/login`

**Request Body:**
```json
{
  "email": "rahul@gmail.com",
  "password": "123456"
}
```

**Successful Response (200):**
```json
{
  "message": "Login successful",
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "user": {
    "id": 1,
    "name": "Rahul",
    "email": "rahul@gmail.com",
    "role": "DOCTOR"
  }
}
```

**Error Responses:**
- `401 Unauthorized` - Invalid email/password

---

### 3. Doctor Profile (Protected)
**Endpoint:** `GET /doctor/profile`

**Headers:**
```
Authorization: Bearer <jwt_token>
```

**Requirements:**
- JWT token required
- User must have DOCTOR role

**Successful Response (200):**
```json
{
  "message": "Doctor profile retrieved",
  "user": {
    "id": 1,
    "name": "Rahul",
    "email": "rahul@gmail.com",
    "role": "DOCTOR",
    "createdAt": "2024-01-15T10:30:00.000Z"
  }
}
```

**Error Responses:**
- `401 Unauthorized` - No token or invalid token
- `403 Forbidden` - User is not a DOCTOR

---

### 4. Patient Profile (Protected)
**Endpoint:** `GET /patient/profile`

**Headers:**
```
Authorization: Bearer <jwt_token>
```

**Requirements:**
- JWT token required
- User must have PATIENT role

**Successful Response (200):**
```json
{
  "message": "Patient profile retrieved",
  "user": {
    "id": 2,
    "name": "Priya",
    "email": "priya@gmail.com",
    "role": "PATIENT",
    "createdAt": "2024-01-15T10:35:00.000Z"
  }
}
```

**Error Responses:**
- `401 Unauthorized` - No token or invalid token
- `403 Forbidden` - User is not a PATIENT

---

## Testing Workflow in Postman

### Step 1: Signup Doctor
```
POST http://localhost:3000/auth/signup
Body:
{
  "name": "Dr. Rahul",
  "email": "doctor@gmail.com",
  "password": "password123",
  "role": "DOCTOR"
}
```
✅ Expected: 201 Created

### Step 2: Signup Patient
```
POST http://localhost:3000/auth/signup
Body:
{
  "name": "Priya",
  "email": "patient@gmail.com",
  "password": "password123",
  "role": "PATIENT"
}
```
✅ Expected: 201 Created

### Step 3: Login Doctor
```
POST http://localhost:3000/auth/login
Body:
{
  "email": "doctor@gmail.com",
  "password": "password123"
}
```
✅ Expected: 200 OK
📌 **Save the token for next requests**

### Step 4: Login Patient
```
POST http://localhost:3000/auth/login
Body:
{
  "email": "patient@gmail.com",
  "password": "password123"
}
```
✅ Expected: 200 OK
📌 **Save this token too**

### Step 5: Doctor Accessing Own Profile (Success)
```
GET http://localhost:3000/doctor/profile
Headers:
Authorization: Bearer <DOCTOR_TOKEN>
```
✅ Expected: 200 OK

### Step 6: Doctor Accessing Patient Profile (Forbidden)
```
GET http://localhost:3000/patient/profile
Headers:
Authorization: Bearer <DOCTOR_TOKEN>
```
❌ Expected: 403 Forbidden

### Step 7: Patient Accessing Own Profile (Success)
```
GET http://localhost:3000/patient/profile
Headers:
Authorization: Bearer <PATIENT_TOKEN>
```
✅ Expected: 200 OK

### Step 8: Patient Accessing Doctor Profile (Forbidden)
```
GET http://localhost:3000/doctor/profile
Headers:
Authorization: Bearer <PATIENT_TOKEN>
```
❌ Expected: 403 Forbidden

### Step 9: No Token Access (Unauthorized)
```
GET http://localhost:3000/doctor/profile
```
❌ Expected: 401 Unauthorized

---

## JWT Token Structure

Token contains:
- `id` - User ID
- `role` - User role (DOCTOR or PATIENT)
- `exp` - Expiration time (24 hours)

**Decode your token at:** https://jwt.io/

---

## Project Structure

```
src/
├── auth/
│   ├── dto/
│   │   ├── signup.dto.ts
│   │   ├── login.dto.ts
│   │   └── index.ts
│   ├── guards/
│   │   └── auth.guard.ts (JWT verification + role checking)
│   ├── middleware/
│   │   ├── jwt-auth.middleware.ts
│   │   └── role-auth.middleware.ts
│   ├── auth.controller.ts
│   ├── auth.service.ts
│   └── auth.module.ts
├── doctor/
│   ├── doctor.controller.ts
│   ├── doctor.service.ts
│   └── doctor.module.ts
├── patient/
│   ├── patient.controller.ts
│   ├── patient.service.ts
│   └── patient.module.ts
├── prisma/
│   ├── prisma.service.ts
│   └── prisma.module.ts
├── app.module.ts
├── main.ts
└── ...
```

---

## Environment Variables

```env
DATABASE_URL="postgresql://username:password@localhost:5432/schedula"
JWT_SECRET="your-secret-key-change-this-in-production"
```

---

## Next Steps

1. ✅ Code implementation complete
2. ⏳ Set up PostgreSQL database
3. ⏳ Run Prisma migration
4. ⏳ Start dev server: `npm run start:dev`
5. ⏳ Test with Postman
6. ⏳ Record Loom video
7. ⏳ Create PR with feature branch

---

## Features Implemented

✅ User registration with password hashing (bcrypt)
✅ User login with JWT token generation
✅ JWT authentication guard
✅ Role-based authorization (DOCTOR/PATIENT)
✅ Protected routes for doctor and patient profiles
✅ Error handling with proper HTTP status codes
✅ Input validation via DTOs
✅ Prisma ORM for database operations
✅ Dependency injection throughout the application
