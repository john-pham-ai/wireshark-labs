# Lab 09 — Capstone: Diagnose It Yourself

> **Goal:** Given symptoms and a capture checklist, work the case solo.

**Prereq:** All previous labs, ideally with the Anki deck reviewed once
through.

No new material. You run the diagnosis. Below are three scenarios to set up
on your own network, each with a checklist — but **don't peek at the
"Expected findings" until you've written down your own diagnosis.**

## Scenario A: The Mystery of the Half-Second Page Load

**Setup:** Pick a site you visit rarely. In your OS settings, set your DNS
servers to a *slow* one (a public resolver on another continent works — e.g.
if you're in the US, use one from a distant region, or just add a bogus
entry first so lookups retry). Browse to the site and capture the whole page
load. Save as `capstone-a.pcapng`. Restore your DNS settings afterward.

**Your case notes (write these down):**

- [ ] How long did the capture last, per Capture File Properties?
- [ ] Protocol Hierarchy: anything unexpected?
- [ ] What's the slowest DNS lookup? (`dns.time > 0.1`)
- [ ] Did the page need multiple lookups (CNAME chain, subresources)?
- [ ] Retransmissions or resets involved?

**Expected findings** (after you've written yours):

> The page load stalls at the very start: a DNS query with a `dns.time`
> far above 100 ms, possibly with retries. Once the connection to the web
> server starts, the TLS handshake and transfer proceed normally. Diagnosis:
> slow resolver, not a slow site.

## Scenario B: The Vanishing Connection

**Setup:** In a terminal:

```bash
curl http://localhost:12345/
```

with nothing listening on port 12345. Capture on the **Loopback interface**
(`lo0` / `Adapter for loopback traffic capture`) while you run it. Save as
`capstone-b.pcapng`.

**Your case notes:**

- [ ] How many packets is the whole exchange? What are they?
- [ ] Which flags does the reply to your SYN carry?
- [ ] What does curl report on its side?
- [ ] Who sent the reset — and can you prove which packet number?

**Expected findings:**

> Three packets: your `SYN`, the system's `RST, ACK` reply, and nothing
> else. The Info column reads `Connection reset` on the reply. Diagnosis:
> connection refused — no listener on that port. This is what a dead
> server-side process looks like in a capture.

## Scenario C: The Noisy Neighbor

**Setup:** Start a long capture (2–3 minutes) and just use your computer
normally — plus start one big obvious data transfer (an OS update check, a
streaming video, a cloud-sync). Save as `capstone-c.pcapng`.

**Your case notes:**

- [ ] IO Graph: how many distinct traffic "hills" can you identify?
- [ ] Conversations (TCP tab), sorted by bytes: what's the biggest
      conversation and can you name *what it is* (browser? sync app? OS)?
- [ ] Protocol Hierarchy: what fraction of bytes is TLS?
- [ ] Check Expert Information: how many warnings did ordinary use generate?

**Expected findings:**

> There's no single right answer — that's the point. The skill being
> drilled is orientation: turning "a capture happened" into "here are the
> three things this capture is about," using the statistics screens before
> reading any single packet.

## Scoring yourself

For each scenario, you pass if your written diagnosis names **the right
suspect** (DNS, RST, or the big conversations) *and* the evidence you'd cite
(the filter and the packet numbers). If you can do that, you are no longer a
beginner at this — you can pick up an unknown capture and say something true
about it.

## Where to go next

- **Sample captures**: <https://wiki.wireshark.org/SampleCaptures> — real
  captures of malware, weird protocols, and broken networks to practice on.
- **tshark**: everything you learned has a command-line version (see the
  [cheat sheet](../CHEATSHEET.md)) — the tool of choice for automation.
- **Decryption**: setting `SSLKEYLOGFILE` in your browser and pointing
  Wireshark at the log decrypts *your own* TLS traffic for app debugging.
- **Certification-ish depth**: the Wireshark Certified Network Analyst
  (WCNA) material, if you want this to be a career skill.

## What you just learned

- You can run a real diagnostic workflow: orient (statistics) → suspect
  (filters) → prove (specific packets)
- You can write down an evidence-backed diagnosis — the actual professional
  skill

**Finished!** Keep the [cheat sheet](../CHEATSHEET.md) handy and review the
flashcards until they're boring.
