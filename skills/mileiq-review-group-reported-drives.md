---
name: Review a MileIQ group's reported drives
description: As a group administrator, list the teams you manage and pull the drives members have reported for review, with driver info and review comments.
api: openapi/mileiq-external-openapi-original.json
operations: [get_groups, get_group, get_group_reported_drives]
---

# Review a MileIQ group's reported drives

For team/administrator reporting flows over the MileIQ External API.

## Auth
- OAuth 2.1 Bearer token from `https://oauth2.mileiq.com`.
- Required scopes: `groups:read` and `groups:read:reported-drives`.
- You must be an administrator of the group (403 otherwise).

## Steps
1. `get_groups` (`GET /v1/groups`) — list groups you administer; read `resources[]` for each group `id` and `display_name`.
2. (Optional) `get_group` (`GET /v1/groups/{group_id}`) — fetch a single group's members and metadata.
3. `get_group_reported_drives` (`GET /v1/groups/{group_id}/reported-drives`) with `limit`, `filter_by` (`ended`|`modified`) and `date_after`/`date_before` (ISO 8601 UTC).
4. Each item carries `driver`, `details` (distance/value/locations), `status`, and `review_comments[]`. Page via `has_more` by advancing the date window.

## Rules
- Avoid the deprecated `modified_before`/`modified_after` params.
- Rate limit is 60 req/min on this operation; back off on 429 using `Retry-After`.
- Treat driver PII (name, email, geolocation) as sensitive.
