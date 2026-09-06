# OTP-invalidation-issue-on-auth.hackclub.com

Status: Fixed
Reported to: Hack Club Security Disclosure, published with permission from Hack Club Security

# TL;DR
Found a login OTP invalidation issue in auth.hackclub.com that allows any previously issued unused OTPs to grant full access to an account. Since OTPs are the only form of authentication (no password), this issue was initially ranked low impact but the report helped triagers chain and find bigger issues in the authentication flow which eventually earned me a bounty. This writeup covers what OTP invalidation issues are in general, how do they arise, how I found one, its impact and other details.

# Background
Hackclub Auth dashboard is the central user dashboard of all hackclub users where their account information, activities and permissions exist. Since this dahsboard does not uses any passwords and strictly relies on OTPs to login users, OTPs must be handled very securely and carefully, ensuring it follows expected secure principles like one valid OTP at a time, invalidating after use, etc.
OTP invalidation issue arises when the server issues more than one OTP for an account and all of them remain valid. An secure server should ALWAYS invalid other OTPs and only treat the most recently issued unused OTP as valid login credential.

# How I discovered the issue:
I was initially looking for other vulnerabilities in the login flow, such as reusing the same OTP twice to check if server properly invalidates the used OTP, checking if I can send the same OTP to two different emails, etc. After testing for a while, I did not find what I was originally looking for. I already had a bunch of login OTP from previous tests. So naturally, I thought the most recent one should only work but instinctively, I attempted to login with previously issued OTPs and to my surprise, all of them seem to work. Meaning the server DOES NOT invalidate other OTPs after issuing the most recent OTPs. I tested this hypothesis again and it turned out to be correct.
After finding the flaw, I reported to hackclub using their bug bounty channel and a triager quickly addressed and fixed the issue. The triager also left a note stating "The issue was of low impact by itself but with the help of your report, we were able to chain it and find bigger issues. So for that reason, we would like award you with a bounty of $$$"
This made me really happy as I was glad to be of help to secure Hackclub and it's users. Most importantly, be helpful enough to aid in uncovering serious issues that otherwise might have gone unnoticed.

#Impact
1)Previously issued unused OTPs remained valid rather than being invalidated when a newer OTP was issued.
2)Any previously exposed or compromised unused OTP could remain usable despite the user requesting a newer OTP. This could lead to full account takeover as OTPs are the prime authentication credential in HackClub.
3)The flaw weakened an important security property of an OTP-only authentication system.
4)While the issue itself had limited standalone exploitability, it became more significant when considered as part of the broader authentication flow.

# Proof of Concept:
I have dropped a link to video PoC below.

https://github.com/user-attachments/assets/c20dc832-b6c6-4fcc-a336-8e60ef234028

# Remediation:
1)Invalidate previous OTPs when issuing a new one.
2)Allow only the latest OTP to authenticate.
3)Invalidate OTPs after successful authentication.

# Timeline:
Vulnerability discovered: August 1, 2026
Issue Reported: August 1, 2026
Issue Resolved: August 2, 2026
Write-up Published: September 6, 2026

# Lessons / Takeaways :
This finding taught me to test state transitions and assumptions, not just individual requests. An unexpected result from a simple test can reveal deeper flaws in an authentication flow. It also reinforced that even low-impact findings can be valuable when they expose weaknesses that help uncover larger vulnerability chains.

