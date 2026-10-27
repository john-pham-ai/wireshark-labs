# Lab 05 — HTTP: Reading the Web's Conversation

> **Goal:** Capture a real web request and response and read them like a
> letter and its reply.

**Prereq:** [Lab 04](04-tcp-handshake.md).

## Capture an HTTP conversation

1. Start a capture.
2. Visit `http://neverssl.com` — a site that exists purely to serve plain,
   unencrypted HTTP for exactly this kind of practice.
3. Stop the capture, then filter on `http`.

You'll see your **GET request** and the site's **response**.

## Read the request

Click the packet whose Info column says `GET / HTTP/1.1` and expand the
**Hypertext Transfer Protocol** layer. This is your browser's letter to the
server:

```
GET / HTTP/1.1
Host: neverssl.com
User-Agent: Mozilla/5.0 ...
Accept: text/html,...
Connection: keep-alive
```

- **GET** — the method: "please send me..." (`POST` would be "here is data
  I'm sending you", used by forms and logins)
- **`/`** — the path: the root of the site. `/about` would be the About page.
- **HTTP/1.1** — the version of the protocol
- **Host** — which website on that server you want (one server can host many)
- **User-Agent** — what browser you are

## Read the response

Click the response (`HTTP/1.1 200 OK`):

```
HTTP/1.1 200 OK
Content-Type: text/html
Content-Length: 1784
```

- **200** — the status code: "OK, here you go." The families you'll see:
  - `2xx` — success (`200 OK`)
  - `3xx` — "it's somewhere else" (redirects, like `301`)
  - `4xx` — your side's fault (`404 Not Found`, `403 Forbidden`)
  - `5xx` — the server's fault (`500 Internal Server Error`)
- **Content-Type** — what kind of thing is coming (a page, an image, JSON…)
- **Content-Length** — how many bytes follow

Expand the **Line-based text data** or **HTML** section in the details pane
and you'll find the actual page text — the same bytes your browser rendered.

## See the whole thing as a transcript

You already know this move: right-click the GET → **Follow → TCP Stream**.
Now the transcript is the full letter exchange: request on top (your color),
response below (their color) — headers, then HTML, all readable.

This is the moment most beginners have the "whoa" moment: **the web is just
text letters flying over TCP.**

## The filter shortcut for this lab

- `http.request` — all requests (any method)
- `http.request.method == "GET"` — only GETs
- `http.response.code >= 400` — only failures

## A note about the real web

Almost everything you actually browse today is **HTTPS** — HTTP wrapped in
TLS encryption. It won't show under the `http` filter; it shows as `TLS`
(covered in [Lab 06](06-tls.md)). Plain HTTP survives on practice sites,
embedded devices, and some internal tools — but the reading skills from
this lab apply to the TLS-unwrapped view too (you'll see how in Lab 06).

## Try it yourself

1. Capture a `404`: visit `http://neverssl.com/this-page-does-not-exist` and
   filter `http.response.code == 404`. What does the response body say?
2. Capture a redirect: visit `http://github.com` and find the `301` response.
   Where does it redirect to?
3. Using the transcript of a page load, count how many separate requests
   your browser made (hint: also filter `http.request` and read the Info
   column — pages are usually a main document plus several images/scripts).

## What you just learned

- An HTTP request = method (`GET`/`POST`) + path + version + headers
- A response = status code (`200`, `301`, `404`, `500`) + headers + body
- Status code families: 2xx success, 3xx redirect, 4xx your fault, 5xx
  server's fault
- Filters: `http.request`, `http.response.code >= 400`
- Modern sites use HTTPS, which is why they show as `TLS` instead of `HTTP`

**Next:** [Lab 06 — TLS](06-tls.md)
