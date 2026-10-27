# Lab 02 — Filters: Cut the Noise Down to What Matters

> **Goal:** Take a chaotic, thousand-packet capture and find the few rows
> you care about in seconds.

**Prereq:** [Lab 01](01-first-capture.md).

## The problem

Start a capture and browse for two minutes. You'll have thousands of rows:
music streaming, cloud sync, chat apps, all shouting over each other.
Nobody reads a capture like a book — **you filter it**.

## Display filters: the green bar

The box above the packet list is the **display filter bar**. It hides
packets you don't want to see — nothing is deleted. Type this and press
Enter:

```
dns
```

Now you only see DNS packets. Note the bar turned **green** — green means
"syntax OK". A **red** bar means your filter is malformed and did nothing.

Essential moves:

- **`dns`** — only DNS packets
- **`ip.addr == 93.184.216.34`** — packets to OR from that IP
- **`http`** — only plain HTTP
- **`!(http)`** — everything *except* HTTP
- **`dns || http`** — either one (`||` = or)
- **`http && ip.addr == 192.168.1.10`** — both conditions (`&&` = and)
- **`!ip`** — traffic that isn't IP at all (you'll find ARP here)

Filters apply live and are fully reversible: clear the bar and press Enter,
everything comes back.

## The trick you'll use forever: Apply as Filter

Filters can be long. The shortcut:

1. Click a packet, and in the **Packet Details** pane expand a layer and click
   any specific field (e.g., `Source Address` inside the IP layer).
2. Right-click that field → **Apply as Filter → Selected**.

Wireshark builds the filter for you (`ip.src == 192.168.1.10` in this case).
This is the single biggest quality-of-life feature in Wireshark; use it
until building filters by hand feels natural.

## The 10 filters worth memorizing

1. `dns` — name lookups
2. `http` — plain web traffic
3. `http.request` — web requests only (not responses)
4. `tls` — encrypted web traffic
5. `ip.addr == <ip>` — one device
6. `tcp.port == 443` — one port, either side
7. `ip.dst == <ip>` — traffic *to* a device
8. `tcp.flags.syn == 1 && tcp.flags.ack == 0` — connection starts (Lab 04)
9. `tcp.analysis.retransmission` — network trouble (Lab 08)
10. `frame contains "text"` — packets whose raw bytes contain that string

## Save your favorites

Right-click the display filter bar → **Manage Filter Favorites** and add the
ones you'll reuse. Your best filters become one-click buttons on the bar.

## Capture filters: the other kind

There's a second filter language that decides what gets *recorded at all*:
the **capture filter**, set via **Capture → Options → the Input tab**. Its
syntax is different — it's tcpdump/BPF syntax:

| Display filter (what you see) | Capture filter (what gets recorded) |
|---|---|
| `ip.addr == 192.168.1.10` | `host 192.168.1.10` |
| `tcp.port == 443` | `tcp port 443` |
| `dns` | `port 53` |

The difference matters:

- A **display filter** is a search over what you already recorded —
  reversible, re-searchable, safe.
- A **capture filter** drops packets *before* they're saved. Anything it
  drops is gone forever.

**Rule of thumb: capture everything, filter with display filters.** Use
capture filters only on very busy networks where recording everything is
impractical.

## Try it yourself

1. Capture a minute of browsing, then show only DNS packets. How many
   different domains did your computer look up in a minute?
2. Use **Apply as Filter** on the *destination IP* field of an HTTP packet.
3. Set the capture filter `port 53` and browse for 30 seconds. Confirm you
   recorded *only* DNS — then notice what you lost (you can't go back and
   study the HTTP traffic, because it was never recorded).

## What you just learned

- Display filters hide packets reversibly; a green bar means valid syntax
- `Apply as Filter` builds filters from a clicked field
- `&&`, `||`, `!`, and parentheses combine filter conditions
- Capture filters (BPF syntax: `host`, `port`) drop packets *before*
  recording — irreversible — so prefer display filters
- Favorite filters can be saved as one-click buttons

**Next:** [Lab 03 — DNS](03-dns.md)
