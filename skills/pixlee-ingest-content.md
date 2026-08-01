---
name: Ingest content and manage products in Pixlee
description: >-
  Add new UGC into a Pixlee album from a URL or an uploaded file, and create or
  update products — including the required HMAC-SHA1 request signing for writes.
api: openapi/pixlee-content-api-openapi.yml
operations: [create-new-content-from-url, create-new-content-from-file, add-product]
---

# Ingest content and manage products in Pixlee

All write operations use the Content API v2 (`https://distillery.pixlee.co/api/v2`).

## Auth (writes)
- Pass your **ACCOUNT PRIVATE API KEY** as `api_key`.
- **Sign every write.** Minify the JSON payload, compute **HMAC-SHA1** with your
  **ACCOUNT SECRET KEY**, Base64-encode it, and send it in the **`Signature`** header.
  POST bodies use `Content-Type: application/json`.

## Add content from a URL
1. Call **`create-new-content-from-url`**: `POST /media`.
2. Body: `album_id`, `photo_uri`, `title`, and either `email` or `connected_user_id`
   (if both are set, `connected_user_id` wins). Optional: `approved`, `product_skus`,
   `category_names`, `avatar_url`.
3. Response returns `album_photo_id`.

## Add content from a file
1. Call **`create-new-content-from-file`**: `POST /media/file` as `multipart/form-data`
   with a binary `file` part and a `json` metadata part (same fields, minus photo_uri).
2. Response returns `album_photo_id` and `connected_user_id`.

## Create an album or add/update a product
1. Call **`add-product`**: `POST /albums`.
2. For an album: `title` + `album_type` (`standard` or `category`).
3. For a product: `album_type: "product"` plus a `product` object
   (`sku`, `name`, `product_photo`, `buy_now_link_url`, `price`, `stock`, `currency`).

## Rules
- There is **no idempotency key** — do not blindly retry a write on a network timeout;
  re-query first. Re-creating the same image is safe (properties are unchanged; new
  products/categories are added as tags).
- Signature failures return 403 (see `errors/pixlee-problem-types.yml`).
