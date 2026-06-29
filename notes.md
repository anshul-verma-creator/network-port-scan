# Personal Notes — Task 1: Nmap Port Scan

## What I learned
- How to find my local IP range using ipconfig/ifconfig
- Difference between TCP SYN scan (-sS) and other scan types (-sT, -sU)
- Why SYN scans need admin/root privileges (they craft raw packets,
  don't complete the full TCP handshake — "half-open" scanning)
- How to read Nmap output: STATE (open/closed/filtered) vs SERVICE
  (Nmap's best guess based on port number, not 100% confirmed)

## Challenges I faced
- (e.g., "Had to run terminal as Administrator or scan returned no results")
- (e.g., "Only 2 hosts showed up since I was on a mobile hotspot,
  not a full home/office network")

## Observations
- My own machine had more open ports than I expected (135, 139, 445,
  3306, 5432) — surprised that MySQL/PostgreSQL were listening even
  though I wasn't actively using them
- Realized these database ports were probably left open by default
  after installing dev tools (XAMPP/MySQL installer/etc.)

## Questions for further research
- Is -sS actually stealthier than a full connect scan (-sT) on a
  modern network with logging/IDS in place?
- What's the difference between nmap's default scan and -sV
  (service version detection)?
- How would a firewall change these scan results?

## Follow-up actions I'd take in a real environment
- Check Windows Firewall rules for ports 445, 135
- Verify MySQL/PostgreSQL are bound to localhost only
  (netstat -ano | findstr "3306 5432")
- Re-scan after applying fixes to confirm ports are closed/filtered