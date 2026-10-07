# REST API Practice Notes

After each exercise, write one line on what surprised you.

## Level 1: HTTP fundamentals

- 1.1 Anatomy of a request:
- 1.2 Collection vs item:
- 1.3 Query parameters:
- 1.4 Nested resources:
- 1.5 Status code tour: (see table below)
- 1.6 Headers you send:
- 1.7 Methods:

### Status codes

| Code | Meaning | What my UI should show |
| --- | --- | --- |
| 200 | OK | |
| 201 | | |
| 204 | | |
| 301 | | |
| 400 | | |
| 401 | | |
| 403 | | |
| 404 | | |
| 500 | | |

## Level 2: CRUD

- 2.1 Create: POST /posts with a JSON body (title, body, userId) and Content-Type: application/json → 201 Created, id 101. Always 101 because JSONPlaceholder already has 100 posts and only pretends to save new ones, so a GET /posts/101 afterward returns 404.
- 2.2 Break the create: (1) Body type Text, Content-Type: application/json kept → still 201 with the full post, because the server reads the raw text and only the header tells it how to parse it. (2) Header removed → 201 but only { "id": 101 }; the server could not tell the body was JSON, so it dropped title, body and userId. In fetch(), JSON.stringify() is needed because a request body can only be text (an object becomes "[object Object]"), and the header is needed because fetch labels a string body text/plain by default.
- 2.3 PUT vs PATCH: PUT /posts/1 with only title → 200 with just title and id; PUT replaces the whole post, so userId and body are lost. PATCH /posts/1 with only title → 200 with all four fields; PATCH changes only the fields sent and keeps the rest. Both must target /posts/1: PUT or PATCH on /posts returns 404 because the URL does not say which post to change.
- 2.4 Delete: DELETE /posts/1 → 200 with an empty {}; there is nothing left to return, so the status code is the answer (many real APIs send 204 No Content instead). GET /posts/1 afterward still returns the post because JSONPlaceholder never really deletes anything. On a real API it would return 404.
- 2.5 Full lifecycle on Restful Booker:
- 2.6 Pagination: /products?limit=10&skip=0, 10, 20 → products 1–10, 11–20, 21–30. skip is how many products to skip from the start (skip = (page − 1) × limit), not a range, so a middle range cannot be skipped in one request; skip=11-30 returns 400. total is 194, so total pages = Math.ceil(194 / 10) = 20, with 4 products on the last page (skip=190). Infinite scroll keeps fetching while skip + limit < total.
- 2.7 Search, sort and select:

## Level 3: Environments, variables and chaining

- 3.1 Environments:
- 3.2 Capture a value:
- 3.3 Chain three requests:

## Level 4: Auth

- 4.1 Basic auth:
- 4.2 Token auth:
- 4.3 JWT login flow:
- 4.4 Decode the JWT:
- 4.5 Refresh tokens:
- 4.6 Real-world tokens:

## Level 5: Testing

- 5.1 No-code assertions:
- 5.2 First JavaScript test:
- 5.3 Array tests:
- 5.4 Performance budget:
- 5.5 Negative tests:
- 5.6 Run it all:

## Level 6: Challenges

- 6.1 The mystery 401:
- 6.2 The silent wrong answer:
- 6.3 Build the dashboard payload:
- 6.4 Fetch the whole catalogue:
- 6.5 Flaky network:
- 6.6 Reverse-engineer an API:
- 6.7 The CORS question:
- 6.8 Design review:
