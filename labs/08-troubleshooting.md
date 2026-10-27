# Lab 08 — Troubleshooting: Diagnosing a Slow Connection

> **Goal:** Learn the three classic failure patterns in packet captures —
> slow DNS, retransmissions, and resets — and practice finding each one.

**Prereq:** [Lab 07](07-statistics.md).

This is the payoff lab: everything so far was training for this.

## The three usual suspects

When "the network feels slow," it is almost always one of:

1. **DNS is slow** — every page starts with a lookup; a slow phone book
   slows everything.
2. **Packets are being lost** — TCP covers for it by re-sending
   (**retransmissions**), which makes transfers crawl.
3. **Connections are being refused/reset** — the RST packets from Lab 04.

## Suspect 1: slow DNS

Capture while you browse and run:

```
dns.time > 0.1
```

`dns.time` is the seconds between each query and its response. Anything over
0.1 s (100 ms) is worth a look; over 0.5 s is a smoking gun — you can even
*feel* a half-second stall on page loads.

Diagnosis: if DNS to your router is fast but lookups still take 500 ms, your
router's upstream resolver is slow. The fix is usually "change DNS server"
(try your ISP's vs. a public one) — but now you're diagnosing it with
*measurements*, not vibes.

## Suspect 2: retransmissions

TCP numbers every byte. When the receiver sees a gap, it doesn't whine — it
just re-acknowledments what it has, and the sender notices and **re-sends
the missing packet**. Wireshark flags every such re-send as a
retransmission, and you can filter them:

```
tcp.analysis.retransmission
```

A couple in a long capture: normal internet weather. Clusters of them in one
conversation: data is being lost somewhere on the path (bad Wi-Fi, congested
link, dodgy cable) and that conversation is running at a fraction of its
speed. Filter with `tcp.analysis.duplicate_ack` too — dup ACKs are the
"still missing that piece" signals that precede retransmissions.

## Suspect 3: resets

```
tcp.flags.reset == 1
```

A **RST** is "hang up *now*." The classic pattern: you see `SYN`, then
`SYN, ACK`… no wait — you see `SYN` and the reply is `RST, ACK`. That means
**connection refused**: nothing is listening on that port, or a firewall
rejected you. In the Info column it literally says `Connection reset`.

Real-world translation: "the app can't reach its server" ≠ "the network is
down." It's often a service or firewall problem wearing a network costume.
The capture distinguishes them.

## Expert Information: let Wireshark point

**View → Expert Information.** Wireshark collects its own warnings from
your capture: retransmissions, resets, malformed packets, slow responses.
This is where troubleshooting starts — the counts are a triage list, and
each item links straight to the packets. Anything under **Errors** deserves
a look before you theorize anything.

## Putting it together: diagnose a slow page load

1. Open **Statistics → IO Graph** while filtering for your test conversation.
   Flat lines between bursts = the transfer keeps stalling.
2. Filter `dns.time > 0.1` — was the lookup slow?
3. Filter `tcp.analysis.retransmission` — is data being re-sent?
4. Filter `tcp.flags.reset == 1` — did the connection get killed?

One of these almost always lights up. Whichever one it is *is* the answer;
everything else is detail.

## Try it yourself

1. Simulate suspect #1: switch your DNS to a deliberately slow/bogus one
   (e.g., set DNS server to 10.255.255.1 — lookups will time out), capture
   a page load, and watch the pattern: queries, silence, retries. (Restore
   your real DNS afterward!)
2. Simulate suspect #2: if you're on Wi-Fi, start a big download and walk to
   the edge of your Wi-Fi range. Capture, then filter
   `tcp.analysis.retransmission` and count the carnage.
3. Simulate suspect #3: with nothing listening, run `curl http://localhost:9999/`
   while capturing on the loopback interface. Find the RST.

## What you just learned

- The three usual suspects of "slow": slow DNS, retransmissions, resets
- `dns.time > 0.1` measures lookup latency; `tcp.analysis.retransmission`
  and `tcp.analysis.duplicate_ack` expose packet loss; `tcp.flags.reset == 1`
  exposes refused/killed connections
- **Expert Information** is a built-in triage list
- IO Graph shape (bursts vs. stalls vs. silence) tells the story at a glance

**Next:** [Lab 09 — Capstone](09-capstone.md)
