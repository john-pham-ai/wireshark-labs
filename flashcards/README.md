# Anki Flashcards — Import Guide

~80 cards covering every lab, tagged `lab-00-setup` through `lab-09-capstone`
plus cross-cutting tags: `filter` (all filter-syntax cards), `protocol`,
`concept`, `shortcut`.

## Import (Anki desktop, 2 minutes)

1. Open Anki (desktop app) → **File → Import**.
2. Select `wireshark-anki.tsv`.
3. In the import dialog, set:
   - **Field separator:** Tab
   - **Field mapping:** Field 1 → Front, Field 2 → Back, Field 3 → Tags
   - **Note type:** Basic
   - **Deck:** create one, e.g., `Wireshark Labs`
4. Click **Import**.

(On older Anki versions the mapping is a row of dropdowns above the
preview — same settings.)

> Note: if a card's answer looks like literal text such as `<br>`, enable
> "Allow HTML in fields" in the import options. None of these cards need
> HTML, so you can also leave it off.

## Suggested study cadence

1. **Same day:** after finishing a lab, study that lab's tag — in the
   Browse window, click the tag (e.g., `lab-03-dns`) → select all →
   **Right-click → Toggle Suspend/Unsuspend** so only your current lab's
   cards are active. Or just study the whole deck; the tags keep things
   organized either way.
2. **Drill the filters:** the `filter` tag is pure muscle memory — which
   filter answers which question. Review those as one group.
3. **Finish line:** keep reviewing until the deck is *boring*. Boring means
   you know it.
