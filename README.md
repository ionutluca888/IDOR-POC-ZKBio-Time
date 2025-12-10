ZKBioTime-IDOR-POC

ZKTeco BioTime – Insecure Direct Object Reference (IDOR) Vulnerabilities
Sensitive Configuration Password Disclosure Across Multiple Versions

This repository contains several proof-of-concept demonstrations showing that multiple versions of ZKTeco BioTime expose highly sensitive configuration passwords through the endpoint:
```
/base/safe_setting/
```

The endpoint returns HTML containing the fields:
```
backup_encryption_password_decrypt

export_encryption_password_decrypt
```
These values are displayed in cleartext inside password input fields.
During real-world penetration testing, these passwords were found to match the default administrator password, meaning that exposure of these values results in full administrative compromise of the BioTime instance.

Tested Versions and Observed Behavior
1. BioTime 9.0.3 – Unauthenticated Critical IDOR

In version 9.0.3, the endpoint is fully accessible without authentication.
Any remote attacker can directly retrieve cleartext backup and export encryption passwords.

PoC Screenshots:
(see images 1 and 2 in this repository)

Payload:
```
GET http://<target>/base/safe_setting/
```

Result:
Full password disclosure without login.

2. ZKBioTime 9.0.4 Build20250624 – Authenticated Low-Privilege IDOR

In version 9.0.4, the endpoint requires authentication, but no authorization enforcement exists.

A low-privilege user is able to access /base/safe_setting/ and retrieve the same sensitive credentials in cleartext.

PoC Screenshots:
(see images 3 and 4)

Payload:
```
GET http://<target>/base/safe_setting/
Cookie: session=<low-privilege-user-session>
```

Result:
Low-privilege users can obtain administrative configuration passwords.

3. BioTime 9.0.5.2 Build20250428.2823 – Unauthenticated Critical IDOR (Regression)

In the more recent BioTime 9.0.5.2 build, the endpoint becomes publicly accessible again.

Just like in 9.0.3, an unauthenticated attacker can retrieve cleartext backup and export encryption passwords.

PoC Screenshots:
(see images 5 and 6)

Payload:
```
GET http://<target>/base/safe_setting/
```

Result:
Unauthenticated critical exposure of passwords.


Summary

Multiple versions of ZKTeco BioTime expose sensitive configuration passwords via /base/safe_setting/.
Depending on the version, the vulnerability is exploitable:

Unauthenticated (9.0.3, 9.0.5.2)

Authenticated low privilege (9.0.4)

The sensitive fields are returned directly in the HTML response, enabling easy extraction and full system takeover.

Fixed Version

The issue was fully resolved in ZKBioTime 9.0.6, where the /base/safe_setting/ endpoint no longer exposes sensitive configuration passwords and proper access controls are enforced.
