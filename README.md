# Vulnerability Scanning & Assessment: OWASP Juice Shop

A non-intrusive vulnerability assessment of **OWASP Juice Shop**, an intentionally vulnerable web application, performed in an isolated local lab. This project is Task 3 of a Cybersecurity & Ethical Hacking task series and covers network reconnaissance, service enumeration, web server scanning, hidden path discovery, risk rating and a formal Vulnerability Assessment Report.

> **Ethics and scope:** All testing was performed only against a system I own and control, on `127.0.0.1`, in an isolated lab. No exploitation, denial-of-service or destructive techniques were used. Do not run these tools against systems you do not own or have written permission to test.

---

## Objectives

- Discover the target and confirm it is reachable
- Enumerate open ports, services and versions
- Identify misconfigurations and exposed paths
- Rate each finding by risk and recommend realistic remediation
- Document everything in a professional Vulnerability Assessment Report (VAR)

## Environment

| Item | Detail |
|---|---|
| Scanner OS | Kali Linux |
| Target | OWASP Juice Shop, `127.0.0.1:3000` (TCP) |
| Setup | Single machine: target and scanning tools on the same host |

## Tools

| Tool | Purpose | Command used |
|---|---|---|
| Nmap 7.99 | Host discovery, port and service enumeration | `nmap -sn 127.0.0.1` and `nmap -sV -p3000 -T4 127.0.0.1` |
| Nikto 2.6.0 | Web server misconfigurations | `nikto -h http://127.0.0.1:3000` |
| Dirsearch | Hidden files and directories | `dirsearch -u http://127.0.0.1:3000` |

Nmap was limited to port 3000 so the assessment covered only the target application and not other services on the host machine.

## Methodology

1. **Host discovery**: confirmed the target was alive and reachable.
2. **Port and service enumeration**: identified the single open port (3000) and the HTTP application behind it.
3. **Web server scan**: Nikto checked for missing headers, exposed paths and misconfigurations.
4. **Hidden path discovery**: Dirsearch looked for unlinked files and directories.
5. **Analysis**: reviewed all output, excluded probable false positives, rated each finding and wrote remediation advice.

## Results summary

**7 findings: 0 Critical, 0 High, 2 Medium, 5 Low**

| ID | Finding | Risk | Source |
|---|---|---|---|
| VULN-01 | Missing Content-Security-Policy header | Medium | Nikto |
| VULN-02 | Publicly reachable `/ftp/` and `/public/` paths (provisional, pending content review) | Medium | Nikto |
| VULN-03 | Missing HSTS, Permissions-Policy and Referrer-Policy headers | Low | Nikto |
| VULN-04 | Permissive CORS policy (`Access-Control-Allow-Origin: *`) | Low | Nikto, Nmap |
| VULN-05 | Unhandled server errors (HTTP 500) on many `/api/` paths | Low | Dirsearch |
| VULN-06 | API documentation endpoint exposed (`/api-docs`) | Low | Dirsearch |
| VULN-07 | `robots.txt` discloses a path of interest | Low | Nikto |

Several Nikto items (for example shell history files and generic `.json` files) were **excluded as probable false positives**, because single-page applications often return the same page for unknown paths. The report documents these exclusions and the reasoning.

## Repository structure

```
.
├── README.md
├── report/
│   └── Vulnerability_Assessment_Report.pdf
├── evidence/
│   ├── host-discovery.txt
│   ├── full-service-scan.txt
│   ├── nikto.txt
│   ├── dirsearch.txt
│   └── scan-log.txt
├── screenshots/
└── scripts/
    └── task3_scan.sh
```

## Reproduce the scans

Run only against your own lab target.

```bash
# 1. Start your own Juice Shop instance and confirm it loads at http://127.0.0.1:3000

# 2. Run the helper script (checks tools, refuses non-lab targets, saves output to ~/lab/scans)
chmod +x scripts/task3_scan.sh
NMAP_PORTS=3000 ./scripts/task3_scan.sh 127.0.0.1 3000
```

The script refuses any target that is not localhost or a private IP address.

## What I learned

- How analysts move from discovery to enumeration to scanning in a structured workflow
- Reading Nmap, Nikto and Dirsearch output and judging which results are real findings
- Spotting likely false positives on single-page applications instead of reporting everything a scanner prints
- Rating risk and writing clear, specific remediation advice
- Presenting results for both technical and non-technical readers

## Next steps

- Task 4: controlled validation of the highest-risk findings and deeper web security testing
- Re-scan after applying the remediation plan to confirm the issues are closed
- Optional deeper scan with OpenVAS/Greenbone or Nessus

## Disclaimer

This repository is for educational purposes. The target is an intentionally vulnerable training application, and all activity took place in a controlled lab.
