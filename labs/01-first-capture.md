# Lab 01 — Your First Capture: Reading the Screen

> **Goal:** Capture a website visit and learn what every part of the
> Wireshark window is telling you.

**Prereq:** [Lab 00](00-setup.md).

## Capture a website visit

1. Start a capture on your active interface.
2. In your browser, visit `http://example.com` (yes, http — we want readable
   traffic for now; you'll see why in [Lab 06](06-tls.md)).
3. Wait for the page to load, then stop the capture.

You now have a recording of everything your computer did to fetch that page.
Let's learn to read it.

## The three panes

Wireshark's window is three panes, connected:

1. **Packet List** (top) — one row per packet: number, time, source,
   destination, protocol, and an Info summary.
2. **Packet Details** (middle) — the selected packet, peeled open layer by
   layer like an onion.
3. **Packet Bytes** (bottom) — the raw bytes of the packet, in hex. You will
   rarely need this as a beginner, but it's nice to know it's there.

Click a few different rows in the packet list and watch the middle and bottom
panes change. **The three panes always describe the same packet.**

## The onion: frames vs. packets vs. layers

Click a packet whose Protocol column says **HTTP**. The middle pane shows
lines like:

```
Frame 42: 617 bytes on wire (4936 bits), 606 bytes captured
Ethernet II, Src: Apple_b1:02:03, Dst: Routersp_0a:0b:0c
Internet Protocol Version 4, Src: 192.168.1.10, Dst: 93.184.216.34
Transmission Control Protocol, Src Port: 51822, Dst Port: 80
Hypertext Transfer Protocol
```

This is the onion. Network communication wraps data in nested envelopes:

- **Frame** — the whole thing as it went over the wire (Wireshark's bookkeeping)
- **Ethernet** — how devices on your local network address each other
  (hardware/MAC addresses)
- **IP (Internet Protocol)** — addresses across the internet (192.168.1.10 →
  93.184.216.34)
- **TCP** — which program gets the data, via port numbers
- **HTTP** — the actual web request

Each layer only knows how to find the next one. Click the little triangles
to expand and collapse each layer. Notice that when you highlight a layer in
the middle pane, the corresponding bytes light up in the bottom pane.

**"Packet" is loose everyday speech for one row of this list.** "Frame" is
the same row, described precisely. Beginners can treat them as synonyms.

## What the colors mean

The packet list is color-coded. Click **View → Coloring Rules** to see them.
The ones you'll notice most:

- **Light blue** — HTTP traffic
- **Light green** — DNS traffic
- **Purple** — TCP "pure ACKs" (tiny acknowledgments — the "yep, got it"
  packets)
- **Black with red text** — a problem Wireshark detected (checksum errors,
  etc.)

Coloring is Wireshark giving you a hint. In Lab 08 you'll learn to take the
black ones seriously.

## The time column

The Time column shows *seconds since the capture started* (a delta from
t=0). You can change it: **View → Time Display Format → Date and Time of Day**
or — very useful for troubleshooting — **Seconds Since Previous Captured
Packet**. Long gaps and long deltas both mean "something was slow here."

## Try it yourself

1. Find the row where Protocol says `DNS` and the Info column mentions
   `example.com`. What IP address came back in the response?
2. Expand the IP layer of any packet. What is your computer's IP address?
3. Set time display to *Seconds Since Previous Captured Packet* and look for
   the biggest gap around your page load.

## What you just learned

- The packet list, packet details, and packet bytes panes all describe the
  currently selected packet
- Packets are nested envelopes: Frame → Ethernet → IP → TCP → (HTTP/DNS/…)
- Protocol colors are Wireshark's hints; black/red means trouble
- The Time column mode you pick changes what story the numbers tell

**Next:** [Lab 02 — Filters](02-filters.md)
