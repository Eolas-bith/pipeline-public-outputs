<img width="1536" height="1024" alt="ChatGPT Image May 1, 2026, 08_00_57 PM" src="https://github.com/user-attachments/assets/f6d58439-6d04-4a69-a46c-c50226ba87c4" />

# Threat Intelligence & Analysis Pipelines

Structured methodology for threat intelligence, malware analysis, OSINT, and narrative intelligence work. Skills are encoded as self-contained files that an analyst or LLM can load as context and execute step-by-step. All core pipelines run on open-source tooling.

---

## Malware Analysis

Automated static analysis pipeline for ELF and PE samples. A single invocation runs the full tool chain and emits structured JSON plus a publication-ready HTML report. An 8-phase manual mode covers adversarial samples that resist automation.

The pipeline extracts: binary structure and section entropy, compiler/packer fingerprinting, static and decoded strings (including stack strings and XOR-obfuscated data), cryptographic constants, MITRE ATT&CK techniques via capability extraction, import tables, anti-analysis artefacts, and — where the family is known — embedded config. YARA rules are generated from the analysis output and validated before finalisation. The completed event is pushed to MISP.

**Tools:** `malcat` · `FLOSS` · `capa` · `signsrch` · `xorsearch` · `diec` · `strings` · `binwalk` · `upx` · `unipacker` · `Ghidra` · `Rizin` · `CyberChef` · `YARA CLI` · `PyMISP`

**Outputs:** structured JSON (iocs, mitre[], capabilities, functions_table, yara_rules, config_analysis) · HTML report · MISP event

Windows PE reversing extends the pipeline with a dedicated 8-phase manual methodology covering dynamic linking reconstruction, anti-debugging defeat, and persistence mechanism identification.

---

## YARA Rule Generation

Synthesises family-level and variant-level YARA signatures from static analysis artefacts — strings, section names, import patterns, and capa output. Minimum 3 conditions per rule with 2-byte byte-string patterns; each condition carries an explanatory comment. Rules are validated against the existing sample corpus before use and packaged for EDR/SIEM deployment.

**Tools:** `YARA CLI` · `FLOSS` · `capa` · `malcat`

---

## Threat Intelligence — IOC Enrichment & Pivoting

6-phase pipeline starting from a single indicator (hash, IP, domain, or URL) and fanning out to reconstruct adversary infrastructure and collect related samples.

Pivot chains: hash → VT contacted domains → passive DNS → related IPs → more domains; domain → historical IP resolution → communicating files → samples; IP → reverse DNS → all hosted domains → cross-campaign linking.

Attribution runs through 3–4 competing hypotheses scored against available evidence. Confidence gates: >70% proceed, 50–70% conditional (hypothesis-testing mode), <50% hold.

**Tools:** `VirusTotal` (public API) · `WHOIS` · `CRT.sh` · `URLScan` · `theHarvester` · `dig` · `PyMISP`

**Outputs:** IOC CSV · STIX 2.1 bundle · MISP event · markdown investigation report

---

## MISP Integration

Structured event creation and push via PyMISP. Maps analysis artefacts to correct MISP attribute types (ip-dst, domain, md5, sha256, mutex, regkey, url, yara, and relationships), validates structure, and pushes to the configured self-hosted instance.

---

## Person of Interest (POI) OSINT

10-phase investigation of individuals: lawyers, judges, journalists, executives, activists. Establishes identity from minimal input, enumerates digital presence across platforms and registries, maps affiliations and infrastructure, and scores conflict-of-interest findings against a defined legal standard.

Czech registry coverage: ARES, ČAK, MoJ expert registry, Commercial Register, Charles University researcher database, notarial registry. Domain `changed` date within 7 days of investigation start is flagged as possible counter-surveillance awareness.

Conflict scoring: CRITICAL (active legal ground for recusal) · HIGH (strong circumstantial conflict) · MEDIUM (possible conflict) · LOW (structural proximity). Each finding is linked to its legal basis.

**Tools:** `Sherlock` · `theHarvester` · `WHOIS` · `dig` · `CRT.sh` · `mcp-webcrawler` · `Playwright` · `ipinfo.io` · `Wayback Machine` · `PyMISP`

**Outputs:** structured POI profile · raw username enumeration · relationship map · MISP attributes

---

## Influence Operations — Media & Court Coverage

8-phase investigation of media activity around court cases and individuals. Catalogs coverage across a 7-year lookback window, extracts narrative framing, and scores inter-outlet coordination by content and timing.

