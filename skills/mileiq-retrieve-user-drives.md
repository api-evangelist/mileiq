---
name: Retrieve a user's MileIQ drives
description: Authenticate with OAuth 2.1 and pull a user's mileage drives with classification and date filters, paging through the full history.
api: openapi/mileiq-external-openapi-original.json
operations: [get_me, get_user_drives]
---

# Retrieve a user's MileIQ drives

Use the MileIQ External API to read a user's mileage trips.

## Auth
- OAuth 2.1 Authorization Code flow against `https://oauth2.mileiq.com`.
  Send the access token as `Authorization: Bearer <token>`.
- Required scopes: `users:read` and `drives:read:all`.

## Steps
1. Confirm identity with `get_me` (`GET /v1/users/me`) — capture the user `id`.
2. Call `get_user_drives` (`GET /v1/users/{user_id}/drives`) with:
   - `classification` = `business` | `personal` | `all` (default `business`)
   - `limit` up to 1000 (default 100)
   - `filter_by` = `ended` or `modified`, plus `date_after` / `date_before` (ISO 8601 UTC, exclusive) to bound the window.
3. Read `results[]` and check `has_more`. To page, narrow `date_after`/`date_before` toward the oldest returned drive and repeat until `has_more` is false.

## Rules
- Do NOT use the deprecated `modified_before` / `modified_after` params — use `date_before`/`date_after` + `filter_by`.
- A user can only read their own drives (403 otherwise).
- Respect rate limits: 20 req/min on this operation; honor `Retry-After` on 429.
- Monetary `value`, `toll_fees`, `parking_fees` are integers in the smallest currency unit with an ISO 4217 `currency`.
