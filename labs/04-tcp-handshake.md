# Lab 04 — The TCP Handshake: Watching a Connection Get Born

> **Goal:** Watch two computers agree to talk to each other, packet by
> packet, and read a whole conversation as a transcript.

**Prereq:** [Lab 03](03-dns.md).

## Why TCP matters

DNS rode on UDP — fire-and-forget. But web traffic uses **TCP
(Transmission Control Protocol)**, which guarantees every byte arrives, in
order, exactly once. To pull that off, TCP first performs a small ceremony
called the **three-way handshake**. You're going to watch it happen.

## Capture a handshake

1. Start a capture.
2. Open `http://example.com` in a browser (a fresh site forces a new
   connection).
3. Stop the capture.

## Find the handshake

Filter on:

```
tcp.flags.syn == 1 && tcp.flags.ack == 0
```

That's "packets with the SYN flag set and the ACK flag *not* set" — the
opening move of every TCP connection. You should find one going to
example.com's IP.

Now clear the filter and filter instead on that conversation:

```
tcp.port == 51822 && ip.addr == 93.184.216.34
```

(Use the port from your SYN packet and example.com's IP — **Apply as
Filter** on the Source Port field does this instantly.)

Right there at the top of the conversation are the three packets of the
handshake:

| # | Direction | Info column | Meaning |
|---|-----------|--------------|---------|
| 1 | you → server | `SYN` | "I'd like to talk. My starting number is X." |
| 2 | server → you | `SYN, ACK` | "Sure. My starting number is Y. Got your X." |
| 3 | you → server | `ACK` | "Got your Y. Let's go." |

Click each one. In the TCP layer of the middle pane, find:

- **Source Port / Destination Port** — the *server* port is 80 (HTTP); your
  side gets a random high-numbered port (e.g. 51822). Port numbers are how
  both sides know which program the data belongs to.
- **Sequence Number** — each side's private byte-counter for this connection
- **Flags** — SYN ("start"), ACK ("received"), FIN ("done"), RST ("abort")

Only after all three packets does the browser send the actual HTTP request.
The request rides packets with `PSH, ACK` flags — "here's real data."

## Follow the whole conversation

This is the star feature of the lab. Click any packet in the conversation,
then:

**Right-click → Follow → TCP Stream**

Wireshark reassembles both directions into one readable transcript — text
exactly as the two programs sent it, with your side's text in one color and
the server's in another. The red highlight in the packet list shows which
packets made up this stream. Click the filter bar's `x` to go back to the
full view.

## How connections end

After the web request completes, look at the tail of the conversation for:

- `FIN, ACK` packets — a polite, mutual hang-up ("done sending", "you too,
  bye"), four packets between the two sides
- or a bare `RST` — an abrupt abort. A few RSTs are normal, but a
  connection that dies with RST *instead of* data is a clue something is
  being refused.

## Try it yourself

1. Find a handshake to a different website. Which side (you or the server)
   sends the last ACK?
2. Follow the TCP stream of your example.com conversation. Can you spot the
   HTTP request text inside the transcript?
3. Filter `tcp.flags.reset == 1`. Do you see RSTs during normal browsing?
   (A handful in a long capture is normal.)

## What you just learned

- TCP guarantees ordered, complete delivery; web traffic uses it
- The handshake is `SYN → SYN-ACK → ACK`; the filter
  `tcp.flags.syn == 1 && tcp.flags.ack == 0` finds connection openings
- Ports identify the programs on each end; your side uses a random high port
- **Follow → TCP Stream** reassembles a conversation into a transcript
- Connections close politely with FIN or abruptly with RST

**Next:** [Lab 05 — HTTP](05-http.md)
