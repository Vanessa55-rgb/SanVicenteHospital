# San Vicente Hospital — Appointment Management Web App

**Repository:** SanVicenteHospital  
**Platform:** .NET 9 (C#) — ASP.NET Core MVC  
**Database:** PostgreSQL (Npgsql)  
**Author:** (Vanessa Gomez )
**Clan:** (van-rossum)

---

## Project Summary

This repository contains a small ASP.NET Core MVC web application to manage patients, doctors and appointments for San Vicente Hospital. The app uses a simple data access layer that runs SQL through Npgsql (Postgres). It includes basic validations for models (DataAnnotations) and controllers for Patients, Doctors and Appointments.

The project structure and code were taken from the uploaded ZIP (`SanVicenteHospital.zip`).

---

## Key Entities (from /Models)
- **Patient**: Id, FullName, DocumentId, Age, Phone, Email, Address (DataAnnotations validations included).
- **Doctor**: Id, FullName, DocumentId, Phone, Email, Specialty (validations included).
- **Appointment**: Id, PatientId, DoctorId, Date, Time, Reason, Status.
- **IEntity**: interface implemented by main domain types.
- **ApplicationDbContext** (in /Data): lightweight DB helper using Npgsql; methods for executing queries and non-query commands.

---

## Controllers (found in /Controllers)
- `PatientController` — CRUD for patients.
- `DoctorController` — CRUD for doctors (filter by specialty implemented in views/controllers).
- `AppointmentController` — scheduling, cancelling, listing appointments; joins patient & doctor for list views.
- `HomeController` / `ErrorViewModel` as default MVC scaffolding.

---

## Requirements / Prerequisites

- .NET 9 SDK installed. Verify:
  ```bash
  dotnet --version
  ```
  Expected: `9.x.x`.
- PostgreSQL server (local or remote) and a database for this app.
- Connection string configured in `appsettings.json`.
- Optional: Visual Studio 2022/2024 or VS Code.

---

## Local setup (example)

1. Clone repository (you already have it locally):
   ```bash
   git clone https://github.com/Vanessa55-rgb/SanVicenteHospital.git
   cd SanVicenteHospital
   ```

2. Restore and run:
   ```bash
   dotnet restore
   dotnet build
   dotnet run --project SanVicenteHospital.csproj
   ```
   By default the app will run on the configured Kestrel port (see console logs).

3. Open the app in a browser at `https://localhost:5001` or `http://localhost:5000` depending on launch output.

---

## Database notes

This project uses raw SQL via Npgsql (ApplicationDbContext helper). There are two ways to prepare a database:

**A. Manual schema (recommended quick start)**
- Create database `sanvicente`.
- Use the SQL DDL in `scripts/` (if provided) or run simple CREATE TABLE statements matching models:
    - `Patients(Id serial primary key, FullName text, DocumentId text unique, Age int, Phone text, Email text, Address text)`
    - `Doctors(Id serial primary key, FullName text, DocumentId text unique, Phone text, Email text, Specialty text)`
    - `Appointments(Id serial primary key, PatientId int references Patients(Id), DoctorId int references Doctors(Id), Date date, Time interval, Reason text, Status text)`

**B. Seed data**
- If a seeder is present (check `Data` folder), run it after connecting the app to your DB.

---

## Business rules enforced in code

- **Patient DocumentId**: regex validated (10 digits).
- **Doctor DocumentId**: regex validated (10 digits).
- **Phone**: validated as 10 digits.
- **Email**: limited pattern applied in DataAnnotations (project restricts domain patterns in the current validation; review if you want all domains).
- **Appointment creation**: controllers check and return appropriate messages when there are scheduling conflicts — ensure server-side checks before insertion.

---

## Known items / suggestions

- The project currently validates emails against limited domains (gmail / hotmail). If this is too restrictive, update the DataAnnotation `[RegularExpression(...)]` in `Patient` and `Doctor`.
- Consider migrating raw SQL + Npgsql helper to EF Core (DbContext + Migrations) for maintainability and easier schema evolution.
- Add server-side uniqueness constraints at DB-level (unique indexes on `DocumentId`) to prevent race conditions.
- Add unit tests for appointment conflict logic.

---

## How to contribute / modify

- Add new controllers under `/Controllers`.
- Add domain objects under `/Models`.
- Place SQL scripts in `/scripts/` and reference them from README for seeding.
- Update `appsettings.Development.json` for local dev settings (do not commit secrets).

---

## Troubleshooting

- **Cannot connect to DB**: verify connection string, credentials, and Postgres service is running.
- **Errors when inserting**: check DB schema types (date/time vs DateTime/TimeSpan) and ensure column names match expected capitalization/quoting used in queries.
- **Validation messages**: DataAnnotation messages are in Spanish in current models — update to English if you want English UI/errors.
