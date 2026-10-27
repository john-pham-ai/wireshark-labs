# Lab 07 — Statistics: Zooming Out on a Capture

> **Goal:** Answer "what is going on in this capture?" in ten seconds,
> without reading a single packet.

**Prereq:** [Lab 06](06-tls.md).

## The problem filters can't solve

Filters tell you about *one thing you already suspect*. Statistics are how
you get oriented in a capture you've never seen — or notice traffic you
didn't know to look for.

Start a capture, browse normally for two minutes, stop, and save it as
`lab07.pcapng`. Now let's interrogate it.

## Protocol Hierarchy: what is this capture made of?

**Statistics → Protocol Hierarchy.**

You get a tree: what fraction of packets (and bytes) is TCP vs. UDP, how
much is TLS vs. HTTP vs. DNS, and so on. This is the fastest fingerprint of
a capture. On a normal browsing capture you'll see something like 75% TLS,
some HTTP, a little DNS, a little ARP. On a broken network, weird ratios
jump out (e.g., DNS nearly zero while RSTs are high).

Bonus: right-click any protocol here → **Apply as Filter** to jump straight
to those packets.

## Conversations: who is talking to whom?

**Statistics → Conversations.**

A *conversation* is one pair of addresses talking. This table answers "who
is my computer talking to and how much?" Columns worth sorting by:

- **Bytes / Packets** — the volume. Sort descending and the top rows are the
  big players of the capture.
- Tabs at the top: **Ethernet** (device-to-device on your LAN), **IPv4**
  (end-to-end), **TCP/UDP** (program-to-program, with ports).

Right-click a conversation → **Apply as Filter** to isolate it.

## Endpoints

**Statistics → Endpoints** — same data grouped by one address instead of a
pair. Great for "which single remote host dominates my traffic?"

## IO Graph: the story over time

**Statistics → IO Graph** (newer versions: the "I/O Graphs" dashboard).

This plots packets/bytes per second over the capture's timeline — traffic as
a heartbeat monitor. On your browsing capture you'll see spikes where pages
loaded and quiet between.

Two moves that make it powerful:

1. Add a second graph with a filter — e.g. one line for all traffic, one for
   `dns` — and compare their shapes.
2. Look for the *shape* of trouble: dead silence (disconnection?), a
   sawtooth of starts and stalls (retransmissions), or one channel suddenly
   saturating everything (a backup or streaming hogging the link).

You'll use exactly this in [Lab 08](08-troubleshooting.md) to see what "slow"
looks like.

## Capture File Properties

**Statistics → Capture File Properties** — the capture's metadata: duration,
total packets, bytes, average packets/sec, and — at the bottom — the
**capture's comments** field, where you can write "captured during the
Tuesday slowdown, laptop on office Wi-Fi" so future-you remembers context.
(Professional habit: always annotate saved captures.)

## Try it yourself

1. In Protocol Hierarchy, what percentage of your capture's bytes are TLS?
2. In Conversations (TCP tab), sort by bytes. What single conversation is
   the biggest, and why (streaming? sync? page load)?
3. In the IO Graph, find your biggest page load. How many packets/sec at the
   peak?

## What you just learned

- **Protocol Hierarchy** fingerprints what a capture is made of
- **Conversations / Endpoints** show who's talking and how much
- **IO Graph** plots traffic over time — spikes, gaps, and saturation are
  visible shapes
- **Capture File Properties** holds duration, totals, and your annotations
- Every statistics screen can pivot to a filter via right-click

**Next:** [Lab 08 — Troubleshooting](08-troubleshooting.md)
