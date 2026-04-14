# Node.js

# Section 2: Introduction to Node.js and NPM

---

## 4. Section Intro

**What is this:** A quick overview of what will be covered in this section of the course.

**Description:** The instructor introduces the topics for this section: what Node.js is, how it works, core modules, file I/O, building a simple web server, routing, templating, and working with NPM packages.

---

## 5. What Is Node.js and Why Use It?

**What is this:** An explanation of what Node.js is, how it differs from browser JavaScript, and why it's useful for backend development.

**Description:** Node.js is a JavaScript runtime built on Chrome's V8 engine. It lets you run JavaScript on the server side, outside the browser. It uses an event-driven, non-blocking I/O model which makes it efficient for handling many concurrent connections.

**Key points:**
- Node.js is NOT a framework — it's a runtime environment
- Uses the same V8 engine as Google Chrome
- Great for I/O-heavy apps (APIs, real-time apps, file servers)
- Single-threaded but non-blocking via the event loop

---

## 6. Running JavaScript Outside the Browser

**What is this:** How to run `.js` files using Node.js in the terminal instead of the browser.

**Description:** Instead of opening a browser console, you use the `node` command in your terminal to execute JavaScript files directly on your machine.

**Examples:**

```js
// index.js
const name = "Node.js";
console.log(`Hello from ${name}!`);
```

```bash
# Run the file
node index.js
# Output: Hello from Node.js!
```

---

## 7. Using Modules 1: Core Modules

**What is this:** Introduction to Node's built-in (core) modules that come with Node.js without needing to install anything.

**Description:** Node.js ships with a set of built-in modules like `fs`, `http`, `path`, `os`, etc. You load them using `require()`.

**Examples:**

```js
// Using the 'os' core module
const os = require("os");

console.log(os.platform());   // e.g. 'win32'
console.log(os.homedir());    // e.g. 'C:\Users\Username'
```

```js
// Using the 'path' core module
const path = require("path");

console.log(path.join(__dirname, "files", "text.txt"));
// Builds a safe file path regardless of OS
```

---

## 8. Reading and Writing Files

**What is this:** How to read from and write to files synchronously using the `fs` (file system) core module.

**Description:** The `fs` module provides methods for interacting with the file system. Synchronous methods (ending in `Sync`) block execution until the operation finishes.

**Examples:**

```js
const fs = require("fs");

// Reading a file synchronously
const data = fs.readFileSync("./txt/input.txt", "utf-8");
console.log(data);

// Writing a file synchronously
const output = `This was read: ${data}`;
fs.writeFileSync("./txt/output.txt", output);
console.log("File written!");
```

---

## 9. Blocking and Non-Blocking: Asynchronous Nature of Node.js

**What is this:** Understanding the difference between synchronous (blocking) and asynchronous (non-blocking) code in Node.js.

**Description:** Node.js runs on a single thread. If you use synchronous operations, the thread is blocked and nothing else can run. Asynchronous operations (with callbacks) let Node.js continue executing other code while waiting for slow I/O operations to finish.

**Key concepts:**
- **Blocking:** Code waits for the operation to complete before moving on
- **Non-blocking:** Code registers a callback and moves on immediately
- The **event loop** picks up completed async tasks and runs their callbacks

**Examples:**

```js
// Blocking — bad for servers
const data = fs.readFileSync("file.txt", "utf-8"); // waits here
console.log(data);
console.log("This runs after the file is read");

// Non-blocking — Node.js way
fs.readFile("file.txt", "utf-8", (err, data) => {
  console.log(data); // runs later, when file is ready
});
console.log("This runs immediately, without waiting");
```

---

## 10. Reading and Writing Files Asynchronously

**What is this:** Using `fs.readFile` and `fs.writeFile` with callbacks to perform non-blocking file I/O.

**Description:** Asynchronous file operations take a callback function as the last argument. The callback receives an error (if any) and the result. This is the preferred pattern in Node.js.

**Examples:**

```js
const fs = require("fs");

// Read a file asynchronously
fs.readFile("./txt/start.txt", "utf-8", (err, data1) => {
  if (err) return console.log("Error:", err);

  // Read another file using the result of the first
  fs.readFile(`./txt/${data1}.txt`, "utf-8", (err, data2) => {
    if (err) return console.log("Error:", err);

    // Write the combined result to a new file
    fs.writeFile("./txt/final.txt", `${data1}\n${data2}`, "utf-8", (err) => {
      if (err) return console.log("Error:", err);
      console.log("File written successfully!");
    });
  });
});
```

---

## 11. Creating a Simple Web Server

**What is this:** Using the `http` core module to create a basic web server that listens for requests and sends responses.

**Description:** Node.js has a built-in `http` module that lets you create a web server without any framework. The `createServer` method takes a callback that runs on every request.

**Examples:**

```js
const http = require("http");

const server = http.createServer((req, res) => {
  res.end("Hello from the server!");
});

server.listen(8000, "127.0.0.1", () => {
  console.log("Server running on http://127.0.0.1:8000");
});
```

```bash
# Start the server
node index.js

# Visit in browser: http://127.0.0.1:8000
```

---

## 12. Routing

**What is this:** Directing different URLs to different responses using the request URL from `req.url`.

**Description:** Routing means checking what URL was requested and responding accordingly. Without a framework, you do this manually by reading `req.url` and using `if/else` or `switch` statements.

**Examples:**

```js
const http = require("http");

const server = http.createServer((req, res) => {
  const url = req.url;

  if (url === "/") {
    res.end("Welcome to the homepage!");
  } else if (url === "/about") {
    res.end("This is the about page.");
  } else {
    res.writeHead(404, { "Content-Type": "text/html" });
    res.end("<h1>404 - Page Not Found</h1>");
  }
});

server.listen(8000, "127.0.0.1", () => {
  console.log("Listening on port 8000...");
});
```

---

## 13. Building a (Very) Simple API

**What is this:** Serving JSON data in response to a specific route, simulating a basic API endpoint.

**Description:** An API endpoint returns data (usually JSON) instead of HTML. You read data from a file once on server start, then serve it as JSON when the `/api` route is hit.

**Examples:**

```js
const http = require("http");
const fs = require("fs");

// Read data once at startup — not inside the request handler
const data = fs.readFileSync(`${__dirname}/dev-data/data.json`, "utf-8");
const productData = JSON.parse(data);

const server = http.createServer((req, res) => {
  if (req.url === "/api") {
    res.writeHead(200, { "Content-Type": "application/json" });
    res.end(JSON.stringify(productData));
  } else {
    res.writeHead(404, { "Content-Type": "text/html" });
    res.end("<h1>Not Found</h1>");
  }
});

server.listen(8000, "127.0.0.1", () => {
  console.log("API server running on port 8000");
});
```

---

## 14. HTML Templating: Building the Templates

**What is this:** Creating HTML template files with placeholders that will be filled with dynamic data.

**Description:** Instead of building HTML strings in JavaScript, you write `.html` template files that contain special placeholder tokens (e.g. `{%PRODUCTNAME%}`). These get replaced with real data at request time.

