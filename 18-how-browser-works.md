# What Happens When You Enter a URL in the Browser and Press Enter?

If you work in IT, especially in web development, there’s a high chance you’ll be asked this question during an interview:  
**“What happens after you enter a website address in the browser and press Enter?”**

Let’s break down the process step by step.

---

## 1. Entering the URL in the Browser

When you type a website address in the browser’s address bar, the browser activates its autocomplete feature. In most browsers, a dropdown list appears, showing previously visited sites and page titles containing the entered text.

If you press **Enter**, the browser determines whether the entered text is a **search query** or a **URL**.

---

## 2. Parsing the URL Structure

A **URL (Uniform Resource Locator)** is a unique address that indicates where a resource is located on the internet. It consists of several parts:
- **Protocol** (e.g., `http://`, `https://`)
- **Host (domain)** (`example.com`)
- **Path to the resource** (`/page`)
- **Query parameters** (`?id=123`)

Browsers support multiple protocols:
- **HTTP/HTTPS** – standard protocols for loading web pages.
- **FILE://** – used to open local files.
- **FTP** – file transfer over a network (rarely used).

If a protocol is not specified in the URL, the browser **automatically prepends `https://`** if the website enforces **HSTS (HTTP Strict Transport Security)**.

👉 **Question:** What ports correspond to HTTP and HTTPS? (Answer in the comments!)

---

## 3. Resolving the Server’s IP Address (DNS Query)

A domain is a human-readable name (`example.com`), but the browser **doesn’t understand it directly**. To communicate with the server, it needs an **IP address**.

### How Does the Browser Obtain the IP Address?
1. It checks the **local DNS cache** (previously visited websites).
2. It checks the **hosts file** on the computer (manual overrides).
3. It queries the **ISP’s DNS server**.
4. If no data is found, the DNS server queries **root DNS servers**.
5. Eventually, the DNS server retrieves the IP address and sends it to the browser.

👉 **Question:** What happens if the DNS record of a site is changed? When will the browser notice the change?

---

## 4. Establishing a Connection (TCP/IP)

Now that the browser knows the server's IP address, it establishes a **TCP connection** with the server to retrieve the web page.

### How Does This Happen?
1. **The browser sends a SYN packet** (connection initiation).
2. **The server responds with a SYN-ACK** (connection acknowledgment).
3. **The browser sends an ACK** (connection established).

This process is called a **"three-way handshake"** and ensures that both the client and server are ready for data exchange.

If the website uses **HTTPS**, an additional **TLS handshake** occurs, where the browser and server exchange cryptographic keys to establish a secure connection.

---

## 5. Sending the HTTP Request

Once the connection is established, the browser constructs an HTTP request.

Example of a **GET request**:
```
GET /index.html HTTP/1.1
Host: example.com
User-Agent: Chrome/120.0
Accept: text/html
```

🔹 **Key HTTP request headers**:
- **Method (GET, POST, PUT, DELETE)** – defines the action.
- **Host** – specifies the website (useful for shared hosting).
- **User-Agent** – contains browser and device information.
- **Cookies, Referer, Authorization** – used for authentication and data exchange.

👉 **Question:** What HTTP request methods do you know? (Share in the comments!)

---

## 6. Server Processes the Request

The server (e.g., Nginx, Apache, or Express.js) receives the request, analyzes it, and determines which resource to return.

### How Does the Server Determine Which Site to Serve?
1. It checks the **IP address and port** (HTTP – 80, HTTPS – 443).
2. It examines the **Host header** (since multiple sites can run on one server).
3. It determines whether it can handle the request.
4. It processes **backend logic** (if the site is dynamic, e.g., Node.js, PHP, Django).
5. It sends an **HTTP response** back to the browser.

Example of an **HTTP response**:
```
HTTP/1.1 200 OK
Content-Type: text/html
Content-Length: 5120

<html>...</html>
```

The **200 OK** status code indicates that the page was successfully loaded.

---

## 7. Rendering the Page in the Browser

After receiving the HTML document, the browser **parses** it:
1. **Analyzes the HTML** (structures the document).
2. **Requests CSS and JS files** (for styling and interactivity).
3. **Executes JavaScript code**.
4. **Renders the page** (creates a DOM tree and applies styles).

If the code contains `<script src="app.js">`, the browser will load and execute the script **before rendering the page** (if it's a **blocking script**).

👉 **Question:** How does the browser handle page rendering? (Frontend developers, share your thoughts!)

---

## 🚀 Conclusion

Now you know what happens **from the moment you enter a URL** to **when the page loads in the browser**. This process includes **multiple steps**: DNS queries, TCP/TLS handshakes, HTTP requests, server processing, and page rendering.

Understanding this mechanism is **crucial for developers, DevOps engineers, and system administrators**. If you found this breakdown useful, let me know which parts you’d like to explore in more detail! 🚀
