# Reconnaissance - STRONG Tags

## Level / Points

Level 2 — 5 points

---

## Prompt

Find STRONG text in conference news pages.

---

## Reference Intelligence

- Target: Conference web server
- Look for `<strong>` HTML tags

---

## Steps Taken

1. Used `wget` to download site content.
2. Grepped for 20-character strings within `<strong>` tags in `news.html`.

---

## Verified Answer

**Answer:** `gRl3iMBv2pMmL4TNn9t4`

---

## Notes

- Flags are 20 characters long — use this as a filter when grepping.
- The `<strong>` tag is used to emphasize important text in HTML.
