# Reconnaissance - Contact Info

## Level / Points

Level 2 — 5 points

---

## Prompt

Scrape contacts from the consulting site.

---

## Reference Intelligence

- Target page: NSC-Contacts.html
- Flag is assembled from fragments hidden in table cells

---

## Steps Taken

1. Used `view-source` to inspect `NSC-Contacts.html`.
2. Identified `<strong>` tag fragments embedded within `<td>` cells.
3. Noted that some fragments are hidden using `opacity:0` CSS styling.
4. Concatenated unique fragments to assemble the full flag.

---

## Verified Answer

**Answer:** `yXw56JqEaI4EpyI2qa6A`

---

## Notes

- Some flag fragments are hidden with `opacity:0` — they are invisible in the browser but present in the source.
- Must inspect raw HTML source rather than rendered page.
- Concatenate only unique fragments in order.
