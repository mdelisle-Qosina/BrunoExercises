# REST API Study Guide with Bruno

Oct 6, 2026 · @Matt Delisle

## How to use this guide

Work through six levels in order, then build the capstone. Each exercise has a task, a goal and a "done when" check, so you know when to move on. Budget about 1–2 hours per level.

**Set up one Bruno collection for the whole guide:**

1. Create a folder such as `rest-api-practice/` and open it as a Bruno collection (Bruno collections are plain folders on disk).
2. Inside it, create one sub-folder per level: `01-fundamentals`, `02-crud`, `03-variables`, `04-auth`, `05-testing`, `06-challenges`.
3. Each request you save becomes a `.bru` text file, so commit the folder to Git and review your own diffs as you go.
4. Keep a `NOTES.md` in the root. After each exercise, write one line on what surprised you.

**Bruno features you will lean on:**

- **Environments** — swap `{{baseUrl}}` between APIs without editing requests.
- **Assertions tab** — quick no-code checks like `res.status eq 200`.
- **Tests tab** — JavaScript tests using `test()` and Chai's `expect`.
- **Pre-request / post-response scripts** — read and set variables with `bru.setVar()` and `bru.getVar()`.
- **Timeline / response headers** — see exactly what was sent and received.

A frontend habit to build from day one: for every request, ask "what would my UI do with this response, and what would it show if it failed?"

## Practice APIs

All of these are free public APIs. Create one Bruno environment per API, each with a `baseUrl` variable.

| API | Base URL | Best for | Notes |
| --- | --- | --- | --- |
| JSONPlaceholder | `https://jsonplaceholder.typicode.com` | GET basics, CRUD, nested resources | Writes are faked: the response looks real but nothing persists |
| DummyJSON | `https://dummyjson.com` | Pagination, search, JWT login | `limit`/`skip` pagination; `/auth/login` returns tokens |
| Restful Booker | `https://restful-booker.herokuapp.com` | Token auth, real CRUD | Data does persist (and resets periodically) |
| httpbin | `https://httpbin.org` | Headers, status codes, debugging | Echoes back exactly what you sent |
| PokéAPI | `https://pokeapi.co/api/v2` | Deep nested JSON, following links | Read-only; responses contain URLs to other resources |
| GitHub REST API | `https://api.github.com` | Rate limits, real-world auth | Unauthenticated calls are heavily rate-limited; use a personal access token |

Public APIs change over time, so if one starts returning errors, check its homepage for new requirements (for example, a required API key header).

## Level 1: HTTP fundamentals

Goal: read any request and response and explain every part of it. Use JSONPlaceholder and httpbin.

**1.1 Anatomy of a request.** Send `GET {{baseUrl}}/posts/1`. In your notes, label the method, URL, status code, response time, size and the `Content-Type` header. *Done when:* you can explain why the status is 200 and what `application/json; charset=utf-8` means.

**1.2 Collection vs item.** Send `GET /posts` and `GET /posts/1`. Compare the response shapes. *Done when:* you can say which one your frontend would `.map()` over and which one fills a detail page.

**1.3 Query parameters.** Fetch only the posts for user 3 using `?userId=3`. Then use Bruno's Params tab instead of typing the query string. *Done when:* both versions return the same 10 posts.

**1.4 Nested resources.** Get the comments for post 1 in two ways: `/posts/1/comments` and `/comments?postId=1`. *Done when:* you can explain when an API designer would choose each style.

**1.5 Status code tour.** On httpbin, call `/status/200`, `/status/201`, `/status/204`, `/status/301`, `/status/400`, `/status/401`, `/status/403`, `/status/404`, `/status/500`. *Done when:* you have a table in `NOTES.md` with each code, its meaning, and what your UI should show for it.

**1.6 Headers you send.** Call httpbin's `/headers`, then add a custom header `X-Study-Session: day1`. *Done when:* you see your header echoed back, plus the headers Bruno added on its own.

**1.7 Methods.** Call httpbin's `/get`, `/post`, `/put`, `/patch` and `/delete`, each with the matching method. Then call `/get` with POST. *Done when:* you can explain the 405 response and which methods are safe and idempotent.

## Level 2: CRUD

Goal: create, read, update and delete resources, and know what each response should look like. Start on JSONPlaceholder, then repeat on Restful Booker where data really persists.

**2.1 Create.** `POST /posts` with a JSON body containing `title`, `body` and `userId`. Set `Content-Type: application/json`. *Done when:* you get 201 and a new `id`, and you can explain why that id is always the same on JSONPlaceholder.

**2.2 Break the create.** Send the same POST with the body as plain text, then with the `Content-Type` header removed. *Done when:* you have noted how the response changed and why a frontend `fetch()` needs `JSON.stringify()` plus the header.

**2.3 PUT vs PATCH.** Update post 1 twice: a PUT with only `title`, then a PATCH with only `title`. *Done when:* you can explain why the PUT response lost fields and the PATCH kept them.

**2.4 Delete.** `DELETE /posts/1`, then `GET /posts/1` again. *Done when:* you can explain the empty `{}` response and why the GET still works on a fake API.

