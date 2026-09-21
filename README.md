# ReqRes API Testing — Postman Collection

A Postman-based API test suite for [ReqRes](https://reqres.in), a public REST API designed for testing and prototyping, covering CRUD operations, pagination, and negative/validation testing.

## What's Covered
- **User retrieval:** List users (with pagination), get a single user by valid ID, get a single user by invalid ID (404 handling)
- **User management:** Create user, full update (PUT), partial update (PATCH), delete user
- **Auth-adjacent validation:** Register with missing password (400), Login with missing password (400)

## Why These Requests
Rather than just confirming each endpoint returns a success response, this collection deliberately includes negative cases most CRUD-only test suites skip:
- An invalid user ID should return a 404, not a silent empty success — tested explicitly.
- Registration and login should reject incomplete requests with a clear validation error, not process them as if the missing field didn't matter — tested explicitly for both.

## Tools & Setup
- **Postman** — collection and environment (`ReqRes QA Env`)
- **Collection Runner** configured with:
  - 1000ms delay between requests (avoids rate-limit issues, more realistic pacing)
  - "Stop run if an error occurs" enabled — a failure halts the run rather than continuing silently past it
  - Session-persisted responses and retained variable values across the run

## Request List

| # | Method | Request | Purpose |
|---|---|---|---|
| 1 | GET | List Users | Verify user list retrieval and response structure |
| 2 | DELETE | Delete User | Verify deletion succeeds |
| 3 | PATCH | Partial Update User | Verify partial field updates apply correctly |
| 4 | PUT | Full Update User | Verify full record update applies correctly |
| 5 | GET | Single User (Valid ID) | Verify correct user data returned for a valid ID |
| 6 | GET | List Users (Page 2) | Verify pagination behavior |
| 7 | GET | Single User (Invalid ID) | Verify 404 returned for a non-existent user |
| 8 | POST | Register (Missing Password) | Verify 400 validation error on incomplete registration |
| 9 | POST | Login (Missing Password) | Verify 400 validation error on incomplete login |
| 10 | POST | Create User | Verify new user creation succeeds with valid data |

## How to Run
1. Import `ReqRes-API-Suite.postman_collection.json` and `ReqRes-QA-Env.postman_environment.json` into Postman.
2. Select the "ReqRes QA Env" environment.
3. Open the collection → Run → confirm settings (1000ms delay, stop on error) → Start Run.
4. Review results in the Runner summary, or export via Newman for a CLI/CI-friendly report.

## Results
*(Add after running: pass/fail count, and any notable findings — e.g., unexpected status codes, response structure issues.)*

## Next Steps
- Add explicit JSON schema validation on response bodies, not just status codes.
- Automate via Newman + GitHub Actions for repeatable CI execution.
- Expand negative coverage: invalid data types, boundary values on pagination parameters.
