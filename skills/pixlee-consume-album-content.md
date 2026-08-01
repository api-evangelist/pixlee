---
name: Consume approved UGC from a Pixlee album
description: >-
  Fetch approved user-generated media from a Pixlee album with filtering,
  sorting, and pagination, to power an on-site or app UGC gallery.
api: openapi/pixlee-content-api-openapi.yml
operations: [get-approved-content-from-album]
---

# Consume approved UGC from a Pixlee album

Use the Pixlee Content API v2 (`https://distillery.pixlee.co/api/v2`) to read
approved media out of an album.

## Auth
- Pass your **ACCOUNT API KEY** as the `api_key` query parameter (read access).
- Reads require no signature. CORS is open, so this works from the browser or a server.

## Steps
1. Call **`get-approved-content-from-album`**: `GET /albums/{album_id}/photos?api_key=...`.
2. Paginate with `page` (default 1) and `per_page` (default 30).
3. Optionally pass a JSON **`filters`** object (intersecting clauses), e.g.
   `{"content_source":["instagram_feed","desktop"],"has_permission":true,"has_product":true}`.
4. Optionally pass a JSON **`sort`** object with one key from
   `recency | approved_time | random | pixlee_shares | pixlee_likes | popularity | dynamic`
   plus `desc` or `asc`.
5. Read results under the response **`data`** key.

## Rules
- Responses are CDN cached for ~2 minutes; add `&unique_id=<numeric_timestamp>` to bust the cache.
- All objects live under the envelope `data` key (see `conventions/pixlee-conventions.yml`).
- On 404 the album_id is wrong or inaccessible; on 401 the api_key is missing/invalid
  (see `errors/pixlee-problem-types.yml`).
