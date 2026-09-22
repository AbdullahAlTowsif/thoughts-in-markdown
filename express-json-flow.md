# JSON Flow: Frontend ↔ Backend (Express) — Interview Revision Notes

## The Core Problem
HTTP requests/responses only transmit **text** (strings). JS objects can't be sent directly over the network — they must be serialized to a string format first. JSON is that format.

---

## 1. Frontend → Backend (Sending Data)

- You have a JS object (e.g., from a form).
- Convert it to a JSON string: `JSON.stringify(data)`
- Send it in the request body.
- **Critical (often missed):** Set the request header:
  ```
  Content-Type: application/json
  ```
  Example with `fetch`:
  ```js
  fetch('/api/users', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(formData)
  });
  ```

### Why the header matters
`express.json()` is middleware that only parses the body **if** it sees `Content-Type: application/json`. If the header is missing/wrong, `express.json()` silently skips parsing, and `req.body` will be `undefined` or `{}` — even if you sent perfectly valid JSON text.

---

## 2. Backend Receives (express.json())

- `express.json()` middleware runs on incoming requests.
- It reads the raw JSON text in the request body.
- Parses it into a JS object.
- Attaches it to `req.body`.

```js
app.use(express.json()); // must be added before your routes

app.post('/api/users', (req, res) => {
  console.log(req.body); // already a JS object, not a string
});
```

---

## 3. Backend → Frontend (Sending Response)

- Use `res.json(data)` — **not** manual `JSON.stringify()`.
- `res.json()` automatically:
  - Converts your JS object to a JSON string.
  - Sets the `Content-Type: application/json` response header for you.

```js
res.json({ success: true, user: newUser });
```

You *could* do `res.send(JSON.stringify(data))` manually, but `res.json()` is the idiomatic way and handles the header for you.

---

## 4. Frontend Receives Response

- If using `fetch`, call `response.json()` — this **automatically parses** the JSON text back into a JS object.
- You do **not** need to manually call `JSON.parse()` in the normal case.

```js
const res = await fetch('/api/users');
const data = await res.json(); // already a JS object
```

- Manual `JSON.parse()` is only needed if you received raw text (e.g., via `response.text()`) instead of using `response.json()`.

---

## Full Round Trip Summary

| Step | Direction | What happens | Method |
|---|---|---|---|
| 1 | Frontend → Backend | JS object → JSON string | `JSON.stringify()` |
| 2 | (transit) | Must declare content type | `Content-Type: application/json` header |
| 3 | Backend receives | JSON string → JS object | `express.json()` middleware → `req.body` |
| 4 | Backend → Frontend | JS object → JSON string (+ sets header) | `res.json()` |
| 5 | Frontend receives | JSON string → JS object | `response.json()` (fetch) |

---

## Common Interview Gotchas
- **`express.json()` returns empty `req.body`** → almost always a missing/incorrect `Content-Type` header on the client, or forgetting to `app.use(express.json())` before the routes.
- **`res.json()` vs `res.send()`**: `res.json()` always stringifies and sets the correct header; `res.send()` will guess content type based on what you pass (string vs object).
- **JSON is a text format**: JSON itself is just a string with a specific grammar (objects, arrays, strings, numbers, booleans, null) — it's not a JS-specific thing, which is why it works as a universal data-interchange format between any client and any server language.
