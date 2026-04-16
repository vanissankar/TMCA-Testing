# TMCA Testing

This repository is the Postman test pack for the TMCA backend. It exists so the reviewer can import one collection, run the API calls in the right order, and verify that the current code works against the actual NestJS endpoints.

## Why this repo exists

- It documents the live API contract in a form the reviewer can execute.
- It keeps the Postman examples aligned with the current backend code.
- It stores a reusable login token so protected routes can be tested quickly.
- It shows which endpoints are available now and which ones are not implemented yet.

## What is covered

- Authentication: login
- Organizations: register, get by id, get all
- Teams: register, get by id, get by organization
- Users: register, get profile, get by id
- Clients: create, update, get my clients, get team clients, onboarding summary, get by id
- Onboarding sections: initial client details save, section-wise save, status fetch, submit
- Dashboards: admin, team_admin, team_lead, team_member routes

## How to test in Postman

1. Import `tmca-testing/TMCA.postman_collection.json` into Postman.
2. Set `baseUrl` to `http://localhost:3000` if needed.
3. Run `Authentication > Login` first.
4. The login request stores `data.access_token` in the collection variable `accessToken`.
5. The collection now auto-populates `organizationId`, `teamId`, `userId`, and `clientId` from create/login responses.
6. Use `memberId` only for member-detail routes when you want to target a specific existing user.
7. Run the requests in this order:
   - `Authentication > Login`
   - `Organizations > Register Organization`
   - `Organizations > Get Organization By Id`
   - `Organizations > Get All Organizations`
   - `Teams > Register Team`
   - `Teams > Get Team By Id`
   - `Teams > Get Teams By Organization`
   - `Users > Register User`
   - `Users > Get My Profile`
   - `Users > Get User By Id`
   - `Clients > Create Client`
   - `Clients > Update Client`
   - `Clients > Get My Clients`
   - `Clients > Get Team Clients`
   - `Clients > Get Onboarding Summary`
   - `Clients > Get Client By Id`
  - `Clients > Onboarding - Initial Save Client Details`
  - `Clients > Onboarding - Save Form Uploads`
  - `Clients > Onboarding - Save TDS Summary (Draft)`
  - `Clients > Onboarding - Save Bank Details`
  - `Clients > Onboarding - Save Business Expenses`
  - `Clients > Onboarding - Save Loan Details`
  - `Clients > Onboarding - Save Loan Summary`
  - `Clients > Onboarding - Get Status`
  - `Clients > Onboarding - Submit`
   - `Dashboard > Admin Overview`
   - `Dashboard > Admin Team Overview`
   - `Dashboard > Team Admin Overview`
   - `Dashboard > Team Admin Member Detail`
   - `Dashboard > Team Lead Overview`
   - `Dashboard > Team Lead Member Detail`
   - `Dashboard > Team Member Overview`

## Current backend rules reflected here

- Login uses `email` and `password`.
- User registration uses `email`, not `username`.
- Organization and team creation require an admin token.
- User registration requires an admin or team_admin token.
- Client and dashboard routes require a valid Bearer token.
- Onboarding starts with `client_details` via `POST /clients/onboarding/save`.
- Other onboarding sections save via `POST /clients/:clientId/onboarding/save`.
- Final onboarding submit uses `POST /clients/:clientId/onboarding/submit`.
- `tds_summary` generation is pending and is treated as draft in tests.

## Example request bodies

### Login

Using email:

```json
{
  "email": "johndoe@example.com",
  "password": "SecurePassword123!"
}
```

Using username:

```json
{
  "username": "anis",
  "password": "anis@123"
}
```

### Register Organization

```json
{
  "name": "The Madras CA"
}
```

### Register Team

```json
{
  "name": "Finance Team",
  "type": "internal",
  "organization_id": "<organization-uuid>"
}
```

### Register User

```json
{
  "name": "Akash",
  "email": "akash2176e@example.com",
  "password": "tmca@123",
  "role": "team_member",
  "team_id": "<team-uuid>",
  "phone": "+91-7305594291"
}
```

### Create Client

```json
{
  "pan_number": "ABCDE1234F",
  "name_as_per_pan": "Amit Kumar",
  "dob": "1990-01-15",
  "proprietary_name": "Amit Traders"
}
```

### Update Client

```json
{
  "name_as_per_pan": "Updated Name",
  "dob": "1990-01-15",
  "proprietary_name": "Updated Firm"
}
```

## Notes for the reviewer

- Do not rely on placeholder UUIDs. Use ids captured from earlier responses.
- `organization_id` and `team_id` inputs must be valid UUID v4 values.
- Keep the JWT token in the `accessToken` collection variable.
- The collection includes only routes currently implemented in backend code.
- Onboarding requests map to existing DB tables used by backend:
  - `documents` for form uploads
  - `bank_accounts` for bank details
  - `expenses` for business expenses
  - `loans` and `loan_summary` for loan sections
