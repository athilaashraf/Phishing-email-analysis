# Case 01: Fake OKX Web3 Extension Promotion

| Field | Detail |
|---|---|
| Sample source | Phishing Pot (public research repository) |
| Email date | 2026-07-08 (UTC) |
| Analysis date | 2026-11-18 |
| Verdict | Phishing (brand impersonation, probable crypto-wallet lure) |
| Severity | Medium |
| Confidence | High that the email is fraudulent; payload not verified (listing unavailable) |

## Summary

An email claiming to promote the "OKX Web3 Extension" urges the reader to
install a browser extension from the Chrome Web Store. The sender display
name says "OKX", but the real sending address belongs to an unrelated
company domain, the email failed authentication checks, and it originated
from a rented hosting server. The install link uses lookalike Cyrillic
characters in the extension name. Microsoft classified the message as spam
and delivered it to Junk.

## Evidence

| # | Check | Finding | Why it matters |
|---|---|---|---|
| 1 | Sender identity | Display name "OKX Extension" (base64-decoded); real address hpayMonitorPlatform@handpay[.]com[.]cn | Display name claims OKX, but the domain is unrelated to okx.com |
| 2 | Return-Path / Reply-To | Return-Path matches From; no Reply-To | No hidden redirect of replies, but the domain is still wrong |
| 3 | SPF | None (sender IP 180.168.61.82) | The sender domain does not authorize this server |
| 4 | DKIM | Fail (signature domain dev[.]roombird[.]ru, key not found) | Signed by a different domain than the one in From, and unverifiable |
| 5 | DMARC | None | No policy told the receiver how to treat failures |
| 6 | Originating server | sb8b3bcb5.fastvps-server[.]com (5.188.28.177), data center in Russia | Email began on a rented hosting server, not an OKX server |
| 7 | Relay server | mail.handpay[.]com[.]cn (180.168.61.82), Shanghai | The company mail server that passed the email to Microsoft |
| 8 | TLS check | verify=FAIL on the hop into spam1.handpay[.]com[.]cn | The connection's certificate could not be verified |
| 9 | Microsoft verdict | SCL 5, delivered to Junk | The provider's own filter judged it as spam |
| 10 | Link | Chrome Web Store URL with Cyrillic lookalike characters in the extension name | Homoglyph trick to imitate "okx-wallet-dashboard" |
| 11 | Link scan (2026-11-18) | VirusTotal 0/93, urlscan no classification, page "This item is not available" | Clean score is expected for a trusted domain; the listing is gone and could not be inspected |
| 12 | Sender domain age | handpay[.]com[.]cn created 2005-10-20, expires 2030-10-20 | Established company domain, not a throwaway attacker domain |
| 13 | IP reputation | 5.188.28.177: 0%, hosting provider. 180.168.61.82: 0%, business line | No reports yet; 0% does not mean safe |

## Assessment of how the email was sent

The email started on a rented server in Russia and was relayed through
handpay's mail server. Handpay's domain is old and legitimate, so its mail
system was probably misused (compromised or misconfigured). This is an
inference from the headers, not a confirmed fact. Handpay is most likely a
victim, not the attacker.

## Indicators of Compromise (IOCs)

| Type | Value | Note |
|---|---|---|
| Sender address | hpayMonitorPlatform@handpay[.]com[.]cn | Display name "OKX Extension" |
| Sender domain | handpay[.]com[.]cn | Legitimate company, likely abused |
| Originating IP | 5.188.28.177 | fastvps hosting, Russia |
| Originating host | sb8b3bcb5.fastvps-server[.]com | |
| Relay IP | 180.168.61.82 | mail.handpay[.]com[.]cn, Shanghai |
| DKIM signing domain | dev[.]roombird[.]ru | Does not match From domain |
| URL | hxxps://chromewebstore[.]google[.]com/detail/[Cyrillic lookalike name]/inophbikolocpigmkhllfkdkiccjpggh | Extension unavailable at time of scan |
| Extension ID | inophbikolocpigmkhllfkdkiccjpggh | Stable identifier for the listing |
| Message-ID | 1783494983.a846f192f19a8fae@handpay[.]com[.]cn | Useful for searching mail logs |
| Subject (decoded) | rodrigo-f-p ⭐ 4.7/5 Rating: OKX Web3 Extension on Chrome Web Store | Useful for searching mail logs |

## Recommended Response

1. **Block the originating IP** 5.188.28.177 at the email gateway.
2. **Block the DKIM signing domain** dev[.]roombird[.]ru, which does not match the sender domain.
3. **Do not block all of handpay[.]com[.]cn.** It is a real company whose mail
   system appears to be abused. Block the specific sender address
   (hpayMonitorPlatform@handpay[.]com[.]cn) instead, and consider notifying
   handpay so they can secure their server.
4. **Search mail logs** for the same Message-ID or subject to see whether other
   users received it, and remove any copies from mailboxes.
5. **Report the extension listing** (ID inophbikolocpigmkhllfkdkiccjpggh) to the
   Chrome Web Store as a suspected impersonation, if it reappears.
6. **If any user clicked or installed anything:** remove the extension, move any
   crypto assets to a new wallet, change passwords, and enable MFA.
7. **User awareness:** remind staff that real crypto exchanges do not send
   unsolicited extension installs by email.

## Limitations

- The extension page was unavailable at scan time (2026-11-18), so the payload
  was not inspected. The email's purpose is inferred from its content.
- No evidence shows whether any recipient interacted with the email. It was
  delivered to Junk.
- The claim that handpay's mail server was misused is an inference from the
  headers. It could be a compromise, an open relay, or stolen credentials.
- AbuseIPDB scores of 0% show no reports, not that the IPs are safe.

## Screenshots

| Evidence | File |
|---|---|
| AbuseIPDB: originating IP | ![AbuseIPDB 5.188.28.177](../screenshots/case01-abuseipdb-5.188.28.177.png) |
| AbuseIPDB: relay IP | ![AbuseIPDB 180.168.61.82](../screenshots/case01-abuseipdb-180.168.61.82.png) |
| VirusTotal: link | ![VirusTotal link](../screenshots/case01-virustotal-link.png) |
| urlscan.io: link | ![urlscan link](../screenshots/case01-urlscan-link.png) |
| WHOIS: sender domain | ![WHOIS handpay](../screenshots/case01-whois-handpay.png) |

## Tools used

Raw header review in a text editor, base64 decoding, AbuseIPDB, VirusTotal,
urlscan.io, WHOIS (CNNIC).