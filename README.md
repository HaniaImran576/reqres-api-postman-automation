# ReqRes API Testing - Postman Collection

A Postman based API test suite for ReqRes (reqres.in), a public REST API designed for testing and prototyping, covering CRUD operations, pagination, and negative/validation testing.

## What's Covered
- User retrieval: List users (with pagination), get a single user by valid ID, get a single user by invalid ID (404 handling)
- User management: Create user, full update (PUT), partial update (PATCH), delete user
- Auth adjacent validation: Register with missing password (400), Login with missing password (400)

## Why These Requests
Rather than just confirming each endpoint returns a success response, this collection deliberately includes negative cases most CRUD only test suites skip. An invalid user ID should return a 404, not a silent empty success, and this is tested explicitly. Registration and login should reject incomplete requests with a clear validation error, not process them as if the missing field didn't matter, and this is tested explicitly for both.

## Tools and Setup
- Postman: collection and environment (ReqRes QA Env)
- Collection Runner configured with:
  - 1000ms delay between requests, to pace requests more realistically and reduce rate limit risk
  - "Stop run if an error occurs" enabled, so a failure halts the run rather than continuing silently past it
  - Session persisted responses and retained variable values across the run

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
3. Open the collection, click Run, confirm settings (1000ms delay, stop on error), then Start Run.
4. Review results in the Runner summary, or export via Newman for a CLI/CI friendly report.

## Known Limitation: Rate Limiting
ReqRes's free public tier enforces request rate limits. During an earlier run, executing the full request suite, even with a 1000ms delay between requests, was enough to trigger a 429 (Too Many Requests) response. This is documented here as a real, observed constraint of testing against a public API, not a defect in the suite itself. The suite was rerun successfully after the rate limit window reset, with the 1000ms delay retained to reduce the chance of hitting the limit again.

**Implication for real world CI/CD:** if this were a production test suite running on every commit, this rate limit would need to be handled through one of: longer inter request delays, exponential backoff on 429 responses, running the suite less frequently, or mocking the API for fast, repeatable local and CI test runs instead of depending on the live service.

## Results
Full clean run completed after the rate limit window reset.

| Metric | Result |
|---|---|
| Total assertions | 21 |
| Passed | 21 |
| Failed | 0 |
| Errors | 0 |
| Run duration | 21s 418ms |
| Average response time | 573ms |

All requests passed, including the chained Delete request, which removes the exact user created earlier in the same run using the dynamically captured user ID rather than a hardcoded one. This confirms the suite correctly reflects a real multi step flow, not just a set of isolated, independent calls.

## Next Steps
- Add explicit JSON schema validation on response bodies, not just status codes.
- Automate via Newman and GitHub Actions for repeatable CI execution, with rate limit handling built in.
- Expand negative coverage: invalid data types, boundary values on pagination parameters.
