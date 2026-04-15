# Node.js + Express.js

# Section 2: Introduction to Node.js and NPM

---

## 4. Section Intro

**What is this:** A quick overview of what will be covered in this section of the course

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

console.log(os.platform()); // e.g. 'win32'
console.log(os.homedir()); // e.g. 'C:\Users\Username'
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
    <div class="cards-container">{%PRODUCT_CARDS%}</div>
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

const tempOverview = fs.readFileSync(
  `${__dirname}/templates/template-overview.html`,
  "utf-8",
);
const tempCard = fs.readFileSync(
  `${__dirname}/templates/template-card.html`,
  "utf-8",
);
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
| ------- | -------------- | ----------------- | --------------------- |
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

| Component       | Language | Role                                                                    |
| --------------- | -------- | ----------------------------------------------------------------------- |
| **V8**          | C++      | Compiles and executes JavaScript                                        |
| **libuv**       | C        | Provides the event loop and async I/O (file system, networking, timers) |
| **http-parser** | C        | Parses HTTP requests and responses                                      |
| **c-ares**      | C        | Async DNS resolution                                                    |
| **OpenSSL**     | C++      | Cryptography (TLS/SSL)                                                  |
| **zlib**        | C        | Compression                                                             |

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

| Type          | Description                              | Example                                 |
| ------------- | ---------------------------------------- | --------------------------------------- |
| **Readable**  | Source you can read from                 | `fs.createReadStream`, `http` request   |
| **Writable**  | Destination you can write to             | `fs.createWriteStream`, `http` response |
| **Duplex**    | Both readable and writable               | `net.Socket` (TCP socket)               |
| **Transform** | Duplex that transforms data as it passes | `zlib.createGzip` (compression)         |

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
   (function (exports, require, module, __filename, __dirname) {
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
  add(a, b) {
    return a + b;
  }
  multiply(a, b) {
    return a * b;
  }
}

module.exports = Calculator;
```

```js
// index.js
const { add, multiply } = require("./math");
console.log(add(2, 3)); // 5
console.log(multiply(2, 3)); // 6

const Calculator = require("./calculator");
const calc = new Calculator();
console.log(calc.add(2, 3)); // 5
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
      writeFile("./txt/final.txt", `${data2}\n${data3}`, "utf-8"),
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
      if (err)
        reject(err); // triggers .catch()
      else resolve(data); // triggers .then()
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
    fs.readFile(file, "utf-8", (err, data) =>
      err ? reject(err) : resolve(data),
    );
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
    fs.readFile(file, "utf-8", (err, data) =>
      err ? reject(err) : resolve(data),
    );
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
    fs.readFile(file, "utf-8", (err, data) =>
      err ? reject(err) : resolve(data),
    );
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

| Action        | Method | URL                 |
| ------------- | ------ | ------------------- |
| Get all tours | GET    | `/api/v1/tours`     |
| Get one tour  | GET    | `/api/v1/tours/:id` |
| Create a tour | POST   | `/api/v1/tours`     |
| Update a tour | PATCH  | `/api/v1/tours/:id` |
| Delete a tour | DELETE | `/api/v1/tours/:id` |

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
  fs.readFileSync(`${__dirname}/dev-data/data/tours-simple.json`),
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
    },
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
  res
    .status(200)
    .json({ status: "success", results: tours.length, data: { tours } });
};

const getTour = (req, res) => {
  const id = Number(req.params.id);
  const tour = tours.find((t) => t.id === id);
  if (!tour)
    return res.status(404).json({ status: "fail", message: "Tour not found" });
  res.status(200).json({ status: "success", data: { tour } });
};

const createTour = (req, res) => {
  const newTour = { id: tours[tours.length - 1].id + 1, ...req.body };
  tours.push(newTour);
  fs.writeFile(
    `${__dirname}/dev-data/data/tours-simple.json`,
    JSON.stringify(tours),
    () => {
      res.status(201).json({ status: "success", data: { tour: newTour } });
    },
  );
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
app
  .route("/api/v1/tours/:id")
  .get(getTour)
  .patch(updateTour)
  .delete(deleteTour);
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
};
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
  res
    .status(500)
    .json({ status: "error", message: "Route not yet implemented" });
};
const getUser = (req, res) => {
  res
    .status(500)
    .json({ status: "error", message: "Route not yet implemented" });
};
const createUser = (req, res) => {
  res
    .status(500)
    .json({ status: "error", message: "Route not yet implemented" });
};
const updateUser = (req, res) => {
  res
    .status(500)
    .json({ status: "error", message: "Route not yet implemented" });
};
const deleteUser = (req, res) => {
  res
    .status(500)
    .json({ status: "error", message: "Route not yet implemented" });
};

app.route("/api/v1/users").get(getAllUsers).post(createUser);
app
  .route("/api/v1/users/:id")
  .get(getUser)
  .patch(updateUser)
  .delete(deleteUser);
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
exports.getAllTours = (req, res) => {
  /* ... */
};
exports.getTour = (req, res) => {
  /* ... */
};
exports.createTour = (req, res) => {
  /* ... */
};

// routes/tourRoutes.js
const express = require("express");
const tourController = require("../controllers/tourController");
const router = express.Router();

router
  .route("/")
  .get(tourController.getAllTours)
  .post(tourController.createTour);
router
  .route("/:id")
  .get(tourController.getTour)
  .patch(tourController.updateTour)
  .delete(tourController.deleteTour);

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

---

# Section 7: Introduction to MongoDB

---

## 69. Section Intro

**What is this:** Overview of the section — what MongoDB is, how to set it up, and how to perform basic CRUD operations.

**Description:** This section introduces MongoDB as the database for the Natours API. It covers the core concepts of document databases, how to install and connect to MongoDB, and how to create, read, update, and delete documents using the MongoDB shell and Compass GUI.

---

## 70. What is MongoDB?

**What is this:** An introduction to MongoDB — what kind of database it is and how it differs from relational (SQL) databases.

**Description:** MongoDB is a NoSQL document database. Instead of storing data in tables with rows and columns, it stores data as JSON-like documents grouped into collections. This makes it flexible and natural to work with from JavaScript.

**Key concepts:**

| SQL term | MongoDB equivalent             |
| -------- | ------------------------------ |
| Database | Database                       |
| Table    | Collection                     |
| Row      | Document                       |
| Column   | Field                          |
| JOIN     | Embedded documents / `$lookup` |

**Document example:**

```json
{
  "_id": "5c88fa8cf4afda39709c2955",
  "name": "The Forest Hiker",
  "duration": 5,
  "difficulty": "easy",
  "price": 397,
  "guides": ["5c8a1d5b0190b214360dc057"]
}
```

**Why MongoDB with Node.js:**

- Documents are stored as BSON (Binary JSON) — maps naturally to JS objects
- Flexible schema — fields can vary between documents
- Scales well horizontally
- Official `mongoose` driver integrates tightly with Node.js

---

## 71. No Need to Install MongoDB Locally

**What is this:** A note that you can use MongoDB Atlas (cloud) instead of installing MongoDB locally.

**Description:** MongoDB Atlas is a fully managed cloud database service. For learning and development, it removes the need to install and manage a local MongoDB instance. The free tier (M0) is sufficient for this course.

---

## 72. [OPTIONAL] Installing MongoDB on macOS

**What is this:** Steps to install MongoDB Community Edition locally on macOS using Homebrew.

**Description:** If you prefer a local installation on macOS, use Homebrew to install the `mongodb-community` formula from the MongoDB tap.

**Examples:**

```bash
# Add the MongoDB tap
brew tap mongodb/brew

# Install MongoDB Community Edition
brew install mongodb-community

# Start MongoDB as a background service
brew services start mongodb-community

# Verify it's running
mongosh
```

---

## 73. [OPTIONAL] Installing MongoDB on Windows

**What is this:** Steps to install MongoDB Community Edition locally on Windows using the MSI installer.

**Description:** On Windows, download the MongoDB MSI installer from the official site. Install it as a Windows Service so it starts automatically. Then add the MongoDB bin folder to your PATH so you can use `mongosh` from any terminal.

**Steps:**

1. Download MongoDB Community Server MSI from mongodb.com
2. Run the installer — choose "Complete" setup and "Install as Service"
3. Add `C:\Program Files\MongoDB\Server\<version>\bin` to your system PATH
4. Open a new terminal and verify:

```bash
mongosh
# Should connect to: mongodb://127.0.0.1:27017
```

---

## 74. [OPTIONAL] Creating a Local Database

**What is this:** Using the MongoDB shell (`mongosh`) to create a database and collection.

**Description:** In MongoDB, databases and collections are created implicitly the first time you insert data. You switch to a database with `use`, then insert a document to create both the database and the collection in one step.

**Examples:**

```js
// In mongosh shell

// Switch to (or create) a database
use natours-test

// Show current database
db

// Insert a document — creates the "tours" collection automatically
db.tours.insertOne({ name: "The Forest Hiker", price: 297, rating: 4.7 })

// List all collections
show collections

// List all databases
show dbs
```

---

## 75. [OPTIONAL] CRUD: Creating Documents

**What is this:** Inserting one or many documents into a MongoDB collection from the shell.

**Description:** Use `insertOne` to add a single document or `insertMany` to add multiple at once. MongoDB automatically adds an `_id` field (a unique ObjectId) to every document.

**Examples:**

```js
// Insert one document
db.tours.insertOne({
  name: "The Sea Explorer",
  duration: 7,
  price: 497,
});

// Insert many documents at once
db.tours.insertMany([
  { name: "The Snow Adventurer", duration: 4, price: 997 },
  { name: "The City Wanderer", duration: 3, price: 197 },
]);
```

---

## 76. [OPTIONAL] CRUD: Querying (Reading) Documents

**What is this:** Reading documents from a collection using `find` with filter and projection operators.

**Description:** `find()` returns all matching documents. You pass a filter object to narrow results. MongoDB has a rich query language with comparison, logical, and element operators.

**Examples:**

```js
// Get all documents in the collection
db.tours.find();

// Filter by exact value
db.tours.find({ name: "The Forest Hiker" });

// Comparison operators
db.tours.find({ price: { $lte: 500 } });
db.tours.find({ price: { $lt: 500 }, rating: { $gte: 4.8 } }); // AND

// OR operator
db.tours.find({ $or: [{ price: { $lt: 500 } }, { rating: { $gte: 4.8 } }] });

// Projection — only return specific fields (1 = include, 0 = exclude)
db.tours.find({ price: { $lte: 500 } }, { name: 1, price: 1 });

// Find one document
db.tours.findOne({ name: "The Forest Hiker" });
```

---

## 77. [OPTIONAL] CRUD: Updating Documents

**What is this:** Modifying existing documents using `updateOne` or `updateMany` with update operators.

**Description:** Updates use a filter to find the target document(s) and an update object with operators like `$set` (set fields), `$unset` (remove fields), or `$inc` (increment). Without operators, you'd replace the whole document.

**Examples:**

```js
// Update one document — set a new field
db.tours.updateOne({ name: "The Snow Adventurer" }, { $set: { price: 597 } });

// Update many — add a field to all matching documents
db.tours.updateMany(
  { price: { $gt: 500 }, rating: { $gte: 4.8 } },
  { $set: { premium: true } },
);

// Upsert — create the document if it doesn't exist
db.tours.updateOne(
  { name: "The Unknown Trail" },
  { $set: { price: 150, rating: 4.0 } },
  { upsert: true },
);
```

---

## 78. [OPTIONAL] CRUD: Deleting Documents

**What is this:** Removing documents from a collection using `deleteOne` or `deleteMany`.

**Description:** `deleteOne` removes the first matching document; `deleteMany` removes all matches. Use an empty filter `{}` with `deleteMany` to wipe an entire collection.

**Examples:**

```js
// Delete one document
db.tours.deleteOne({ name: "The City Wanderer" });

// Delete all documents matching a condition
db.tours.deleteMany({ rating: { $lt: 4.8 } });

// Delete ALL documents in the collection (careful!)
db.tours.deleteMany({});
```

---

## 79. Using Compass App for CRUD Operations

**What is this:** Using MongoDB Compass — a graphical UI — to browse, query, and edit data visually instead of through the shell.

**Description:** MongoDB Compass is the official GUI for MongoDB. It lets you browse collections, run queries with a visual filter builder, view documents in a tree view, and insert/edit/delete records without writing shell commands.

**Key features:**

- Connect using a connection string (e.g. `mongodb://localhost:27017`)
- Browse databases and collections
- Filter documents with a GUI query builder
- View aggregation pipelines visually
- Inspect indexes and schema analysis

---

## 80. Creating a Hosted Database with Atlas

**What is this:** Setting up a free cloud MongoDB database using MongoDB Atlas.

**Description:** MongoDB Atlas is the official cloud hosting service for MongoDB. The free M0 tier gives you 512MB of storage — enough for learning and small projects. You create a cluster, configure network access, and create a database user.

**Setup steps:**

1. Sign up at mongodb.com/atlas
2. Create a free M0 cluster (choose any region)
3. Under **Security > Database Access** — create a database user (username + password)
4. Under **Security > Network Access** — add your IP (or `0.0.0.0/0` for all IPs during dev)
5. Under **Deployment > Clusters** — click "Connect" to get your connection string

**Connection string format:**

```
mongodb+srv://<username>:<password>@cluster0.xxxxx.mongodb.net/<dbname>?retryWrites=true&w=majority
```

---

## 81. Connecting to Our Hosted Database

**What is this:** Connecting the Node.js app to the Atlas cloud database using the connection string and the `mongoose` package.

**Description:** Store the Atlas connection string in `config.env` (never hardcode it). Use `mongoose.connect()` to establish the connection. Mongoose wraps the native MongoDB driver and adds schema validation, query helpers, and middleware.

**Examples:**

```bash
npm install mongoose
```

```
# config.env
DATABASE=mongodb+srv://username:<PASSWORD>@cluster0.xxxxx.mongodb.net/natours?retryWrites=true&w=majority
DATABASE_PASSWORD=yourpassword
```

```js
// server.js
const mongoose = require("mongoose");
const dotenv = require("dotenv");
dotenv.config({ path: "./config.env" });

// Replace placeholder with actual password from env
const DB = process.env.DATABASE.replace(
  "<PASSWORD>",
  process.env.DATABASE_PASSWORD,
);

mongoose
  .connect(DB, {
    useNewUrlParser: true,
    useUnifiedTopology: true,
  })
  .then(() => console.log("DB connection successful!"));

const app = require("./app");
const port = process.env.PORT || 3000;
app.listen(port, () => console.log(`App running on port ${port}...`));
```

---

# Section 8: Using MongoDB with Mongoose

---

## 82. Section Intro

**What is this:** Overview of this section — using Mongoose to model data, perform CRUD, and build advanced API features on top of MongoDB.

**Description:** This section covers everything from connecting Mongoose to the app, defining schemas and models, performing all CRUD operations, building filtering/sorting/pagination, using aggregation pipelines, and adding Mongoose middleware and validation.

---

## 83. Connecting Our Database with the Express App

**What is this:** Wiring the Mongoose connection into the existing Express app so the server connects to MongoDB on startup.

**Description:** The connection is established once in `server.js` before the server starts listening. The connection string is pulled from environment variables to keep credentials out of the code.

**Examples:**

```js
// server.js
const mongoose = require("mongoose");
const dotenv = require("dotenv");
dotenv.config({ path: "./config.env" });

const DB = process.env.DATABASE.replace(
  "<PASSWORD>",
  process.env.DATABASE_PASSWORD,
);

mongoose
  .connect(DB, {
    useNewUrlParser: true,
    useUnifiedTopology: true,
  })
  .then(() => console.log("DB connection successful!"));

const app = require("./app");
app.listen(process.env.PORT || 3000, () => console.log("Server running..."));
```

---

## 84. What Is Mongoose?

**What is this:** An explanation of what Mongoose is and what it adds on top of the native MongoDB driver.

**Description:** Mongoose is an ODM (Object Data Modeling) library for MongoDB and Node.js. It lets you define schemas for your data, add validation, create model methods, and use middleware — all things the raw MongoDB driver doesn't provide.

**What Mongoose provides:**

- **Schemas** — define the shape and types of your documents
- **Models** — JS classes built from schemas, used to interact with a collection
- **Validation** — built-in and custom validators run before saving
- **Middleware (hooks)** — run logic before/after save, find, update, etc.
- **Query helpers** — chainable methods like `.sort()`, `.select()`, `.limit()`
- **Virtual properties** — computed fields not stored in the DB

---

## 85. Creating a Simple Tour Model

**What is this:** Defining a Mongoose schema and model for the Tour resource.

**Description:** A schema defines the fields, their types, and constraints. A model is created from the schema with `mongoose.model()` and represents the collection in MongoDB.

**Examples:**

```js
// models/tourModel.js
const mongoose = require("mongoose");

const tourSchema = new mongoose.Schema({
  name: {
    type: String,
    required: [true, "A tour must have a name"],
    unique: true,
    trim: true,
  },
  duration: {
    type: Number,
    required: [true, "A tour must have a duration"],
  },
  maxGroupSize: {
    type: Number,
    required: [true, "A tour must have a group size"],
  },
  difficulty: {
    type: String,
    required: [true, "A tour must have a difficulty"],
  },
  price: {
    type: Number,
    required: [true, "A tour must have a price"],
  },
  summary: {
    type: String,
    trim: true,
    required: [true, "A tour must have a summary"],
  },
  createdAt: {
    type: Date,
    default: Date.now(),
    select: false, // never send this field to the client
  },
});

// Model name "Tour" → collection name "tours" (lowercased + pluralized)
const Tour = mongoose.model("Tour", tourSchema);

module.exports = Tour;
```

---

## 86. Creating Documents and Testing the Model

**What is this:** Using the Tour model to create and save a document to MongoDB, and testing it directly in `server.js`.

**Description:** You create a new instance of the model with `new Tour({...})` and call `.save()` to persist it. `.save()` returns a Promise with the saved document. This is a quick way to test the model before wiring it to the API.

**Examples:**

```js
// Quick test in server.js (remove after testing)
const Tour = require("./models/tourModel");

const testTour = new Tour({
  name: "The Park Camper",
  price: 997,
});

testTour
  .save()
  .then((doc) => console.log(doc))
  .catch((err) => console.log("ERROR:", err));
```

---

## 87. Intro to Back-End Architecture: MVC, Types of Logic, and More

**What is this:** An overview of the MVC (Model-View-Controller) pattern and how it applies to an Express API.

**Description:** MVC separates an application into three layers. In a REST API (no views), you have Models and Controllers. Each layer has a clear responsibility, making the code easier to maintain and test.

**MVC in an Express API:**

| Layer          | File location     | Responsibility                             |
| -------------- | ----------------- | ------------------------------------------ |
| **Model**      | `models/`         | Data, business logic, DB interaction       |
| **View**       | (not used in API) | Templates / UI (irrelevant for pure APIs)  |
| **Controller** | `controllers/`    | Receive request, call model, send response |
| **Router**     | `routes/`         | Map URLs to controllers                    |

**Types of logic:**

