# Wireshark Labs

A hands-on, lab-driven tutorial that takes you from "what is a packet?" to
diagnosing a slow network connection — using Wireshark, the world's most
popular network protocol analyzer.

No networking background required. Every concept is explained the moment you
need it.

## How this works

Each lab is a short, guided exercise: you do something on your own machine,
then read what just happened. You learn by capturing real traffic, not by
reading theory.

**Start at [Lab 00](labs/00-setup.md).** Do one lab a day — the arc takes about
a week and a half.

| Lab | What you'll do | What you'll learn |
|-----|----------------|-------------------|
| [00 — Setup](labs/00-setup.md) | Install Wireshark, run your first capture | Capture interfaces, promiscuous mode, saving `.pcap` files |
| [01 — Your First Capture](labs/01-first-capture.md) | Capture a website visit | The 3-pane layout, frames vs. packets, coloring rules |
| [02 — Filters](labs/02-filters.md) | Cut a noisy capture down to what matters | Display vs. capture filters, the 10 filters you'll actually use |
| [03 — DNS](labs/03-dns.md) | Trace what happens when you type a URL | DNS queries and responses, packet-detail anatomy |
| [04 — The TCP Handshake](labs/04-tcp-handshake.md) | Watch a connection get established | SYN / SYN-ACK / ACK, sequence numbers, Follow Stream |
| [05 — HTTP](labs/05-http.md) | Read a full request/response conversation | Follow TCP Stream, HTTP headers and status codes |
| [06 — TLS](labs/06-tls.md) | See why you can't read HTTPS contents | The TLS handshake, certificates, plaintext vs. encrypted |
| [07 — Statistics](labs/07-statistics.md) | Zoom out on a capture | Conversations, protocol hierarchy, IO graphs |
| [08 — Troubleshooting](labs/08-troubleshooting.md) | Diagnose a deliberately slow site | Retransmissions, latency, expert information |
| [09 — Capstone](labs/09-capstone.md) | Diagnose a "broken" scenario solo | Everything, under mild pressure |

## Flashcards

A matching Anki deck (~80 cards) lives in
[`flashcards/wireshark-anki.tsv`](flashcards/wireshark-anki.tsv), tagged per
lab. See [`flashcards/README.md`](flashcards/README.md) for the 60-second
import instructions and a suggested study cadence.

## Cheat sheet

[`CHEATSHEET.md`](CHEATSHEET.md) is a one-page reference of the filters,
protocol facts, and shortcuts you pick up along the way — keep it open in a
tab while you work through the labs.

## Requirements

- A computer you can install software on (Windows, macOS, or Linux)
- Wireshark (free — installed in Lab 00)
- A web browser

## License

[MIT](LICENSE) — use it, fork it, share it.