**Examples:**

```html
<!-- template-card.html -->
<div class="card">
  <h2>{%PRODUCTNAME%}</h2>
  <p>Price: {%PRICE%}</p>
  <p>{%DESCRIPTION%}</p>
  <a href="/product?id={%ID%}">Details</a>
</div>
```

```html
<!-- template-overview.html -->
<html>
  <body>
    <h1>Fresh Fruits</h1>
    <div class="cards-container">
      {%PRODUCT_CARDS%}
    </div>
  </body>
</html>
```

---

## 15. HTML Templating: Filling the Templates

**What is this:** Replacing placeholder tokens in HTML templates with real data from a JSON file at request time.

**Description:** A helper function reads the template, then uses `String.replace()` (or `replaceAll`) to swap each `{%TOKEN%}` with actual values. For lists, you map over the data and fill one card template per item, then join them all into one string.

**Examples:**

```js
const fs = require("fs");
const http = require("http");

const tempOverview = fs.readFileSync(`${__dirname}/templates/template-overview.html`, "utf-8");
const tempCard = fs.readFileSync(`${__dirname}/templates/template-card.html`, "utf-8");
const data = fs.readFileSync(`${__dirname}/dev-data/data.json`, "utf-8");
const products = JSON.parse(data);

// Helper to fill a single card template with one product's data
const fillTemplate = (template, product) => {
  return template
    .replace(/{%PRODUCTNAME%}/g, product.productName)
    .replace(/{%PRICE%}/g, product.price)
    .replace(/{%DESCRIPTION%}/g, product.description)
    .replace(/{%ID%}/g, product.id);
};

const server = http.createServer((req, res) => {
  if (req.url === "/" || req.url === "/overview") {
    res.writeHead(200, { "Content-Type": "text/html" });

    const cardsHtml = products.map((p) => fillTemplate(tempCard, p)).join("");
    const output = tempOverview.replace("{%PRODUCT_CARDS%}", cardsHtml);

    res.end(output);
  }
});

server.listen(8000, "127.0.0.1", () => console.log("Server running..."));
```

---

## 16. Parsing Variables from URLs

**What is this:** Extracting query string parameters from the URL (e.g. `/product?id=2`) using Node's built-in `url` module.

**Description:** The `url` module can parse a URL string into its parts. The `query` property gives you an object with key-value pairs from the query string.

**Examples:**

```js
const http = require("http");
const url = require("url");

const server = http.createServer((req, res) => {
  const { pathname, query } = url.parse(req.url, true);
  // true → parse query string into an object

  if (pathname === "/product") {
    const id = query.id; // e.g. "2"
    res.end(`You requested product with ID: ${id}`);
  }
});

server.listen(8000, "127.0.0.1", () => console.log("Listening..."));
```

---

## 17. Using Modules 2: Our Own Modules

**What is this:** Creating your own reusable modules and exporting/importing them using `module.exports` and `require`.

**Description:** Every file in Node.js is its own module. You can export functions, objects, or values from one file and import them in another. This keeps code organized and reusable.

**Examples:**

```js
// replaceTemplate.js — our own module
const replaceTemplate = (template, product) => {
  return template
    .replace(/{%PRODUCTNAME%}/g, product.productName)
    .replace(/{%PRICE%}/g, product.price)
    .replace(/{%DESCRIPTION%}/g, product.description)
    .replace(/{%ID%}/g, product.id);
};

module.exports = replaceTemplate;
```

```js
// index.js — importing and using the module
const replaceTemplate = require("./replaceTemplate");

const card = replaceTemplate(tempCard, products[0]);
console.log(card);
```

---

## 18. Introduction to NPM and the package.json File

**What is this:** What NPM is, how to initialize a project with `package.json`, and why it matters.

**Description:** NPM (Node Package Manager) is the default package manager for Node.js. `package.json` is a file that describes your project and tracks its dependencies.

**Examples:**

```bash
# Initialize a new project — creates package.json
npm init

# Or skip the questions with defaults
npm init -y
```

```json
// package.json (example)
{
  "name": "learn-node",
  "version": "1.0.0",
  "description": "Learning Node.js",
  "main": "index.js",
  "scripts": {
    "start": "node index.js"
  },
  "author": "Your Name",
  "license": "ISC"
}
```

---

## 19. Types of Packages and Installs

**What is this:** The difference between regular dependencies, dev dependencies, and global installs.

**Description:** NPM packages can be installed in different ways depending on their purpose.

**Examples:**

```bash
# Regular dependency — needed in production
npm install slugify

# Dev dependency — only needed during development
npm install nodemon --save-dev

# Global install — available as a CLI tool anywhere on your system
npm install -g nodemon
```

```json
// These show up in package.json like this:
{
  "dependencies": {
    "slugify": "^1.6.6"
  },
  "devDependencies": {
    "nodemon": "^3.0.0"
  }
}
```

---

## 20. Using Modules 3: 3rd Party Modules

**What is this:** Installing and using third-party packages from NPM in your project.

**Description:** After installing a package with `npm install`, you use `require()` just like with core modules. The package lives in the `node_modules` folder.

**Examples:**

```bash
npm install slugify
```

```js
const slugify = require("slugify");

const slug = slugify("Fresh Avocados", { lower: true });
console.log(slug); // "fresh-avocados"

// Useful for creating clean URL-friendly strings from product names
```

---

## 21. Package Versioning and Updating

**What is this:** Understanding semantic versioning (semver) and how to safely update packages.

**Description:** NPM uses semantic versioning: `MAJOR.MINOR.PATCH`. The symbols `^` and `~` in `package.json` control what updates are accepted.

**Key points:**
- `^1.6.6` — accept minor and patch updates (e.g. 1.7.0, 1.6.9) — most common
- `~1.6.6` — accept only patch updates (e.g. 1.6.7, 1.6.9)
- `1.6.6` — exact version only, no updates

**Examples:**

```bash
# Check for outdated packages
npm outdated

# Update packages within allowed range (respects ^ and ~)
npm update

# Install a specific version
npm install slugify@1.6.0

# Install the latest version
npm install slugify@latest
```

---

## 22. Setting up Prettier in VS Code

**What is this:** Installing and configuring Prettier as a dev dependency to auto-format your code.

**Description:** Prettier is an opinionated code formatter. You can install it as a dev dependency and configure it via a `.prettierrc` file. Combined with the VS Code extension, it formats your code on save.

**Examples:**

```bash
npm install prettier --save-dev
```

```json
// .prettierrc
{
  "singleQuote": true,
  "trailingComma": "all"
}
```

```json
// package.json — add a format script
{
  "scripts": {
    "start": "node index.js",
    "format": "prettier --write ."
  }
}
```

```bash
# Format all files manually
npm run format
```

---

# Section 3: Introduction to Back-End Web Development

---

## 24. Section Intro

**What is this:** A brief overview of what this section covers — how the web works, HTTP, and the difference between front-end and back-end development.

