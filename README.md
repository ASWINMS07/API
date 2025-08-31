# API — *Application Programming Interface* (Full Guide)
Your clean, exam-ready, GitHub-friendly notes. Save this as your repo README and drop the images inside an `images/` folder (already included below).

---

## 🧠 What is an **API**?
An **API (Application Programming Interface)** is a contract that lets one software talk to another safely. Think of it as a **restaurant menu** 🧾: the menu lists what you can order (valid operations), the kitchen is the server, and your waiter is the API carrying requests and responses.

### What an API does
- **Hides the internals** (you don’t see the server code or database).
- **Defines endpoints** (URLs) + **methods** (GET/POST/PUT/DELETE).
- **Validates/authenticates** who can do what.
- **Returns structured data** (usually JSON).

### How it works (high level)
1. **Client** (app/website/Postman) sends an HTTP request to an **endpoint** like `/api/users/42`.
2. **Server** checks auth, runs logic, hits a database, etc.
3. **Response** comes back with a **status code** (200, 201, 404, 500…) and a **body** (JSON/XML).
4. Optional: caching, rate‑limits, logging, retries, etc.

### Real‑time example
You open a government **e‑Sevai** portal to see Aadhaar‑linked data.  
- Your browser makes a **GET** request to `/api/aadhaar/1234`  
- Server validates your session, fetches data, returns JSON.  
No direct DB access, everything flows via the API.

---

## 🧭 Types of APIs (Big Picture)
We can classify APIs in a few useful ways:

### 1) By **Access/Visibility**
- **Public/Open APIs** → available to everyone (e.g., weather APIs).  
- **Partner APIs** → shared with selected orgs for B2B integration.  
- **Internal/Private APIs** → used inside a company (e.g., payroll ↔ HR).  
- **Composite APIs** → one request fan‑outs to multiple services and combines the results.

### 2) By **Style/Protocol**
- **REST** → resource‑based, uses HTTP verbs, JSON, stateless.
- **SOAP** → XML + strict contract (WSDL); heavy but very standardized (banking/enterprise).
- **GraphQL** → client asks for **exact fields** it needs; great for mobile.
- **gRPC** → super fast, binary (Protocol Buffers), great for microservices.

