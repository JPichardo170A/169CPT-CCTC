# Linux Auditing and Logging - Challenges

Challenges covering syslog, XML parsing, and log analysis.

---

## Syslog Facility Codes Reference

| Code | Facility           |
|------|--------------------|
| 0    | Kernel messages    |
| 1    | User-level messages |
| 2    | Mail system        |
| 3    | System daemons     |
| 4    | Authorization      |
| 5    | Syslog internal    |
| 6    | Printer            |
| 7    | Network news       |
| 8    | UUCP               |
| 9    | Clock daemon       |
| 10   | Authorization (private) |
| 11   | FTP daemon         |
| 12   | NTP subsystem      |
| 13   | Log audit          |
| 14   | Log alert          |
| 15   | Clock daemon (2)   |
| 16   | Local use 0        |
| 17   | Local use 1        |
| 18   | Local use 2        |
| 19   | Local use 3        |
| 20   | Local use 4        |
| 21   | Local use 5        |
| 22   | Local use 6        |
| 23   | Local use 7        |

---

## Syslog Severity Levels Reference

| Code | Severity      | Description                        |
|------|---------------|------------------------------------|
| 0    | Emergency     | System is unusable                 |
| 1    | Alert         | Action must be taken immediately   |
| 2    | Critical      | Critical conditions                |
| 3    | Error         | Error conditions                   |
| 4    | Warning       | Warning conditions                 |
| 5    | Notice        | Normal but significant condition   |
| 6    | Informational | Informational messages             |
| 7    | Debug         | Debug-level messages               |

---

## 1. Given an XML document, what is the name of the first XML element?

**Answer:**

Examine the XML structure and identify the element tag name:

```
Scaninfo
```

---

## 2. Give an example of an XML attribute from the same document.

**Answer:**

Attributes are key-value pairs within an element tag. Valid examples:

```
type, protocol, numservices, services
```

Any one of these is a correct answer (e.g., `type="syn"` or `protocol="tcp"`).

---

## 3. What RFC defines the Syslog protocol?

**Answer:**

```
RFC 5424
```

RFC 5424 defines "The Syslog Protocol" and supersedes RFC 3164.

---

## 4. What is the facility code for Authorization messages?

**Answer:**

```
4
```

Facility code 4 corresponds to security/authorization messages.

---

## 5. How many severity codes are defined in Syslog?

**Answer:**

```
8
```

Severity codes range from 0 (Emergency) through 7 (Debug), totaling 8 levels.

---

## 6. What severity level indicates complete system instability (system is unusable)?

**Answer:**

```
0 (Emergency)
```

Severity 0 (Emergency) indicates the system is completely unusable.

---

## 7. Analyze the file `0.log`. What syslog facility generated these messages?

**Answer:**

Calculate the facility from the PRI value: `Facility = PRI / 8`

```
Kernel
```

Facility code 0 corresponds to Kernel messages.

---

## 8. How many different severity levels appear in `0.log`?

**Answer:**

Extract the PRI values and calculate severity for each: `Severity = PRI % 8`

```
8
```

All 8 severity levels (0 through 7) appear in the log file.

---

## 9. Analyze `4min.log`. Which severity levels appear in this log? List them by name from most severe to least severe.

**Answer:**

Calculate severity from PRI values in the log entries:

```
emergency,alert,critical,error,warning
```

These are severities 0 through 4.

---

## 10. Analyze `4sig.log`. Which severity levels appear in this log? List them by name from most severe to least severe.

**Answer:**

```
notice,informational,debug
```

These are severities 5, 6, and 7.

---

## 11. What information is contained in `not.log`? List the facility, clock type, time protocol, and severity.

**Answer:**

Analyze the PRI values and message content:

```
mail,clock,ntp,Notice
```

The log contains messages from the mail facility, clock daemon, NTP subsystem, at Notice severity.

---

## 12. Examine the rsyslog configuration. What facilities are forwarded to a remote server? What is the remote server's IP?

**Answer:**

```bash
cat /etc/rsyslog.conf
```

Look for remote forwarding rules (lines with `@` or `@@`):

```
auth,authpriv,8,10.30.0.1
```

Facilities `auth` and `authpriv` (codes 4 and 10, but listed as `auth,authpriv` and facility `8`) are forwarded to `10.30.0.1`.

---

## 13. Are any logs saved locally according to the rsyslog configuration?

**Answer:**

```bash
grep -v "^#" /etc/rsyslog.conf | grep "/"
```

Look for lines writing to local file paths:

```
Yes
```

---

## 14. What messages are sent to 10.84.0.1? What are the facility code, severity code, and transport protocol?

**Answer:**

Examine the rsyslog configuration for rules forwarding to `10.84.0.1`:

```
0,7,UDP
```

- Facility: **0** (Kernel)
- Severity: **7** (Debug)
- Protocol: **UDP** (single `@` prefix indicates UDP)

---

## 15. Parse all IP addresses from the provided XML file and generate an MD5 hash of the sorted output. What is the hash?

**Answer:**

```bash
grep -oP '\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}' scan.xml | sort -u | md5sum
```

```
0e850f14fc192c5105955ec094287bd2
```

---

## 16. Pretty-print the provided JSON file and generate an MD5 hash of the formatted output. What is the hash?

**Answer:**

```bash
python3 -m json.tool <input.json> | md5sum
```

```
25ebedf7442e470eaaa48b5f7d5b96f4
```
