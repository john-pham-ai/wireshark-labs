# Wireshark Cheat Sheet

Everything the labs teach you, on one page. Keep it open in a tab.

## The 15 filters you'll actually use

Type these into the **display filter bar** (the green/white box above the
packet list):

| Filter | Shows |
|--------|-------|
| `dns` | DNS queries and responses |
| `http` | HTTP requests and responses (not HTTPS) |
| `http.request` | Only HTTP requests |
| `http.response.code >= 400` | Failed web requests (404, 500, …) |
| `tls` | Encrypted HTTPS traffic |
| `ip.addr == 192.168.1.10` | Traffic to OR from that IP |
| `ip.src == 192.168.1.10` | Traffic FROM that IP |
| `ip.dst == 192.168.1.10` | Traffic TO that IP |
| `tcp.port == 443` | Traffic on that port (either side) |
| `tcp.flags.syn == 1 && tcp.flags.ack == 0` | The SYN of a new connection |
| `tcp.analysis.retransmission` | Re-sent packets (often = network trouble) |
| `dns.time > 0.1` | DNS lookups slower than 100 ms |
| `!(tcp) && !(tls) && !(dns)` | Hide the big three protocols |
| `frame contains "password"` | Packets whose bytes contain that text |
| `!ip` | Non-IP traffic (ARP, etc.) |

Combine with `&&` (and), `||` (or), `!` (not), and parentheses.

## Display vs. capture filters

| | Display filter | Capture filter |
|---|---|---|
| When | Filters what you **see** | Filters what gets **recorded** |
| Where | The green bar | Options → Input / "Capture filter" box |
| Syntax | Wireshark's own (`ip.addr == 1.2.3.4`) | BPF / tcpdump syntax (`host 1.2.3.4`) |
| Example | `tcp.port == 443` | `tcp port 443` |
| Reversible? | Yes (toggle) | No — packets never recorded are gone |

## TCP flags (the four you need)

| Flag | Name | Meaning |
|------|------|---------|
| `SYN` | synchronize | "Let's start a connection" |
| `ACK` | acknowledge | "I received your data" |
| `FIN` | finish | "I'm done sending, let's close" |
| `RST` | reset | "Abort — something is wrong" |

The handshake: `SYN → SYN-ACK → ACK`, then data flows. The Info column shows
flags as `[SYN]`, `[SYN, ACK]`, `[FIN, ACK]`, `[RST]`.

## DNS, in one breath

You type a name → your computer asks a DNS server → the server answers with
an IP. Query = Standard Query A record; Response = Standard Query Response
with one or more **A records** (IPv4 addresses). **CNAME** = an alias
pointing to another name. **TTL** = how long the answer may be cached.

## Common ports

| Port | Protocol |
|------|----------|
| 53 | DNS |
| 80 | HTTP |
| 443 | HTTPS (TLS) |
| 22 | SSH |

## Keyboard shortcuts

| Keys | Action |
|------|--------|
| `Ctrl+E` | Start / stop capture |
| `Ctrl+F` | Find a packet (in visible columns) |
| `Ctrl+.` | Mark a packet |
| → | Expand selected protocol layer |
| ← | Collapse it |

## Right-click power moves

- **Follow → TCP Stream** — read a whole conversation as a transcript
- **Apply as Filter** — filter on the field you clicked
- **Copy → …** — copy a field value without retyping it
- **Protocol Preferences** — per-protocol settings

## Expert information (troubleshooting gold)

**View → Expert Information** collects Wireshark's own warnings:
retransmissions, connection resets, malformed packets, slow responses. Click
a warning to jump to the packet.

## tshark — Wireshark on the command line

```bash
# Capture 50 packets and print a summary of each
tshark -c 50

# Capture DNS traffic only, printing it live
tshark -f "port 53" -i eth0

# Read a saved capture, showing only HTTP requests
tshark -r capture.pcap -Y "http.request"

# Extract the requested URLs from a capture
tshark -r capture.pcap -Y "http.request" -T fields -e http.host -e http.request.uri
```

`-f` = capture filter (BPF syntax), `-Y` = display filter (Wireshark syntax).

## Three rules of thumb

1. **Capture with no filter, narrow with display filters.** Recorded packets
   can be filtered any way you like later; unrecorded ones are gone forever.
2. **A red/black packet is Wireshark telling you something.** Check Expert
   Information before forming a theory.
3. **Most "the network is broken" problems are DNS, retransmissions, or a
   reset (RST).** Check those three first.