**Description:** Before diving deeper into Node.js, this section builds the mental model of what actually happens when you type a URL into a browser — from DNS lookup to HTTP response — and where Node.js fits in that picture.

---

## 25. An Overview of How the Web Works

**What is this:** A step-by-step breakdown of what happens behind the scenes when a browser makes a request to a server.

**Description:** Every web interaction follows the same basic flow: the browser looks up the server's IP via DNS, opens a TCP/IP connection, sends an HTTP request, the server processes it and sends back an HTTP response, and the browser renders the result.

**Key steps:**
1. User types a URL (e.g. `https://example.com/about`)
2. **DNS lookup** — domain name is resolved to an IP address
3. **TCP/IP handshake** — a connection is established between client and server
4. **HTTP request** sent — method, path, headers, body
5. **Server processes** the request (Node.js, etc.)
6. **HTTP response** sent back — status code, headers, body (HTML/JSON/etc.)
7. Browser renders the response

**Key terms:**
- **Client** — the browser or any app making the request
- **Server** — the machine that receives and responds to the request
- **TCP/IP** — the protocol that handles data transmission over the internet
- **DNS** — Domain Name System, like a phonebook mapping names to IPs

---

## 26. HTTP in Action

**What is this:** A closer look at what an actual HTTP request and response look like — methods, headers, status codes, and body.

**Description:** HTTP (HyperText Transfer Protocol) is the language of the web. Every request has a method and a path; every response has a status code. Headers carry metadata, and the body carries the actual content.

**HTTP Request structure:**
```
GET /overview HTTP/1.1
Host: example.com
User-Agent: Mozilla/5.0
Accept-Language: en-US
```

**HTTP Response structure:**
```
HTTP/1.1 200 OK
Content-Type: text/html
Content-Length: 1234

<html>...</html>
```

**Common HTTP methods:**
- `GET` — retrieve data
- `POST` — send data to create something
- `PUT` / `PATCH` — update existing data
- `DELETE` — remove data

**Common status codes:**
- `200 OK` — success
- `301 Moved Permanently` — redirect
- `404 Not Found` — resource doesn't exist
- `500 Internal Server Error` — something went wrong on the server

---

## 27. Front-End vs. Back-End Web Development

**What is this:** The difference between what runs in the browser (front-end) and what runs on the server (back-end).

**Description:** Front-end code runs in the user's browser and handles what the user sees and interacts with. Back-end code runs on a server and handles data, business logic, authentication, and databases. Node.js is a back-end technology.

**Front-end:**
- HTML, CSS, JavaScript in the browser
- Frameworks: React, Angular, Vue
- Responsible for: UI, animations, user interaction

**Back-end:**
- Runs on a server, invisible to the user
- Languages/runtimes: Node.js, Python, PHP, Java
- Responsible for: data storage, authentication, APIs, business logic

**Node.js role:**
- Runs JavaScript on the server
- Handles HTTP requests, talks to databases, serves responses
- Can serve HTML pages (SSR) or pure JSON (API)

---

## 28. Static vs Dynamic vs API

**What is this:** The three main types of websites/web applications and how each one delivers content to the client.

**Description:** There are three models for how a server responds to requests. Understanding which one you're building determines your architecture.

**Static websites:**
- Files (HTML, CSS, JS, images) are pre-built and served as-is
- No server-side processing per request
- Fast and simple, but content can't change based on users or data
- Example: a portfolio site deployed on Netlify

**Dynamic websites (Server-Side Rendering — SSR):**
- The server builds the HTML on each request using templates + data from a database
- The browser receives a fully-rendered HTML page
- Example: a product page that shows different content per user
- Node.js + a template engine (like Pug or EJS) is used for this

**API-based (Client-Side Rendering — CSR):**
- The server only sends JSON data via an API
- A front-end framework (React, Angular) fetches the data and builds the UI in the browser
- Clean separation between back-end (API) and front-end (SPA)
- Most modern apps work this way

**Comparison:**

| Type    | Server sends   | Who builds the UI | Example tech          |
|---------|---------------|-------------------|-----------------------|
| Static  | Pre-built HTML | Nobody (pre-made) | Netlify, GitHub Pages |
| Dynamic | Built HTML     | Server            | Node + Pug/EJS        |
| API     | JSON           | Browser (JS)      | Node + React/Angular  |

---

# Section 4: How Node.js Works: A Look Behind the Scenes

---

## 29. Section Intro

**What is this:** Overview of what this section covers — the internals of Node.js: its architecture, the event loop, events, streams, and the module system.

**Description:** This section goes under the hood of Node.js to explain why it behaves the way it does. Understanding these internals helps you write better, more efficient code and debug tricky async issues.

---

## 30. Node, V8, Libuv and C++

**What is this:** The core components that make up Node.js and what each one is responsible for.

**Description:** Node.js is not just JavaScript. It is built on top of multiple C++ libraries that give it its power. Understanding this stack explains how JavaScript (a single-threaded language) can handle async I/O efficiently.

**Key components:**

| Component | Language | Role |
|-----------|----------|------|
| **V8**    | C++      | Compiles and executes JavaScript |
| **libuv** | C        | Provides the event loop and async I/O (file system, networking, timers) |
| **http-parser** | C | Parses HTTP requests and responses |
| **c-ares** | C       | Async DNS resolution |
| **OpenSSL** | C++    | Cryptography (TLS/SSL) |
| **zlib**  | C        | Compression |

**How they connect:**
- Your JS code runs in V8
- When you call `fs.readFile`, Node passes it to libuv
- libuv handles the OS-level I/O and calls back into V8 when done

---

## 31. Processes, Threads and the Thread Pool

**What is this:** How Node.js uses a single main thread but offloads heavy work to a background thread pool via libuv.

**Description:** Node.js runs in a single process with a single main thread. This thread runs the event loop. But some expensive operations (file I/O, DNS, crypto) are sent to libuv's thread pool, which runs them in parallel on separate threads.

**Key concepts:**
- **Process** — a running instance of a program; has its own memory space
- **Thread** — a sequence of instructions within a process; threads share memory
- **Node's main thread** — runs JavaScript and the event loop
- **Thread pool** — 4 extra threads by default (configurable via `UV_THREADPOOL_SIZE`)
- Operations that use the thread pool: `fs`, `crypto`, `dns.lookup`, `zlib`
- Operations that do NOT use the thread pool: network I/O (handled directly by the OS)

```js
// You can increase the thread pool size via environment variable
process.env.UV_THREADPOOL_SIZE = 8;
```

---

## 32. The Node.js Event Loop

**What is this:** A deep dive into how the event loop works — the mechanism that makes Node.js non-blocking despite being single-threaded.

**Description:** The event loop is what allows Node.js to perform non-blocking I/O. It continuously checks for completed async tasks and runs their callbacks. It has distinct phases, each with its own callback queue.

**Event loop phases (in order):**

1. **Timers** — runs callbacks from `setTimeout` and `setInterval` whose delay has expired
2. **Pending callbacks** — runs I/O callbacks deferred from the previous iteration
3. **Idle / Prepare** — internal use only
4. **Poll** — retrieves new I/O events and runs their callbacks (most callbacks happen here)
5. **Check** — runs `setImmediate` callbacks
6. **Close callbacks** — runs close event callbacks (e.g. `socket.on('close', ...)`)

