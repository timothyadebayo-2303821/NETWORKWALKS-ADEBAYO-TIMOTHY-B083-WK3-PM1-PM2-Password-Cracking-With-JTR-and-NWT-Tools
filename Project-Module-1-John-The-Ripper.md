# Case Study: Password Cracking a Protected PDF with John the Ripper

**Category:** Cybersecurity | Ethical Hacking | Password Security **Tools Used:** Kali Linux, John the Ripper (JTR), pdf2john

---

## Overview

As part of my hands-on cybersecurity training, I completed a lab exercise focused on password recovery — specifically cracking the password of encrypted PDF files using **John the Ripper (JTR)**, one of the most widely used password auditing tools in the industry.

The goal wasn't just to "break" a password — it was to understand *how* password hashes work, *why* weak passwords fail so quickly under attack, and how security professionals use these same techniques defensively (to test password strength, recover lost credentials, and audit organizational security).

---

## Objective

Recover the password of a protected PDF file (`MyLockedPDF1.pdf`) using John the Ripper on Kali Linux, then apply the same method to two additional sample files to confirm the workflow.

---

## Methodology

**1. The Target File** Started with a password-protected PDF that needed to be cracked — encrypted the way organizations lock sensitive documents like contracts, financial reports, or HR files, so no reader can open it without the correct password.

![Target locked PDF](01-target-pdf.png)

**2. Environment Setup** Worked directly in Kali Linux, which ships with John the Ripper pre-installed — no additional downloads needed. Running `john` with no arguments simply confirmed the binary was available and showed the usage syntax, meaning no setup time was lost before getting into the actual work.

![John the Ripper available in Kali](02-john-tool.png)

**3. Navigated to the Target File** Set the working directory to the location of the locked PDF to keep file handling clean and simple:

```
cd /home/kali/Desktop
```

This is a small but important habit: keeping hash files, wordlists, and target files in one predictable folder matters a lot once you're juggling multiple targets — as was the case here, with three separate PDFs to work through.

![Navigating to the working directory](03-navigate-directory.png)

**4. Extracted the Password Hash** Used `pdf2john`, a helper script bundled with JTR, to convert the PDF's encryption metadata into a crackable hash format:

```
pdf2john MyLockedPDF1.pdf > hash1.txt
```

This is the conceptually important step. John the Ripper can't crack a PDF file directly — it cracks *hashes*. `pdf2john` doesn't touch the password itself; it reads the PDF's internal encryption metadata (algorithm used, key length, salt, and encrypted verification data) and reformats it into a single hash string that JTR understands — essentially extracting the mathematical "puzzle" that the password creates.

![Running pdf2john to extract the hash](04-pdf2john-command.png)

This produced a hash string beginning with `$pdf$...`, containing the PDF's encryption details (algorithm, revision, key length, and cipher data). Each part of that string tells JTR what it's dealing with — PDF version, encryption revision, key length, and the encrypted data blocks. JTR parses this automatically, but it's worth knowing that structure exists, since different file types (ZIP, Office docs, Linux shadow files) each have their own hash formats.

![Extracted hash contents](05-extracted-hash.png)

**5. Ran the Attack** Pointed John the Ripper at the extracted hash file. By default, JTR ran a **wordlist (dictionary) attack**, trying every word in its bundled wordlist (`/usr/share/john/password.lst`) as a candidate password, hashing each guess the same way the PDF's original password was hashed, and comparing the result. When a guess matches, that's the password.

**6. Password Recovered** JTR cracked the hash almost instantly, recovering the password: `password1`. Attacks like this succeed quickly when passwords are weak or common, since they appear early in standard wordlists. The same attack against a long, random passphrase would take vastly longer — or fail outright — which is really the core lesson of this exercise.

![Password successfully cracked](06-cracked-password2.png)

**7. Verified the Result** Opened the PDF using the recovered password to confirm successful decryption — file opened cleanly, confirming the crack was accurate. This step matters because cracking a hash is only proof on paper until the recovered password is actually used to open the real file; false matches can occasionally occur with weaker hash algorithms, though that wasn't an issue here.

![PDF successfully unlocked and opened](08-pdf3-opened.png)

**8. Repeated for Additional Samples** Applied the identical workflow to two further locked PDFs, successfully cracking both and capturing their embedded "flags." Repeating the process and getting clean results both times turns "I got lucky once" into "I understand and can reliably repeat this workflow" — the kind of demonstrated consistency that matters far more than a single screenshot.

![Third PDF unlocked with flag captured](08-pdf3-opened.png)
![Third PDF unlocked with flag captured](09-pdf3-opened.png)

---

## Key Takeaways

- **Weak passwords fail instantly.** A dictionary-based attack cracked `password1` in seconds — no brute force needed. This is exactly how real-world attackers compromise poorly secured documents and accounts.
- **Hashing vs. Encryption matters.** This exercise reinforced the distinction: encryption is reversible with the right key, while hashing is one-way. PDF password protection uses encryption, which is why the hash can be attacked and the key recovered.
- **Tooling matters, but understanding matters more.** `pdf2john` and JTR make the technical process straightforward — the real value is understanding *why* the attack works, so it can inform better defensive practices (strong, unique passwords + passphrase policies).
- **Repeatability = reliability.** Running the same method against three different files and getting consistent results is what separates a fluke from a validated skill.

---

## Why This Matters

Password auditing is a core skill in blue team, GRC, and penetration testing roles. Understanding how quickly weak passwords fall to basic tooling is what makes password policy recommendations credible — you're not just repeating best practice, you've seen the failure mode firsthand.

---

*This is part of an ongoing hands-on cybersecurity learning journey — documenting real lab work as I build toward a career in blue team defense, threat detection, and AI-augmented security operations.*

#CyberSecurity #EthicalHacking #JohnTheRipper #PasswordSecurity #InfoSec #BlueTeam #LearningInPublic