![API Types](https://github.com/ASWINMS07/API/blob/main/Screenshot%202025-08-31%20040447.png)

### 3) By **Use Case**
- **Web APIs** (browser ↔ server), **OS APIs** (Android/iOS/Windows),
- **Database APIs** (SQL/NoSQL drivers), **Hardware APIs** (GPS/Camera),
- **Library/SDK APIs** (functions you call inside code).

---

## 🧩 Deep Dive: **REST API**
**REST (Representational State Transfer)** is the most common style on the web.

### REST core principles
- **Client–Server** separation
- **Stateless** (each request carries all it needs; server doesn’t remember a session state)
- **Cacheable** responses (speed boosts)
- **Uniform interface** (resources, URIs, standard methods)
- **Layered system** (gateways, proxies okay)
- **Representations** (same resource can be JSON, XML, etc.)

### REST building blocks
- **Resource**: a thing (e.g., `User`, `Order`, `Student`).  
- **URI**: where it lives → `/api/students/17`  
- **Method**: what you do → **GET/POST/PUT/PATCH/DELETE**  
- **Representation**: JSON you send/receive  
- **Status codes**: `200 OK`, `201 Created`, `400 Bad Request`, `401 Unauthorized`, `404 Not Found`, `500 Server Error`

### Auth & safety in REST
- **API keys** for simple use‑cases
- **OAuth 2.0** for delegated access (Sign‑in with Google etc.)
- **JWT** for stateless tokens
- **Rate‑limit**, **input validation**, **CORS**, **HTTPS** only

### REST methods at a glance
![REST Methods](https://github.com/ASWINMS07/API/blob/main/Screenshot%202025-08-31%20040549.png)

---

## 🔍 **GET** — read data (safe & idempotent)
Use **GET** to **fetch** a resource. It **doesn’t change** server state.

**Example (Government portal)**  
User checks Aadhaar data:
```
GET /api/aadhaar/1234
Accept: application/json
```
**Possible response**
```json
{"aadhaar":"1234","name":"Vijay","dob":"2005-04-12"}
```

![GET Method](./images/get-method.png)

---

## ➕ **POST** — create data (not idempotent)
Use **POST** to **create** a new resource or trigger an operation.

**Example (School admission)**  
Submit student KYC:
```
POST /api/students
Content-Type: application/json
```
Body
```json
{"name":"Ananya","dob":"2008-07-10","location":"Coimbatore","photo":"base64..."}
```
**Response** → `201 Created` with the new resource.

![POST Method](./images/post-method.png)

---

## ♻️ **PUT** — full update (idempotent)
Use **PUT** to **replace** an entire resource representation.

**Example (Profile update)**  
```
PUT /api/students/42
Content-Type: application/json
```
Body
```json
{"name":"Vijay","phone":"4321"}
```
**Response** → `200 OK` (or `204 No Content`).

![PUT Method](./images/put-method.png)

---

## 🗑️ **DELETE** — remove data (idempotent)
```
DELETE /api/students/42
```
**Response** → `204 No Content` if successfully deleted.

## ✂️ **PATCH** — partial update
```
PATCH /api/students/42
Content-Type: application/json
```
Body
```json
{"phone":"9999"}
```

---

## 🛠 Real‑World Flow (End‑to‑End)
**Use‑case: Student Portal**
1. App **GET**s `/api/students?year=2025` to list students.  
2. Admin **POST**s `/api/students` to add a new student.  
3. Student **PUT**s `/api/students/42` to update profile.  
4. Admin **DELETE**s `/api/students/99` if needed.  
All requests go through auth, validation, business logic, and DB.

---

## 🐍 What is **Flask**?
**Flask** is a lightweight **Python web framework**—perfect for building APIs quickly.

**Why Flask rocks**
- Minimal but extensible (add SQLAlchemy, Marshmallow, JWT, etc.).
- Clear routing: `@app.route('/students')`.
- Great for microservices, prototypes, and teaching.

**Tiny real‑time example (Student API)**
```python
from flask import Flask, request, jsonify

app = Flask(__name__)
DB = {42: {"id": 42, "name": "Vijay", "phone": "4321"}}

@app.get("/api/students/<int:sid>")
def get_student(sid):
    student = DB.get(sid)
    return (jsonify(student), 200) if student else ({"error":"not found"}, 404)

@app.post("/api/students")
def create_student():
    data = request.get_json()
    sid = max(DB) + 1 if DB else 1
    DB[sid] = {"id": sid, **data}
    return DB[sid], 201

@app.put("/api/students/<int:sid>")
def update_student(sid):
    DB[sid] = {"id": sid, **request.get_json()}
    return DB[sid], 200

if __name__ == "__main__":
    app.run(debug=True)
```
Run it: `python app.py` → test with Postman/cURL.

![Flask Logo](./images/flask-logo.svg)

---

## 🧪 What is **Postman**?
**Postman** is a desktop/web app to **build, send, and test API requests** without writing front‑end code.

**How it works**
- Create a **Collection** of requests (GET/POST/PUT/DELETE).
- Use **Environments** for variables (like `{{baseUrl}}`).
- Write **Tests** (JS snippets) to assert status codes/fields.
- Run **Collection Runner** or **Monitor** to automate checks.

**Real‑time example (testing our Flask API)**
1. Set `baseUrl = http://localhost:5000`.
2. **POST** `{{baseUrl}}/api/students` with JSON body → expect **201**.
3. **GET** `{{baseUrl}}/api/students/1` → expect **200** + correct fields.
4. Add **Tests** tab:
```js
pm.test("Status is 200", function () { pm.response.to.have.status(200); });
pm.test("Has name", function () { pm.expect(pm.response.json()).to.have.property("name"); });
```

![Postman Logo](./images/postman-logo.svg)

---

## 🔐 What is a **URL Decoder**?
Web URLs can’t carry some characters directly, so browsers **percent‑encode** them.
- Space → `%20`
- `+` can mean space in query strings
- `:` `?` `&` `/` and Unicode are encoded too

A **URL decoder** reverses this so apps can read the real text.

**Real‑time example**
```
Encoded: /search?q=C%2B%2B%20vs%20Java&city=Coimbatore
Decoded: /search?q=C++ vs Java&city=Coimbatore
```
Your API layer should **decode** incoming query params before parsing.

**In Python**
```python
from urllib.parse import unquote_plus
print(unquote_plus("name=Vijay%20Kumar&city=Coimbatore"))
# name=Vijay Kumar&city=Coimbatore
```

**In JavaScript**
```js
decodeURIComponent("C%2B%2B%20vs%20Java") // "C++ vs Java"
```

---

## 🧾 Summary (TL;DR)
| Topic | Key Idea | Real‑World Hook |
|---|---|---|
| API | Contract for apps to talk | e‑Sevai portal fetching Aadhaar |
| Types | Public, Partner, Internal, Composite; REST, SOAP, GraphQL, gRPC | Open weather; bank SOAP; GitHub GraphQL |
| REST | Stateless, resource‑based, JSON, standard verbs | Most web/mobile backends |
| GET | Read data | View profile / Aadhaar details |
| POST | Create data | Submit KYC / new student |
| PUT | Replace whole resource | Update full profile |
| PATCH | Partial update | Change just phone no. |
| DELETE | Remove resource | Delete student record |
| Flask | Python micro‑framework to build APIs | Quick student API |
| Postman | GUI client to test APIs | Collections, tests, environments |
| URL Decoder | Turns `%xx` back to characters | Search queries, user input |

---

### How to use this in your repo
- Keep this file as `README.md`.
- Put all images in `images/` and keep the file names as referenced above.
- Push both to GitHub. The images will render automatically.
