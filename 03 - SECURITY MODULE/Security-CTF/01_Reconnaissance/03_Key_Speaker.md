# Reconnaissance - Key Speaker

## Challenge Info

- **Level:** 2
- **Points:** 5

## Prompt

Intelligence shows that the Donovian Government is preparing for a conference. You have been tasked to collect all information relating to the speakers. Identify the key individual to find the flag. Your flag will be a unique string of twenty random characters.

---

## Reference Intelligence

- Use `proxychains wget -r http://192.168.28.111:8080` to recursively download website content
  (Collects all accessible files from the conference site through a proxy)
- Directory listing shows multiple HTML files and folders including `speakers.html` and `index.html`
- Browser network proxy set to localhost port 9050 (likely Tor) to access the site and perform manual inspection
- Searching through `speakers.html` and `index.html` revealed the flag embedded in the index page

---

## Steps Taken

1. Configured browser proxy to route traffic through local proxy on port 9050 for anonymity/access

2. Used proxychains with wget to recursively download the full site content on port 8080

```bash
proxychains wget -r http://192.168.28.111:8080
```

3. Inspected speaker information files and the main `index.html` for relevant flags

4. Found the unique flag string hidden in the `<title>` or content of `index.html`

---

## Verified Answer

**Flag:** `uz181llAEc9HZFon69mU`

---

## Notes

- Proxychains combined with browser proxy settings can be critical for accessing restricted or proxied sites
- Flags in CTFs are often hidden in metadata or web page content related to event key individuals
- Manual inspection with browser developer tools helps find subtle clues in downloaded data
