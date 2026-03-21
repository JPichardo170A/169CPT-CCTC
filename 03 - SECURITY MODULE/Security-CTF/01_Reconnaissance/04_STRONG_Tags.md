# Reconnaissance - STRONG Tags

## Challenge Info

- **Level:** 2
- **Points:** 5

## Prompt

There is some STRONG text within the Donovian Conference News that needs to be collected. It will provide the flag to our operations. Your flag will be a unique string of twenty random characters.

---

## Reference Intelligence

- Use `proxychains wget -r http://192.168.28.111:8080` to recursively download website content
  (Retrieves all accessible files from the web server on port 8080 through proxychains)
- Use `ls -l` to list downloaded files and identify relevant HTML documents
  (Confirms presence of `news.html`, which likely contains STRONG tags)
- Use `cat news.html | grep -oP '[A-Za-z0-9]{20}'` to extract 20-character alphanumeric strings
  (Filters out potential flag values embedded in STRONG tags or other content)

---

## Steps Taken

1. Downloaded full site content using proxychains and wget from port 8080

```bash
proxychains wget -r http://192.168.28.111:8080
```

2. Located `news.html` among the downloaded files

```bash
ls -l
```

3. Used grep with regex to extract 20-character strings from the HTML content

```bash
cat news.html | grep -oP '[A-Za-z0-9]{20}'
```

4. Identified the flag embedded in STRONG-tagged text or similar markup

---

## Verified Answer

**Flag:** `gRl3iMBv2pMmL4TNn9t4`

---

## Notes

- STRONG tags often highlight key information in HTML, making them ideal hiding spots for flags
- Regex filtering is a powerful tool for extracting structured data from raw files
- Always inspect content-rich pages like news, index, and about for embedded clues