Coordination tiers: CONFIRMED (identical verbatim phrases or same factual error across outlets) · HIGH (same framing angle, same day, different outlets) · MEDIUM (repeated framing across weeks) · LOW (timing alignment without content overlap).

Narrative frames tracked: credibility attack, evidence contamination, victim-blaming, proportionality deflection, institutional capture.

Czech legal mapping: §8b CrPC, §180 CC, §337 CC, Press Law 46/2000 Sb., GDPR Art. 6/9.

The pipeline produces an interactive social graph with node/edge typing (vis.js) and exports Gephi-compatible files (GEXF + CSV) for offline clustering of large graphs.

**Tools:** `mcp-webcrawler` · `Playwright` · `vis.js` · `Gephi` · `PyMISP` · `Wayback Machine`

**Outputs:** article catalog · coordination evidence · master timeline · relationship map · interactive HTML report · MISP event · markdown report

---

## Narrative Threat Actor — Compound CTI + Narrative Investigation

Applies CTI discipline to human influence actors. Combines POI OSINT, media analysis, and threat intelligence into unified campaign attribution, mapping operations to DISARM TTPs and scoring attribution confidence against formal criteria.

Attribution model: CONFIRMED (direct evidence — byline, court record, on-record statement) · HIGH (multiple independent corroborating indicators) · MEDIUM (single strong or multiple weak indicators) · LOW (circumstantial) · SPECULATIVE (excluded from final report).

Campaign structure follows the infrastructure analogy: Production → Processing → Distribution → Amplification. Ongoing monitoring runs monthly Sherlock sweeps, WHOIS change detection, and weekly article alerts.

---

## Analytical Frameworks

**Structured Analytical Techniques:** Analysis of Competing Hypotheses (ACH, Heuer falsification method), Key Assumptions Check, Quality of Information Check, Red Team, Devil's Advocacy, Alternative Futures Analysis.

**Intrusion mapping:** Diamond Model of Intrusion Analysis (4-vertex, Axiom 7 infrastructure-reuse pivoting), Cyber Kill Chain (7 phases), MITRE ATT&CK (14 tactics, Enterprise / Mobile / ICS).

**Threat modeling:**

| Framework | Use case |
|-----------|----------|
| STRIDE / STRIDE-LM | DFD-based system decomposition, trust boundary analysis |
| DREAD | Numeric risk prioritisation per threat (0–50) |
| PASTA | 7-stage attacker-centric, business-impact-aligned |
| VAST | Agile/DevOps CI/CD integration |
| OCTAVE Allegro | Organizational and insider threat |
| LINDDUN / LINDDUN PRO | Privacy threat analysis with PET mapping |

**Quantitative risk:** FAIR with Beta-PERT distributions and Monte Carlo simulation (10k–100k iterations) → Loss Exceedance Curve; FMEA (Severity × Occurrence × Detection RPN); NIST RMF (SP 800-37 Rev. 2); ISO/IEC 27005:2022.

Convergent pipeline: Diamond Model → FMEA → FAIR → Monte Carlo → LEC → executive report.

---

## Threat Report & Intelligence Writing

Agentic multi-format writing pipeline. Single structured input produces any of 20+ document types via parallel section-writer agents, a Mermaid diagram agent, a coherence review pass, and Pandoc compilation to PDF/DOCX/HTML.

| Category | Types |
|----------|-------|
| Threat Intelligence | flash, intel-bulletin, advisory, sitrep, campaign-report, actor-profile, annual-report |
| Malware & Vulnerability | malware-report, re-writeup, cve-advisory, exploit-analysis, rule-doc |
| Practitioner | tech-blog, strategic-blog, whitepaper, case-study, exec-summary |
| Policy & Regulatory | policy-brief, consultation, expert-witness |

All outputs carry TLP markings and explicit confidence levels (HIGH / MODERATE / LOW) with stated basis. MITRE ATT&CK IDs are verified against current Navigator. Gaps are flagged `[VERIFY]`, never filled.

**Tools:** `Pandoc` · `Mermaid CLI` · `python-stix2` · `Vale`

---

## Campaign Workflows

**BPFDoor / GTPdoor** — end-to-end workflow for ELF backdoor variants targeting telecom infrastructure: sample intake → TLSH clustering → 8-phase static analysis → variant diff → IOC extraction → MISP push → YARA update.

Families covered: BPFDoor (11+ variants), GTPdoor, BPF-Backdoor, PwnKit (CVE-2021-4034). Artefacts include Suricata rules (strict + production variants), YARA signatures, EDR/SIEM deployment packages, and a MISP event template.

https://eolas-bith.github.io/pipeline-public-outputs/gpt-proxy-backdoor_report.html
