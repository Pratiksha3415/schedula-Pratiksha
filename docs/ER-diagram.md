# Schedula — ER Diagram

Entities: `USERS`, `DOCTORS`, `PATIENTS`, `AVAILABILITY_SLOTS`, `APPOINTMENTS`, `NOTIFICATIONS`.

`USERS` is the base identity table (auth/login). `DOCTORS` and `PATIENTS` each hold
a one-to-one `user_id` foreign key back to `USERS`, extending it with role-specific
fields. Doctors define `AVAILABILITY_SLOTS`; a patient books an `APPOINTMENT` against
one doctor and one slot. Every appointment update can trigger a `NOTIFICATION`, which
is also linked back to the user who should receive it.

```mermaid
erDiagram
  USERS ||--o| DOCTORS : "is a"
  USERS ||--o| PATIENTS : "is a"
  DOCTORS ||--o{ AVAILABILITY_SLOTS : defines
  DOCTORS ||--o{ APPOINTMENTS : accepts
  PATIENTS ||--o{ APPOINTMENTS : books
  AVAILABILITY_SLOTS ||--o| APPOINTMENTS : "reserved as"
  APPOINTMENTS ||--o{ NOTIFICATIONS : triggers
  USERS ||--o{ NOTIFICATIONS : receives

  USERS {
    uuid id PK
    string name
    string email
    string password_hash
    string role "doctor | patient"
    timestamp created_at
  }
  DOCTORS {
    uuid id PK
    uuid user_id FK
    string specialization
    string qualification
    int experience_years
    string clinic_address
  }
  PATIENTS {
    uuid id PK
    uuid user_id FK
    int age
    string gender
    string medical_history
  }
  AVAILABILITY_SLOTS {
    uuid id PK
    uuid doctor_id FK
    date slot_date
    time start_time
    time end_time
    boolean is_booked
  }
  APPOINTMENTS {
    uuid id PK
    uuid patient_id FK
    uuid doctor_id FK
    uuid slot_id FK
    string status "pending | confirmed | cancelled | completed"
    timestamp created_at
  }
  NOTIFICATIONS {
    uuid id PK
    uuid user_id FK
    uuid appointment_id FK
    string message
    boolean is_read
    timestamp created_at
  }
```

## Relationship notes

- `USERS` → `DOCTORS` / `PATIENTS`: one-to-one. A single login row expands into a
  role-specific profile, avoiding duplicated auth fields.
- `DOCTORS` → `AVAILABILITY_SLOTS`: one-to-many. A doctor opens many bookable slots.
- `AVAILABILITY_SLOTS` → `APPOINTMENTS`: one-to-one once booked (`is_booked` flips to
  true and the slot is tied to exactly one appointment).
- `PATIENTS` / `DOCTORS` → `APPOINTMENTS`: one-to-many on both sides — a patient can
  hold many appointments over time, and a doctor accepts many appointments.
- `APPOINTMENTS` → `NOTIFICATIONS`: one-to-many. Each state change (booked,
  rescheduled, cancelled, reminder) can spawn its own notification row.
- `USERS` → `NOTIFICATIONS`: one-to-many, since a notification is always addressed
  to a specific user (patient or doctor).