**2.5 Full lifecycle on Restful Booker.** Create a booking, read it back by id, update it, delete it, then read it again. *Done when:* the final GET returns 404. (Update and delete need a token, which is exercise 4.2, so come back to finish this.)

**2.6 Pagination.** On DummyJSON, fetch `/products?limit=10&skip=0`, then pages 2 and 3. *Done when:* you can calculate the total number of pages from the `total` field, the way an infinite-scroll component would.

**2.7 Search, sort and select.** On DummyJSON, search products for "phone", sort them by price descending, and return only `title` and `price` (try `select=title,price`). *Done when:* the response contains only the fields your product card would need.

## Level 3: Environments, variables and chaining

Goal: stop hard-coding values and make requests feed each other, the same way your app passes data between API calls.

**3.1 Environments.** Create `jsonplaceholder` and `dummyjson` environments, each with `baseUrl`. Rewrite every saved request to use `{{baseUrl}}`. *Done when:* switching the environment dropdown is the only change needed to point at a different API.

**3.2 Capture a value.** After `POST /posts`, add a post-response script that saves the new id:

```javascript
bru.setVar("postId", res.getBody().id);
```

Then create `GET {{baseUrl}}/posts/{{postId}}`. *Done when:* the GET uses the id from the POST without you copying it.

**3.3 Chain three requests.** On PokéAPI: get `/pokemon/pikachu`, save the URL of its first ability, then request that ability URL and save the name of one other Pokémon that has it. *Done when:* you can run all three in order from the collection runner.

**3.4 Dynamic data.** In a pre-request script, generate a unique title with a timestamp and set it as a variable used in the POST body. *Done when:* every run creates a post with a different title.

**3.5 Secrets out of Git.** Put an API key or token in a `.env` file in the collection root and reference it as `{{process.env.API_TOKEN}}`. Add `.env` to `.gitignore`. *Done when:* `git status` shows no secrets, but requests still work.

**3.6 Variable precedence.** Define `pageSize` at collection level as 5 and in the environment as 20, then use it in a query param. *Done when:* you have worked out (and noted) which value wins, and how you would confirm it.

## Level 4: Authentication

Goal: understand the auth flows your frontend will implement, and see each one at the HTTP level.

**4.1 Basic auth.** Call httpbin's `/basic-auth/student/secret123` using Bruno's Auth tab (Basic). Then do it again by writing the `Authorization` header yourself, base64-encoding `student:secret123`. *Done when:* both return 200, and a wrong password returns 401.

**4.2 Token auth.** On Restful Booker, `POST /auth` with the demo credentials from its docs. Save the token with a post-response script, then use it as a `Cookie: token={{token}}` header to update and delete a booking. *Done when:* you can finish exercise 2.5.

**4.3 JWT login flow.** On DummyJSON, `POST /auth/login` with a demo user from its docs. Save the access token, then call `GET /auth/me` with `Authorization: Bearer {{accessToken}}`. *Done when:* `/auth/me` returns the logged-in user, and removing the header returns 401.

**4.4 Decode the JWT.** Paste your access token into a JWT decoder (or decode the middle segment yourself in a Bruno script with `atob`). *Done when:* you can name the claims inside and find the expiry time.

**4.5 Refresh tokens.** Use DummyJSON's refresh endpoint to get a new access token from the refresh token. Write a pre-request script at collection level that adds the Bearer header to every request automatically. *Done when:* no individual request has its own auth header set.

**4.6 Real-world tokens.** Create a GitHub personal access token with read-only scope, store it in `.env`, and call `GET /user`. Compare the `x-ratelimit-remaining` header with and without the token. *Done when:* you can explain why authenticated calls get a higher limit.

## Level 5: Testing and assertions

Goal: turn your collection into an automated contract check, so you notice when an API changes before your UI breaks.

**5.1 No-code assertions.** On `GET /posts/1`, add assertions in the Assertions tab: `res.status eq 200`, `res.body.id eq 1`, `res.body.title isString`. *Done when:* all pass, and changing the URL to `/posts/9999` makes them fail.

**5.2 First JavaScript test.**

```javascript
test("returns a post with the fields the UI needs", function () {
  const body = res.getBody();
  expect(res.getStatus()).to.equal(200);
  expect(body).to.have.all.keys("userId", "id", "title", "body");
});
```

*Done when:* you have written the same style of test for one request in every earlier level.

**5.3 Array tests.** For `GET /posts?userId=3`, test that every item has `userId === 3` and the array is not empty. *Done when:* the test would catch the API ignoring your filter.

**5.4 Performance budget.** Assert that `res.getResponseTime()` is under 1000 ms. *Done when:* you have picked a budget that matches what your UI can tolerate before showing a spinner.

**5.5 Negative tests.** Write tests that expect failure: 404 for a missing resource, 401 with no token, 400 with a bad body. *Done when:* your collection has at least as many negative tests as happy-path tests.