- **Business logic** — rules about the data (e.g. a tour can't have more than 15 guests) → belongs in the Model
- **Application logic** — request/response handling, routing → belongs in the Controller
- Keep controllers thin — don't put business rules there

---

## 88. Refactoring for MVC

**What is this:** Moving logic out of route files and into the proper Model/Controller layers following the MVC pattern.

**Description:** After this refactor the project has a clean separation: models own the data layer, controllers handle HTTP logic, routes just map paths to controller functions.

**Final structure:**

```
natours/
├── server.js              # DB connect + app.listen
├── app.js                 # Express setup, middleware, mount routers
├── config.env
├── models/
│   └── tourModel.js       # Schema + Model
├── controllers/
│   ├── tourController.js  # getAllTours, getTour, createTour, updateTour, deleteTour
│   └── userController.js
└── routes/
    ├── tourRoutes.js      # express.Router() wired to tourController
    └── userRoutes.js
```

---

## 89. Another Way of Creating Documents

**What is this:** Using `Model.create()` as a cleaner alternative to `new Model().save()` for creating documents.

**Description:** `Tour.create(data)` is a shorthand that creates a new instance and saves it in one step. It returns a Promise with the saved document. This is the standard pattern used in controllers.

**Examples:**

```js
// controllers/tourController.js
const Tour = require("../models/tourModel");

exports.createTour = async (req, res) => {
  try {
    const newTour = await Tour.create(req.body);
    // Tour.create() applies schema validation before saving

    res.status(201).json({
      status: "success",
      data: { tour: newTour },
    });
  } catch (err) {
    res.status(400).json({
      status: "fail",
      message: err.message,
    });
  }
};
```

---

## 90. Reading Documents

**What is this:** Using `Model.find()` and `Model.findById()` to query documents from MongoDB.

**Description:** Mongoose queries return a Query object that you can chain methods on. `find()` returns all matching documents; `findById()` finds a single document by its `_id`.

**Examples:**

```js
// GET all tours
exports.getAllTours = async (req, res) => {
  try {
    const tours = await Tour.find();
    res.status(200).json({
      status: "success",
      results: tours.length,
      data: { tours },
    });
  } catch (err) {
    res.status(404).json({ status: "fail", message: err.message });
  }
};

// GET one tour by id
exports.getTour = async (req, res) => {
  try {
    const tour = await Tour.findById(req.params.id);
    // Tour.findById(id) is shorthand for Tour.findOne({ _id: id })

    if (!tour) {
      return res
        .status(404)
        .json({ status: "fail", message: "Tour not found" });
    }

    res.status(200).json({ status: "success", data: { tour } });
  } catch (err) {
    res.status(404).json({ status: "fail", message: err.message });
  }
};
```

---

## 91. Updating Documents

**What is this:** Using `Model.findByIdAndUpdate()` to find and update a document in one operation.

**Description:** `findByIdAndUpdate(id, update, options)` finds the document by id, applies the update, and returns the result. The `new: true` option returns the updated document instead of the original. `runValidators: true` re-runs schema validators on the update.

**Examples:**

```js
exports.updateTour = async (req, res) => {
  try {
    const tour = await Tour.findByIdAndUpdate(req.params.id, req.body, {
      new: true, // return the updated document
      runValidators: true, // run schema validators on the new values
    });

    if (!tour) {
      return res
        .status(404)
        .json({ status: "fail", message: "Tour not found" });
    }

    res.status(200).json({ status: "success", data: { tour } });
  } catch (err) {
    res.status(400).json({ status: "fail", message: err.message });
  }
};
```

---

## 92. Deleting Documents

**What is this:** Using `Model.findByIdAndDelete()` to remove a document from MongoDB.

**Description:** `findByIdAndDelete(id)` finds the document and removes it atomically. A successful delete returns `204 No Content` with no body — the standard HTTP convention.

**Examples:**

```js
exports.deleteTour = async (req, res) => {
  try {
    const tour = await Tour.findByIdAndDelete(req.params.id);

    if (!tour) {
      return res
        .status(404)
        .json({ status: "fail", message: "Tour not found" });
    }

    res.status(204).json({ status: "success", data: null });
  } catch (err) {
    res.status(400).json({ status: "fail", message: err.message });
  }
};
```

---

## 93. Modelling the Tours

**What is this:** Expanding the Tour schema with all real fields, proper types, constraints, and nested objects.

**Description:** A production schema defines every field precisely — types, required flags, default values, min/max constraints, enums, and nested objects. This is the complete Tour model used throughout the rest of the course.

**Examples:**

```js
const tourSchema = new mongoose.Schema(
  {
    name: {
      type: String,
      required: [true, "A tour must have a name"],
      unique: true,
      trim: true,
      maxlength: 40,
      minlength: 10,
    },
    slug: String,
    duration: { type: Number, required: [true, "A tour must have a duration"] },
    maxGroupSize: {
      type: Number,
      required: [true, "A tour must have a group size"],
    },
    difficulty: {
      type: String,
      required: [true, "A tour must have a difficulty"],
      enum: {
        values: ["easy", "medium", "difficult"],
        message: "Difficulty must be easy, medium, or difficult",
      },
    },
    ratingsAverage: {
      type: Number,
      default: 4.5,
      min: [1, "Rating must be above 1.0"],
      max: [5, "Rating must be below 5.0"],
    },
    ratingsQuantity: { type: Number, default: 0 },
    price: { type: Number, required: [true, "A tour must have a price"] },
    priceDiscount: Number,
    summary: {
      type: String,
      trim: true,
      required: [true, "A tour must have a summary"],
    },
    description: { type: String, trim: true },
    imageCover: {
      type: String,
      required: [true, "A tour must have a cover image"],
    },
    images: [String],
    createdAt: { type: Date, default: Date.now(), select: false },
    startDates: [Date],
  },
  {
    toJSON: { virtuals: true }, // include virtual properties in JSON output
    toObject: { virtuals: true },
  },
);

const Tour = mongoose.model("Tour", tourSchema);
module.exports = Tour;
```

---

## 94. Importing Development Data

**What is this:** Writing a one-off script to import JSON data into the database or delete all existing data.

**Description:** Instead of manually inserting documents, you write a script that reads a JSON file and calls `Model.create()` to bulk-import it. The same script can delete all documents. Run it from the terminal with a flag.

**Examples:**

```js
// dev-data/import-dev-data.js
const fs = require("fs");
const mongoose = require("mongoose");
const dotenv = require("dotenv");
dotenv.config({ path: "./config.env" });

const Tour = require("../../models/tourModel");

const DB = process.env.DATABASE.replace(
  "<PASSWORD>",
  process.env.DATABASE_PASSWORD,
);
mongoose.connect(DB, { useNewUrlParser: true, useUnifiedTopology: true });

const tours = JSON.parse(fs.readFileSync(`${__dirname}/tours.json`, "utf-8"));

const importData = async () => {
  try {
    await Tour.create(tours);
    console.log("Data successfully loaded!");
  } catch (err) {
    console.log(err);
  }
  process.exit();
};

const deleteData = async () => {
  try {
    await Tour.deleteMany();
    console.log("Data successfully deleted!");
  } catch (err) {
    console.log(err);
  }
  process.exit();
};

if (process.argv[2] === "--import") importData();
if (process.argv[2] === "--delete") deleteData();
```

```bash
node dev-data/import-dev-data.js --delete
node dev-data/import-dev-data.js --import
```

---

## 95. Making the API Better: Filtering

**What is this:** Allowing clients to filter results by passing query string parameters (e.g. `?difficulty=easy&price[lte]=1500`).

**Description:** `req.query` contains all query string params as an object. You pass it directly to `Tour.find()` after removing reserved keywords (`page`, `sort`, `limit`, `fields`) that are handled separately.

**Examples:**

```js
exports.getAllTours = async (req, res) => {
  try {
    // 1. Remove reserved query keys
    const queryObj = { ...req.query };
    const excludedFields = ["page", "sort", "limit", "fields"];
    excludedFields.forEach((el) => delete queryObj[el]);

    // 2. Replace gt/gte/lt/lte with $gt/$gte/$lt/$lte (MongoDB operators)
    let queryStr = JSON.stringify(queryObj);
    queryStr = queryStr.replace(/\b(gte|gt|lte|lt)\b/g, (match) => `$${match}`);

    // 3. Execute query
    const tours = await Tour.find(JSON.parse(queryStr));

    res
      .status(200)
      .json({ status: "success", results: tours.length, data: { tours } });
  } catch (err) {
    res.status(404).json({ status: "fail", message: err.message });
  }
};

// Usage: GET /api/v1/tours?difficulty=easy&price[lte]=1500
```

---

## 96. Making the API Better: Advanced Filtering

**What is this:** Supporting MongoDB comparison operators in the query string using bracket notation.

**Description:** When a client sends `?price[lte]=1500`, Express parses it as `{ price: { lte: '1500' } }`. MongoDB expects `$lte`, not `lte`. A regex replacement on the JSON string adds the `$` prefix to all known operators.

**Examples:**

```js
// The regex replacement from lesson 95 handles this:
queryStr = queryStr.replace(/\b(gte|gt|lte|lt)\b/g, (match) => `$${match}`);

// Client sends:  GET /api/v1/tours?price[gte]=500&ratingsAverage[gte]=4.5
// req.query →   { price: { gte: '500' }, ratingsAverage: { gte: '4.5' } }
// After replace: { price: { $gte: '500' }, ratingsAverage: { $gte: '4.5' } }
// Passed to:     Tour.find({ price: { $gte: 500 }, ratingsAverage: { $gte: 4.5 } })
```

---

## 97. Making the API Better: Sorting

**What is this:** Allowing clients to sort results by any field using `?sort=price` or `?sort=-price` (descending).

**Description:** Mongoose's `.sort()` method takes a space-separated string of field names. A `-` prefix means descending. The client sends a comma-separated list which we convert to space-separated for Mongoose.

**Examples:**

```js
let query = Tour.find(JSON.parse(queryStr));

// Sort
if (req.query.sort) {
  const sortBy = req.query.sort.split(",").join(" ");
  // "price,-ratingsAverage" → "price -ratingsAverage"
  query = query.sort(sortBy);
} else {
  query = query.sort("-createdAt"); // default: newest first
}

const tours = await query;

// Usage:
// GET /api/v1/tours?sort=price           → cheapest first
// GET /api/v1/tours?sort=-price          → most expensive first
// GET /api/v1/tours?sort=price,-ratingsAverage → price asc, rating desc
```

---

## 98. Making the API Better: Limiting Fields

**What is this:** Allowing clients to request only specific fields in the response using `?fields=name,price,difficulty`.

**Description:** Mongoose's `.select()` method takes a space-separated list of fields to include or exclude. This reduces the response payload — clients only get what they need.

**Examples:**

```js
// Field limiting (projection)
if (req.query.fields) {
  const fields = req.query.fields.split(",").join(" ");
  // "name,price,difficulty" → "name price difficulty"
  query = query.select(fields);
} else {
  query = query.select("-__v"); // exclude __v (Mongoose internal) by default
}

// Usage:
// GET /api/v1/tours?fields=name,price,difficulty
// Response only contains: name, price, difficulty (+ _id)
```

---

## 99. Making the API Better: Pagination

**What is this:** Splitting results across pages using `?page=2&limit=10`.

**Description:** Pagination uses Mongoose's `.skip()` and `.limit()` methods. `skip` calculates how many documents to skip based on the current page; `limit` caps how many are returned. If the requested page is beyond available data, throw an error.

**Examples:**

```js
// Pagination
const page = Number(req.query.page) || 1;
const limit = Number(req.query.limit) || 100;
const skip = (page - 1) * limit;

query = query.skip(skip).limit(limit);

// Throw error if page doesn't exist
if (req.query.page) {
  const numTours = await Tour.countDocuments();
  if (skip >= numTours) throw new Error("This page does not exist");
}

// Usage:
// GET /api/v1/tours?page=2&limit=10
// → skips 10 documents, returns the next 10
```

---

## 100. Making the API Better: Aliasing

**What is this:** Creating a pre-set route that automatically applies common query parameters, like a "top 5 cheap tours" shortcut.

**Description:** An alias middleware sets `req.query` fields before the main handler runs. This lets you expose a friendly URL like `/top-5-cheap` that internally applies sort, limit, and field selection.

**Examples:**

```js
// controllers/tourController.js
exports.aliasTopTours = (req, res, next) => {
  req.query.limit = "5";
  req.query.sort = "-ratingsAverage,price";
  req.query.fields = "name,price,ratingsAverage,summary,difficulty";
  next();
};

// routes/tourRoutes.js
router
  .route("/top-5-cheap")
  .get(tourController.aliasTopTours, tourController.getAllTours);

// Usage: GET /api/v1/tours/top-5-cheap
```

---

## 101. Refactoring API Features

**What is this:** Extracting filtering, sorting, field limiting, and pagination into a reusable `APIFeatures` class.

**Description:** All four query features are moved into a class that wraps the Mongoose query. Each feature is a method that modifies `this.query` and returns `this` for chaining. The controller calls it cleanly without repeating logic.

**Examples:**

```js
// utils/apiFeatures.js
class APIFeatures {
  constructor(query, queryString) {
    this.query = query;
    this.queryString = queryString;
  }

  filter() {
    const queryObj = { ...this.queryString };
    ["page", "sort", "limit", "fields"].forEach((el) => delete queryObj[el]);
    let queryStr = JSON.stringify(queryObj).replace(
      /\b(gte|gt|lte|lt)\b/g,
      (m) => `$${m}`,
    );
    this.query = this.query.find(JSON.parse(queryStr));
    return this;
  }

  sort() {
    this.query = this.query.sort(
      this.queryString.sort
        ? this.queryString.sort.split(",").join(" ")
        : "-createdAt",
    );
    return this;
  }

  limitFields() {
    this.query = this.query.select(
      this.queryString.fields
        ? this.queryString.fields.split(",").join(" ")
        : "-__v",
    );
    return this;
  }

  paginate() {
    const page = Number(this.queryString.page) || 1;
    const limit = Number(this.queryString.limit) || 100;
    this.query = this.query.skip((page - 1) * limit).limit(limit);
    return this;
  }
}

module.exports = APIFeatures;
```

```js
// controllers/tourController.js
const APIFeatures = require("../utils/apiFeatures");

exports.getAllTours = async (req, res) => {
  try {
    const features = new APIFeatures(Tour.find(), req.query)
      .filter()
      .sort()
      .limitFields()
      .paginate();

    const tours = await features.query;
    res
      .status(200)
      .json({ status: "success", results: tours.length, data: { tours } });
  } catch (err) {
    res.status(404).json({ status: "fail", message: err.message });
  }
};
```

---

## 102. Aggregation Pipeline: Matching and Grouping

**What is this:** Using MongoDB's aggregation pipeline to compute statistics across documents — like average ratings and prices grouped by difficulty.

**Description:** The aggregation pipeline transforms documents through a series of stages. `$match` filters documents; `$group` groups them and computes aggregate values. The result is a new set of computed documents, not the original records.

**Examples:**

```js
exports.getTourStats = async (req, res) => {
  try {
    const stats = await Tour.aggregate([
      { $match: { ratingsAverage: { $gte: 4.5 } } }, // filter first
      {
        $group: {
          _id: { $toUpper: "$difficulty" }, // group by difficulty
          numTours: { $sum: 1 },
          numRatings: { $sum: "$ratingsQuantity" },
          avgRating: { $avg: "$ratingsAverage" },
          avgPrice: { $avg: "$price" },
          minPrice: { $min: "$price" },
          maxPrice: { $max: "$price" },
        },
      },
      { $sort: { avgPrice: 1 } }, // sort result by avgPrice ascending
    ]);

    res.status(200).json({ status: "success", data: { stats } });
  } catch (err) {
    res.status(404).json({ status: "fail", message: err.message });
  }
};
```

---

## 103. Aggregation Pipeline: Unwinding and Projecting

**What is this:** Using `$unwind` to deconstruct array fields and `$project` to rename/reshape output fields.

**Description:** `$unwind` creates one document per array element — useful for arrays like `startDates`. Combined with `$group` and `$project`, you can build reports like "how many tours start each month of a given year."

**Examples:**

```js
exports.getMonthlyPlan = async (req, res) => {
  try {
    const year = Number(req.params.year);

    const plan = await Tour.aggregate([
      { $unwind: "$startDates" }, // one doc per start date
      {
        $match: {
          startDates: {
            $gte: new Date(`${year}-01-01`),
            $lte: new Date(`${year}-12-31`),
          },
        },
      },
      {
        $group: {
          _id: { $month: "$startDates" }, // group by month number
          numTourStarts: { $sum: 1 },
          tours: { $push: "$name" }, // collect tour names into array
        },
      },
      { $addFields: { month: "$_id" } }, // rename _id to month
      { $project: { _id: 0 } }, // hide _id field
      { $sort: { numTourStarts: -1 } }, // busiest months first
      { $limit: 12 },
    ]);

    res
      .status(200)
      .json({ status: "success", results: plan.length, data: { plan } });
  } catch (err) {
    res.status(404).json({ status: "fail", message: err.message });
  }
};
```

---

## 104. Virtual Properties

**What is this:** Computed schema fields that are derived from existing data but not stored in MongoDB.

**Description:** Virtual properties are defined on the schema with `.virtual()`. They are computed on the fly when you access them. Because they're not stored, you can't query by them. They're useful for things like converting units or combining fields.

**Examples:**

```js
// In tourSchema definition — add options object as second argument
const tourSchema = new mongoose.Schema(
  {
    /* fields... */
  },
  {
    toJSON: { virtuals: true }, // include virtuals when converting to JSON
    toObject: { virtuals: true }, // include virtuals when converting to object
  },
);

// Define the virtual property
tourSchema.virtual("durationWeeks").get(function () {
  return this.duration / 7; // duration in weeks, not stored in DB
  // use regular function (not arrow) so 'this' refers to the document
});

// Result — when you GET a tour, the response includes:
// "durationWeeks": 1.43  (if duration is 10 days)
```

---

## 105. Document Middleware

**What is this:** Mongoose middleware that runs before or after a document is saved to the database.

**Description:** Document middleware hooks into the `.save()` and `.create()` lifecycle. `pre('save')` runs before the document is saved — useful for generating slugs, hashing passwords, etc. `post('save')` runs after.

**Examples:**

```js
const slugify = require("slugify");

// pre-save hook — runs before .save() and .create()
tourSchema.pre("save", function (next) {
  this.slug = slugify(this.name, { lower: true });
  // 'this' = the document being saved
  next();
});

// Another pre-save hook — multiple hooks can be chained
tourSchema.pre("save", function (next) {
  console.log("Will save document...");
  next();
});

// post-save hook — runs after the document has been saved
tourSchema.post("save", function (doc, next) {
  console.log(doc); // 'doc' = the finished saved document
  next();
});
```

---

## 106. Query Middleware

**What is this:** Mongoose middleware that runs before or after a query is executed — letting you modify queries globally.

**Description:** Query middleware hooks into find operations. `pre('find')` runs before any `find()` query — useful for hiding secret/inactive documents from all query results without changing every controller.

**Examples:**

```js
// Add a 'secretTour' field to the schema
// secretTour: { type: Boolean, default: false }

// pre-find hook — 'this' = the query object
tourSchema.pre("find", function (next) {
  this.find({ secretTour: { $ne: true } }); // exclude secret tours from all finds
  next();
});

// Use a regex to catch all find variants (find, findOne, findById, etc.)
tourSchema.pre(/^find/, function (next) {
  this.find({ secretTour: { $ne: true } });
  this.start = Date.now(); // attach data to the query for use in post hook
  next();
});

// post-find hook — 'docs' = the returned documents
tourSchema.post(/^find/, function (docs, next) {
  console.log(`Query took ${Date.now() - this.start} ms`);
  next();
});
```

---

## 107. Aggregation Middleware

**What is this:** Mongoose middleware that runs before or after an aggregation pipeline executes.

**Description:** `pre('aggregate')` lets you add stages to every aggregation pipeline automatically. `this.pipeline()` gives you access to the pipeline array so you can prepend a `$match` stage to exclude secret tours from all aggregations.

**Examples:**

```js
// pre-aggregate hook — 'this' = the aggregation object
tourSchema.pre("aggregate", function (next) {
  // Prepend a $match stage to exclude secret tours from all aggregations
  this.pipeline().unshift({ $match: { secretTour: { $ne: true } } });
  next();
});
```

---

## 108. Data Validation: Built-In Validators

**What is this:** Using Mongoose's built-in schema validators to enforce data rules before saving.

**Description:** Mongoose has built-in validators for common constraints. They run automatically on `.save()` and `.create()`, and optionally on updates when `runValidators: true` is set. Failed validation throws a `ValidationError`.

**Built-in validators:**

```js
const tourSchema = new mongoose.Schema({
  name: {
    type: String,
    required: [true, "A tour must have a name"], // required
    maxlength: [40, "Name must be <= 40 characters"], // String only
    minlength: [10, "Name must be >= 10 characters"], // String only
    trim: true,
  },
  ratingsAverage: {
    type: Number,
    min: [1, "Rating must be at or above 1.0"], // Number and Date
    max: [5, "Rating must be at or below 5.0"], // Number and Date
  },
  difficulty: {
    type: String,
    enum: {
      values: ["easy", "medium", "difficult"], // String only
      message: "Difficulty must be: easy, medium, or difficult",
    },
  },
});
```

---

## 109. Data Validation: Custom Validators

**What is this:** Writing your own validator functions for rules that built-in validators can't express.

**Description:** Custom validators are functions assigned to the `validate` property of a field. The function receives the field value and must return `true` (valid) or `false` (invalid). You can also use the `validator` npm package for common patterns.

**Examples:**

```js
const validator = require("validator");

const tourSchema = new mongoose.Schema({
  priceDiscount: {
    type: Number,
    validate: {
      validator: function (val) {
        // 'this' = current document (only works on create, not update)
        return val < this.price; // discount must be less than price
      },
      message: "Discount price ({VALUE}) must be below the regular price",
      // {VALUE} is replaced with the actual value that failed
    },
  },
  name: {
    type: String,
    validate: [validator.isAlpha, "Tour name must only contain letters"],
    // Using the 'validator' npm package for common string checks
  },
});
```

```bash
npm install validator
```

---

# Section 9: Error Handling with Express

---

## 110. Section Intro

**What is this:** Overview of the section — building a robust, centralized error handling system for the Express API.

**Description:** This section moves from ad-hoc `try/catch` error responses scattered across controllers to a single global error handling middleware that handles all errors consistently — in both development and production, for operational errors and programming bugs.

---

## 111. Debugging Node.js with ndb

**What is this:** Using Google's `ndb` debugger to set breakpoints and step through Node.js code instead of relying on `console.log`.

**Description:** `ndb` is an improved debugging experience for Node.js powered by Chrome DevTools. You can set breakpoints, inspect variables, and step through async code visually.

**Examples:**

```bash
# Install globally
npm install -g ndb

# Start your app under the debugger
ndb node server.js
# Or if you have a start script:
ndb npm start
```

**How to use:**

- Open the Sources panel and find your file
- Click the line number to set a breakpoint
- Make a request to trigger that code path
- Use Step Over / Step Into to walk through execution
- Inspect `req`, `res`, local variables in the Scope panel

---

## 112. Handling Unhandled Routes

**What is this:** Catching requests to routes that don't exist and returning a clean 404 JSON response.

**Description:** In Express, if no route handler matches the request, it falls through to whatever comes after. By adding a catch-all `app.all('*', ...)` after all routes, you intercept unmatched routes and return a proper error instead of letting Express return its default HTML response.

**Examples:**

```js
// app.js — place this AFTER all route middleware
app.all("*", (req, res, next) => {
  res.status(404).json({
    status: "fail",
    message: `Can't find ${req.originalUrl} on this server!`,
  });
});
```

---

## 113. An Overview of Error Handling

**What is this:** Understanding the two types of errors in Node.js/Express and the strategy for handling them centrally.

**Description:** There are two categories of errors. The goal is to distinguish them and funnel all errors into one central error handling middleware.

**Operational errors** — expected, runtime problems:

- Invalid user input (validation fails)
- Route not found (404)
- Failed to connect to database
- Request timeout

**Programming errors** — bugs in the code:

- Reading a property of `undefined`
- Passing wrong arguments to a function
- Async code without error handling

**Strategy:**

- Create an `AppError` class for operational errors
- Wrap all async route handlers in a `catchAsync` utility
- Add a global Express error handler as the last middleware
- In the error handler, distinguish operational vs programming errors for different responses

---

## 114. Implementing a Global Error Handling Middleware

**What is this:** Creating an Express error handling middleware that catches all errors passed via `next(err)` and sends consistent JSON responses.

**Description:** Express error handling middleware has four parameters: `(err, req, res, next)`. Express knows it's an error handler because of the four-parameter signature. All errors from anywhere in the app are funneled here by calling `next(err)`.

**Examples:**

```js
// utils/appError.js
class AppError extends Error {
  constructor(message, statusCode) {
    super(message); // sets this.message

    this.statusCode = statusCode;
    this.status = `${statusCode}`.startsWith("4") ? "fail" : "error";
    this.isOperational = true; // flag to distinguish from programming errors

    Error.captureStackTrace(this, this.constructor);
  }
}

