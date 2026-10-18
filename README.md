# Password Security and Credential Attack Analysis

## Overview
This project investigates the direct relationship between password complexity, hashing mechanisms, local security policies, and system resistance against credential-based attacks. By combining attack simulations (brute-force and dictionary attacks) in Kali Linux with defensive policy enforcement in Windows Local Group Policy Editor (`gpedit.msc`), this lab demonstrates how authentication controls significantly reduce the attack surface against automated credential recovery techniques.

### **Core Portfolio Question**
> *"How do password strength and authentication controls affect resistance to credential attacks?"*

---

## Points Investigated

* **Password Strength & Entropy:** Evaluating character set variety (uppercase, lowercase, numbers, special characters) and length against automated cracking tools.
* **Cryptographic Hashes:** Analyzing how passwords are stored using algorithms like LM (LanMan) and NTLM hashes.
* **Brute-Force Attacks:** Demonstrating systematic character combination guessing using computational tools.
* **Dictionary Attacks:** Measuring the speed of hash recovery using precomputed lookup tables and wordlists.
* **Password Policies:** Implementing strict password length, complexity, age, and history rules in Windows.
* **Account Protection & Rate Limiting / Lockout:** Configuring threshold triggers, duration timeouts, and reset counters to neutralize automated attacks.

---

## Tools & Environment

* **Target Operating Systems:** Kali Linux (Virtual Machine) & Windows 10/11 Local Environment.
* **Policy Management Tool:** Windows Local Group Policy Editor (`gpedit.msc`).
* **Hash Generation:** LM Hash Calculator (`toolkk.com`).
* **Cracking Tools:**
  * **Ophcrack:** Executed in Kali Linux for brute-force attacks against LM hashes[cite: 2].
  * **CrackStation:** Used for precomputed dictionary lookup attacks against NTLM hashes[cite: 1].

---

## Methodology & Implementation

### Phase 1: Attack Simulations (Kali Linux & Online Lookup Tables)

#### 1. Brute-Force Attack (LM Hashes & Ophcrack)
* **Objective:** Test how quickly simple passwords can be cracked compared to complex combinations.
* **Execution:**
  * Created LM hashes for simple passwords like `"car"` and `"van"` using an online LM hash calculator.
  * Executed a brute-force attack in **Ophcrack** within a Kali Linux VM[cite: 2].
  * Tested the same brute-force attack against a complex password (`"P@ssw0rd"`).
* **Results:**
  * `"car"`: **Cracked** in Ophcrack in approximately 2 minutes and 5 seconds[cite: 2].
  * `"P@ssw0rd"`: **Not Cracked**; successfully resisted brute-force attempts due to increased length, mixed-case letters, digits, and special characters.

#### 2. Dictionary Attack (NTLM Hashes & CrackStation)
* **Objective:** Assess the vulnerability of common or predictable passwords against precomputed rainbow/lookup tables.
* **Execution:**
  * Submitted NTLM hashes for passwords such as `"P@ssword"` and `"Admin"` to **CrackStation**[cite: 1].
* **Results:**
  * NTLM Hash `A25B2710BA9DE114396ADC7DFB0A7235`: **Cracked** instantly as `"Admin"`[cite: 1].
  * NTLM Hash `E19CCF75EE54E06B06A5907AF13CEF42`: **Cracked** instantly as `"P@ssword"`[cite: 1].
  * Unknown/High-entropy Hash `956FE8EBA7DFCF617857E9639849B4C1`: **Not Found** in the database[cite: 1].

---

### Phase 2: Windows Authentication Policy Hardening (`gpedit.msc`)

To defend against the credential attacks observed in Phase 1, Windows security policies were configured under `Computer Configuration -> Windows Settings -> Security Settings -> Account Policies` using `gpedit.msc`:

1. **Password Policy Configuration:**
   * **Minimum Password Length:** Enforced higher minimum character counts.
   * **Password Must Meet Complexity Requirements:** Enabled (requiring uppercase, lowercase, numbers, and symbols).
   * **Maximum Password Age:** Set rotation period to enforce periodic credential updates.
   * **Minimum Password Age:** Configured to prevent immediate back-to-back password cycling.
   * **Enforce Password History:** Set history retention to prevent users from immediately reusing old passwords.

2. **Account Lockout Policy Configuration:**
   * **Account Lockout Threshold:** Defined the maximum allowed failed login attempts before an account is locked.
   * **Account Lockout Duration:** Set the time duration an account remains locked out following threshold breaches.
   * **Reset Account Lockout Counter After:** Specified the time window before failed attempt counters reset.

---

## Summary of Findings

| Test Scenario | Input / Credential | Method / Tool | Outcome & Observations |
| :--- | :--- | :--- | :--- |
| **Brute-Force Attack** | `"car"`, `"van"` (LM Hash) | Ophcrack (Kali Linux VM) | **Cracked** (~2m 5s)[cite: 2]. Short length and weak LM hash format made it easy to recover. |
| **Brute-Force Attack** | `"P@ssw0rd"` (LM Hash) | Ophcrack (Kali Linux VM) | **Not Cracked**. High character variety (uppercase, lowercase, numbers, symbols) drastically expanded search space. |
| **Dictionary Attack** | `"Admin"`, `"P@ssword"` (NTLM Hash) | CrackStation Lookup Table | **Cracked** instantly[cite: 1]. Dictionary and common substitution patterns are easily recovered via precomputed tables. |
| **Policy Hardening** | `gpedit.msc` Rules | Windows Local Group Policy | **Protected**. Rate limiting, lockout thresholds, and length rules block automated brute-force scripts. |

---

## Key Takeaways & Security Recommendations

1. **Password Complexity & Length Matter:** Simple, short passwords (e.g., `"car"`, `"Admin"`) are trivially cracked within seconds or minutes using basic automated tools[cite: 1, 2]. Users must combine uppercase letters, lowercase letters, numbers, and special characters to significantly boost entropy.
2. **Prevent Password Reuse:** Enforcing password history policies in Local Group Policy prevents users from cycling back to compromised or recycled credentials, mitigating **password spraying** and **credential stuffing** attacks.
3. **Limit Failed Login Attempts (Rate Limiting & Lockout):** Setting an Account Lockout Threshold limits consecutive failed authentication attempts, effectively neutralizing automated brute-force scripts before they can guess the correct combination.
4. **Mandate Regular Password Expiration:** Setting a maximum password age forces users to update credentials periodically, reducing the lifespan and utility of stolen or leaked hashes.