**5.6 Run it all.** Use the collection runner to run every folder in order. Then install the CLI (`npm install -g @usebruno/cli`) and run `bru run --env dummyjson` from the collection folder. *Done when:* you get a pass/fail summary in the terminal, ready to drop into a CI pipeline.

## Level 6: Problem-solving challenges

These have no step-by-step instructions. Each one is a scenario you will meet on a real frontend team. Write your reasoning in `NOTES.md` before looking anything up.

**6.1 The mystery 401.** Your token request works, but the next request returns 401. Deliberately cause this three different ways: a typo in the variable name, `Bearer` missing from the header, and an expired or tampered token. *Done when:* you have a debugging checklist for 401s, using Bruno's Timeline to see the request actually sent.

**6.2 The silent wrong answer.** Call `GET /posts?userid=3` (lowercase "id") on JSONPlaceholder. *Done when:* you can explain why you got 100 posts instead of 10 with a 200 status, and you have written a test that would catch it.

**6.3 Build the dashboard payload.** A dashboard needs: a user's name, their post count, and the title of their latest post. Find the fewest requests that produce this from JSONPlaceholder. *Done when:* a single runner execution sets variables for all three values, and you can argue for or against a dedicated backend endpoint.

**6.4 Fetch the whole catalogue.** Get every product from DummyJSON when the API only returns pages. Use a post-response script to loop through pages, or reason out the smallest number of calls. *Done when:* you have every product id, and you have noted the trade-off between one huge request and many small ones.

**6.5 Flaky network.** Use httpbin's `/delay/3` and set a request timeout below 3 seconds in Bruno settings. Then call `/status/503`. *Done when:* you have written how your UI should handle a timeout versus a server error, including retry rules.

**6.6 Reverse-engineer an API.** Open a public website you use, watch its network tab in browser DevTools, and recreate 3 of its GET requests in Bruno (use "Copy as cURL" and import it). *Done when:* you can explain each header it sends and which ones are actually required.

**6.7 The CORS question.** A request works in Bruno but fails in your React app with a CORS error. *Done when:* you can explain why Bruno never sees CORS errors, what the `Access-Control-Allow-Origin` header does, and what an OPTIONS preflight is (use httpbin and check the response headers).

**6.8 Design review.** Write down 5 things you would change about one of the practice APIs: naming, status codes, error shape, pagination or filtering. *Done when:* each change includes the frontend pain it would remove.

## Capstone: API-first mini store

Build a small storefront UI on DummyJSON, but design and test every API call in Bruno *before* writing frontend code. This mirrors how good frontend teams work against a new backend.

1. **Map the screens.** Product list (paginated, searchable), product detail, login, cart, profile.
2. **Build the Bruno collection.** One folder per screen, with every request the screen needs, chained with variables.
3. **Write the contract.** Add tests asserting the exact fields each component reads. These are your component's data requirements.
4. **Cover the failure states.** For each screen, save one request that produces an empty, error, or unauthorized response.
5. **Run it headless.** `bru run` must pass with a single command.
6. **Build the UI.** Use React, Vue or plain JS. Write one fetch wrapper that handles the base URL, Bearer token, JSON parsing, and errors by status code.
7. **Compare.** Check that every UI loading, empty and error state matches a request in your collection.

*Done when:* a teammate could clone the repo, run `bru run`, and understand the whole API surface of the app without reading your frontend code.

## Cheat sheet

| Task | Bruno syntax |
| --- | --- |
| Use a variable | `{{baseUrl}}/posts/{{postId}}` |
| Use a `.env` secret | `{{process.env.API_TOKEN}}` |
| Save a runtime variable | `bru.setVar("token", res.getBody().token)` |
| Read a variable in a script | `bru.getVar("token")` |
| Save to the environment | `bru.setEnvVar("token", value)` |
| Set a header in a pre-request script | `req.setHeader("Authorization", "Bearer " + bru.getVar("token"))` |
| Status, body, header, time | `res.getStatus()`, `res.getBody()`, `res.getHeader("content-type")`, `res.getResponseTime()` |
| Assertion (no code) | `res.status eq 200`, `res.body.items isArray` |
| Test | `test("name", () => expect(res.getStatus()).to.equal(200))` |
| Run from terminal | `bru run --env <name>` |

| Status | Meaning | What the UI usually does |
| --- | --- | --- |
| 200 / 201 / 204 | OK / created / no content | Render data, show success, or just close the form |
| 400 / 422 | Bad input | Show field-level validation errors |
| 401 | Not authenticated | Refresh token or redirect to login |
| 403 | Not allowed | Show a permission message, hide the action |
| 404 | Not found | Empty or "not found" state |
| 429 | Rate limited | Back off and retry later |
| 500 / 503 | Server problem | Error state with a retry button |

## Progress checklist

- [ ] Level 1: HTTP fundamentals
- [ ] Level 2: CRUD
- [ ] Level 3: Environments, variables and chaining
- [ ] Level 4: Authentication
- [ ] Level 5: Testing and assertions
- [ ] Level 6: Problem-solving challenges
- [ ] Capstone: API-first mini store