module.exports = AppError;
```

```js
// controllers/errorController.js
const globalErrorHandler = (err, req, res, next) => {
  err.statusCode = err.statusCode || 500;
  err.status = err.status || "error";

  res.status(err.statusCode).json({
    status: err.status,
    message: err.message,
  });
};

module.exports = globalErrorHandler;
```

```js
// app.js — must be LAST middleware
const globalErrorHandler = require("./controllers/errorController");
app.use(globalErrorHandler);
```

```js
// Using AppError in a controller
const AppError = require("../utils/appError");

exports.getTour = async (req, res, next) => {
  const tour = await Tour.findById(req.params.id);

  if (!tour) {
    return next(new AppError("No tour found with that ID", 404));
    // passes the error to globalErrorHandler
  }

  res.status(200).json({ status: "success", data: { tour } });
};
```

---

## 115. Better Errors and Refactoring

**What is this:** Improving the `AppError` class and the error controller to handle more cases cleanly.

**Description:** The unhandled route handler is updated to use `AppError`. The error controller is refactored to distinguish between operational errors (safe to expose the message) and unknown programming errors (send a generic message to the client).

**Examples:**

```js
// app.js — unhandled routes now use AppError
const AppError = require("./utils/appError");

app.all("*", (req, res, next) => {
  next(new AppError(`Can't find ${req.originalUrl} on this server!`, 404));
});
```

```js
// controllers/errorController.js
const sendErrorDev = (err, res) => {
  // In development: send everything for debugging
  res.status(err.statusCode).json({
    status: err.status,
    error: err,
    message: err.message,
    stack: err.stack,
  });
};

const sendErrorProd = (err, res) => {
  if (err.isOperational) {
    // Known operational error — safe to send message to client
    res.status(err.statusCode).json({
      status: err.status,
      message: err.message,
    });
  } else {
    // Unknown programming error — don't leak details
    console.error("ERROR:", err);
    res.status(500).json({
      status: "error",
      message: "Something went very wrong!",
    });
  }
};

module.exports = (err, req, res, next) => {
  err.statusCode = err.statusCode || 500;
  err.status = err.status || "error";

  if (process.env.NODE_ENV === "development") {
    sendErrorDev(err, res);
  } else if (process.env.NODE_ENV === "production") {
    sendErrorProd(err, res);
  }
};
```

---

## 116. Catching Errors in Async Functions

**What is this:** Eliminating repetitive `try/catch` blocks in async controllers using a `catchAsync` wrapper utility.

**Description:** Every async controller currently has the same `try/catch` pattern. You extract it into a `catchAsync` higher-order function that wraps the async function and passes any rejected promise to `next(err)` automatically.

**Examples:**

```js
// utils/catchAsync.js
const catchAsync = (fn) => {
  return (req, res, next) => {
    fn(req, res, next).catch(next);
    // .catch(next) is shorthand for .catch(err => next(err))
  };
};

module.exports = catchAsync;
```

```js
// controllers/tourController.js
const catchAsync = require("../utils/catchAsync");
const AppError = require("../utils/appError");

// No try/catch needed — errors are caught and forwarded to next()
exports.createTour = catchAsync(async (req, res, next) => {
  const newTour = await Tour.create(req.body);
  res.status(201).json({ status: "success", data: { tour: newTour } });
});

exports.getTour = catchAsync(async (req, res, next) => {
  const tour = await Tour.findById(req.params.id);
  if (!tour) return next(new AppError("No tour found with that ID", 404));
  res.status(200).json({ status: "success", data: { tour } });
});
```

---

## 117. Adding 404 Not Found Errors

**What is this:** Using `AppError` to handle the case where a valid MongoDB id is given but no matching document exists.

**Description:** When `findById` returns `null`, it means the id format was valid but no document matched. This is a 404 operational error — pass it to `next()` and let the global error handler respond.

**Examples:**

```js
exports.getTour = catchAsync(async (req, res, next) => {
  const tour = await Tour.findById(req.params.id);

  // null returned → valid id format but no document found
  if (!tour) {
    return next(new AppError("No tour found with that ID", 404));
  }

  res.status(200).json({ status: "success", data: { tour } });
});

// Same pattern for update and delete:
exports.deleteTour = catchAsync(async (req, res, next) => {
  const tour = await Tour.findByIdAndDelete(req.params.id);
  if (!tour) return next(new AppError("No tour found with that ID", 404));
  res.status(204).json({ status: "success", data: null });
});
```

---

## 118. Errors During Development vs Production

**What is this:** Sending different error responses depending on the environment — full details in dev, sanitized messages in prod.

**Description:** The `sendErrorDev` / `sendErrorProd` split (from lesson 115) is the full solution. In development you want the stack trace and full error object. In production only operational errors expose their message; programming errors get a generic "Something went wrong" response.

**Summary of the pattern:**

```
NODE_ENV=development  →  sendErrorDev: full error + stack trace
NODE_ENV=production   →  sendErrorProd:
                            isOperational=true  → send err.message
                            isOperational=false → send generic message
```

---

## 119. Handling Invalid Database IDs

**What is this:** Handling the `CastError` MongoDB throws when an invalid ObjectId format is passed as a route parameter.

**Description:** If someone requests `/api/v1/tours/invalidid`, MongoDB throws a `CastError` because `invalidid` is not a valid ObjectId. In production, this is a programming-style DB error — you convert it to an operational `AppError` so the client gets a clean 404.

**Examples:**

```js
// controllers/errorController.js
const handleCastErrorDB = (err) => {
  const message = `Invalid ${err.path}: ${err.value}`;
  return new AppError(message, 400);
};