**Special queues (run between every phase):**
- `process.nextTick()` — runs before the next event loop phase
- `Promise` microtasks — runs after `nextTick` queue is drained

**Execution priority (highest to lowest):**
```
process.nextTick → Promise microtasks → timers → I/O → setImmediate → close
```

---

## 33. The Event Loop in Practice

**What is this:** Hands-on examples showing the exact order callbacks execute in the event loop.

**Description:** Seeing the event loop phases in action makes the theory concrete. The key insight is that `nextTick` and Promises always run before timers, and `setImmediate` always runs after I/O callbacks in the same iteration.

**Examples:**

```js
const fs = require("fs");

// Order of execution demo
console.log("1 - start"); // sync — runs first

setTimeout(() => console.log("2 - setTimeout"), 0); // timers phase

setImmediate(() => console.log("3 - setImmediate")); // check phase

fs.readFile("./test.txt", () => {
  // After I/O, setImmediate runs before setTimeout
  setTimeout(() => console.log("4 - setTimeout inside I/O"), 0);
  setImmediate(() => console.log("5 - setImmediate inside I/O"));
  process.nextTick(() => console.log("6 - nextTick inside I/O"));
});

process.nextTick(() => console.log("7 - nextTick")); // runs before any phase

console.log("8 - end"); // sync — runs second

// Output order: 1, 8, 7, 2, 3, (after file read:) 6, 5, 4
```

---

## 34. Events and Event-Driven Architecture

**What is this:** How Node.js uses an event-driven model — emitting named events and reacting to them with listener callbacks.

**Description:** Much of Node.js is built around the `EventEmitter` class. Objects emit named events, and you register listener functions that run when those events fire. This is the pattern used by `http.Server`, `fs.ReadStream`, `net.Socket`, and others.

**Key concepts:**
- **EventEmitter** — the base class for objects that emit events
- **`emit(eventName, ...args)`** — triggers an event, passing data to all listeners
- **`on(eventName, callback)`** — registers a listener for an event
- **`once(eventName, callback)`** — listener fires only the first time the event occurs

```js
const EventEmitter = require("events");

const emitter = new EventEmitter();

// Register a listener before emitting
emitter.on("newSale", (quantity) => {
  console.log(`New sale! Quantity: ${quantity}`);
});

// Emit the event
emitter.emit("newSale", 5); // "New sale! Quantity: 5"
```

---

## 35. Events in Practice

**What is this:** Using EventEmitter in a real context — creating a custom class that extends EventEmitter.

**Description:** The correct pattern in Node.js is not to use a bare `EventEmitter` instance but to create a class that extends it. This is how Node's own built-in classes (like `http.Server`) work.

**Examples:**

```js
const EventEmitter = require("events");
const http = require("http");

class Sales extends EventEmitter {
  constructor() {
    super();
  }
}

const myEmitter = new Sales();

myEmitter.on("newSale", () => {
  console.log("There was a new sale!");
});

myEmitter.on("newSale", (stock) => {
  console.log(`There are now ${stock} items left in stock.`);
});

myEmitter.emit("newSale", 9);

// Node's http.Server is also an EventEmitter
const server = http.createServer();

server.on("request", (req, res) => {
  console.log("Request received!");
  res.end("Request received");
});

server.on("close", () => {
  console.log("Server closed");
});

server.listen(8000, () => {
  console.log("Waiting for requests...");
});
```

---

## 36. Introduction to Streams

**What is this:** What streams are, why they exist, and the four types of streams in Node.js.

**Description:** Streams let you process data piece by piece (in chunks) without loading it all into memory at once. This is critical for handling large files, HTTP requests/responses, or any continuous data source.

**Four types of streams:**

| Type        | Description                              | Example                    |
|-------------|------------------------------------------|----------------------------|
| **Readable** | Source you can read from               | `fs.createReadStream`, `http` request |
| **Writable** | Destination you can write to           | `fs.createWriteStream`, `http` response |
| **Duplex**   | Both readable and writable             | `net.Socket` (TCP socket)  |
| **Transform** | Duplex that transforms data as it passes | `zlib.createGzip` (compression) |

**Key events on Readable streams:**
- `data` — emitted when a chunk is available
- `end` — emitted when there is no more data
- `error` — emitted on error

**Key events on Writable streams:**
- `drain` — emitted when it's safe to write more
- `finish` — emitted when all data has been flushed
- `error` — emitted on error

---

## 37. Streams in Practice

**What is this:** Hands-on examples using readable and writable streams, including the pipe method to connect them efficiently.

**Description:** The most common and correct pattern is to use `pipe()` to connect a readable stream directly to a writable stream. This handles backpressure automatically — it prevents the readable from overwhelming the writable.

**Examples:**

```js
const fs = require("fs");
const http = require("http");

const server = http.createServer();

server.on("request", (req, res) => {
  // Solution 1 — load entire file into memory (bad for large files)
  // fs.readFile("test-file.txt", (err, data) => {
  //   res.end(data);
  // });

  // Solution 2 — stream chunks manually (works but no backpressure handling)
  // const readable = fs.createReadStream("test-file.txt");
  // readable.on("data", (chunk) => res.write(chunk));
  // readable.on("end", () => res.end());

  // Solution 3 — pipe (best: handles backpressure automatically)
  const readable = fs.createReadStream("test-file.txt");
  readable.pipe(res);
  // readable → res (writable)
});

server.listen(8000, () => console.log("Server ready"));
```

```js
// Piping with compression (transform stream)
const zlib = require("zlib");

const readable = fs.createReadStream("test-file.txt");
const writable = fs.createWriteStream("test-file.txt.gz");
const gzip = zlib.createGzip();

readable.pipe(gzip).pipe(writable);
// readable → gzip (transform) → writable
```

---

## 38. How Requiring Modules Really Works

**What is this:** What Node.js does internally when you call `require()` — the module loading process step by step.

**Description:** `require()` is not magic. Node.js follows a specific sequence to find and load a module. Understanding this helps you debug module resolution issues and understand why modules are cached.

**Steps Node takes when you call `require('something')`:**

1. **Resolve the path** — figure out which file to load:
   - Core module? Use it directly (e.g. `require('fs')`)
   - Starts with `./` or `../`? Load as a relative file path
   - Otherwise? Look in `node_modules` folder (walks up directories)

2. **Load the file** — read the file contents

3. **Wrap in a function** — Node wraps every module in an IIFE:
   ```js
   (function(exports, require, module, __filename, __dirname) {
     // Your module code here
   });
   ```
   This is why `__dirname`, `__filename`, `require`, `module`, and `exports` are available in every file.

4. **Execute** — the wrapper function is called, your code runs

5. **Cache** — the result is stored in `require.cache`. Next time you `require` the same module, the cached version is returned — the file is NOT re-executed.

6. **Return `module.exports`** — whatever you assigned to `module.exports` is returned to the caller

