# Lab 06 — TLS: Why You Can't Read HTTPS (And What You Can Still See)

> **Goal:** Capture an HTTPS site visit and understand exactly what's
> readable, what's encrypted, and how the two sides agreed on keys without
> anyone else learning them.

**Prereq:** [Lab 05](05-http.md).

## The bad news first

Visit any normal site — `https://wikipedia.org` — and filter on `http`.
Nothing. Filter on `tls` instead and you'll see plenty. **HTTPS is HTTP
wearing a lock:** TLS (Transport Layer Security) encrypts everything
before it goes over TCP. Wireshark faithfully recorded the bytes, but the
bytes are scrambled.

## The good news: the handshake is readable

Even encrypted connections start with a public negotiation. Capture a fresh
visit to `https://wikipedia.org` and filter `tls`. The first few packets
are the **TLS handshake** — still in cleartext:

1. **Client Hello** (you → server). Expand it and find:
   - **Version** and the list of cipher suites your browser supports
   - **Server Name Indication (SNI)** — *the name of the site you're
     visiting, in plain text.* Even on HTTPS, anyone sniffing the network
     learns which sites you visit from SNI. (Encrypted Client Hello is
     newer and rarer.)
2. **Server Hello** (server → you) — the server picks one cipher suite.
3. **Certificate** — the server's ID card: its name, who signed the
   certificate, expiry date. Expand it and read the fields — Wireshark
   shows you the whole chain of trust. Your browser checks this certificate
   in the background; if it fails, you get the scary warning page.
4. Then a few **Key Change / Finished** packets, and from that point on the
   Info column just says **Application Data** — encrypted payload.

## What a snooper still sees (and what they can't)

With HTTPS, an observer on the network can see:

- The **destination IP** (and often the site name via SNI)
- **Timing and sizes** — roughly when and how much
- The DNS lookup that preceded it

They *cannot* see: URLs, page contents, passwords, cookies, form data.

This division is the everyday working knowledge of HTTPS: not "invisible"
but "sealed envelope with the address written outside."

## Prove it to yourself

1. Capture a visit to `https://wikipedia.org`.
2. Filter `tls.handshake.extensions_server_name` (or just find the Client
   Hello). Read the site name in the clear.
3. Click any **Application Data** packet and try to find anything readable
   in the details or bytes pane. There's nothing — just opaque binary.
4. Right-click the Client Hello → **Follow → TLS Stream**. Readable at the
   start, garbage after.

## What about Wireshark decrypting my own traffic?

Wireshark *can* decrypt TLS if you give it the browser's session keys
(a technique involving `SSLKEYLOGFILE`) — that's a legitimate advanced
technique for debugging your own applications. It's out of scope for this
beginner course; know that it exists, and that it only works for traffic
where *you* control an endpoint.

## Legal/ethical footnote

Everything in these labs happens on your own machine and your own traffic.
Analyzing other people's traffic without permission is illegal in most
places (wiretapping laws) even when it's unencrypted. Wireshark is a
network engineer's stethoscope, not a skeleton key.

## Try it yourself

1. Which sites show up in your capture's SNI fields after 30 seconds of
   ordinary browsing? (Filter `tls.handshake.type == 1` and expand a few
   Client Hellos.)
2. Find a certificate in your capture. Who was the signing authority? When
   does the certificate expire?
3. Visit an HTTPS site with a deliberately bad reputation in your browser's
   eyes — e.g. a captive-portal login page — and see whether you find RSTs
   or cleartext HTTP there.

## What you just learned

- HTTPS = HTTP encrypted by TLS; contents are unreadable to a sniffer
- The TLS handshake is cleartext: Client Hello, Server Hello, Certificate
- **SNI** leaks the site name; destination IP and timing are also visible;
  URLs and content are not
- The browser validates the server's certificate automatically
- Wireshark can decrypt your own TLS traffic with session keys (advanced,
  optional)

**Next:** [Lab 07 — Statistics](07-statistics.md)