module.exports = (err, req, res, next) => {
  err.statusCode = err.statusCode || 500;
  err.status = err.status || "error";

  if (process.env.NODE_ENV === "development") {
    sendErrorDev(err, res);
  } else if (process.env.NODE_ENV === "production") {
    let error = { ...err, message: err.message };

    if (error.name === "CastError") error = handleCastErrorDB(error);

    sendErrorProd(error, res);
  }
};
```

---

## 120. Handling Duplicate Database Fields

**What is this:** Handling the MongoDB error (code 11000) thrown when you try to insert a document with a duplicate value in a unique field.

**Description:** When a duplicate key violates a unique index, MongoDB throws an error with code `11000`. This is not a Mongoose validation error — it comes from the driver. You detect it by checking `err.code` and extract the duplicate value from the error message with a regex.

**Examples:**

```js
// controllers/errorController.js
const handleDuplicateFieldsDB = (err) => {
  const value = err.errmsg.match(/(["'])(\\?.)*?\1/)[0];
  // Extracts the duplicated value from the error message string
  const message = `Duplicate field value: ${value}. Please use another value!`;
  return new AppError(message, 400);
};

// In the error handler:
if (error.code === 11000) error = handleDuplicateFieldsDB(error);
```

---

## 121. Handling Mongoose Validation Errors

**What is this:** Converting Mongoose `ValidationError` objects (from schema validators) into clean `AppError` responses.

**Description:** When multiple fields fail validation, Mongoose bundles them into one `ValidationError` with an `errors` object. You extract all the messages, join them, and wrap in an `AppError` so the client gets all failures in one readable response.

**Examples:**

```js
// controllers/errorController.js
const handleValidationErrorDB = (err) => {
  const errors = Object.values(err.errors).map((el) => el.message);
  // err.errors = { name: ValidatorError, difficulty: ValidatorError, ... }

  const message = `Invalid input data. ${errors.join(". ")}`;
  return new AppError(message, 400);
};

// In the error handler:
if (error.name === "ValidationError") error = handleValidationErrorDB(error);

// Example response when name and difficulty both fail:
// "Invalid input data. A tour must have a name. Difficulty must be: easy, medium, or difficult."
```

---

## 122. Errors Outside Express: Unhandled Rejections

**What is this:** Catching Promise rejections that are not handled anywhere in the code (e.g. a failed database connection).

**Description:** If a Promise rejects and there is no `.catch()` or `try/catch` for it, Node.js emits the `unhandledRejection` event. You listen for it on `process`, log the error, then gracefully shut down the server.

**Examples:**

```js
// server.js
process.on("unhandledRejection", (err) => {
  console.log("UNHANDLED REJECTION! Shutting down...");
  console.log(err.name, err.message);

  // Give the server time to finish pending requests before shutting down
  server.close(() => {
    process.exit(1); // 1 = uncaught exception / failure
  });
});

// Example: wrong DB password in config.env triggers this
// mongoose.connect() rejects → caught by unhandledRejection
```

---

## 123. Catching Uncaught Exceptions

**What is this:** Catching synchronous errors that are not caught anywhere using the `uncaughtException` event.

**Description:** Synchronous code that throws and is not wrapped in `try/catch` causes an `uncaughtException`. Unlike `unhandledRejection`, this must be handled **before** any other code runs. After this event fires, the Node process is in an undefined state — you must exit.

**Examples:**

```js
// server.js — MUST be at the very top, before anything else
process.on("uncaughtException", (err) => {
  console.log("UNCAUGHT EXCEPTION! Shutting down...");
  console.log(err.name, err.message);
  process.exit(1); // no graceful shutdown needed — process state is corrupt
});

// Example trigger (never do this in real code):
// console.log(x); // ReferenceError: x is not defined → uncaughtException
```

**Order in `server.js`:**

```js
// 1. Handle uncaught exceptions FIRST
process.on("uncaughtException", (err) => { ... });

// 2. Load config
dotenv.config({ path: "./config.env" });

// 3. Connect to DB + start server
mongoose.connect(DB).then(() => { ... });

// 4. Handle unhandled rejections AFTER server is defined
const server = app.listen(port, () => { ... });
process.on("unhandledRejection", (err) => {
  server.close(() => process.exit(1));
});
```

---

# Section 10: Authentication, Authorization and Security

---

## 124. Section Intro

**What is this:** Overview of the section — implementing a complete authentication and authorization system with JWTs, plus hardening the API against common security threats.

**Description:** This section builds user sign-up, login, password reset via email, role-based authorization, and a range of security measures: rate limiting, HTTP headers, data sanitization, and more.

---

## 125. Modelling Users

**What is this:** Creating the User Mongoose schema and model with all required fields for authentication.

**Description:** The User model stores name, email, password (hashed), role, and password reset fields. Email uniqueness is enforced at the schema level.

**Examples:**

```js
// models/userModel.js
const mongoose = require("mongoose");
const validator = require("validator");

const userSchema = new mongoose.Schema({
  name: {
    type: String,
    required: [true, "Please tell us your name!"],
  },
  email: {
    type: String,
    required: [true, "Please provide your email"],
    unique: true,
    lowercase: true,
    validate: [validator.isEmail, "Please provide a valid email"],
  },
  photo: String,
  role: {
    type: String,
    enum: ["user", "guide", "lead-guide", "admin"],
    default: "user",
  },
  password: {
    type: String,
    required: [true, "Please provide a password"],
    minlength: 8,
    select: false, // never send password in query results
  },
  passwordConfirm: {
    type: String,
    required: [true, "Please confirm your password"],
    validate: {
      validator: function (val) {
        return val === this.password; // only works on .save() / .create()
      },
      message: "Passwords are not the same!",
    },
  },
  passwordChangedAt: Date,
  passwordResetToken: String,
  passwordResetExpires: Date,
  active: {
    type: Boolean,
    default: true,
    select: false,
  },
});

const User = mongoose.model("User", userSchema);
module.exports = User;
```

---

## 126. Creating New Users

**What is this:** Implementing the `POST /api/v1/users/signup` route that creates a new user account.

**Description:** The signup controller uses `User.create()` with the request body. Only specific fields are passed to prevent mass assignment (e.g. setting `role: 'admin'` via the request body). After creation, a JWT is sent back.

**Examples:**

```js
// controllers/authController.js
const User = require("../models/userModel");
const catchAsync = require("../utils/catchAsync");

exports.signup = catchAsync(async (req, res, next) => {
  const newUser = await User.create({
    name: req.body.name,
    email: req.body.email,
    password: req.body.password,
    passwordConfirm: req.body.passwordConfirm,
    // Do NOT spread req.body — prevents role injection
  });

  res.status(201).json({
    status: "success",
    data: { user: newUser },
  });
});
```

```js
// routes/userRoutes.js
const authController = require("../controllers/authController");
router.post("/signup", authController.signup);
```

---

## 127. Managing Passwords

**What is this:** Hashing passwords before saving with `bcryptjs` and clearing `passwordConfirm` so it's never stored.

**Description:** Passwords must never be stored in plain text. A `pre('save')` hook hashes the password with bcrypt before the document is persisted. `passwordConfirm` is set to `undefined` after validation so it's not stored in the DB.

**Examples:**

```bash
npm install bcryptjs
```

```js
// models/userModel.js
const bcrypt = require("bcryptjs");

userSchema.pre("save", async function (next) {
  // Only run if password was modified
  if (!this.isModified("password")) return next();

  this.password = await bcrypt.hash(this.password, 12);
  // 12 = cost factor (higher = slower = more secure)

  this.passwordConfirm = undefined; // don't persist this field
  next();
});

// Instance method to check a candidate password against the stored hash
userSchema.methods.correctPassword = async function (
  candidatePassword,
  userPassword,
) {
  return await bcrypt.compare(candidatePassword, userPassword);
  // returns true if they match
};
```

---

## 128. How Authentication with JWT Works

**What is this:** A conceptual overview of JSON Web Tokens — what they are, how they're structured, and the login/request flow.

**Description:** JWT is a stateless authentication mechanism. The server creates a signed token on login; the client stores it and sends it with every request. The server verifies the signature without needing a session store.

**JWT structure:**

```
header.payload.signature
```

- **Header** — algorithm and token type (base64 encoded)
- **Payload** — claims: user id, issued-at, expiry (base64 encoded, NOT encrypted)
- **Signature** — `HMAC(header + payload, secret)` — verifies the token wasn't tampered with

**Flow:**

```
1. User logs in (email + password)
2. Server verifies credentials
3. Server creates JWT: sign({ id: user._id }, SECRET, { expiresIn: '90d' })
4. Server sends JWT to client
5. Client stores token (cookie or localStorage)
6. Client sends token on every request: Authorization: Bearer <token>
7. Server verifies signature and extracts user id
8. Server fetches user from DB and allows access
```

**Key rule:** The payload is readable by anyone — never store sensitive data in it.

---

## 129. Signing up Users

**What is this:** Generating and sending a JWT token after a user successfully signs up.

**Description:** After creating the user, a JWT is signed with the user's `_id` as the payload. It's sent in the response so the client can use it immediately for subsequent requests.

**Examples:**

```bash
npm install jsonwebtoken
```

```js
// controllers/authController.js
const jwt = require("jsonwebtoken");

const signToken = (id) => {
  return jwt.sign(
    { id }, // payload
    process.env.JWT_SECRET, // secret from config.env
    { expiresIn: process.env.JWT_EXPIRES_IN }, // e.g. "90d"
  );
};

const createSendToken = (user, statusCode, res) => {
  const token = signToken(user._id);

  // Hide password in output
  user.password = undefined;

  res.status(statusCode).json({
    status: "success",
    token,
    data: { user },
  });
};

exports.signup = catchAsync(async (req, res, next) => {
  const newUser = await User.create({
    name: req.body.name,
    email: req.body.email,
    password: req.body.password,
    passwordConfirm: req.body.passwordConfirm,
  });

  createSendToken(newUser, 201, res);
});
```

```
# config.env
JWT_SECRET=my-ultra-secure-and-ultra-long-secret-at-least-32-chars
JWT_EXPIRES_IN=90d
```

---

## 130. Logging in Users

**What is this:** Implementing the `POST /api/v1/users/login` route that verifies credentials and issues a JWT.

**Description:** Login checks that email and password are provided, finds the user (including the hidden password field), then uses `bcrypt.compare` to verify the password. On success, a JWT is signed and returned.

**Examples:**

```js
exports.login = catchAsync(async (req, res, next) => {
  const { email, password } = req.body;

  // 1. Check email and password were provided
  if (!email || !password) {
    return next(new AppError("Please provide email and password!", 400));
  }

  // 2. Find user — password is select:false so must be explicitly selected
  const user = await User.findOne({ email }).select("+password");

  // 3. Verify password
  if (!user || !(await user.correctPassword(password, user.password))) {
    return next(new AppError("Incorrect email or password", 401));
    // Vague on purpose — don't reveal whether email exists
  }

  // 4. Send JWT
  createSendToken(user, 200, res);
});
```

```js
// routes/userRoutes.js
router.post("/login", authController.login);
```

---

## 131. Protecting Tour Routes - Part 1

**What is this:** Creating a `protect` middleware that verifies the JWT and blocks unauthenticated requests before they reach route handlers.

**Description:** The `protect` middleware runs before protected route handlers. It extracts the token from the `Authorization` header, verifies it, then checks the user still exists. It attaches the user to `req.user` for use in subsequent middleware.

**Examples:**

```js
const { promisify } = require("util");

exports.protect = catchAsync(async (req, res, next) => {
  // 1. Get token from Authorization header
  let token;
  if (
    req.headers.authorization &&
    req.headers.authorization.startsWith("Bearer")
  ) {
    token = req.headers.authorization.split(" ")[1];
  }

  if (!token) {
    return next(
      new AppError("You are not logged in! Please log in to get access.", 401),
    );
  }

  // 2. Verify token — throws if expired or tampered
  const decoded = await promisify(jwt.verify)(token, process.env.JWT_SECRET);
  // decoded = { id: '...', iat: 1234567890, exp: 1234567890 }

  // 3. Check user still exists
  const currentUser = await User.findById(decoded.id);
  if (!currentUser) {
    return next(
      new AppError("The user belonging to this token no longer exists.", 401),
    );
  }

  // 4. Attach user to request for downstream use
  req.user = currentUser;
  next();
});
```

---

## 132. Protecting Tour Routes - Part 2

**What is this:** Checking if the user changed their password after the JWT was issued, and applying `protect` to all tour routes.

**Description:** If a user changes their password after a JWT is issued, old tokens should be invalidated. A `passwordChangedAfter` instance method checks the `passwordChangedAt` timestamp against the JWT's `iat` (issued-at) claim.

**Examples:**

```js
// models/userModel.js — instance method
userSchema.methods.changedPasswordAfter = function (JWTTimestamp) {
  if (this.passwordChangedAt) {
    const changedTimestamp = parseInt(
      this.passwordChangedAt.getTime() / 1000,
      10,
    );
    return JWTTimestamp < changedTimestamp; // true = password changed after token issued
  }
  return false; // password never changed
};

// pre-save hook — set passwordChangedAt when password is modified (but not on first create)
userSchema.pre("save", function (next) {
  if (!this.isModified("password") || this.isNew) return next();
  this.passwordChangedAt = Date.now() - 1000; // -1s to ensure token is issued after
  next();
});
```

```js
// controllers/authController.js — add step 4 to protect
const currentUser = await User.findById(decoded.id);
if (!currentUser) return next(new AppError("User no longer exists.", 401));

if (currentUser.changedPasswordAfter(decoded.iat)) {
  return next(
    new AppError("User recently changed password! Please log in again.", 401),
  );
}

req.user = currentUser;
next();
```

```js
// routes/tourRoutes.js — protect all routes below this line
router.use(authController.protect);
router
  .route("/")
  .get(tourController.getAllTours)
  .post(tourController.createTour);
// ...
```

---

## 133. Advanced Postman Setup

**What is this:** Configuring Postman environments and scripts to automatically store and use the JWT token across requests.

**Description:** Instead of manually copying the token from a login response and pasting it into every request, you set up a Postman environment variable and a test script on the login request that saves the token automatically.

**Setup:**

1. Create a Postman environment (e.g. "Natours Dev") with a variable `jwt`
2. On the Login request → **Tests** tab, add:

```js
pm.environment.set("jwt", pm.response.json().token);
```

3. On protected requests → **Authorization** tab → Bearer Token → `{{jwt}}`
4. Now every login automatically stores the token and all protected requests use it

---

## 134. Authorization: User Roles and Permissions

**What is this:** Restricting certain routes to specific user roles (e.g. only admins can delete tours).

**Description:** A `restrictTo` middleware factory takes a list of allowed roles and returns middleware that checks `req.user.role`. If the user's role isn't in the list, access is denied with a 403 Forbidden.

**Examples:**

```js
// controllers/authController.js
exports.restrictTo = (...roles) => {
  // roles = ['admin', 'lead-guide']
  return (req, res, next) => {
    if (!roles.includes(req.user.role)) {
      return next(
        new AppError("You do not have permission to perform this action", 403),
      );
    }
    next();
  };
};
```

```js
// routes/tourRoutes.js
router
  .route("/:id")
  .delete(
    authController.protect,
    authController.restrictTo("admin", "lead-guide"),
    tourController.deleteTour,
  );
```

---

## 135. Password Reset Functionality: Reset Token

**What is this:** Generating a secure, time-limited password reset token and sending it to the user's email.

**Description:** A random token is generated with Node's `crypto` module. The raw token is sent to the user; only a hashed version is stored in the DB (same principle as passwords). The token expires after 10 minutes.

**Examples:**

```js
// models/userModel.js — instance method
const crypto = require("crypto");

userSchema.methods.createPasswordResetToken = function () {
  const resetToken = crypto.randomBytes(32).toString("hex");
  // raw token — sent to user by email

  this.passwordResetToken = crypto
    .createHash("sha256")
    .update(resetToken)
    .digest("hex");
  // hashed token — stored in DB (never store raw token)

  this.passwordResetExpires = Date.now() + 10 * 60 * 1000; // 10 minutes

  return resetToken; // return the unhashed version to send via email
};
```

```js
// controllers/authController.js
exports.forgotPassword = catchAsync(async (req, res, next) => {
  const user = await User.findOne({ email: req.body.email });
  if (!user)
    return next(new AppError("There is no user with that email address.", 404));

  const resetToken = user.createPasswordResetToken();
  await user.save({ validateBeforeSave: false }); // skip validators

  // TODO: send resetToken by email (next lesson)
  res.status(200).json({ status: "success", message: "Token sent to email!" });
});
```

---

## 136. Sending Emails with Nodemailer

**What is this:** Sending the password reset token to the user's email using Nodemailer and a development SMTP service (Mailtrap).

**Description:** Nodemailer is the standard Node.js email library. During development, Mailtrap catches all outgoing emails so they never reach real inboxes. A reusable `sendEmail` utility wraps the transporter logic.

**Examples:**

```bash
npm install nodemailer
```

```js
// utils/email.js
const nodemailer = require("nodemailer");

const sendEmail = async (options) => {
  // 1. Create a transporter
  const transporter = nodemailer.createTransport({
    host: process.env.EMAIL_HOST,
    port: process.env.EMAIL_PORT,
    auth: {
      user: process.env.EMAIL_USERNAME,
      pass: process.env.EMAIL_PASSWORD,
    },
  });

  // 2. Define the email options
  const mailOptions = {
    from: "Natours <noreply@natours.io>",
    to: options.email,
    subject: options.subject,
    text: options.message,
  };

  // 3. Send the email
  await transporter.sendMail(mailOptions);
};

module.exports = sendEmail;
```

```js
// controllers/authController.js — send the reset token by email
const sendEmail = require("../utils/email");

exports.forgotPassword = catchAsync(async (req, res, next) => {
  const user = await User.findOne({ email: req.body.email });
  if (!user)
    return next(new AppError("There is no user with that email address.", 404));

  const resetToken = user.createPasswordResetToken();
  await user.save({ validateBeforeSave: false });

  const resetURL = `${req.protocol}://${req.get("host")}/api/v1/users/resetPassword/${resetToken}`;
  const message = `Forgot your password? Submit a PATCH request with your new password to: ${resetURL}.\nIf you didn't forget your password, please ignore this email.`;

  try {
    await sendEmail({
      email: user.email,
      subject: "Your password reset token (valid for 10 min)",
      message,
    });
    res
      .status(200)
      .json({ status: "success", message: "Token sent to email!" });
  } catch (err) {
    user.passwordResetToken = undefined;
    user.passwordResetExpires = undefined;
    await user.save({ validateBeforeSave: false });
    return next(
      new AppError(
        "There was an error sending the email. Try again later!",
        500,
      ),
    );
  }
});
```

---

## 137. Password Reset Functionality: Setting New Password

**What is this:** Implementing the `PATCH /resetPassword/:token` route that verifies the reset token and updates the password.

**Description:** The raw token from the URL is hashed and compared against the stored hash. If it matches and hasn't expired, the new password is saved (triggering the bcrypt pre-save hook) and a new JWT is issued.

**Examples:**

```js
exports.resetPassword = catchAsync(async (req, res, next) => {
  // 1. Hash the token from the URL
  const hashedToken = crypto
    .createHash("sha256")
    .update(req.params.token)
    .digest("hex");

  // 2. Find user by token + check expiry
  const user = await User.findOne({
    passwordResetToken: hashedToken,
    passwordResetExpires: { $gt: Date.now() },
  });

  if (!user) return next(new AppError("Token is invalid or has expired", 400));

  // 3. Set the new password
  user.password = req.body.password;
  user.passwordConfirm = req.body.passwordConfirm;
  user.passwordResetToken = undefined;
  user.passwordResetExpires = undefined;
  await user.save(); // triggers bcrypt pre-save hook + passwordConfirm validator

  // 4. Issue new JWT
  createSendToken(user, 200, res);
});
```

---

## 138. Updating the Current User: Password

**What is this:** Allowing a logged-in user to change their own password by providing their current password first.

**Description:** Password updates must go through `save()` (not `findByIdAndUpdate`) so the bcrypt pre-save hook runs. The current password is verified before allowing the change.

**Examples:**

```js
exports.updatePassword = catchAsync(async (req, res, next) => {
  // 1. Get user (password is select:false so must select explicitly)
  const user = await User.findById(req.user.id).select("+password");

  // 2. Verify current password
  if (!(await user.correctPassword(req.body.passwordCurrent, user.password))) {
    return next(new AppError("Your current password is wrong.", 401));
  }

  // 3. Update password — must use .save() for hooks to run
  user.password = req.body.password;
  user.passwordConfirm = req.body.passwordConfirm;
  await user.save();
  // findByIdAndUpdate would skip pre-save hooks and validators!

  // 4. Issue new JWT
  createSendToken(user, 200, res);
});
```

---

## 139. Updating the Current User: Data

**What is this:** Allowing a logged-in user to update their own non-sensitive data (name, email) — not password.

**Description:** Non-password fields are safe to update with `findByIdAndUpdate`. A `filterObj` helper is used to whitelist only allowed fields, preventing users from updating their `role` or other sensitive fields via this endpoint.

**Examples:**

```js
const filterObj = (obj, ...allowedFields) => {
  const newObj = {};
  Object.keys(obj).forEach((key) => {
    if (allowedFields.includes(key)) newObj[key] = obj[key];
  });
  return newObj;
};

exports.updateMe = catchAsync(async (req, res, next) => {
  // 1. Block password updates through this route
  if (req.body.password || req.body.passwordConfirm) {
    return next(
      new AppError(
        "This route is not for password updates. Please use /updateMyPassword.",
        400,
      ),
    );
  }

  // 2. Whitelist allowed fields
  const filteredBody = filterObj(req.body, "name", "email");

  // 3. Update — findByIdAndUpdate is fine here (no password/hooks involved)
  const updatedUser = await User.findByIdAndUpdate(req.user.id, filteredBody, {
    new: true,
    runValidators: true,
  });

  res.status(200).json({ status: "success", data: { user: updatedUser } });
});
```

---

## 140. Deleting the Current User

**What is this:** Allowing a logged-in user to "delete" their account — implemented as a soft delete by setting `active: false`.

**Description:** Users are never truly deleted from the DB (for data integrity). Instead, `active` is set to `false`. A query middleware on the User model filters out inactive users from all `find` queries automatically.

**Examples:**

```js
// controllers/userController.js
exports.deleteMe = catchAsync(async (req, res, next) => {
  await User.findByIdAndUpdate(req.user.id, { active: false });
  res.status(204).json({ status: "success", data: null });
});
```

```js
// models/userModel.js — query middleware to hide inactive users
userSchema.pre(/^find/, function (next) {
  this.find({ active: { $ne: false } });
  next();
});
```

---

## 141. Security Best Practices

**What is this:** A summary of the key security measures implemented in this section and general Node.js/Express security guidelines.

**Description:** Security in Node.js APIs requires layers of protection. The remaining lessons in this section each implement one of these measures.

**Key measures covered:**
| Threat | Solution |
|--------|----------|
| Brute force / DoS | Rate limiting (`express-rate-limit`) |
| Sensitive data exposure | Set security HTTP headers (`helmet`) |
| NoSQL injection | Sanitize query input (`express-mongo-sanitize`) |
| XSS | Sanitize HTML input (`xss-clean`) |
| Parameter pollution | Whitelist query params (`hpp`) |
| JWT theft | Short expiry + send via HttpOnly cookie |
| Password cracking | bcrypt with high cost factor |

---

## 142. Sending JWT via Cookie

**What is this:** Sending the JWT in an HTTP-only cookie instead of (or in addition to) the response body, to protect it from XSS attacks.

**Description:** An HTTP-only cookie cannot be accessed by JavaScript — it's sent automatically by the browser on every request. Combined with the `secure` flag (HTTPS only in production), this is safer than storing the token in `localStorage`.

**Examples:**

```js
const createSendToken = (user, statusCode, res) => {
  const token = signToken(user._id);

  const cookieOptions = {
    expires: new Date(
      Date.now() + process.env.JWT_COOKIE_EXPIRES_IN * 24 * 60 * 60 * 1000,
    ),
    httpOnly: true, // cookie cannot be accessed or modified by browser JS
  };

  if (process.env.NODE_ENV === "production") {
    cookieOptions.secure = true; // only send over HTTPS
  }

  res.cookie("jwt", token, cookieOptions);

  user.password = undefined;
  res.status(statusCode).json({ status: "success", token, data: { user } });
};
```

```
# config.env
JWT_COOKIE_EXPIRES_IN=90
```

---

## 143. Implementing Rate Limiting

**What is this:** Limiting the number of requests a single IP can make to the API to prevent brute force and DoS attacks.

**Description:** `express-rate-limit` adds a middleware that tracks requests per IP. When the limit is exceeded, it responds with a 429 Too Many Requests error. Applied globally to `/api` routes.

**Examples:**

```bash
npm install express-rate-limit
```

```js
// app.js
const rateLimit = require("express-rate-limit");

const limiter = rateLimit({
  max: 100, // max 100 requests...
  windowMs: 60 * 60 * 1000, // ...per hour per IP
  message: "Too many requests from this IP, please try again in an hour!",
});

app.use("/api", limiter); // apply only to /api routes
```

---

## 144. Setting Security HTTP Headers

**What is this:** Using `helmet` to set secure HTTP response headers that protect against common browser-based attacks.

**Description:** `helmet` is a collection of small middleware functions that set HTTP headers. These headers tell browsers to enable security protections like preventing clickjacking, disabling MIME sniffing, and enforcing HTTPS.

**Examples:**

```bash
npm install helmet
```

```js
// app.js — use helmet early, before any other middleware
const helmet = require("helmet");

app.use(helmet());

// Key headers helmet sets:
// Content-Security-Policy  — restrict resource origins
// X-Frame-Options          — prevent clickjacking
// X-Content-Type-Options   — prevent MIME sniffing
// Strict-Transport-Security — enforce HTTPS (HSTS)
// X-XSS-Protection         — enable browser XSS filter
```

---

## 145. Data Sanitization

**What is this:** Sanitizing request data to prevent NoSQL injection and XSS attacks.

**Description:** Two packages handle different attack vectors. `express-mongo-sanitize` strips `$` and `.` from user input, blocking NoSQL injection. `xss-clean` escapes HTML special characters in input, blocking XSS.

**Examples:**

```bash
npm install express-mongo-sanitize xss-clean
```

```js
// app.js — apply after body parser
const mongoSanitize = require("express-mongo-sanitize");
const xss = require("xss-clean");

app.use(mongoSanitize());
// Removes $ and . from req.body, req.query, req.params
// Blocks: { "email": { "$gt": "" } } — a NoSQL injection trick

app.use(xss());
// Converts HTML special chars in user input
// Blocks: { "name": "<script>alert('xss')</script>" }
```

---

## 146. Preventing Parameter Pollution

**What is this:** Using `hpp` (HTTP Parameter Pollution) to prevent duplicate query string parameters from breaking API logic.

**Description:** If a client sends `?sort=price&sort=duration`, Express parses `req.query.sort` as an array `['price', 'duration']`, which breaks code that expects a string. `hpp` clears duplicate parameters (using the last value) and lets you whitelist fields where arrays are intentional.

**Examples:**

```bash
npm install hpp
```

```js
// app.js — apply after other middleware
const hpp = require("hpp");

app.use(
  hpp({
    whitelist: [
      "duration",
      "ratingsQuantity",
      "ratingsAverage",
      "maxGroupSize",
      "difficulty",
      "price",
    ],
    // Whitelisted fields are allowed to have duplicate values (used for filtering)
    // e.g. ?duration=5&duration=9 → { duration: ['5', '9'] } — intentional
  }),
);
```

---

# Section 11: Modelling Data and Advanced Mongoose

---

## 147. Section Intro

**What is this:** Overview of this section — advanced data modelling patterns, Mongoose relationships, factory handler functions, indexes, and geospatial queries.

**Description:** This section completes the Natours API data layer: adding geospatial data to tours, modelling the reviews resource with parent referencing, building reusable CRUD factory functions, and implementing geospatial queries.

---

## 148. MongoDB Data Modelling

**What is this:** How to decide between embedding documents and referencing them — the core trade-off in NoSQL schema design.

**Description:** Unlike SQL, MongoDB gives you a choice: store related data inside the same document (embedding) or store it separately and reference it (normalizing). The right choice depends on how the data is accessed and how often it changes.

**Embedding (denormalization):**

- Store related data inside the parent document
- Best when: data is always queried together, data doesn't change often, 1:few relationship
- Example: tour locations embedded in the tour document

**Referencing (normalization):**

- Store related data in separate collections, reference by `_id`
- Best when: data is queried independently, data changes often, 1:many or many:many
- **Child referencing** — parent stores array of child `_id`s (use for small arrays)
- **Parent referencing** — child stores a reference to the parent `_id` (use for large/unbounded arrays)
- **Two-way referencing** — both sides store references (use for many:many)

**Decision guide:**

| Relationship               | Preferred approach |
| -------------------------- | ------------------ |
| 1:few, always together     | Embed              |
| 1:many, queried separately | Parent reference   |
| 1:few, frequently updated  | Child reference    |
| Many:many                  | Two-way reference  |

---

## 149. Designing Our Data Model

**What is this:** Planning the Natours data model — deciding which resources exist and how they relate to each other.

**Description:** Before writing schema code, map out what collections you need and how they relate. This architectural decision shapes the entire application.

**Natours resources and relationships:**

```
Tours ──────────────── Locations (embedded GeoJSON — always needed with tour)
Tours ──────────────── Guides (referenced — users exist independently)
Tours ──────────────── Reviews (parent ref on review — unbounded array)
Users ──────────────── Reviews (parent ref on review)
Bookings ───────────── Tours + Users (many:many)
```

---

## 150. Modelling Locations (Geospatial Data)

**What is this:** Embedding GeoJSON location objects into the Tour schema for start location and tour stop locations.

**Description:** MongoDB supports GeoJSON natively. A GeoJSON point needs a `type` field set to `"Point"` and a `coordinates` array `[longitude, latitude]` (note: longitude first, unlike most mapping conventions).

**Examples:**

```js
// models/tourModel.js
const tourSchema = new mongoose.Schema({
  startLocation: {
    // GeoJSON — embedded object
    type: {
      type: String,
      default: "Point",
      enum: ["Point"],
    },
    coordinates: [Number], // [longitude, latitude]
    address: String,
    description: String,
  },
  locations: [
    {
      // Array of GeoJSON points — embedded
      type: {
        type: String,
        default: "Point",
        enum: ["Point"],
      },
      coordinates: [Number],
      address: String,
      description: String,
      day: Number, // which day of the tour
    },
  ],
});
```

---

## 151. Modelling Tour Guides: Embedding

**What is this:** Storing tour guide data directly inside the tour document by embedding user documents at save time.

**Description:** With embedding, a pre-save hook fetches the full user documents for the guide ids in `req.body.guides` and replaces them with the actual user objects. The guides live inside the tour — no separate query needed to display them.

**Examples:**

```js
// models/tourModel.js
tourSchema.pre("save", async function (next) {
  const guidesPromises = this.guides.map((id) => User.findById(id));
  this.guides = await Promise.all(guidesPromises);
  // replaces array of ids with array of full user documents
  next();
});
```

**Downside:** If a guide updates their profile, all tour documents that embed them must also be updated — embedding is rejected in favour of referencing in the next lesson.

---

## 152. Modelling Tour Guides: Child Referencing

**What is this:** Storing only guide user `_id`s in the tour document (child referencing) instead of embedding full user objects.

**Description:** Child referencing keeps the tour document lean. The guides field holds an array of ObjectId references to User documents. Mongoose populates the full user data at query time when needed.

**Examples:**

```js
// models/tourModel.js
const tourSchema = new mongoose.Schema({
  guides: [
    {
      type: mongoose.Schema.ObjectId, // just the id — reference to User
      ref: "User", // tells Mongoose which model to use for populate
    },
  ],
});
```

---

## 153. Populating Tour Guides

**What is this:** Using Mongoose's `populate()` to replace stored guide ObjectIds with the actual User documents at query time.

**Description:** `populate()` performs an additional query behind the scenes to fetch the referenced documents and replace the ids. You can select specific fields and exclude internal ones. A query middleware makes this automatic for all `find` queries.

**Examples:**

```js
// Manual populate in a controller
const tour = await Tour.findById(req.params.id).populate({
  path: "guides",
  select: "-__v -passwordChangedAt", // exclude internal fields
});
```

```js
// Automatic populate via query middleware — applies to all find queries
tourSchema.pre(/^find/, function (next) {
  this.populate({
    path: "guides",
    select: "-__v -passwordChangedAt",
  });
  next();
});
```

---

## 154. Modelling Reviews: Parent Referencing

**What is this:** Creating the Review model with parent references to both the Tour and User it belongs to.

**Description:** Reviews belong to a tour and a user, but neither collection should store an array of review ids (it could grow unbounded). Instead, each review stores a reference to its parent tour and user — this is parent referencing.

**Examples:**

```js
// models/reviewModel.js
const mongoose = require("mongoose");

const reviewSchema = new mongoose.Schema(
  {
    review: {
      type: String,
      required: [true, "Review cannot be empty"],
    },
    rating: {
      type: Number,
      min: 1,
      max: 5,
    },
    createdAt: {
      type: Date,
      default: Date.now,
    },
    tour: {
      type: mongoose.Schema.ObjectId,
      ref: "Tour",
      required: [true, "Review must belong to a tour"],
    },
    user: {
      type: mongoose.Schema.ObjectId,
      ref: "User",
      required: [true, "Review must belong to a user"],
    },
  },
  {
    toJSON: { virtuals: true },
    toObject: { virtuals: true },
  },
);

const Review = mongoose.model("Review", reviewSchema);
module.exports = Review;
```

---

## 155. Creating and Getting Reviews

**What is this:** Implementing the review controller and routes for creating and reading reviews.

**Description:** Reviews need their own router, controller, and routes file. The `createReview` controller attaches the tour and user ids from the request (not from user input — for security).

**Examples:**

```js
// controllers/reviewController.js
const Review = require("../models/reviewModel");
const catchAsync = require("../utils/catchAsync");

exports.getAllReviews = catchAsync(async (req, res, next) => {
  const reviews = await Review.find();
  res
    .status(200)
    .json({ status: "success", results: reviews.length, data: { reviews } });
});

exports.createReview = catchAsync(async (req, res, next) => {
  // Allow nested routes — tour/user from params if not in body
  if (!req.body.tour) req.body.tour = req.params.tourId;
  if (!req.body.user) req.body.user = req.user.id;

  const newReview = await Review.create(req.body);
  res.status(201).json({ status: "success", data: { review: newReview } });
});
```

```js
// routes/reviewRoutes.js
const express = require("express");
const reviewController = require("../controllers/reviewController");
const authController = require("../controllers/authController");

const router = express.Router({ mergeParams: true }); // needed for nested routes

router.use(authController.protect);

router
  .route("/")
  .get(reviewController.getAllReviews)
  .post(authController.restrictTo("user"), reviewController.createReview);

module.exports = router;
```

---

## 156. Populating Reviews

**What is this:** Using `populate()` in query middleware on the Review schema to fill in user and tour data automatically.

**Description:** A `pre(/^find/)` hook on the Review schema auto-populates the `user` field (and optionally `tour`) whenever reviews are queried, so the response always includes the reviewer's name and photo.

**Examples:**

```js
// models/reviewModel.js
reviewSchema.pre(/^find/, function (next) {
  this.populate({
    path: "user",
    select: "name photo", // only include name and photo — not password etc.
  });
  // Note: not populating 'tour' here to avoid circular over-population
  next();
});
```

---

## 157. Virtual Populate: Tours and Reviews

**What is this:** Using Mongoose virtual populate to make reviews accessible on a tour document without storing review ids in the tour collection.

**Description:** Virtual populate is the best of both worlds — the tour doesn't store review ids (no unbounded array), but you can still access reviews on a tour by telling Mongoose where to find them. It only runs the extra query when you explicitly `populate('reviews')`.

**Examples:**

```js
// models/tourModel.js
tourSchema.virtual("reviews", {
  ref: "Review", // the model to query
  foreignField: "tour", // the field in Review that references Tour
  localField: "_id", // the field in Tour to match against
});
```

```js
// controllers/tourController.js — populate reviews only on getTour
exports.getTour = catchAsync(async (req, res, next) => {
  const tour = await Tour.findById(req.params.id).populate("reviews");

  if (!tour) return next(new AppError("No tour found with that ID", 404));
  res.status(200).json({ status: "success", data: { tour } });
});
```

---

## 158. Implementing Simple Nested Routes

**What is this:** Supporting URLs like `POST /api/v1/tours/:tourId/reviews` so reviews can be created in the context of a tour.

**Description:** Nested routes express resource ownership in the URL — a review belongs to a tour. The simplest approach mounts the review router inside the tour router using `router.use()`.

**Examples:**

```js
// routes/tourRoutes.js
const reviewRouter = require("./reviewRoutes");

// Redirect nested route to reviewRouter
router.use("/:tourId/reviews", reviewRouter);

// routes/reviewRoutes.js — mergeParams lets the router access tourId from the parent
const router = express.Router({ mergeParams: true });
// Now req.params.tourId is available in reviewController
```

---

## 159. Nested Routes with Express

**What is this:** Properly connecting the review router to tour routes using `mergeParams` so parent URL parameters are accessible.

**Description:** When a router is mounted as a sub-router, its `req.params` only contains its own parameters. `mergeParams: true` merges the parent's params (like `:tourId`) into `req.params`, making them available in the nested router's controllers.

**Examples:**

```js
// routes/reviewRoutes.js
const router = express.Router({ mergeParams: true });
// Without mergeParams: req.params = {}
// With mergeParams:    req.params = { tourId: '5c88fa8cf4afda39709c2955' }

router
  .route("/")
  .get(reviewController.getAllReviews)
  .post(
    authController.protect,
    authController.restrictTo("user"),
    reviewController.createReview,
  );

// Now both routes work:
// POST /api/v1/reviews               (standalone)
// POST /api/v1/tours/:tourId/reviews (nested)
```

---

## 160. Adding a Nested GET Endpoint

**What is this:** Making `GET /api/v1/tours/:tourId/reviews` return only the reviews for a specific tour.

**Description:** When `tourId` is present in the URL (via nested route), filter reviews by that tour. Otherwise return all reviews. A single `getAllReviews` handler handles both cases.

**Examples:**

```js
// controllers/reviewController.js
exports.getAllReviews = catchAsync(async (req, res, next) => {
  let filter = {};
  if (req.params.tourId) filter = { tour: req.params.tourId };
  // Nested route → filter by tour; standalone → return all

  const reviews = await Review.find(filter);
  res
    .status(200)
    .json({ status: "success", results: reviews.length, data: { reviews } });
});
```

---

## 161. Building Handler Factory Functions: Delete

**What is this:** Creating a `deleteOne` factory function that generates a delete handler for any Mongoose model.

**Description:** The delete handler is identical across all resources (tours, users, reviews) — find by id, delete, return 204. A factory function takes a Model and returns the middleware function, eliminating the repeated code.

**Examples:**

```js
// controllers/handlerFactory.js
const catchAsync = require("../utils/catchAsync");
const AppError = require("../utils/appError");

exports.deleteOne = (Model) =>
  catchAsync(async (req, res, next) => {
    const doc = await Model.findByIdAndDelete(req.params.id);

    if (!doc) {
      return next(new AppError("No document found with that ID", 404));
    }

    res.status(204).json({ status: "success", data: null });
  });
```

```js
// controllers/tourController.js
const factory = require("./handlerFactory");

exports.deleteTour = factory.deleteOne(Tour);
// controllers/reviewController.js
exports.deleteReview = factory.deleteOne(Review);
// controllers/userController.js
exports.deleteUser = factory.deleteOne(User);
```

---

## 162. Factory Functions: Update and Create

**What is this:** Extending the handler factory with `updateOne` and `createOne` factory functions.

**Description:** The same pattern as `deleteOne` — a factory takes a Model and returns the appropriate middleware. Update uses `findByIdAndUpdate`; create uses `Model.create`.

**Examples:**

```js
// controllers/handlerFactory.js
exports.updateOne = (Model) =>
  catchAsync(async (req, res, next) => {
    const doc = await Model.findByIdAndUpdate(req.params.id, req.body, {
      new: true,
      runValidators: true,
    });

    if (!doc) return next(new AppError("No document found with that ID", 404));

    res.status(200).json({ status: "success", data: { data: doc } });
  });

exports.createOne = (Model) =>
  catchAsync(async (req, res, next) => {
    const doc = await Model.create(req.body);
    res.status(201).json({ status: "success", data: { data: doc } });
  });
```

```js
// controllers/tourController.js
exports.updateTour = factory.updateOne(Tour);
exports.createTour = factory.createOne(Tour);
```

---

## 163. Factory Functions: Reading

**What is this:** Adding `getOne` and `getAll` factory functions to complete the CRUD factory.

**Description:** `getOne` accepts an optional `popOptions` argument for populate. `getAll` supports a filter object (for nested routes). Both handle the 404 case and follow the same pattern.

**Examples:**

```js
// controllers/handlerFactory.js
exports.getOne = (Model, popOptions) =>
  catchAsync(async (req, res, next) => {
    let query = Model.findById(req.params.id);
    if (popOptions) query = query.populate(popOptions);

    const doc = await query;
    if (!doc) return next(new AppError("No document found with that ID", 404));

    res.status(200).json({ status: "success", data: { data: doc } });
  });

exports.getAll = (Model) =>
  catchAsync(async (req, res, next) => {
    let filter = {};
    if (req.params.tourId) filter = { tour: req.params.tourId };

    const features = new APIFeatures(Model.find(filter), req.query)
      .filter()
      .sort()
      .limitFields()
      .paginate();

    const docs = await features.query;
    res
      .status(200)
      .json({ status: "success", results: docs.length, data: { data: docs } });
  });
```

```js
// controllers/tourController.js
exports.getTour = factory.getOne(Tour, { path: "reviews" });
exports.getAllTours = factory.getAll(Tour);
```

---

## 164. Adding a /me Endpoint

**What is this:** Adding a `GET /api/v1/users/me` route that returns the currently logged-in user's own data.

**Description:** The `/me` endpoint reuses the `getOne` factory. A small middleware (`getMe`) sets `req.params.id = req.user.id` before the factory handler runs, so it reads the authenticated user's id rather than a URL param.

**Examples:**

```js
// controllers/userController.js
exports.getMe = (req, res, next) => {
  req.params.id = req.user.id; // use logged-in user's id as the param
  next();
};

exports.getUser = factory.getOne(User);
```

```js
// routes/userRoutes.js
router.get(
  "/me",
  authController.protect,
  userController.getMe,
  userController.getUser,
);
```

---

## 165. Adding Missing Authentication and Authorization

**What is this:** Applying `protect` and `restrictTo` to all routes that require authentication or specific roles.

**Description:** A full audit of all routes to ensure every sensitive operation requires login and that destructive operations (delete, update) are restricted to appropriate roles.

**Examples:**

```js
// routes/tourRoutes.js
router.use(authController.protect); // all routes below require login

router
  .route("/")
  .get(tourController.getAllTours)
  .post(
    authController.restrictTo("admin", "lead-guide"),
    tourController.createTour,
  );

router
  .route("/:id")
  .get(tourController.getTour)
  .patch(
    authController.restrictTo("admin", "lead-guide"),
    tourController.updateTour,
  )
  .delete(
    authController.restrictTo("admin", "lead-guide"),
    tourController.deleteTour,
  );
```

```js
// routes/reviewRoutes.js
router.use(authController.protect);
router
  .route("/:id")
  .get(reviewController.getReview)
  .patch(
    authController.restrictTo("user", "admin"),
    reviewController.updateReview,
  )
  .delete(
    authController.restrictTo("user", "admin"),
    reviewController.deleteReview,
  );
```

---

## 166. Importing Review and User Data

**What is this:** Extending the data import script to also seed users and reviews from JSON files.

**Description:** The existing import script is extended to handle all three models. The import order matters: users first (tours reference them as guides), then tours, then reviews (they reference both).

**Examples:**

```js
// dev-data/import-dev-data.js
const Tour = require("../../models/tourModel");
const User = require("../../models/userModel");
const Review = require("../../models/reviewModel");

const tours = JSON.parse(fs.readFileSync(`${__dirname}/tours.json`, "utf-8"));
const users = JSON.parse(fs.readFileSync(`${__dirname}/users.json`, "utf-8"));
const reviews = JSON.parse(
  fs.readFileSync(`${__dirname}/reviews.json`, "utf-8"),
);

const importData = async () => {
  await User.create(users, { validateBeforeSave: false }); // skip password hashing
  await Tour.create(tours);
  await Review.create(reviews);
  console.log("Data successfully loaded!");
  process.exit();
};

const deleteData = async () => {
  await Tour.deleteMany();
  await User.deleteMany();
  await Review.deleteMany();
  console.log("Data successfully deleted!");
  process.exit();
};
```

---

## 167. Improving Read Performance with Indexes

**What is this:** Adding MongoDB indexes to fields that are frequently queried or sorted to dramatically speed up reads.

**Description:** Without an index, MongoDB does a full collection scan for every query. An index pre-sorts a field so queries on it are O(log n) instead of O(n). Every `_id` has an index by default; you add indexes for fields you frequently filter or sort on.

**Examples:**

```js
// models/tourModel.js
// Compound index for common filter: price ascending + ratings descending
tourSchema.index({ price: 1, ratingsAverage: -1 });

// Single field index for slug lookups
tourSchema.index({ slug: 1 });

// 2dsphere index required for geospatial queries
tourSchema.index({ startLocation: "2dsphere" });
```

```js
// models/reviewModel.js
// Compound unique index — prevents one user leaving multiple reviews on one tour
reviewSchema.index({ tour: 1, user: 1 }, { unique: true });
```

---

## 168. Calculating Average Rating on Tours - Part 1

**What is this:** Writing a static method on the Review model that uses the aggregation pipeline to calculate and update a tour's average rating and count.

**Description:** Whenever a review is created, updated, or deleted, the tour's `ratingsAverage` and `ratingsQuantity` fields should update automatically. A static method on the Review schema runs the calculation and patches the tour.

**Examples:**

```js
// models/reviewModel.js
reviewSchema.statics.calcAverageRatings = async function (tourId) {
  const stats = await this.aggregate([
    { $match: { tour: tourId } },
    {
      $group: {
        _id: "$tour",
        nRating: { $sum: 1 },
        avgRating: { $avg: "$rating" },
      },
    },
  ]);

  if (stats.length > 0) {
    await Tour.findByIdAndUpdate(tourId, {
      ratingsQuantity: stats[0].nRating,
      ratingsAverage: stats[0].avgRating,
    });
  } else {
    await Tour.findByIdAndUpdate(tourId, {
      ratingsQuantity: 0,
      ratingsAverage: 4.5, // reset to default
    });
  }
};

// Call after every new review is saved
reviewSchema.post("save", function () {
  this.constructor.calcAverageRatings(this.tour);
  // 'this' = the current review; 'this.constructor' = the Review model
});
```

---

## 169. Calculating Average Rating on Tours - Part 2

**What is this:** Triggering the rating recalculation after update and delete operations (which use `findOneAndUpdate` / `findOneAndDelete`).

**Description:** `post('save')` only fires on `.save()` and `.create()`. For updates and deletes (which go through query middleware), you need `pre` to get the document and `post` to call the static method after the operation completes.

**Examples:**

```js
// models/reviewModel.js

// For findByIdAndUpdate and findByIdAndDelete:
reviewSchema.pre(/^findOneAnd/, async function (next) {
  // 'this' is the query — execute it to get the document before the change
  this.r = await this.findOne(); // save to 'this' to pass to post hook
  next();
});

reviewSchema.post(/^findOneAnd/, async function () {
  // 'this.r' is the document retrieved in the pre hook
  // The update/delete has now happened — recalculate
  await this.r.constructor.calcAverageRatings(this.r.tour);
});
```

---

## 170. Preventing Duplicate Reviews

**What is this:** Using a unique compound index to ensure each user can only leave one review per tour.

**Description:** A compound index on `{ tour, user }` with `unique: true` tells MongoDB to reject any document where the same `tour` + `user` combination already exists. MongoDB enforces this at the database level.

**Examples:**

```js
// models/reviewModel.js — added in lesson 167, shown in context here
reviewSchema.index({ tour: 1, user: 1 }, { unique: true });

// If a user tries to review the same tour twice:
// MongoError: E11000 duplicate key error
// → Caught by the handleDuplicateFieldsDB error handler (lesson 120)
// → Responds: "Duplicate field value: ... Please use another value!"
```

---

## 171. Geospatial Queries: Finding Tours Within Radius

**What is this:** Using MongoDB's `$geoWithin` and `$centerSphere` operators to find all tours whose start location is within a given distance from a point.

**Description:** MongoDB can perform geospatial queries on fields indexed with `2dsphere`. Given a centre point (lat/lng) and a radius, `$geoWithin` returns all documents whose location falls within that circle.

**Examples:**

```js
// controllers/tourController.js
exports.getToursWithin = catchAsync(async (req, res, next) => {
  const { distance, latlng, unit } = req.params;
  const [lat, lng] = latlng.split(",");

  if (!lat || !lng) {
    return next(
      new AppError(
        "Please provide latitude and longitude in format lat,lng.",
        400,
      ),
    );
  }

  // Convert distance to radians: divide by Earth's radius
  const radius = unit === "mi" ? distance / 3963.2 : distance / 6378.1;

  const tours = await Tour.find({
    startLocation: {
      $geoWithin: {
        $centerSphere: [[lng, lat], radius], // [longitude, latitude]
      },
    },
  });

  res
    .status(200)
    .json({ status: "success", results: tours.length, data: { data: tours } });
});
```

```js
// routes/tourRoutes.js
router
  .route("/tours-within/:distance/center/:latlng/unit/:unit")
  .get(tourController.getToursWithin);
// GET /api/v1/tours/tours-within/250/center/34.111745,-118.113491/unit/mi
```

---

## 172. Geospatial Aggregation: Calculating Distances

**What is this:** Using the `$geoNear` aggregation stage to calculate the distance from a given point to each tour's start location.

**Description:** `$geoNear` must be the first stage in the pipeline. It calculates distances from a given point to each document (using the `2dsphere` index) and adds a distance field to each result. The distance is returned in metres by default.

**Examples:**

```js
// controllers/tourController.js
exports.getDistances = catchAsync(async (req, res, next) => {
  const { latlng, unit } = req.params;
  const [lat, lng] = latlng.split(",");

  if (!lat || !lng) {
    return next(
      new AppError(
        "Please provide latitude and longitude in format lat,lng.",
        400,
      ),
    );
  }

  const multiplier = unit === "mi" ? 0.000621371 : 0.001;
  // Convert metres → miles or km

  const distances = await Tour.aggregate([
    {
      $geoNear: {
        near: {
          type: "Point",
          coordinates: [Number(lng), Number(lat)],
        },
        distanceField: "distance", // name of the output field
        distanceMultiplier: multiplier, // multiply raw metres by this
      },
    },
    {
      $project: { distance: 1, name: 1 }, // only return name and distance
    },
  ]);

  res.status(200).json({ status: "success", data: { data: distances } });
});
```

```js
// routes/tourRoutes.js
router.route("/distances/:latlng/unit/:unit").get(tourController.getDistances);
// GET /api/v1/tours/distances/34.111745,-118.113491/unit/km
```

---

## 173. Creating API Documentation Using Postman

**What is this:** Using Postman's built-in documentation features to generate and publish API docs from your saved request collection.

**Description:** Postman can auto-generate documentation from a collection. Each request gets a description, example responses are saved from real requests, and the whole thing is published as a public webpage — no manual doc writing needed.

**Steps:**

1. Organize requests into folders by resource (Tours, Users, Reviews, etc.)
2. Add a description to each request (supports Markdown)
3. Save example responses: send a request → "Save Response" → "Save as example"
4. In Postman: Collection → View Documentation → Publish
5. A public URL is generated — share it as your API reference

**Best practices:**

- Document every endpoint — method, URL, params, body, response
- Include example request bodies for POST/PATCH routes
- Show both success (200/201) and error (400/404) example responses
- Version your collection name to match your API version (`v1`)

---

# Section 12: Server-Side Rendering with Pug Templates

---

## 174. Section Intro

**What is this:** Overview of this section — adding a server-rendered frontend to the Natours API using Pug templates.

**Description:** Instead of a separate frontend framework, this section uses Pug (a Node.js template engine) to render HTML on the server and serve complete pages. Topics include routing for views, building layouts, rendering dynamic data, integrating Mapbox, and wiring frontend login/logout to the API.

---

## 175. Recap: Server-Side vs Client-Side Rendering

**What is this:** A quick recap of the two rendering strategies and why SSR is used in this section.

**Description:** SSR (Server-Side Rendering) means the server builds the full HTML page and sends it to the browser. CSR (Client-Side Rendering) means the browser downloads a JS bundle and builds the UI itself. This section uses SSR with Pug for simplicity — the API still exists in parallel.

|                 | SSR                      | CSR                     |
| --------------- | ------------------------ | ----------------------- |
| Who builds HTML | Server                   | Browser (JS)            |
| First load      | Faster to paint          | Slower (JS bundle)      |
| SEO             | Good                     | Harder                  |
| Tech            | Pug, EJS, Handlebars     | React, Angular, Vue     |
| Data flow       | Server renders with data | Client fetches from API |

---

## 176. Setting up Pug in Express

**What is this:** Configuring Express to use Pug as its view engine and serve rendered HTML pages.

**Description:** Express has built-in support for template engines. You set the engine with `app.set('view engine', 'pug')` and the views directory with `app.set('views', ...)`. Views are `.pug` files stored in the `views/` folder.

**Examples:**

```js
// app.js
const path = require("path");

app.set("view engine", "pug");
app.set("views", path.join(__dirname, "views"));
// path.join is safer than string concatenation — handles OS path separators
```

```js
// A view route — renders views/overview.pug
app.get("/", (req, res) => {
  res.render("overview", { title: "All Tours" });
  // res.render(templateName, locals)
  // locals = variables passed into the template
});
```

---

## 177. First Steps with Pug

**What is this:** Learning Pug syntax — indentation-based HTML, variables, iteration, and conditionals.

**Description:** Pug uses indentation instead of closing tags. Variables passed from `res.render()` are available directly in the template. It supports JavaScript expressions, loops, and conditionals natively.

**Examples:**

```pug
//- views/overview.pug
doctype html
html
  head
    title Natours | #{title}
  body
    h1= title
    //- #{varName} interpolates inline; tag= varName sets tag content

    //- Iteration
    ul
      each tour in tours
        li= tour.name

    //- Conditionals
    if user
      p Welcome, #{user.name}!
    else
      a(href="/login") Log in
```

```js
// Pass data to the template
res.render("overview", { title: "All Tours", tours, user });
```

---

## 178. Creating Our Base Template

**What is this:** Building a base Pug layout (`base.pug`) with a shared header, footer, and a `block` for page-specific content.

**Description:** Pug's template inheritance lets you define a base layout with `block` placeholders. Child templates extend the base and fill in the blocks. This avoids duplicating the header/footer on every page.

**Examples:**

```pug
//- views/base.pug
doctype html
html
  head
    meta(charset='UTF-8')
    title Natours | #{title}
    link(rel='stylesheet' href='/css/style.css')

  body
    header.header
      nav.nav
        a(href='/') Natours
        //- Navigation links

    main.main
      block content
      //- Child templates fill this block

    footer.footer
      p &copy; Natours. All rights reserved.

    script(src='/js/index.js')
```

---

## 179. Including Files into Pug Templates

**What is this:** Using Pug's `include` to pull reusable partial templates into a parent template.

**Description:** Large templates can be broken into partials (header, footer, nav) and included where needed. `include` inlines the content of another Pug file at that position.

**Examples:**

```pug
//- views/base.pug
doctype html
html
  head
    include _head.pug
    //- _head.pug contains <meta>, <title>, <link> tags

  body
    include _header.pug
    //- _header.pug contains the nav and header markup

    main.main
      block content

    include _footer.pug
```

```pug
//- views/_header.pug
header.header
  nav.nav.nav--tours
    a.nav__el(href='/') All tours
  .nav__user
    if user
      a.nav__el(href='/me') #{user.name.split(' ')[0]}
    else
      a.nav__el(href='/login') Log in
```

---

## 180. Extending Our Base Template with Blocks

**What is this:** Using Pug's template inheritance — child templates extend `base.pug` and override its `block content`.

**Description:** Child templates use `extends` to inherit the base layout and `block content` to inject their own HTML into the placeholder. The header, footer, and shared assets are automatically included from the base.

**Examples:**

```pug
//- views/overview.pug
extends base

block content
  main.main
    .card-container
      each tour in tours
        .card
          .card__header
            h3.card__heading #{tour.name}
          .card__details
            p.card__text #{tour.summary}
            span.card__detail-box #{tour.duration} days
            span.card__detail-box Up to #{tour.maxGroupSize} people
          .card__footer
            a.btn.btn--green(href=`/tour/${tour.slug}`) Details
```

---

## 181. Setting up the Project Structure

**What is this:** Organising the views folder, adding view routes, and a view controller to separate rendering logic.

**Description:** View routes are separate from API routes. A `viewController.js` handles rendering pages. Static assets (CSS, images, JS) are served from the `public/` folder.

**Examples:**

```
views/
├── base.pug
├── overview.pug
├── tour.pug
├── login.pug
├── account.pug
└── _header.pug
```

```js
// controllers/viewsController.js
const Tour = require("../models/tourModel");
const catchAsync = require("../utils/catchAsync");

exports.getOverview = catchAsync(async (req, res) => {
  const tours = await Tour.find();
  res.render("overview", { title: "All Tours", tours });
});

exports.getTour = catchAsync(async (req, res, next) => {
  const tour = await Tour.findOne({ slug: req.params.slug }).populate({
    path: "reviews",
    fields: "review rating user",
  });
  if (!tour) return next(new AppError("There is no tour with that name.", 404));
  res.render("tour", { title: `${tour.name} Tour`, tour });
});
```

```js
// routes/viewRoutes.js
const express = require("express");
const viewsController = require("../controllers/viewsController");
const router = express.Router();

router.get("/", viewsController.getOverview);
router.get("/tour/:slug", viewsController.getTour);

module.exports = router;
```

```js
// app.js
const viewRouter = require("./routes/viewRoutes");
app.use("/", viewRouter);
```

---

## 182. Building the Tour Overview - Part 1

**What is this:** Rendering the tour cards on the overview page using data from the database.

**Description:** The overview page fetches all tours from MongoDB and passes them to the Pug template. The template iterates over the tours array and renders a card for each one using the tour's fields.

**Examples:**

```js
// controllers/viewsController.js
exports.getOverview = catchAsync(async (req, res) => {
  const tours = await Tour.find();
  res.render("overview", { title: "All Tours", tours });
});
```

```pug
//- views/overview.pug
extends base

block content
  main.main
    .card-container
      each tour in tours
        .card
          .card__header
            img.card__picture(src=`/img/tours/${tour.imageCover}` alt=`${tour.name}`)
            h3.card__heading
              span #{tour.name}
          .card__details
            h4.card__sub-heading #{tour.difficulty} #{tour.duration}-day tour
            p.card__text #{tour.summary}
          .card__footer
            p
              span.card__footer-value #{tour.price}
              span.card__footer-text  per person
            a.btn.btn--green.btn--small(href=`/tour/${tour.slug}`) Details
```

---

## 183. Building the Tour Overview - Part 2

**What is this:** Completing the tour overview page — adding ratings, group size, and guide count to each card.

**Description:** Additional fields are pulled from the tour document and displayed in the card footer. Pug expressions compute values inline (e.g. number of guides).

**Examples:**

```pug
//- Inside each card
.card__details
  h4.card__sub-heading #{tour.difficulty} #{tour.duration}-day tour
  p.card__text #{tour.summary}
  .card__data
    span.card__data--icon ⭐ #{tour.ratingsAverage} ratings (#{tour.ratingsQuantity})
    span.card__data--icon 👥 #{tour.maxGroupSize} people
    span.card__data--icon 🚩 #{tour.guides.length} guides
```

---

## 184. Building the Tour Page - Part 1

**What is this:** Creating the individual tour detail page with header, description, and stats section.

**Description:** The tour page renders a single tour document. The slug from the URL is used to query the tour (slug is SEO-friendly). The page shows the tour's images, duration, difficulty, and description.

**Examples:**

```js
// controllers/viewsController.js
exports.getTour = catchAsync(async (req, res, next) => {
  const tour = await Tour.findOne({ slug: req.params.slug }).populate({
    path: "reviews",
    select: "review rating user",
  });

  if (!tour) return next(new AppError("There is no tour with that name.", 404));
  res.render("tour", { title: `${tour.name} Tour`, tour });
});
```

```pug
//- views/tour.pug
extends base

block content
  section.section-header
    .section-header__hero
      img.section-header__img(src=`/img/tours/${tour.imageCover}`)
    .section-header__heading
      h1 #{tour.name} tour

  section.section-description
    .overview-box
      .overview-box__detail
        span.overview-box__label Next date
        span.overview-box__text #{tour.startDates[0].toLocaleString('en-us', {month: 'long', year: 'numeric'})}
      .overview-box__detail
        span.overview-box__label Difficulty
        span.overview-box__text #{tour.difficulty}
      .overview-box__detail
        span.overview-box__label Participants
        span.overview-box__text #{tour.maxGroupSize} people
      .overview-box__detail
        span.overview-box__label Rating
        span.overview-box__text #{tour.ratingsAverage} / 5
```

---

## 185. Building the Tour Page - Part 2

**What is this:** Completing the tour page — adding the guides section, tour stops, and reviews.

**Description:** The tour page's lower sections display the tour guides with their photos and roles, the individual tour stop locations and descriptions, and all reviews for the tour rendered as cards.

**Examples:**

```pug
//- Guides section
.overview-box__group
  h2.heading-secondary.ma-bt-lg Your tour guides
  each guide in tour.guides
    .overview-box__detail
      img.overview-box__img(src=`/img/users/${guide.photo}` alt=`${guide.name}`)
      - const role = guide.role === 'lead-guide' ? 'Lead guide' : 'Tour guide'
      span.overview-box__label #{role}
      span.overview-box__text #{guide.name}

//- Reviews section
section.section-reviews
  .reviews
    each review in tour.reviews
      .reviews__card
        .reviews__avatar
          img.reviews__avatar-img(src=`/img/users/${review.user.photo}`)
          h6.reviews__user #{review.user.name}
        p.reviews__text #{review.review}
        .reviews__rating
          each star in [1,2,3,4,5]
            svg.reviews__star(class=`reviews__star--${review.rating >= star ? 'active' : 'inactive'}`)
```

---

## 186. Including a Map with Mapbox - Part 1

**What is this:** Embedding a Mapbox map on the tour page to show the tour's locations as pins on an interactive map.

**Description:** Mapbox GL JS is a client-side JavaScript library for interactive maps. Tour location coordinates stored in the database are passed to the frontend via a `data` attribute on a DOM element, then read by a client-side JS file that initialises the map.

**Examples:**

```pug
//- views/tour.pug — pass locations to frontend JS via data attribute
#map(data-locations=`${JSON.stringify(tour.locations)}`)
```

```js
// public/js/mapbox.js — client-side JS (not Node.js)
export const displayMap = (locations) => {
  mapboxgl.accessToken = "YOUR_MAPBOX_TOKEN";

  const map = new mapboxgl.Map({
    container: "map",
    style: "mapbox://styles/mapbox/light-v10",
    scrollZoom: false,
  });

  const bounds = new mapboxgl.LngLatBounds();

  locations.forEach((loc) => {
    // Add a marker at each location
    new mapboxgl.Marker({ color: "#55c57a" })
      .setLngLat(loc.coordinates)
      .addTo(map);

    // Add a popup
    new mapboxgl.Popup({ offset: 30 })
      .setLngLat(loc.coordinates)
      .setHTML(`<p>Day ${loc.day}: ${loc.description}</p>`)
      .addTo(map);

    bounds.extend(loc.coordinates);
  });

  map.fitBounds(bounds, {
    padding: { top: 200, bottom: 150, left: 100, right: 100 },
  });
};
```

---

## 187. Including a Map with Mapbox - Part 2

**What is this:** Bundling the client-side JS with Parcel and wiring the map into the page entry point.

**Description:** Client-side JS modules (mapbox, login, etc.) are bundled with Parcel (a zero-config bundler) so you can use ES modules in the browser. The `index.js` entry point reads the DOM, imports the map module, and calls it with the location data.

**Examples:**

```bash
npm install parcel --save-dev
```

```js
// public/js/index.js — entry point for browser JS
import { displayMap } from "./mapbox.js";

// Read locations from the data attribute set in Pug
const mapEl = document.getElementById("map");
if (mapEl) {
  const locations = JSON.parse(mapEl.dataset.locations);
  displayMap(locations);
}
```

```json
// package.json
{
  "scripts": {
    "watch:js": "parcel watch ./public/js/index.js --out-dir ./public/js --out-file bundle.js",
    "build:js": "parcel build ./public/js/index.js --out-dir ./public/js --out-file bundle.js"
  }
}
```

```pug
//- base.pug — load the bundle
script(src='/js/bundle.js')
```

---

## 188. Building the Login Screen

**What is this:** Creating the login page Pug template with an email/password form.

**Description:** The login page is a simple form that submits to the API via JavaScript (not a traditional HTML form submit). The form fields are styled and the submit button triggers a frontend function that calls `POST /api/v1/users/login`.

**Examples:**

```js
// routes/viewRoutes.js
router.get("/login", viewsController.getLoginForm);
```

```js
// controllers/viewsController.js
exports.getLoginForm = (req, res) => {
  res.render("login", { title: "Log into your account" });
};
```

```pug
//- views/login.pug
extends base

block content
  main.main
    .login-form
      h2.heading-secondary.ma-bt-lg Log into your account
      form.form.form--login
        .form__group
          label.form__label(for='email') Email address
          input#email.form__input(type='email' placeholder='you@example.com' required)
        .form__group.ma-bt-md
          label.form__label(for='password') Password
          input#password.form__input(type='password' placeholder='••••••••' minlength='8' required)
        .form__group
          button.btn.btn--green.btn--login Log in
```

---

## 189. Logging in Users with Our API - Part 1

**What is this:** Writing the frontend JavaScript that intercepts the login form submission and calls the login API endpoint with `fetch`.

**Description:** Instead of the form doing a traditional POST (which causes a page reload), you prevent the default submit, collect the values, and use the Fetch API to call `/api/v1/users/login`. On success, you redirect to the homepage.

**Examples:**

```js
// public/js/login.js
import { showAlert } from "./alerts.js";

export const login = async (email, password) => {
  try {
    const res = await fetch("/api/v1/users/login", {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify({ email, password }),
    });

    const data = await res.json();

    if (data.status === "success") {
      showAlert("success", "Logged in successfully!");
      window.setTimeout(() => location.assign("/"), 1500);
    } else {
      showAlert("error", data.message);
    }
  } catch (err) {
    showAlert("error", err.message);
  }
};
```

---

## 190. Logging in Users with Our API - Part 2

**What is this:** Wiring the login function to the form's submit event and building the `showAlert` utility.

**Description:** The form's submit event listener calls `login(email, password)`. A reusable `showAlert` function injects a temporary notification banner into the DOM.

**Examples:**

```js
// public/js/alerts.js
export const hideAlert = () => {
  const el = document.querySelector(".alert");
  if (el) el.parentElement.removeChild(el);
};

export const showAlert = (type, msg) => {
  hideAlert();
  const markup = `<div class="alert alert--${type}">${msg}</div>`;
  document.querySelector("body").insertAdjacentHTML("afterbegin", markup);
  window.setTimeout(hideAlert, 5000);
};
```

```js
// public/js/index.js
import { login } from "./login.js";

const loginForm = document.querySelector(".form--login");
if (loginForm) {
  loginForm.addEventListener("submit", (e) => {
    e.preventDefault();
    const email = document.getElementById("email").value;
    const password = document.getElementById("password").value;
    login(email, password);
  });
}
```

---

## 191. Logging in Users with Our API - Part 3

**What is this:** Reading the JWT from the cookie on the server and attaching the current user to `res.locals` so Pug templates can access it.

**Description:** When the API sends the JWT as a cookie, the browser sends it back on every request. A `isLoggedIn` middleware reads the cookie, verifies it, and attaches the user to `res.locals.user` — making it available in all Pug templates without explicitly passing it.

**Examples:**

```bash
npm install cookie-parser
```

```js
// app.js
const cookieParser = require("cookie-parser");
app.use(cookieParser()); // parses cookies into req.cookies
```

```js
// controllers/authController.js
exports.isLoggedIn = async (req, res, next) => {
  if (req.cookies.jwt) {
    try {
      const decoded = await promisify(jwt.verify)(
        req.cookies.jwt,
        process.env.JWT_SECRET,
      );
      const currentUser = await User.findById(decoded.id);

      if (!currentUser || currentUser.changedPasswordAfter(decoded.iat)) {
        return next();
      }

      res.locals.user = currentUser;
      // res.locals is available in all Pug templates as 'user'
      return next();
    } catch (err) {
      return next();
    }
  }
  next();
};
```

```js
// routes/viewRoutes.js — use isLoggedIn (not protect) for view routes
router.use(authController.isLoggedIn);
```

```pug
//- base.pug — user is now available in all templates
if user
  a.nav__el(href='/me') #{user.name}
else
  a.nav__el(href='/login') Log in
```

---

## 192. Logging out Users

**What is this:** Implementing logout by overwriting the JWT cookie with an expired dummy cookie, effectively clearing the session.

**Description:** Since the JWT is in an HTTP-only cookie, JavaScript can't delete it directly. Instead, the logout route sends a new cookie with the same name but a fake value and an immediate expiry — the browser overwrites the old cookie and the user is no longer authenticated.

**Examples:**

```js
// controllers/authController.js
exports.logout = (req, res) => {
  res.cookie("jwt", "loggedout", {
    expires: new Date(Date.now() + 10 * 1000), // expires in 10 seconds
    httpOnly: true,
  });
  res.status(200).json({ status: "success" });
};
```

```js
// routes/userRoutes.js
router.get("/logout", authController.logout);
```

```js
// public/js/index.js — client-side logout button
import { logout } from "./login.js";

const logOutBtn = document.querySelector(".nav__el--logout");
if (logOutBtn) logOutBtn.addEventListener("click", logout);
```

```js
// public/js/login.js
export const logout = async () => {
  const res = await fetch("/api/v1/users/logout");
  const data = await res.json();
  if (data.status === "success") location.reload(true); // force reload from server
};
```

---

## 193. Rendering Error Pages

**What is this:** Sending rendered HTML error pages (not JSON) when an error occurs on a view route.

**Description:** The global error handler currently sends JSON for all errors. View routes need HTML error pages. You check `req.originalUrl` to determine if it's an API or view request and respond accordingly.

**Examples:**

```js
// controllers/errorController.js
module.exports = (err, req, res, next) => {
  err.statusCode = err.statusCode || 500;
  err.status = err.status || "error";

  if (req.originalUrl.startsWith("/api")) {
    // API request — send JSON
    if (process.env.NODE_ENV === "development") return sendErrorDev(err, res);
    return sendErrorProd(err, res);
  }

  // View request — render error page
  res.status(err.statusCode).render("error", {
    title: "Something went wrong!",
    msg: err.message,
  });
};
```

```pug
//- views/error.pug
extends base

block content
  main.main
    .error
      .error__title
        h2.heading-secondary.heading--error Uh oh! Something went wrong!
        h2.error__emoji 🤦‍♂️
      .error__msg #{msg}
```

---

## 194. Building the User Account Page

**What is this:** Creating the `/me` page that shows the logged-in user's profile with their photo, name, and email.

**Description:** The account page is a protected view — only accessible to logged-in users. It displays the user's current data and forms to update name/email and password.

**Examples:**

```js
// routes/viewRoutes.js
router.get("/me", authController.protect, viewsController.getAccount);
```

```js
// controllers/viewsController.js
exports.getAccount = (req, res) => {
  res.render("account", { title: "Your account" });
  // req.user is set by protect middleware
  // res.locals.user is set by isLoggedIn middleware
};
```

```pug
//- views/account.pug
extends base

block content
  main.main
    .user-view
      nav.user-view__menu
        ul.side-nav
          li.side-nav--active
            a(href='#') Settings
      .user-view__content
        .user-view__form-container
          h2.heading-secondary.ma-bt-md Your account settings
          form.form.form-user-data
            .form__group
              label.form__label(for='name') Name
              input#name.form__input(type='text' value=`${user.name}` required)
            .form__group.ma-bt-md
              label.form__label(for='email') Email
              input#email.form__input(type='email' value=`${user.email}` required)
            .form__group.form__photo-upload
              img.form__user-photo(src=`/img/users/${user.photo}` alt='User photo')
            button.btn.btn--small.btn--green.right Save settings
```

---

## 195. Updating User Data

**What is this:** Handling the account settings form submission using a traditional HTML form POST (without JavaScript).

**Description:** As an alternative to the fetch approach, a regular HTML form can POST directly to an Express route. Express reads `req.body` (with `express.urlencoded` middleware) and updates the user. This approach doesn't require client-side JS.

**Examples:**

```js
// app.js — parse URL-encoded form data
app.use(express.urlencoded({ extended: true, limit: "10kb" }));
```

```js
// routes/viewRoutes.js
router.post(
  "/submit-user-data",
  authController.protect,
  viewsController.updateUserData,
);
```

```js
// controllers/viewsController.js
const User = require("../models/userModel");

exports.updateUserData = catchAsync(async (req, res, next) => {
  const updatedUser = await User.findByIdAndUpdate(
    req.user.id,
    { name: req.body.name, email: req.body.email },
    { new: true, runValidators: true },
  );

  res.status(200).render("account", {
    title: "Your account",
    user: updatedUser,
  });
});
```

```pug
//- views/account.pug — form with action and method
form.form.form-user-data(action='/submit-user-data' method='POST')
```

---

## 196. Updating User Data with Our API

**What is this:** Replacing the HTML form POST approach with a fetch-based API call to `PATCH /api/v1/users/updateMe`, supporting file uploads with `FormData`.

**Description:** Using the API approach allows file uploads (profile photo) alongside text data. `FormData` is used to bundle the file and text fields into a single multipart request that the API's `multer` middleware can process.

**Examples:**

```js
// public/js/updateSettings.js
import { showAlert } from "./alerts.js";

export const updateSettings = async (data, type) => {
  // type = 'data' | 'password'
  const url =
    type === "password"
      ? "/api/v1/users/updateMyPassword"
      : "/api/v1/users/updateMe";

  try {
    const res = await fetch(url, {
      method: "PATCH",
      body: data, // FormData for data, JSON string for password
    });

    const result = await res.json();

    if (result.status === "success") {
      showAlert("success", `${type.toUpperCase()} updated successfully!`);
    } else {
      showAlert("error", result.message);
    }
  } catch (err) {
    showAlert("error", err.message);
  }
};
```

```js
// public/js/index.js
import { updateSettings } from "./updateSettings.js";

const userDataForm = document.querySelector(".form-user-data");
if (userDataForm) {
  userDataForm.addEventListener("submit", (e) => {
    e.preventDefault();
    const form = new FormData();
    form.append("name", document.getElementById("name").value);
    form.append("email", document.getElementById("email").value);
    form.append("photo", document.getElementById("photo").files[0]);
    updateSettings(form, "data");
  });
}
```

---

## 197. Updating User Password with Our API

**What is this:** Wiring the password update form on the account page to call `PATCH /api/v1/users/updateMyPassword` via fetch.

**Description:** The password update sends JSON (not FormData) because there's no file. After a successful update the API returns a new JWT — the cookie is updated automatically by the browser since it's HTTP-only.

**Examples:**

```js
// public/js/index.js
const userPasswordForm = document.querySelector(".form-user-password");
if (userPasswordForm) {
  userPasswordForm.addEventListener("submit", async (e) => {
    e.preventDefault();

    document.querySelector(".btn--save-password").textContent = "Updating...";

    const passwordCurrent = document.getElementById("password-current").value;
    const password = document.getElementById("password").value;
    const passwordConfirm = document.getElementById("password-confirm").value;

    await updateSettings(
      JSON.stringify({ passwordCurrent, password, passwordConfirm }),
      "password",
    );

    // Clear password fields after update
    document.getElementById("password-current").value = "";
    document.getElementById("password").value = "";
    document.getElementById("password-confirm").value = "";
    document.querySelector(".btn--save-password").textContent = "Save password";
  });
}
```

````pug
//- views/account.pug — password form
form.form.form-user-password
  .form__group
    label.form__label(for='password-current') Current password
    input#password-current.form__input(type='password' placeholder='••••••••' minlength='8')
  .form__group
    label.form__label(for='password') New password
    input#password.form__input(type='password' placeholder='••••••••' minlength='8')
  .form__group.ma-bt-lg
    label.form__label(for='password-confirm') Confirm password
    input#password-confirm.form__input(type='password' placeholder='••••••••' minlength='8')
  .right
    button.btn.btn--small.btn--green.btn--save-password Save password
---

# Section 13: Advanced Features: Payments, Email, File Uploads

## 198. Section Intro

**What is this:** Overview of the final section — adding image uploads, email sending, and Stripe payments to the Natours app.

**Description:** This section rounds out the full-stack application by integrating three advanced production features: file uploads with Multer and Sharp, transactional emails with Nodemailer and SendGrid, and credit card payments with Stripe. By the end, users can upload profile photos, receive welcome and password-reset emails, and book tours by paying with a credit card

## 199. Image Uploads Using Multer: Users

**What is this:** Adding profile photo upload support to the user update endpoint using the Multer middleware.

**Description:** Multer is a Node.js middleware for handling `multipart/form-data`, which is the encoding type used when a form submits files. It is configured with a storage engine (memory or disk) and a file filter, then mounted on the route that needs it.

**Key points:**

- `multer()` returns middleware that parses the `multipart/form-data` body
- `.single('photo')` tells Multer to accept one file under the field name `photo`
- The parsed file is available on `req.file`; text fields are on `req.body`
- Using `memoryStorage` keeps the file in a `Buffer` — ideal when a subsequent step (Sharp) will process the image before writing it

**Examples:**

```js
// utils/multer.js (or inline in userRouter.js)
const multer = require("multer");

const multerStorage = multer.memoryStorage(); // store as buffer

const multerFilter = (req, file, cb) => {
  if (file.mimetype.startsWith("image")) {
    cb(null, true);
  } else {
    cb(new AppError("Not an image! Please upload only images.", 400), false);
  }
};

const upload = multer({ storage: multerStorage, fileFilter: multerFilter });

exports.uploadUserPhoto = upload.single("photo");
````

```js
// routes/userRoutes.js
router.patch("/updateMe", uploadUserPhoto, resizeUserPhoto, updateMe);
```

---

## 200. Configuring Multer

**What is this:** Fine-tuning Multer's storage and filter options to control where files land and which file types are accepted.

**Description:** Multer can save directly to disk (`diskStorage`) or to memory (`memoryStorage`). Disk storage requires a `destination` and `filename` callback; memory storage skips the disk entirely and puts the file in `req.file.buffer`. Disk storage is useful when no processing is needed; memory storage is preferred when Sharp will resize the image before writing.

**Examples:**

```js
// Disk storage example (no processing needed)
const multerStorage = multer.diskStorage({
  destination: (req, file, cb) => {
    cb(null, "public/img/users");
  },
  filename: (req, file, cb) => {
    const ext = file.mimetype.split("/")[1]; // e.g. 'jpeg'
    cb(null, `user-${req.user.id}-${Date.now()}.${ext}`);
  },
});
```

```js
// Memory storage (when Sharp processing follows)
const multerStorage = multer.memoryStorage();
// File is available as req.file.buffer
```

---

## 201. Saving Image Name to Database

**What is this:** Persisting the uploaded photo filename to the user document in MongoDB after the image has been processed and saved to disk.

**Description:** After Multer (and optionally Sharp) handle the file, the resulting filename is attached to `req.file.filename`. The `updateMe` controller then includes `photo: req.file.filename` in the fields it passes to `findByIdAndUpdate`.

**Examples:**

```js
// controllers/userController.js
exports.updateMe = catchAsync(async (req, res, next) => {
  // 1) Error if user tries to update password
  if (req.body.password || req.body.passwordConfirm)
    return next(new AppError("This route is not for password updates.", 400));

  // 2) Filter out fields that should not be updated
  const filteredBody = filterObj(req.body, "name", "email");
  if (req.file) filteredBody.photo = req.file.filename;

  // 3) Update user document
  const updatedUser = await User.findByIdAndUpdate(req.user.id, filteredBody, {
    new: true,
    runValidators: true,
  });

  res.status(200).json({ status: "success", data: { user: updatedUser } });
});
```

---

## 202. Resizing Images

**What is this:** Using the Sharp library to resize and convert uploaded profile photos before writing them to disk.

**Description:** Sharp is a high-performance Node.js image processing library. It reads from `req.file.buffer` (memory storage), resizes to a square, converts to JPEG, sets quality, and writes the output file. The middleware runs between Multer and the controller.

**Key points:**

- `sharp(buffer)` creates a Sharp instance from a Buffer
- `.resize(w, h)` resizes; `.toFormat('jpeg')` converts; `.jpeg({ quality: 90 })` sets compression
- `.toFile(path)` writes to disk and returns a Promise — must be `await`ed
- Assign `req.file.filename` manually so the controller can save it to the DB

**Examples:**

```js
// controllers/userController.js
const sharp = require("sharp");

exports.resizeUserPhoto = catchAsync(async (req, res, next) => {
  if (!req.file) return next();

  req.file.filename = `user-${req.user.id}-${Date.now()}.jpeg`;

  await sharp(req.file.buffer)
    .resize(500, 500)
    .toFormat("jpeg")
    .jpeg({ quality: 90 })
    .toFile(`public/img/users/${req.file.filename}`);

  next();
});
```

---

## 203. Adding Image Uploads to Form

**What is this:** Wiring the account page's profile photo upload form on the frontend to send the file via `FormData` and the Fetch API.

**Description:** HTML forms with `enctype="multipart/form-data"` can submit files, but to use the API approach the frontend must build a `FormData` object, append the file input's `files[0]`, and send it as the fetch body. The browser automatically sets the `Content-Type` header to `multipart/form-data`.

**Examples:**

```js
// public/js/index.js
import { updateSettings } from "./updateSettings.js";

const userDataForm = document.querySelector(".form-user-data");
if (userDataForm) {
  userDataForm.addEventListener("submit", (e) => {
    e.preventDefault();
    const form = new FormData();
    form.append("name", document.getElementById("name").value);
    form.append("email", document.getElementById("email").value);
    form.append("photo", document.getElementById("photo").files[0]);
    updateSettings(form, "data");
  });
}
```

```pug
//- views/account.pug
form.form.form-user-data(enctype='multipart/form-data')
  .form__group
    label.form__label(for='photo') Profile photo
    input#photo.form__upload(type='file' accept='image/*' name='photo')
    img.form__user-photo(src=`/img/users/${user.photo}` alt='User photo')
```

---

## 204. Uploading Multiple Images: Tours

**What is this:** Extending Multer to accept multiple image fields on the tour update endpoint — one cover image and up to three detail images.

**Description:** `upload.fields()` accepts an array of field descriptors, each with a `name` and optional `maxCount`. Files land in `req.files` (plural) as an object keyed by field name, unlike `req.file` used by `.single()`.

**Examples:**

```js
// controllers/tourController.js
const upload = multer({
  storage: multer.memoryStorage(),
  fileFilter: multerFilter,
});

exports.uploadTourImages = upload.fields([
  { name: "imageCover", maxCount: 1 },
  { name: "images", maxCount: 3 },
]);
```

```js
// Accessing uploaded files
req.files.imageCover[0]; // the cover image File object
req.files.images; // array of up to 3 File objects
```

```js
// routes/tourRoutes.js
router
  .route("/:id")
  .patch(
    protect,
    restrictTo("admin", "lead-guide"),
    uploadTourImages,
    resizeTourImages,
    updateTour,
  );
```

---

## 205. Processing Multiple Images

**What is this:** Running Sharp on the tour cover image and each detail image to resize and save them before the controller updates the database.

**Description:** The resize middleware processes the cover synchronously (single file) and loops over the detail images with `Promise.all` so all Sharp operations complete before `next()` is called. Filenames are stored directly on `req.body` so `updateTour` picks them up without modification.

**Examples:**

```js
// controllers/tourController.js
exports.resizeTourImages = catchAsync(async (req, res, next) => {
  if (!req.files.imageCover || !req.files.images) return next();

  // 1) Cover image
  req.body.imageCover = `tour-${req.params.id}-${Date.now()}-cover.jpeg`;
  await sharp(req.files.imageCover[0].buffer)
    .resize(2000, 1333)
    .toFormat("jpeg")
    .jpeg({ quality: 90 })
    .toFile(`public/img/tours/${req.body.imageCover}`);

  // 2) Detail images
  req.body.images = [];
  await Promise.all(
    req.files.images.map(async (file, i) => {
      const filename = `tour-${req.params.id}-${Date.now()}-${i + 1}.jpeg`;
      await sharp(file.buffer)
        .resize(2000, 1333)
        .toFormat("jpeg")
        .jpeg({ quality: 90 })
        .toFile(`public/img/tours/${filename}`);
      req.body.images.push(filename);
    }),
  );

  next();
});
```

---

## 206. Building a Complex Email Handler

**What is this:** Creating a reusable `Email` class that can send different types of emails (welcome, password reset) using Nodemailer with different transports in dev vs production.

**Description:** Instead of scattered `nodemailer.createTransport()` calls, a class centralises the transport creation, template rendering, and send logic. In development it uses Mailtrap (a fake SMTP inbox) so emails never reach real addresses; in production it switches to SendGrid.

**Key points:**

- Constructor stores `to`, `firstName`, and `url` from the user object
- `newTransport()` returns a Nodemailer transporter based on `NODE_ENV`
- `send(template, subject)` renders a Pug template to HTML, then calls `transporter.sendMail()`
- `sendWelcome()` and `sendPasswordReset()` are public convenience methods

**Examples:**

```js
// utils/email.js
const nodemailer = require("nodemailer");
const pug = require("pug");
const { htmlToText } = require("html-to-text");

module.exports = class Email {
  constructor(user, url) {
    this.to = user.email;
    this.firstName = user.name.split(" ")[0];
    this.url = url;
    this.from = `Natours <${process.env.EMAIL_FROM}>`;
  }

  newTransport() {
    if (process.env.NODE_ENV === "production") {
      // SendGrid
      return nodemailer.createTransport({
        service: "SendGrid",
        auth: {
          user: process.env.SENDGRID_USERNAME,
          pass: process.env.SENDGRID_PASSWORD,
        },
      });
    }
    // Mailtrap (development)
    return nodemailer.createTransport({
      host: process.env.EMAIL_HOST,
      port: process.env.EMAIL_PORT,
      auth: {
        user: process.env.EMAIL_USERNAME,
        pass: process.env.EMAIL_PASSWORD,
      },
    });
  }

  async send(template, subject) {
    // 1) Render HTML from a Pug template
    const html = pug.renderFile(
      `${__dirname}/../views/emails/${template}.pug`,
      { firstName: this.firstName, url: this.url, subject },
    );

    // 2) Define mail options
    const mailOptions = {
      from: this.from,
      to: this.to,
      subject,
      html,
      text: htmlToText(html),
    };

    // 3) Send
    await this.newTransport().sendMail(mailOptions);
  }

  async sendWelcome() {
    await this.send("welcome", "Welcome to the Natours Family!");
  }

  async sendPasswordReset() {
    await this.send(
      "passwordReset",
      "Your password reset token (valid for 10 minutes)",
    );
  }
};
```

---

## 207. Email Templates with Pug: Welcome Emails

**What is this:** Building a styled Pug email template for welcome messages that renders to HTML compatible with email clients.

**Description:** Email HTML must be self-contained (inline CSS, no external stylesheets). The Pug template extends a `baseEmail.pug` layout, receives locals (`firstName`, `url`) from the `Email.send()` call, and uses conditional blocks for the email body content.

**Key points:**

- Use inline styles — email clients strip `<style>` tags
- `html-to-text` generates a plain-text fallback from the rendered HTML
- Variables passed from `pug.renderFile()` are available as template locals
- Keep the CTA button's `href` pointing at `url` (the token URL or dashboard link)

**Examples:**

```pug
//- views/emails/welcome.pug
extends baseEmail

block content
  p Hi #{firstName},
  p Welcome to Natours, we're glad to have you here! 🎉
  p We're all a big family of nature lovers, and we're excited to have you on board.
  table.btn.btn-primary
    tbody
      tr
        td
          a(href=`${url}`) Start exploring tours
  p If you need any help, just reply to this email — we always love hearing from you!
```

```js
// Called from authController.js after user creation
const url = `${req.protocol}://${req.get("host")}/me`;
await new Email(newUser, url).sendWelcome();
```

---

## 208. Sending Password Reset Emails

**What is this:** Using the `Email` class to send a password reset link containing a hashed token to the user's email address.

**Description:** When a user requests a password reset, the server generates a plain-text token (stored as a hash in the DB), builds a reset URL, and calls `email.sendPasswordReset()`. If sending fails the reset token fields are cleared and an error is returned so the user isn't left with a dangling token.

**Examples:**

```js
// controllers/authController.js
exports.forgotPassword = catchAsync(async (req, res, next) => {
  // 1) Get user by email
  const user = await User.findOne({ email: req.body.email });
  if (!user)
    return next(new AppError("There is no user with that email address.", 404));

  // 2) Generate random reset token
  const resetToken = user.createPasswordResetToken();
  await user.save({ validateBeforeSave: false });

  // 3) Send email
  try {
    const resetURL = `${req.protocol}://${req.get("host")}/api/v1/users/resetPassword/${resetToken}`;
    await new Email(user, resetURL).sendPasswordReset();

    res
      .status(200)
      .json({ status: "success", message: "Token sent to email!" });
  } catch (err) {
    user.passwordResetToken = undefined;
    user.passwordResetExpires = undefined;
    await user.save({ validateBeforeSave: false });
    return next(
      new AppError(
        "There was an error sending the email. Try again later.",
        500,
      ),
    );
  }
});
```

---

## 209. Using Sendgrid for "Real" Emails

**What is this:** Swapping the development Mailtrap transport for SendGrid to send real emails in production.

**Description:** SendGrid is a cloud email delivery service. Nodemailer supports it via the `service: 'SendGrid'` shorthand — no host/port config needed, just API credentials. The `EMAIL_FROM` env var must match a verified sender identity in the SendGrid dashboard.

**Key points:**

- Create a SendGrid account, verify a sender email, and generate an API key
- Set `SENDGRID_USERNAME=apikey` and `SENDGRID_PASSWORD=<your-api-key>` in `.env`
- The `Email` class already switches to SendGrid when `NODE_ENV=production`
- All outgoing email volume, bounces, and open rates are visible in the SendGrid dashboard

**Examples:**

```js
// utils/email.js — production branch of newTransport()
if (process.env.NODE_ENV === "production") {
  return nodemailer.createTransport({
    service: "SendGrid",
    auth: {
      user: process.env.SENDGRID_USERNAME, // literally 'apikey'
      pass: process.env.SENDGRID_PASSWORD, // the actual API key value
    },
  });
}
```

```bash
# .env (production values)
EMAIL_FROM=hello@natours.io
SENDGRID_USERNAME=apikey
SENDGRID_PASSWORD=SG.xxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

---

## 210. Credit Card Payments with Stripe

**What is this:** Introduction to Stripe's payment flow and the two objects at the centre of it: the Payment Intent and the client secret.

**Description:** Stripe separates payment creation (server-side) from payment confirmation (client-side). The server creates a `PaymentIntent` and returns the `client_secret` to the browser. The browser uses the Stripe.js library to collect card details and confirm the payment — card numbers never touch the server, satisfying PCI-DSS requirements.

**Key points:**

- Server: creates `PaymentIntent` via `stripe.paymentIntents.create()`
- Client: uses `stripe.confirmCardPayment(clientSecret, cardElement)` from Stripe.js
- The `client_secret` is a one-time token that authorises the browser to confirm exactly that payment
- Test card: `4242 4242 4242 4242`, any future expiry, any CVC

**Examples:**

```
Payment flow:
1. User clicks "Book tour"
2. Browser → GET /api/v1/bookings/checkout-session/:tourId
3. Server → stripe.paymentIntents.create({ amount, currency })
             returns { client_secret }
4. Browser → stripe.confirmCardPayment(client_secret, { card })
5. Stripe → charges card, fires webhook
6. Server → webhook handler creates Booking document
```

---

## 211. Integrating Stripe into the Back-End

**What is this:** Creating the `getCheckoutSession` controller that builds a Stripe Checkout Session and returns it to the frontend.

**Description:** `stripe.checkout.sessions.create()` produces a hosted Stripe Checkout page URL. The session carries the tour price, success/cancel URLs, and customer metadata. The frontend redirects to `session.url` and Stripe handles the card UI — no custom payment form needed with this approach.

**Key points:**

- Install: `npm install stripe`
- Initialise with secret key: `const stripe = require('stripe')(process.env.STRIPE_SECRET_KEY)`
- `success_url` receives `?session_id={CHECKOUT_SESSION_ID}` which is used later in the webhook
- Pass tour data in `metadata` so the webhook knows which tour was booked

**Examples:**

```js
// controllers/bookingController.js
const stripe = require("stripe")(process.env.STRIPE_SECRET_KEY);
const Tour = require("../models/tourModel");

exports.getCheckoutSession = catchAsync(async (req, res, next) => {
  // 1) Get the booked tour
  const tour = await Tour.findById(req.params.tourId);

  // 2) Create checkout session
  const session = await stripe.checkout.sessions.create({
    payment_method_types: ["card"],
    success_url: `${req.protocol}://${req.get("host")}/?tour=${req.params.tourId}&user=${req.user.id}&price=${tour.price}`,
    cancel_url: `${req.protocol}://${req.get("host")}/tour/${tour.slug}`,
    customer_email: req.user.email,
    client_reference_id: req.params.tourId,
    line_items: [
      {
        name: `${tour.name} Tour`,
        description: tour.summary,
        images: [`https://www.natours.dev/img/tours/${tour.imageCover}`],
        amount: tour.price * 100, // Stripe expects cents
        currency: "usd",
        quantity: 1,
      },
    ],
  });

  // 3) Send session to client
  res.status(200).json({ status: "success", session });
});
```

```js
// routes/bookingRoutes.js
const router = express.Router();
router.get("/checkout-session/:tourId", protect, getCheckoutSession);
```

---

## 212. Processing Payments on the Front-End

**What is this:** Using Stripe.js on the tour detail page to redirect the user to the hosted Stripe Checkout page after fetching the session from the server.

**Description:** The frontend loads Stripe.js from the CDN, initialises it with the publishable key, calls the server to get a Checkout Session, then calls `stripe.redirectToCheckout({ sessionId })` to hand off to Stripe's hosted UI. No card numbers are handled by the app code.

**Examples:**

```js
// public/js/stripe.js
import { showAlert } from "./alerts.js";

