# Understanding JSON in Frontend & Express Backend

## Introduction
When a frontend and backend communicate, they cannot send JavaScript objects directly because JavaScript objects only exist inside a JavaScript runtime. HTTP sends bytes/text over the network, so objects are converted to JSON.

## Frontend → Backend

### JavaScript Object
```js
const user = { name: "Towsif", age: 22 };
```

### Convert to JSON
```js
JSON.stringify(user)
```

Object:
```js
{ name: "Towsif", age: 22 }
```

Becomes:
```json
{"name":"Towsif","age":22}
```

### Send using fetch()
```js
fetch("/user", {
  method: "POST",
  headers: {
    "Content-Type": "application/json"
  },
  body: JSON.stringify(user)
});
```

The Content-Type header tells Express that the request body contains JSON.

The browser sends:

```http
POST /user HTTP/1.1
Content-Type: application/json

{"name":"Towsif","age":22}
```

## express.json()

```js
app.use(express.json());
```

Responsibilities:
1. Reads the request body.
2. Checks Content-Type: application/json.
3. Parses the JSON string.
4. Stores the JavaScript object in req.body.

Without middleware:
```js
req.body // undefined
```

With middleware:
```js
req.body
// { name: "Towsif", age: 22 }
```

## Backend → Frontend

```js
app.get("/user", (req, res) => {
  res.json({
    name: "Towsif",
    age: 22
  });
});
```

res.json() converts the JavaScript object into JSON before sending it.

## Receiving on the Frontend

```js
const response = await fetch("/user");
const data = await response.json();
```

Important:
- fetch() only sends the request and receives the response.
- fetch() does NOT automatically parse JSON.
- response.json() parses the JSON (similar to JSON.parse()) and returns a JavaScript object.

Equivalent idea:

```js
const text = await response.text();
const data = JSON.parse(text);
```

## JSON.parse()

```js
const json = '{"name":"Towsif"}';
const obj = JSON.parse(json);
```

Result:

```js
{ name: "Towsif" }
```

## Summary Table

| Operation | Method |
|-----------|--------|
| Object → JSON String | JSON.stringify() |
| JSON String → Object | JSON.parse() |
| Request JSON → req.body | express.json() |
| Response JSON → Object | response.json() |

## Complete Flow

```text
Frontend Object
      │
      ▼
JSON.stringify()
      │
      ▼
JSON String
      │
      ▼
HTTP Request
      │
      ▼
express.json()
      │
      ▼
Backend Object (req.body)
      │
      ▼
Process Request
      │
      ▼
res.json()
      │
      ▼
JSON Response
      │
      ▼
fetch()
      │
      ▼
response.json()
      │
      ▼
Frontend Object
```

## Common Misconceptions

❌ fetch() automatically parses JSON.
✔ No. response.json() does.

❌ express.json() creates JSON.
✔ No. It parses incoming JSON.

❌ JSON.stringify() creates an object.
✔ No. It creates a JSON string.

❌ JSON.parse() creates JSON.
✔ No. It converts JSON into a JavaScript object.

## Final Summary

We take input from the user on the frontend and store it in a normal JavaScript object.

Then we use **JSON.stringify()** to convert that object into a JSON string because HTTP cannot send JavaScript objects directly.

We send that JSON string to the backend using fetch() together with the **Content-Type: application/json** header.

On the backend, **express.json()** reads the request body, parses the JSON string, and converts it back into a JavaScript object stored in **req.body**.

After processing the request, the server sends data back using **res.json()**, which converts the backend JavaScript object into JSON.

On the frontend, **fetch() does NOT automatically parse the JSON response**. We must call **response.json()**, which parses the JSON response (internally similar to JSON.parse()) and converts it into a JavaScript object.

**Frontend Object → JSON.stringify() → JSON → express.json() → Backend Object → res.json() → JSON → response.json() → Frontend Object**