---

## 39. Requiring Modules in Practice

**What is this:** Practical examples of how the module system works, including caching behavior and the difference between `exports` and `module.exports`.

**Description:** `module.exports` is the actual object returned by `require()`. `exports` is just a shorthand reference to `module.exports`. If you reassign `exports` directly, you break the reference — use `module.exports` for exporting a single value.

**Examples:**

```js
// math.js
// Correct — attaching to exports (shorthand for module.exports)
exports.add = (a, b) => a + b;
exports.multiply = (a, b) => a * b;
```

```js
// calculator.js
// Correct — replacing module.exports entirely with a class
class Calculator {
  add(a, b) { return a + b; }
  multiply(a, b) { return a * b; }
}

module.exports = Calculator;
```

```js
// index.js
const { add, multiply } = require("./math");
console.log(add(2, 3));       // 5
console.log(multiply(2, 3));  // 6

const Calculator = require("./calculator");
const calc = new Calculator();
console.log(calc.add(2, 3));  // 5
```

```js
// Caching demo — module code runs only once
// counter.js
let count = 0;
exports.increment = () => ++count;
exports.getCount = () => count;

// index.js
const c1 = require("./counter");
const c2 = require("./counter"); // same cached instance

c1.increment();
console.log(c2.getCount()); // 1 — same object, not re-executed
```

---

# Section 5: [Optional] Asynchronous JavaScript: Promises and Async/Await

---

## 40. Section Intro

**What is this:** Overview of the section — solving the callback hell problem using Promises and async/await.

**Description:** This section covers the evolution of async JavaScript: from nested callbacks, to Promises, to the cleaner async/await syntax. These are essential tools for writing readable asynchronous Node.js code.

---

## 41. The Problem with Callbacks: Callback Hell

**What is this:** Why deeply nested callbacks make async code hard to read and maintain.

**Description:** When async operations depend on each other, you have to nest callbacks inside callbacks. This creates "callback hell" — a pyramid of doom that is hard to read, hard to debug, and hard to maintain.

**Examples:**

```js
const fs = require("fs");

// Callback hell — each step depends on the previous
fs.readFile("./txt/start.txt", "utf-8", (err, data1) => {
  if (err) return console.log(err);

  fs.readFile(`./txt/${data1}.txt`, "utf-8", (err, data2) => {
    if (err) return console.log(err);

    fs.readFile("./txt/append.txt", "utf-8", (err, data3) => {
      if (err) return console.log(err);

      fs.writeFile("./txt/final.txt", `${data2}\n${data3}`, "utf-8", (err) => {
        if (err) return console.log(err);
        console.log("Done writing file!");
      });
    });
  });
});

// Problems:
// - Hard to read (indentation keeps growing)
// - Error handling duplicated at every level
// - Difficult to reuse or test individual steps
```

---

## 42. From Callback Hell to Promises

**What is this:** Refactoring callback-based code to use Promises for flat, readable async chains.

**Description:** A Promise represents a value that will be available in the future. Instead of passing a callback into a function, the function returns a Promise. You chain `.then()` for success and `.catch()` for errors — keeping the code flat instead of nested.

**Promise states:**
- **Pending** — async operation is still running
- **Fulfilled** — operation succeeded, `.then()` runs
- **Rejected** — operation failed, `.catch()` runs

**Examples:**

```js
const fs = require("fs");
const { promisify } = require("util");

// Convert callback-based fs methods to promise-based
const readFile = promisify(fs.readFile);
const writeFile = promisify(fs.writeFile);

// Flat promise chain — no nesting
readFile("./txt/start.txt", "utf-8")
  .then((data1) => readFile(`./txt/${data1}.txt`, "utf-8"))
  .then((data2) => {
    return readFile("./txt/append.txt", "utf-8").then((data3) =>
      writeFile("./txt/final.txt", `${data2}\n${data3}`, "utf-8")
    );
  })
  .then(() => console.log("Done writing file!"))
  .catch((err) => console.log("Error:", err)); // single catch for all errors
```

---

## 43. Building Promises

**What is this:** Creating your own Promises from scratch using the `Promise` constructor.

**Description:** You wrap an async (or sync) operation in `new Promise((resolve, reject) => {...})`. Call `resolve(value)` on success and `reject(error)` on failure. This is how you promisify things that don't support Promises natively.

**Examples:**

```js
const fs = require("fs");

// Manual promisification of fs.readFile
const readFilePro = (file) => {
  return new Promise((resolve, reject) => {
    fs.readFile(file, "utf-8", (err, data) => {
      if (err) reject(err);     // triggers .catch()
      else resolve(data);       // triggers .then()
    });
  });
};

const writeFilePro = (file, data) => {
  return new Promise((resolve, reject) => {
    fs.writeFile(file, data, "utf-8", (err) => {
      if (err) reject(err);
      else resolve("success");
    });
  });
};

// Use the custom promises
readFilePro("./txt/start.txt")
  .then((data) => {
    console.log(data);
    return readFilePro(`./txt/${data}.txt`);
  })
  .then((data) => writeFilePro("./txt/final.txt", data))
  .then(() => console.log("File written!"))
  .catch((err) => console.log(err));
```

---

## 44. Consuming Promises with Async/Await

**What is this:** Using `async`/`await` as cleaner syntax for consuming Promises — looks like synchronous code but is still non-blocking.

**Description:** `async` functions always return a Promise. Inside them, `await` pauses execution until the awaited Promise resolves — without blocking the event loop. This eliminates `.then()` chains and makes async code read top-to-bottom like regular code.

**Examples:**

```js
const readFilePro = (file) =>
  new Promise((resolve, reject) => {
    fs.readFile(file, "utf-8", (err, data) => (err ? reject(err) : resolve(data)));
  });

const writeFilePro = (file, data) =>
  new Promise((resolve, reject) => {
    fs.writeFile(file, data, "utf-8", (err) => (err ? reject(err) : resolve()));
  });

// async/await — reads like synchronous code
const readWriteFiles = async () => {
  const data1 = await readFilePro("./txt/start.txt");
  const data2 = await readFilePro(`./txt/${data1}.txt`);
  const data3 = await readFilePro("./txt/append.txt");
  await writeFilePro("./txt/final.txt", `${data2}\n${data3}`);
  console.log("Done!");
};

readWriteFiles();
```

---

## 45. Returning Values from Async Functions

**What is this:** How to get the return value out of an async function, and handling errors with try/catch.

**Description:** An `async` function always returns a Promise. To get its resolved value outside the function, you either `await` it (inside another async function) or use `.then()`. Errors inside async functions should be caught with `try/catch`.

**Examples:**

