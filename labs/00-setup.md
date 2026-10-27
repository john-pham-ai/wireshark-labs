# Lab 00 — Setup: Install Wireshark, Capture Your First Packets

> **Goal:** Get Wireshark installed and capture a few seconds of real network
> traffic from your own machine.

## Install Wireshark

**Windows:** Download from <https://www.wireshark.org/download.html> and run
the installer. When the installer asks about **Npcap**, leave the defaults —
Npcap is the driver that lets Wireshark listen to your network card.

**macOS:** Download the `.dmg` from the same page, or use Homebrew:

```bash
brew install --cask wireshark
```

**Linux (Debian/Ubuntu):**

```bash
sudo apt install wireshark
```

When Debian's installer asks "Should non-superusers be able to capture
packets?", answer **Yes**, then log out and back in.

## What a "capture" actually is

Your network card sends and receives *packets* — small chunks of data — all
day long. Wireshark can copy every packet your card sees into a file so you
can inspect them one by one. That copy is a **capture**.

## Run your first capture

1. Open Wireshark. The first screen shows a list of **interfaces** — your
   network connections.
2. Find the *active* one:
   - **Wi-Fi** if you're on wireless (there'll be a small activity graph
     next to it)
   - **Ethernet** if you're plugged in with a cable
   - On Linux you might see `eth0` or `wlan0`; on macOS, `en0`
3. Double-click the interface with the wiggling graph — the capture starts
   immediately.
4. Let it run for 20–30 seconds while you open a website in your browser.
5. Click the **red stop button** (top left, or `Ctrl+E` / `Cmd+E`).

You should see *hundreds* of rows. Each row is one packet. If your screen is
empty, you picked an inactive interface — go back to
**Capture → Refresh Interfaces** and try the other one.

## Save it

**File → Save As** → `lab00.pcapng`.

`.pcapng` is the standard capture-file format. You can reopen it later, share
it, or send it to someone else with Wireshark. Captures are the "recordings"
of network analysis.

## About promiscuous mode

You may notice a setting called **promiscuous mode** (it's on by default).
Normally your network card ignores packets addressed to other devices;
promiscuous mode makes it copy *everything* passing by, which is essential
on shared networks (and harmless on your home Wi-Fi, where most traffic
between other devices never reaches you anyway).

## Try it yourself

1. Capture for a full minute while you browse normally. How many packets did
   you get? (The status bar at the bottom shows a count.)
2. Save one capture as `lab00.pcapng`, close Wireshark, reopen the file, and
   confirm it all came back.
3. Start a capture, put your computer to sleep for a few seconds, wake it,
   and stop the capture. Can you see the gap in the timestamps?

## What you just learned

- Wireshark copies every packet a network interface sees into a capture
- A capture is saved as a `.pcapng` file you can reopen and share
- Promiscuous mode makes the network card copy all traffic, not just yours
- `Ctrl+E` starts and stops a capture

**Next:** [Lab 01 — Your First Capture](01-first-capture.md)
