# Testing the Architecture
 
This document records the connectivity tests performed to verify that the multi-tier VPC architecture enforces proper network segmentation.
 
## Test Environment
 
All three instances running in multi-tier-vpc, europe-west1-b:
 
| Instance    | Subnet   | Internal IP | External IP | Tags |
|-------------|----------|-------------|-------------|------|
| web-server  | public   | 10.0.1.x    | Yes         | web  |
| app-server  | private  | 10.0.11.2   | None        | app  |
| db-server   | isolated | 10.0.21.2   | None        | db   |
 
## Test 1: SSH via IAP
 
**Purpose:** Verify administrative access works through Identity-Aware Proxy without exposing SSH to the public internet.
 
**Method:** Clicked SSH button in GCP Console for web-server.
 
**Result:** Connected successfully. The connection was routed through Google's IAP service (35.235.240.0/20), not over the public internet. No public SSH port (22) is open to 0.0.0.0/0 — only to Google's IAP range.
 
**Verdict:** ✅ Pass
 
## Test 2: Web server → App server (should succeed)
 
**Purpose:** Verify that the web tier can communicate with the app tier on port 8080.
 
**Method:** From web-server SSH session:
```
curl -v --connect-timeout 5 10.0.11.2:8080
```
 
**Result:** "Connection refused" — the packet reached the app server (proving the firewall allowed it), but no application is listening on port 8080 yet. This is expected behaviour.
 
**Why "refused" is a pass:** "Connection refused" means the TCP SYN packet arrived at the destination and the OS responded with a RST because no process is bound to that port. This confirms network-level connectivity exists. If the firewall had blocked it, we would see "Connection timed out" instead — the packet would be silently dropped with no response.
 
**Verdict:** ✅ Pass — network path is open
 
## Test 3: Web server → Database server (should fail)
 
**Purpose:** Verify that the web tier CANNOT communicate directly with the database tier. This is the core security property of the architecture.
 
**Method:** From web-server SSH session:
```
curl -v --connect-timeout 5 10.0.21.2:5432
```
 
**Result:** "Timeout was reached" after 5 seconds — the packet was silently dropped by the firewall. The web server has no firewall rule permitting traffic to instances tagged "db".
 
**Why this matters:** If an attacker compromises the web server, they cannot directly access the database. They would need to also compromise the app server first, which is an additional barrier. This is defense in depth.
 
**Verdict:** ✅ Pass — isolation confirmed
 
## Test 4: App server → Database server (should succeed)
 
**Purpose:** Verify that the app tier can communicate with the database tier on port 5432 (PostgreSQL).
 
**Method:** SSH into app-server via IAP, then:
```
curl -v --connect-timeout 5 10.0.21.2:5432
```
 
**Result:** "Connection refused" — the packet reached the database server (firewall allowed it), but no database is running yet. This confirms the allow-app-to-db firewall rule is working correctly.
 
**Verdict:** ✅ Pass — network path is open
 
## Summary
 
| Test                  | Expected      | Actual        | Status |
|-----------------------|---------------|---------------|--------|
| SSH via IAP           | Connect       | Connected     | ✅     |
| web → app (8080)      | Reach server  | Refused       | ✅     |
| web → db (5432)       | Blocked       | Timed out     | ✅     |
| app → db (5432)       | Reach server  | Refused       | ✅     |
 
All four tests confirm that the network segmentation is working as designed. Traffic flows only through the intended paths, and the isolated tier is genuinely unreachable from the public tier.
 
## Key Takeaway
 
The difference between "Connection refused" and "Connection timed out" is critical for network troubleshooting:
 
- **Connection refused** = the packet arrived, but nothing is listening. The network is fine; the application is missing or not running.
- **Connection timed out** = the packet never arrived. Something in the network (a firewall rule, a missing route, a misconfigured security group) is blocking it.