```js
const readFilePro = (file) =>
  new Promise((resolve, reject) => {
    fs.readFile(file, "utf-8", (err, data) => (err ? reject(err) : resolve(data)));
  });

const writeFilePro = (file, data) =>
  new Promise((resolve, reject) => {
    fs.writeFile(file, data, "utf-8", (err) => (err ? reject(err) : resolve()));
  });

// try/catch handles rejected promises inside async functions
const readWriteFiles = async () => {
  try {
    const data1 = await readFilePro("./txt/start.txt");
    const data2 = await readFilePro(`./txt/${data1}.txt`);
    const data3 = await readFilePro("./txt/append.txt");
    await writeFilePro("./txt/final.txt", `${data2}\n${data3}`);
    return "Files processed successfully"; // becomes the resolved value
  } catch (err) {
    console.log("Error:", err.message);
    throw err; // re-throw so the caller can also catch it
  }
};

// Consuming the return value from outside
readWriteFiles()
  .then((result) => console.log(result))
  .catch((err) => console.log("Caught outside:", err.message));
```

---

## 46. Waiting for Multiple Promises Simultaneously

**What is this:** Running multiple independent async operations in parallel instead of sequentially using `Promise.all`.

**Description:** When multiple async operations don't depend on each other, running them one after another with `await` wastes time. `Promise.all` fires them all at once and waits for all to finish — as fast as the slowest one.

**Examples:**

```js
const readFilePro = (file) =>
  new Promise((resolve, reject) => {
    fs.readFile(file, "utf-8", (err, data) => (err ? reject(err) : resolve(data)));
  });

// Sequential — slow (each waits for the previous)
const sequential = async () => {
  const data1 = await readFilePro("./txt/file1.txt"); // waits
  const data2 = await readFilePro("./txt/file2.txt"); // then waits
  const data3 = await readFilePro("./txt/file3.txt"); // then waits
};

// Parallel — fast (all three start at the same time)
const parallel = async () => {
  const [data1, data2, data3] = await Promise.all([
    readFilePro("./txt/file1.txt"),
    readFilePro("./txt/file2.txt"),
    readFilePro("./txt/file3.txt"),
  ]);
  console.log(data1, data2, data3);
};

parallel();

// Note: if ANY promise rejects, Promise.all rejects immediately
// Use Promise.allSettled() if you want all results regardless of failures
```

---

# Section 6: Express: Let's Start Building the Natours API!

---

## 47. Section Intro

**What is this:** Overview of the section — building a real REST API with Express from scratch.

**Description:** This section introduces Express and uses it to build the Natours API step by step. Topics include routing, middleware, RESTful design, file structure, and environment configuration.

---

## 48. What is Express?

**What is this:** An introduction to Express — what it is, what it gives you over raw Node.js, and why it's the most popular Node.js framework.

**Description:** Express is a minimal, unopinionated web framework for Node.js. It wraps Node's `http` module and adds routing, middleware support, request/response helpers, and more — so you don't have to build these things yourself.

**What Express adds over raw Node.js:**
- Clean routing (`app.get`, `app.post`, etc.)
- Middleware pipeline (`app.use`)
- Easy access to query strings, URL params, request body
- Static file serving
- Template engine integration

```bash
npm install express
```

---

## 49. Installing Postman

**What is this:** Setting up Postman to test API endpoints during development.

**Description:** Postman is a GUI tool for sending HTTP requests to your API. Instead of building a frontend to test routes, you use Postman to fire GET, POST, PATCH, DELETE requests and inspect responses directly.

**Key features used:**
- Sending requests with different HTTP methods
- Setting request body (JSON)
- Viewing response status codes, headers, and body
- Saving requests in collections for reuse

---

## 50. Setting up Express and Basic Routing

**What is this:** Creating an Express app, defining routes, and starting the server.

**Description:** An Express app is created by calling `express()`. Routes are defined with `app.METHOD(path, handler)`. The handler receives `req` (request) and `res` (response) objects with many built-in helpers.

**Examples:**

```js
const express = require("express");
const app = express();

// Route handler — GET /
app.get("/", (req, res) => {
  res.status(200).json({ message: "Hello from the server!", app: "Natours" });
});

// POST route
app.post("/", (req, res) => {
  res.send("You can post to this endpoint.");
});

// Start the server
const port = 3000;
app.listen(port, () => {
  console.log(`App running on port ${port}...`);
});
```

---

## 51. APIs and RESTful API Design

**What is this:** The principles of REST and how they translate into API design decisions.

**Description:** REST (Representational State Transfer) is an architectural style for building APIs. Following REST conventions makes your API predictable and easy to consume.

**Core REST principles:**
- **Separate API from client** — server only sends data, client decides how to display it
- **Stateless** — each request contains all info needed; server stores no session state
- **Use HTTP methods correctly** — GET (read), POST (create), PUT/PATCH (update), DELETE (delete)
- **Use logical resource-based URLs** — nouns not verbs

**URL design:**

| Action              | Method | URL                  |
|---------------------|--------|----------------------|
| Get all tours       | GET    | `/api/v1/tours`      |
| Get one tour        | GET    | `/api/v1/tours/:id`  |
| Create a tour       | POST   | `/api/v1/tours`      |
| Update a tour       | PATCH  | `/api/v1/tours/:id`  |
| Delete a tour       | DELETE | `/api/v1/tours/:id`  |

**Key rules:**
- URLs identify resources (nouns), HTTP methods describe actions (verbs)
- Version your API in the URL: `/api/v1/...`
- Always send JSON responses with a status field

---

## 52. Starting Our API: Handling GET Requests

**What is this:** Reading data from a JSON file and sending it as a JSON response on a GET route.

**Description:** Data is read once at startup (not on every request). The GET `/api/v1/tours` route returns all tours; `/api/v1/tours/:id` returns a single tour found by its id.

**Examples:**

```js
const fs = require("fs");
const express = require("express");
const app = express();

const tours = JSON.parse(
  fs.readFileSync(`${__dirname}/dev-data/data/tours-simple.json`)
);

// GET all tours
app.get("/api/v1/tours", (req, res) => {
  res.status(200).json({
    status: "success",
    results: tours.length,
    data: { tours },
  });
});

// GET one tour by id
app.get("/api/v1/tours/:id", (req, res) => {
  const id = Number(req.params.id);
  const tour = tours.find((t) => t.id === id);

  if (!tour) {
    return res.status(404).json({ status: "fail", message: "Tour not found" });
  }

  res.status(200).json({ status: "success", data: { tour } });
});

app.listen(3000, () => console.log("Server running..."));
```

---

## 53. Handling POST Requests

**What is this:** Reading the request body and appending new data to the JSON file on a POST route.

**Description:** Express does not parse the request body by default. You need the `express.json()` middleware to make `req.body` available. New data is assigned an id, added to the array, and written back to the file.

**Examples:**

```js
app.use(express.json()); // middleware to parse JSON body

app.post("/api/v1/tours", (req, res) => {
  const newId = tours[tours.length - 1].id + 1;
  const newTour = { id: newId, ...req.body };

  tours.push(newTour);

  fs.writeFile(
    `${__dirname}/dev-data/data/tours-simple.json`,
    JSON.stringify(tours),
    (err) => {
      res.status(201).json({ status: "success", data: { tour: newTour } });
    }
  );
});
```

---

## 54. Responding to URL Parameters

**What is this:** Capturing dynamic segments from the URL path using `:paramName` and accessing them via `req.params`.