const stripe = Stripe("pk_test_xxxxxxxxxxxxxxxxxxxxxxxxxxxx"); // publishable key

export const bookTour = async (tourId) => {
  try {
    // 1) Get checkout session from server
    const res = await fetch(`/api/v1/bookings/checkout-session/${tourId}`);
    const data = await res.json();

    // 2) Redirect to Stripe Checkout
    await stripe.redirectToCheckout({ sessionId: data.session.id });
  } catch (err) {
    showAlert("error", err);
  }
};
```

```js
// public/js/index.js
import { bookTour } from "./stripe.js";

const bookBtn = document.getElementById("book-tour");
if (bookBtn) {
  bookBtn.addEventListener("click", (e) => {
    e.target.textContent = "Processing...";
    bookTour(e.target.dataset.tourId);
  });
}
```

```pug
//- views/tour.pug — book button
button#book-tour.btn.btn--green(data-tour-id=`${tour.id}`) Book tour now!
```

---

## 213. Modelling the Bookings

**What is this:** Creating the Mongoose `Booking` model to record which user booked which tour at what price.

**Description:** A booking document links a `tour` (ref to Tour), a `user` (ref to User), and stores the `price` paid at the time of booking. It also includes `createdAt` and a `paid` boolean that defaults to `true` for card payments (manual cash bookings can set it to `false`).

**Examples:**

```js
// models/bookingModel.js
const mongoose = require("mongoose");

