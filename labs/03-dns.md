# Lab 03 — DNS: What Happens When You Type a URL

> **Goal:** Watch your computer turn a website name into an IP address, and
> read every field of the exchange.

**Prereq:** [Lab 02](02-filters.md).

## What DNS is

Computers find each other with IP addresses like `93.184.216.34`, but humans
use names like `example.com`. **DNS (the Domain Name System)** is the
phone book that translates names to numbers. Nearly every internet action
starts with a DNS lookup — which makes it the first suspect when "the
internet is slow."

## Capture a lookup

1. Start a capture.
2. In a terminal, run:

   ```bash
   nslookup example.com        # Windows/macOS/Linux
   # or, if you have it:
   dig example.com             # macOS/Linux
   ```

   (Browsing to a fresh website works too, but it may be cached — see below.
   The terminal command forces a fresh lookup.)
3. Stop the capture and filter on `dns`.

You should see at least two rows: a **Standard Query** and a **Standard
Query response**. Same source, same destination, one asks and one answers.

## Read the query

Click the **query** (the first one) and expand every layer in the middle
pane:

```
Frame 7: ...
Ethernet II, Src: ..., Dst: ...        <- how devices on your LAN talk
Internet Protocol Version 4, Src: 192.168.1.10, Dst: 192.168.1.1
User Datagram Protocol, Src Port: 53x, Dst Port: 53
Domain Name System (query)
```

Notice the onion from Lab 01: this packet rides on **UDP** (DNS's favorite
transport — fast, "fire and forget") to port **53** on your router
(`192.168.1.1` — your router is usually your DNS server, forwarding your
question upstream).

Expand the DNS layer and find:

- **Transaction ID** — matches the query to its response (like a ticket number)
- **Flags** — `Recursion desired` means "please do the full lookup for me"
- **Queries** — the name being asked about and the record type, **A** =
  "give me the IPv4 address"

## Read the response

Click the response and expand **Answers**:

- **A record** — the answer: `example.com has address 93.184.216.34`
- Sometimes a **CNAME** record first — an alias, e.g.
  `www.example.com` → `example.com` → address. Names can chain through
  aliases before landing on an address.
- **TTL (time to live)** — how many seconds this answer may be *cached*.
  Your computer remembers the answer until the TTL runs out, which is why
  revisiting a site often shows no DNS traffic at all.

## Why you'll care about DNS later

Two classic "internet feels slow" diagnoses involve DNS:

1. **Slow lookups** — the response takes ages to arrive. Filter:
   `dns.time > 0.1` shows lookups that took over 100 ms.
2. **Missing responses** — queries with no answer. The site "doesn't load"
   even though the network is fine.

You'll use both in [Lab 08](08-troubleshooting.md).

## Try it yourself

1. Visit a *new* website you haven't been to today, capture it, and find the
   A record that came back.
2. Look up a big name like `www.wikipedia.org`. Can you spot a CNAME in the
   answer chain?
3. Filter on `dns.time > 0` and sort by the time delta — which domain took
   longest to answer?

## What you just learned

- DNS translates names to IP addresses; queries and responses ride UDP port 53
- The Transaction ID pairs a query with its response
- **A** = IPv4 address record, **CNAME** = alias record, **TTL** = cache lifetime
- `dns.time > 0.1` exposes slow lookups
- Unanswered DNS queries = "site won't load" symptoms

**Next:** [Lab 04 — The TCP Handshake](04-tcp-handshake.md)