**Description:** Route parameters are named segments in the URL path preceded by `:`. They are available on `req.params` as strings. Optional parameters use `?`.

**Examples:**

```js
// Single param
app.get("/api/v1/tours/:id", (req, res) => {
  console.log(req.params); // { id: '5' }
  const id = Number(req.params.id);
  // ...
});

// Multiple params
app.get("/api/v1/tours/:id/reviews/:reviewId", (req, res) => {
  console.log(req.params); // { id: '5', reviewId: '12' }
});

// Optional param — the ? makes it not required
app.get("/api/v1/tours/:id/:x?", (req, res) => {
  console.log(req.params);
});
```

---

## 55. Handling PATCH Requests

**What is this:** Updating an existing resource partially using a PATCH route.

**Description:** PATCH updates only the fields provided in the request body (unlike PUT which replaces the whole resource). You find the item by id, confirm it exists, then respond — in a real app you'd update the database here.

**Examples:**

```js
app.patch("/api/v1/tours/:id", (req, res) => {
  const id = Number(req.params.id);
  const tour = tours.find((t) => t.id === id);

  if (!tour) {
    return res.status(404).json({ status: "fail", message: "Tour not found" });
  }

  // In a real app: update the record in the database with req.body fields
  res.status(200).json({
    status: "success",
    data: { tour: "<updated tour here>" },
  });
});
```

---

## 56. Handling DELETE Requests

**What is this:** Deleting a resource by id using a DELETE route.

**Description:** DELETE routes respond with `204 No Content` on success — no body is sent back. This is the standard HTTP convention for a successful delete.

**Examples:**

```js
app.delete("/api/v1/tours/:id", (req, res) => {
  const id = Number(req.params.id);
  const tour = tours.find((t) => t.id === id);

  if (!tour) {
    return res.status(404).json({ status: "fail", message: "Tour not found" });
  }

  // In a real app: delete from database
  res.status(204).json({
    status: "success",
    data: null, // 204 = no content, so null is conventional
  });
});
```

---

## 57. Refactoring Our Routes

**What is this:** Separating route handler logic into named functions to keep route definitions clean.

**Description:** Instead of defining the callback inline in `app.get(...)`, you extract each handler into a named function. This makes the route definitions much easier to read and the handlers easier to test individually.

**Examples:**

```js
// Handler functions
const getAllTours = (req, res) => {
  res.status(200).json({ status: "success", results: tours.length, data: { tours } });
};

const getTour = (req, res) => {
  const id = Number(req.params.id);
  const tour = tours.find((t) => t.id === id);
  if (!tour) return res.status(404).json({ status: "fail", message: "Tour not found" });
  res.status(200).json({ status: "success", data: { tour } });
};

const createTour = (req, res) => {
  const newTour = { id: tours[tours.length - 1].id + 1, ...req.body };
  tours.push(newTour);
  fs.writeFile(`${__dirname}/dev-data/data/tours-simple.json`, JSON.stringify(tours), () => {
    res.status(201).json({ status: "success", data: { tour: newTour } });
  });
};

const updateTour = (req, res) => {
  if (!tours.find((t) => t.id === Number(req.params.id)))
    return res.status(404).json({ status: "fail", message: "Tour not found" });
  res.status(200).json({ status: "success", data: { tour: "<updated>" } });
};

const deleteTour = (req, res) => {
  if (!tours.find((t) => t.id === Number(req.params.id)))
    return res.status(404).json({ status: "fail", message: "Tour not found" });
  res.status(204).json({ status: "success", data: null });
};

// Clean route definitions
app.route("/api/v1/tours").get(getAllTours).post(createTour);
app.route("/api/v1/tours/:id").get(getTour).patch(updateTour).delete(deleteTour);
```

---

## 58. Middleware and the Request-Response Cycle

**What is this:** Understanding how middleware functions work and how they form a pipeline that every request passes through.

**Description:** In Express, a request passes through a pipeline of middleware functions before reaching the route handler. Each middleware has access to `req`, `res`, and `next`. Calling `next()` passes control to the next middleware. If `next()` is not called, the request gets stuck.

**The request-response cycle:**
```
Request → middleware 1 → middleware 2 → ... → route handler → Response
```

**Middleware signature:**
```js
(req, res, next) => {
  // do something
  next(); // pass to next middleware
}
```

**Types of middleware:**
- **Application-level** — `app.use(fn)` — runs on all requests
- **Router-level** — `router.use(fn)` — runs on routes in that router
- **Error-handling** — `(err, req, res, next)` — 4 arguments, catches errors
- **Built-in** — `express.json()`, `express.static()`
- **Third-party** — `morgan`, `cors`, `helmet`, etc.

---

## 59. Creating Our Own Middleware

**What is this:** Writing custom middleware functions with `app.use()` to run logic on every request.

**Description:** You define middleware with `app.use(fn)` before your route definitions. It runs in order for every request that reaches it. Common uses: logging, adding data to `req`, authentication checks.

**Examples:**

```js
const express = require("express");
const app = express();

app.use(express.json());

// Custom middleware — runs on every request
app.use((req, res, next) => {
  console.log("Hello from the middleware!");
  next(); // MUST call next() or the request hangs
});

// Add a custom property to req — available in all subsequent handlers
app.use((req, res, next) => {
  req.requestTime = new Date().toISOString();
  next();
});

app.get("/api/v1/tours", (req, res) => {
  res.status(200).json({
    status: "success",
    requestedAt: req.requestTime, // set by middleware above
    data: { tours },
  });
});

app.listen(3000);
```

---

## 60. Using 3rd-Party Middleware

**What is this:** Installing and using popular third-party middleware packages like `morgan` for HTTP request logging.

**Description:** Third-party middleware is installed via npm and plugged into Express with `app.use()`. `morgan` is a popular HTTP request logger that logs method, URL, status code, response time, and size.

**Examples:**

```bash
npm install morgan
```

```js
const morgan = require("morgan");

// Log all requests to the console
app.use(morgan("dev"));
// Output: GET /api/v1/tours 200 4.123 ms - 512
```

**Morgan formats:**
- `"dev"` — colored, concise output for development
- `"combined"` — Apache-style, verbose, good for production logs
- `"tiny"` — minimal output

---

## 61. Implementing the "Users" Routes

**What is this:** Adding a second resource (users) to the API following the same RESTful pattern.

**Description:** A real API has multiple resources. You add users routes the same way as tours — define handler functions and wire them up with `app.route()`. Handlers return placeholder responses until a database is connected.

**Examples:**

```js
const getAllUsers = (req, res) => {
  res.status(500).json({ status: "error", message: "Route not yet implemented" });
};
const getUser = (req, res) => {
  res.status(500).json({ status: "error", message: "Route not yet implemented" });
};
const createUser = (req, res) => {
  res.status(500).json({ status: "error", message: "Route not yet implemented" });
};
const updateUser = (req, res) => {
  res.status(500).json({ status: "error", message: "Route not yet implemented" });
};
const deleteUser = (req, res) => {
  res.status(500).json({ status: "error", message: "Route not yet implemented" });
};

app.route("/api/v1/users").get(getAllUsers).post(createUser);
app.route("/api/v1/users/:id").get(getUser).patch(updateUser).delete(deleteUser);
```