const bookingSchema = new mongoose.Schema({
  tour: {
    type: mongoose.Schema.ObjectId,
    ref: "Tour",
    required: [true, "Booking must belong to a Tour!"],
  },
  user: {
    type: mongoose.Schema.ObjectId,
    ref: "User",
    required: [true, "Booking must belong to a User!"],
  },
  price: {
    type: Number,
    required: [true, "Booking must have a price."],
  },
  createdAt: {
    type: Date,
    default: Date.now(),
  },
  paid: {
    type: Boolean,
    default: true,
  },
});

// Auto-populate tour and user on queries
bookingSchema.pre(/^find/, function (next) {
  this.populate("user").populate({ path: "tour", select: "name" });
  next();
});

const Booking = mongoose.model("Booking", bookingSchema);
module.exports = Booking;
```

---

## 214. Creating New Bookings on Checkout Success

**What is this:** Persisting a `Booking` document when Stripe redirects the user back to the success URL after a completed payment.

**Description:** As a temporary workaround (before proper webhooks), the success URL carries `?tour=&user=&price=` query params that the view router reads to create a booking, then redirects to the clean home page. A production app should use Stripe webhooks instead of query params to prevent forged bookings.

**Key points:**

- The workaround creates the booking inside `viewsController.getOverview` when the query params are present
- After saving the booking it redirects to `/` (without params) so a page refresh doesn't create a duplicate
- The permanent solution is a `stripe.webhooks.constructEvent()` handler on `POST /webhook-checkout`

**Examples:**

```js
// controllers/bookingController.js
const Booking = require("../models/bookingModel");

