# Reconnaissance - Contact Info

## Challenge Info

- **Level:** 2
- **Points:** 5

## Prompt

Intelligence suggests that the Donovian Consulting Group appears to have sensitive contacts available on their public-facing site. Scrape the data and piece together the flag. Your flag will be a unique string of twenty random characters.

---

## Reference Intelligence

- Accessed raw HTML via browser: `view-source:http://192.168.28.111/sites/NSC-Contacts.html`
  (Used browser source view to inspect hidden and styled elements)
- Flag fragments were embedded in `<strong>` tags within `<td>` cells styled to obscure visibility
- Some entries used `style="opacity: .0;"` to hide duplicate or misleading data
- Extracted and concatenated unique fragments from visible and hidden entries

---

## Examples Found in HTML Source

```html
1. <td class="f1aG">f1rstpArt@<strong>yXw56</strong></td>
2. <td class="f1aG" style="opacity: .0;">s3cond0n3@<strong>JqEaI</strong></td>
3. <td class="f1aG">th1rd@<strong>4EpyI</strong></td>
4. <td class="f1aG" style="opacity: .0;">f0urthpArt@<strong>2qa6A</strong></td>
5. <td class="f1aG" style="opacity: .0;">s3cond0n3@<strong>JqEaI</strong></td>  (duplicate - ignored)
```

---

## Steps Taken

1. Opened the NSC-Contacts.html page in browser source view
2. Located all `<td>` entries containing `<strong>` tags
3. Filtered out duplicates and hidden entries using CSS clues
4. Concatenated the unique fragments in order of appearance

---

## Verified Answer

**Flag:** `yXw56JqEaI4EpyI2qa6A`

---

## Notes

- Flags may be split across multiple HTML elements and require manual reconstruction
- Hidden styles like `opacity: .0;` are often used to obfuscate or distract in CTF challenges
- Always inspect both visible and hidden elements when scraping sensitive web data
