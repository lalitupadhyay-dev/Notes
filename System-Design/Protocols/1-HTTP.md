# 1. HTTP

1. **Hyper Text Transfer Protocol** - a stateless application-layer message protocol for data exchange on web

2. It is the protocol used in <code>**Client-Server**</code> model

3. It came into existence for providing standard rules to establish communication between <code>**Client**</code> and <code>**Server**</code>
<!-- 4. HTTP messages are sent over a <code>**TLS encrypted TCP Connection**</code> for maintaining reliability and correct ordering of data  -->

## 1.1 HTTP Evolution

Let's see how HTTP evolved during the past years

### 1.1.1 WWW - World Wide Web

1. All started with **WWW (World Wide Web)** or **(called as "Mesh" earlier)** - Tim Berners Lee, build a system over internet to access/share web documents. A standard communication protocol was needed to access/share the documents on the Web, that gave birth to HTTP

2. Building blocks of WWW:
   - <code>**HTML**</code> to represent **Hypertext** documents

   - Protocol to access/share those **Hypertext** documents <code>**(HTTP)**</code>

   - A <code>**Client**</code> to display data <code>**Request Initiator**</code>

   - A <code>**Server**</code> to handle requests from clients

### 1.1.2 HTTP/0.9: one-line protocol

- Communication between **client** and **server** happens in plain-text (human-readable format)

- Supported only 1 HTTP method - <code>**GET**</code>

- Supported only single line request

  ```js
  GET / index.html;
  ```

- Server returns only HTML document
  ```html
  <html>
    <body>
      Hello World
    </body>
  </html>
  ```

### 1.1.3 HTTP/1.0

- <code>**Status codes**</code> were introduced

- <code>**Headers**</code> were introduced to share metadata

- Connections were not persistent - _for every <code>**Request-Response**</code> a new <code>**TCP Connection**</code> is created, resulted in slowing down the website_

- New <code>**HTTP methods**</code> were introduced:
  - POST
  - HEAD

- Supported different content types with the help of <code>**Content-Type**</code> header
  - HTML
  - CSS
  - PDFs
  - Images
  - Videos

- Basic <code>**caching**</code> was introduced with these headers:
  - <code>**Expires**</code>: Tells the browser until what time it can use the cached file without asking the server again
  - <code>**If-Modified-Since**</code>: Lets the browser ask the server to send the file only if it has changed since the last download

- HTTP Request & Response looked like this

  ```js
  // Request
  GET /index.html HTTP/1.0
  Host: example.com
  User-Agent: Mozilla/5.0
  Accept: text/html

  // Response
  HTTP/1.0 200 OK
  Content-Type: text/html
  Content-Length: 120

  <html>
      <body>Hello</body>
  </html>
  ```

### 1.1.4 HTTP/1.1

- Introduced persistent connections i.e, 1 <code>**TCP Connection**</code> can handle multiple requests

- <code>**Connection: keep-alive**</code> became the default behaviour

- <code>**Host**</code> header was introduced, because multiple websites can run on 1 IP address, so <code>**Host**</code> header distinguishes which website client needs

- Introduced <code>**Pipelining**</code> - A new request does not have to wait for previous request to finish, we hit 1 **URL** and everything loads quickly <code>**HTML, CSS, JS, Images, Videos... **</code> etc **(Speed Improved)**

- New <code>**HTTP methods**</code> were introduced:
  - PUT
  - DELETE
  - OPTIONS
  - TRACE

- Chunked Transfer Encoding:
  - Before this server must know the full size of response to send (like <code>**Content-Length: 1200**</code>)
  - A response header <code>**Transfer-Encoding: chunked**</code>, that tells the <code>**client**</code>, that response will come in chunks (like <code>**Event Streaming**</code>)

  - This made responses faster

- Caching improvements with new header <code>**Cache-Control, ETag**</code>

- When large files downloading fails, then downloading has to start from 0 again. It wastes a lot bandwidth of network that's why the support of <code>**Range Requests**</code> added in <code>**HTTP/1.1**</code>, with the help of a new <code>**Range**</code> header, with it the browser can request a portion of the file which is failed to download.

### 1.1.5 HTTP/2.0 

## HTTP Request

```js
POST /users HTTP/1.1

Host: api.example.com
Content-Type: application/json
Authorization: Bearer token123
Content-Length: 27

{"name":"Lalit","age":25}
```

## HTTP Response

```js
HTTP/1.1 200 OK

Content-Type: application/json
Content-Length: 34

{"message":"User created"}
```