// Temporary: called by views router on success redirect
exports.createBookingCheckout = catchAsync(async (req, res, next) => {
  const { tour, user, price } = req.query;
  if (!tour || !user || !price) return next();

  await Booking.create({ tour, user, price });

  // Redirect to clean URL to prevent duplicate on refresh
  res.redirect(req.originalUrl.split("?")[0]);
});
```

```js
// routes/viewRoutes.js
const { createBookingCheckout } = require("../controllers/bookingController");

router.get("/", createBookingCheckout, getOverview);
```

---

## 215. Rendering a User's Booked Tours

**What is this:** Adding a "My Bookings" page that lists all tours the logged-in user has booked.

**Description:** The controller queries all `Booking` documents for the current user, extracts the tour IDs, then queries the `Tour` collection for those IDs. The result is a list of tour documents that the `overview` Pug template can render with the same tour cards used on the home page.

**Examples:**

```js
// controllers/bookingController.js
exports.getMyTours = catchAsync(async (req, res, next) => {
  // 1) Find all bookings for this user
  const bookings = await Booking.find({ user: req.user.id });

  // 2) Find tours with the returned IDs
  const tourIDs = bookings.map((el) => el.tour);
  const tours = await Tour.find({ _id: { $in: tourIDs } });

  res.status(200).render("overview", {
    title: "My Tours",
    tours,
  });
});
```

```js
// routes/viewRoutes.js
router.get("/my-tours", protect, getMyTours);
```

```pug
//- views/account.pug — nav link
li: a.side-nav--active(href='/my-tours') My bookings
```

---

## 216. Finishing the Bookings API

**What is this:** Wiring up full CRUD endpoints for bookings so admins can manage all bookings via the API.

**Description:** The booking router reuses the `handlerFactory` helpers (like every other resource) for `getAll`, `getOne`, `createOne`, `updateOne`, and `deleteOne`. Only admins and lead-guides should be able to access these endpoints.

**Examples:**

```js
// controllers/bookingController.js
const factory = require("./handlerFactory");