---

## 62. Creating and Mounting Multiple Routers

**What is this:** Splitting routes into separate routers per resource and mounting them on the app.

**Description:** Instead of defining all routes on `app`, you create a separate `express.Router()` for each resource. The router is then mounted on the app at a base path with `app.use()`. This keeps route files clean and focused.

**Examples:**

```js
const tourRouter = express.Router();
const userRouter = express.Router();

// Tours router
tourRouter.route("/").get(getAllTours).post(createTour);
tourRouter.route("/:id").get(getTour).patch(updateTour).delete(deleteTour);

// Users router
userRouter.route("/").get(getAllUsers).post(createUser);
userRouter.route("/:id").get(getUser).patch(updateUser).delete(deleteUser);

// Mount routers — base path is prepended to all routes in the router
app.use("/api/v1/tours", tourRouter);
app.use("/api/v1/users", userRouter);
```

---

## 63. A Better File Structure

**What is this:** Splitting a single `app.js` file into multiple files — routers, controllers, and the server entry point.

**Description:** As the app grows, everything in one file becomes unmanageable. The conventional Express project structure separates concerns into distinct files and folders.

**Recommended structure:**
```
natours/
├── app.js              # Express app setup, middleware, routers mounted
├── server.js           # server.listen() — entry point only
├── routes/
│   ├── tourRoutes.js   # tourRouter definition
│   └── userRoutes.js   # userRouter definition
└── controllers/
    ├── tourController.js  # getAllTours, getTour, createTour, etc.
    └── userController.js  # getAllUsers, getUser, etc.
```

```js
// controllers/tourController.js
exports.getAllTours = (req, res) => { /* ... */ };
exports.getTour = (req, res) => { /* ... */ };
exports.createTour = (req, res) => { /* ... */ };

// routes/tourRoutes.js
const express = require("express");
const tourController = require("../controllers/tourController");
const router = express.Router();

router.route("/").get(tourController.getAllTours).post(tourController.createTour);
router.route("/:id").get(tourController.getTour).patch(tourController.updateTour).delete(tourController.deleteTour);

module.exports = router;

// app.js
const tourRouter = require("./routes/tourRoutes");
const userRouter = require("./routes/userRoutes");
app.use("/api/v1/tours", tourRouter);
app.use("/api/v1/users", userRouter);

// server.js
const app = require("./app");
app.listen(3000, () => console.log("Server running..."));
```

---

## 64. Param Middleware

**What is this:** Middleware that runs only when a specific URL parameter is present in the route.

**Description:** `router.param('paramName', handler)` lets you define middleware that only executes when a particular named parameter exists in the URL. Useful for validation that should happen before any route that uses that param (e.g. checking if a tour id is valid).

**Examples:**

```js
// routes/tourRoutes.js
router.param("id", (req, res, next, val) => {
  const id = Number(val);
  const tour = tours.find((t) => t.id === id);

  if (!tour) {
    return res.status(404).json({ status: "fail", message: "Tour not found" });
  }

  req.tour = tour; // attach to req so handlers don't have to look it up again
  next();
});

// Now getTour, updateTour, deleteTour can all skip the id check
const getTour = (req, res) => {
  res.status(200).json({ status: "success", data: { tour: req.tour } });
};
```

---

## 65. Chaining Multiple Middleware Functions

**What is this:** Passing multiple middleware functions to a single route to run them in sequence before the final handler.

**Description:** Route handlers in Express accept multiple functions separated by commas (or as an array). They run in order, each calling `next()` to pass control along. This is useful for things like request validation before the main handler runs.

**Examples:**

```js
// Validation middleware
const checkBody = (req, res, next) => {
  if (!req.body.name || !req.body.price) {
    return res.status(400).json({
      status: "fail",
      message: "Missing name or price",
    });
  }
  next();
};

const checkId = (req, res, next) => {
  if (Number(req.params.id) > tours.length) {
    return res.status(404).json({ status: "fail", message: "Invalid id" });
  }
  next();
};

// Chain middleware on a route
router.route("/").post(checkBody, createTour);
router.route("/:id").get(checkId, getTour);
```

---

## 66. Serving Static Files

**What is this:** Using the built-in `express.static()` middleware to serve files like HTML, images, and CSS directly from a folder.

**Description:** `express.static('public')` tells Express to serve any file in the `public` folder as if it were at the root URL. No route handler needed — Express finds and sends the file automatically.

**Examples:**

```js
// Serve everything in the 'public' folder as static files
app.use(express.static(`${__dirname}/public`));

// Now a file at public/overview.html is accessible at:
// http://localhost:3000/overview.html

// A file at public/img/logo.png is at:
// http://localhost:3000/img/logo.png
```

---

## 67. Environment Variables

**What is this:** Using environment variables to configure the app differently for development and production.

**Description:** Environment variables are external values injected into the process. They keep secrets (like database passwords) out of code and allow different config per environment. Node.js exposes them via `process.env`. Express sets `NODE_ENV` automatically.

**Examples:**

```bash
# Set environment variable inline (Linux/Mac)
NODE_ENV=development node server.js

# Or use a .env file with the dotenv package
npm install dotenv
```

```
# config.env
NODE_ENV=development
PORT=3000
DATABASE_PASSWORD=mypassword
```

```js
// server.js — load .env before anything else
const dotenv = require("dotenv");
dotenv.config({ path: "./config.env" });

const app = require("./app");

const port = process.env.PORT || 3000;
app.listen(port, () => {
  console.log(`App running in ${process.env.NODE_ENV} mode on port ${port}`);
});
```

```js
// app.js — use NODE_ENV to conditionally load middleware
const morgan = require("morgan");

if (process.env.NODE_ENV === "development") {
  app.use(morgan("dev")); // only log in development
}
```

---

## 68. Setting up ESLint + Prettier in VS Code

**What is this:** Configuring ESLint for code quality linting and Prettier for formatting, with both working together in VS Code.

**Description:** ESLint catches code errors and enforces style rules. Prettier handles formatting. They can conflict, so you install `eslint-config-prettier` to disable ESLint rules that Prettier already handles.

**Examples:**

```bash
npm install eslint prettier eslint-config-prettier eslint-plugin-node --save-dev
```

```json
// .eslintrc.json
{
  "extends": ["eslint:recommended", "plugin:node/recommended", "prettier"],
  "plugins": ["node"],
  "parserOptions": {
    "ecmaVersion": 2020
  },
  "rules": {
    "no-unused-vars": "warn",
    "no-console": "off",
    "node/no-unsupported-features/es-syntax": "off"
  }
}
```

```json
// .prettierrc
{
  "singleQuote": true,
  "trailingComma": "all"
}
```

```json
// VS Code settings.json — auto-fix on save
{
  "editor.formatOnSave": true,
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
  }
}
```