exports.getAllBookings = factory.getAll(Booking);
exports.getBooking = factory.getOne(Booking);
exports.createBooking = factory.createOne(Booking);
exports.updateBooking = factory.updateOne(Booking);
exports.deleteBooking = factory.deleteOne(Booking);
```

```js
// routes/bookingRoutes.js
const express = require("express");
const { protect, restrictTo } = require("../controllers/authController");
const {
  getCheckoutSession,
  getAllBookings,
  getBooking,
  createBooking,
  updateBooking,
  deleteBooking,
} = require("../controllers/bookingController");

const router = express.Router();

router.use(protect);
router.get("/checkout-session/:tourId", getCheckoutSession);

router.use(restrictTo("admin", "lead-guide"));
router.route("/").get(getAllBookings).post(createBooking);
router.route("/:id").get(getBooking).patch(updateBooking).delete(deleteBooking);

module.exports = router;
```

---

## 217. Final Considerations

**What is this:** Closing notes on production readiness, security hardening, and next steps after completing the course project.

**Description:** The course project is a solid foundation but several production concerns should be addressed before going live. This lecture highlights the most important ones: moving to Stripe webhooks, tightening security headers, rate limiting, and ongoing dependency updates.

**Key points:**

- **Stripe webhooks:** Replace the query-param booking workaround with a proper `POST /webhook-checkout` endpoint that verifies `stripe.webhooks.constructEvent()` — prevents forged bookings
- **Security headers:** `helmet` is already in place; review CSP directives and ensure HSTS is enabled in production
- **Rate limiting:** `express-rate-limit` is set globally; consider tighter limits on `/api/v1/users/login` and `/api/v1/users/forgotPassword`
- **Data sanitisation:** `express-mongo-sanitize` and `xss-clean` guard against injection; keep them updated
- **Dependency audits:** Run `npm audit` regularly and update packages, especially security patches
- **Environment variables:** Never commit `.env` to version control; use a secrets manager in production
- **Logging:** Add a production-grade logger (e.g., Winston + Morgan) to capture request logs and errors
- **Testing:** Add unit tests (Jest) and integration tests to catch regressions before deployment

**Examples:**

```js
// Stripe webhook handler (production approach)
// app.js — must use raw body, registered BEFORE express.json()
app.post(
  "/webhook-checkout",
  express.raw({ type: "application/json" }),
  bookingController.webhookCheckout,
);

// controllers/bookingController.js
exports.webhookCheckout = (req, res, next) => {
  const signature = req.headers["stripe-signature"];
  let event;
  try {
    event = stripe.webhooks.constructEvent(
      req.body,
      signature,
      process.env.STRIPE_WEBHOOK_SECRET,
    );
  } catch (err) {
    return res.status(400).send(`Webhook error: ${err.message}`);
  }

  if (event.type === "checkout.session.completed") {
    const session = event.data.object;
    Booking.create({
      tour: session.client_reference_id,
      user: session.customer_email, // resolve to user _id in real code
      price: session.amount_total / 100,
    });
  }

  res.status(200).json({ received: true });
};
```

# Section 14: Setting Up Git and Deployment

---

## 218. Section Intro

**What is this:** Overview of the final section — version-controlling the project with Git and deploying it to the web.

**Description:** This section covers the full deployment pipeline: initialising a Git repo, pushing to GitHub, preparing the Express app for a production environment, deploying to Heroku, enforcing HTTPS, handling OS signals for graceful shutdown, enabling CORS so the API can be consumed from other origins, and replacing the Stripe query-param workaround with a proper webhook.

---

## 219. Setting Up Git and GitHub

**What is this:** Initialising a local Git repository and connecting it to a new GitHub remote.

**Description:** Every project should be version-controlled from day one. Git tracks changes locally; GitHub hosts the remote copy and acts as a backup and collaboration hub. A `.gitignore` file prevents secrets and large folders (`node_modules`, `.env`) from ever being committed.

**Key points:**

- `git init` creates the local repo; `git remote add origin <url>` links it to GitHub
- Always add `.env` and `node_modules/` to `.gitignore` before the first commit
- Use SSH keys or the GitHub CLI (`gh auth login`) for authentication
- `main` is the conventional default branch name (GitHub's new default)

**Examples:**

```bash
# Initialise and make the first commit
git init
git add -A
git commit -m "Initial commit"

# Link to GitHub remote and push
git remote add origin git@github.com:username/natours.git
git branch -M main
git push -u origin main
```

```gitignore
# .gitignore
node_modules/
.env
*.log
```

---

## 220. Git Fundamentals

**What is this:** A recap of the core Git workflow — staging, committing, branching, and merging — as used throughout a real project.

**Description:** Git's three-tree model (working directory → staging area → repository) means changes are always reviewed before they become permanent. Branches let you develop features in isolation; merging (or rebasing) integrates them back into `main`.

**Key points:**

- `git status` — see what has changed
- `git add <file>` — stage specific files; `git add -A` stages everything
- `git commit -m "message"` — record a snapshot
- `git log --oneline` — compact history view
- `git branch <name>` / `git checkout -b <name>` — create and switch branches
- `git merge <branch>` — integrate a branch into the current one
- `git diff` — see unstaged changes; `git diff --staged` — see staged changes

**Examples:**

```bash
# Everyday workflow
git status
git add controllers/bookingController.js
git commit -m "feat: add webhook checkout handler"

# Feature branch
git checkout -b feature/stripe-webhooks
# ... make changes ...
git add -A
git commit -m "feat: verify Stripe webhook signature"
git checkout main
git merge feature/stripe-webhooks
```

---

## 221. Pushing to GitHub

**What is this:** Syncing local commits to the GitHub remote and understanding the push/pull cycle.

**Description:** `git push` uploads local commits to the remote; `git pull` fetches and merges remote commits into the local branch. After the initial `git push -u origin main`, subsequent pushes only need `git push`.

**Examples:**

```bash
# First push (sets upstream tracking)
git push -u origin main

# Subsequent pushes
git push

# Pull latest changes from remote
git pull

# Push a feature branch
git push origin feature/stripe-webhooks
```

---

## 222. Preparing Our App for Deployment

**What is this:** Hardening the Express app for a production environment — environment variables, the `start:prod` script, and Heroku-specific config.

**Description:** Before deploying, several things must be production-ready: `NODE_ENV` must be set to `production`, all secrets must come from environment variables (not hard-coded), the app must listen on `process.env.PORT` (Heroku assigns a dynamic port), and `npm start` must launch the production server.

**Key points:**

- Heroku sets `PORT` automatically — use `process.env.PORT || 3000`
- Set all env vars in the Heroku dashboard (Config Vars) instead of `.env`
- Add a `Procfile` so Heroku knows how to start the app
- The `engines` field in `package.json` pins the Node version

**Examples:**

```js
// server.js — dynamic port
const port = process.env.PORT || 3000;
app.listen(port, () => console.log(`App running on port ${port}...`));
```

```json
// package.json
{
  "scripts": {
    "start": "node server.js",
    "start:dev": "nodemon server.js",
    "start:prod": "NODE_ENV=production node server.js"
  },
  "engines": {
    "node": ">=18.0.0"
  }
}
```

```
# Procfile (in project root, no extension)
web: node server.js
```

---

## 223. Deploying Our App to Heroku

**What is this:** Pushing the app to Heroku using the Heroku CLI and Git, then verifying it runs in production.

**Description:** Heroku is a PaaS (Platform as a Service) that hosts Node apps without managing servers. After installing the CLI and running `heroku create`, Heroku adds a `heroku` Git remote. A `git push heroku main` builds and deploys the app. All environment variables are set via `heroku config:set` or the dashboard.

**Key points:**

- `heroku create <app-name>` provisions the app and adds the remote
- `heroku config:set KEY=VALUE` sets environment variables on the dyno
- `heroku logs --tail` streams live logs for debugging
- Free dynos sleep after 30 min of inactivity (paid dynos do not)

**Examples:**

```bash
# Login and create app
heroku login
heroku create natours-yourname

# Set environment variables
heroku config:set NODE_ENV=production
heroku config:set DATABASE=mongodb+srv://...
heroku config:set DATABASE_PASSWORD=yourpassword
heroku config:set JWT_SECRET=yourjwtsecret
heroku config:set JWT_EXPIRES_IN=90d
heroku config:set JWT_COOKIE_EXPIRES_IN=90
# ... (all other .env vars)

# Deploy
git push heroku main

# Open the app
heroku open

# Stream logs
heroku logs --tail
```

---

## 224. Testing for Secure HTTPS Connections

**What is this:** Detecting when the app is behind a proxy (like Heroku's router) and enforcing HTTPS redirects in production.

**Description:** Heroku terminates SSL at its load balancer and forwards requests to the app over plain HTTP — so `req.secure` is always `false` on Heroku even when the browser used HTTPS. Setting `app.enable('trust proxy')` tells Express to trust the `X-Forwarded-Proto` header set by Heroku's router, making `req.secure` reliable again.

**Key points:**

- `app.enable('trust proxy')` — trust the first proxy (Heroku's router)
- Check `req.headers['x-forwarded-proto'] === 'https'` or simply `req.secure`
- Redirect HTTP → HTTPS in a global middleware before all routes
- Only enforce in production — dev uses plain HTTP

**Examples:**

```js
// app.js
app.enable("trust proxy");

// Redirect HTTP to HTTPS in production
app.use((req, res, next) => {
  if (process.env.NODE_ENV === "production" && !req.secure) {
    return res.redirect(301, `https://${req.headers.host}${req.url}`);
  }
  next();
});
```

---

## 225. Responding to a SIGTERM Signal

**What is this:** Gracefully shutting down the HTTP server when the process receives a `SIGTERM` signal (e.g., from Heroku's dyno manager).

**Description:** Heroku sends `SIGTERM` before stopping a dyno (e.g., during a restart or scale-down). If the app ignores it, Heroku follows up with `SIGKILL` after 30 seconds, killing in-flight requests. Listening for `SIGTERM` and calling `server.close()` lets the server finish existing connections before exiting cleanly.

**Key points:**

- `process.on('SIGTERM', handler)` — register the shutdown handler
- `server.close(cb)` — stop accepting new connections; `cb` fires when all existing connections are closed
- Exit with code `0` (clean shutdown) after `server.close()`
- The same pattern handles `SIGINT` (Ctrl+C in the terminal)

**Examples:**

```js
// server.js
const server = app.listen(port, () => {
  console.log(`App running on port ${port}...`);
});

process.on("SIGTERM", () => {
  console.log("SIGTERM received. Shutting down gracefully...");
  server.close(() => {
    console.log("Process terminated.");
    // Node exits automatically when no more work remains
  });
});
```

---

## 226. Implementing CORS

**What is this:** Enabling Cross-Origin Resource Sharing (CORS) so the API can be called from frontend apps hosted on different origins.

**Description:** Browsers block cross-origin fetch/XHR requests by default (the Same-Origin Policy). CORS headers tell the browser which external origins are allowed to read the response. The `cors` npm package adds the correct `Access-Control-Allow-*` headers automatically. A preflight `OPTIONS` request is sent by browsers before non-simple requests (e.g., `PATCH`, `DELETE`, requests with custom headers) — `cors()` handles these too.

**Key points:**

- Simple requests (GET, POST with standard headers) only need `Access-Control-Allow-Origin`
- Non-simple requests trigger a preflight `OPTIONS` — must respond with CORS headers and `204`
- Use `origin: '*'` to allow all origins, or a specific URL for tighter control
- Set CORS before all other middleware so preflight responses go out immediately

**Examples:**

```bash
npm install cors
```

```js
// app.js
const cors = require("cors");

// Allow all origins (public API)
app.use(cors());

// OR: Allow only a specific frontend origin (with cookies/auth headers)
app.use(
  cors({
    origin: "https://www.natours.com",
    credentials: true,
  })
);

// Handle preflight for all routes
app.options("*", cors());
```

```js
// For a specific route only
router.route("/:id").patch(cors(), protect, updateTour);
```

---

## 227. Finishing Payments with Stripe Webhooks

**What is this:** Replacing the insecure query-param booking workaround with a verified Stripe webhook that creates bookings only after Stripe confirms payment.

**Description:** Stripe calls `POST /webhook-checkout` on the server after every successful payment. The server verifies the request is genuinely from Stripe by checking the `stripe-signature` header with `stripe.webhooks.constructEvent()`. Because this endpoint needs the raw binary body (not parsed JSON), it must be registered **before** `express.json()` using `express.raw()`.

**Key points:**

- Register the raw-body route before `express.json()` in `app.js`
- `stripe.webhooks.constructEvent(body, sig, secret)` throws if the signature is invalid — return `400`
- The webhook secret (`STRIPE_WEBHOOK_SECRET`) comes from the Stripe dashboard (or `stripe listen` CLI in dev)
- Only handle the `checkout.session.completed` event
- After switching to webhooks, remove the temporary `createBookingCheckout` middleware from the views router

**Examples:**

```js
// app.js — MUST be before express.json()
app.post(
  "/webhook-checkout",
  express.raw({ type: "application/json" }),
  bookingController.webhookCheckout
);

app.use(express.json({ limit: "10kb" }));
```

```js
// controllers/bookingController.js
const createBooking = async (session) => {
  const tour = session.client_reference_id;
  const user = (await User.findOne({ email: session.customer_email })).id;
  const price = session.amount_total / 100;
  await Booking.create({ tour, user, price });
};

exports.webhookCheckout = (req, res, next) => {
  const signature = req.headers["stripe-signature"];
  let event;
  try {
    event = stripe.webhooks.constructEvent(
      req.body,
      signature,
      process.env.STRIPE_WEBHOOK_SECRET
    );
  } catch (err) {
    return res.status(400).send(`Webhook error: ${err.message}`);
  }

  if (event.type === "checkout.session.completed") {
    createBooking(event.data.object);
  }

  res.status(200).json({ received: true });
};
```

```bash
# Test webhooks locally with the Stripe CLI
stripe listen --forward-to localhost:3000/webhook-checkout
# Copy the printed signing secret → set as STRIPE_WEBHOOK_SECRET in .env
```

# Section 15: That's It, Everyone!

---

## 228. Where to Go from Here

**What is this:** Closing thoughts and a roadmap of what to learn next after completing the course.

**Description:** The course covered a complete, production-grade Node.js/Express/MongoDB application from scratch. This final lecture reflects on what was built and points to the logical next steps — both for deepening Node.js knowledge and for expanding into adjacent areas of the stack.

**Key points:**

- **What you built:** A full REST API with authentication, authorisation, file uploads, emails, payments, server-side rendering, and deployment — the Natours project is a solid portfolio piece
- **Deepen Node.js:** Explore the official Node.js docs, the event loop in detail, streams, worker threads, and clustering for multi-core utilisation
- **Testing:** Add a proper test suite with Jest + Supertest for integration tests and unit tests on utility functions
- **TypeScript:** Gradually adopt TypeScript for type safety — `@types/express`, `@types/mongoose` are good starting points
- **Frontend frameworks:** Pair the API with a React, Vue, or Next.js frontend to build a full-stack SPA or SSR app
- **GraphQL:** Consider GraphQL as an alternative API layer — Apollo Server integrates cleanly with Express
- **Microservices / Docker:** Learn Docker and Docker Compose to containerise the app; explore breaking a monolith into microservices with message queues (RabbitMQ, Kafka)
- **Cloud:** Go beyond Heroku — AWS (EC2, ECS, Lambda), GCP, or DigitalOcean App Platform for more control and scalability
- **Keep building:** The best way to consolidate everything is to start a new project from scratch, making your own architectural decisions along the way
