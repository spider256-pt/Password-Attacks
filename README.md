# 🔐 Introduction to Password Attacks

## 📌 CIA Triad  
At the core of every information security practice lies the **CIA triad**:  

- **Confidentiality** → Ensuring data is accessible only to authorized users.  
- **Integrity** → Ensuring data is accurate and unaltered.  
- **Availability** → Ensuring resources are accessible when needed.  

This balance is maintained through:  
- Auditing and accounting for files, objects, and hosts.  
- Validating that users have the correct **permissions (authorizations)**.  
- Verifying each user’s **identity (authentication)**.  

---

## 🔑 Authentication

**Authentication** is the validation of identity by presenting factors to a verification mechanism.  
The four primary factors are:  
- **Something you know** (passwords, PINs)  
- **Something you have** (smart cards, tokens)  
- **Something you are** (fingerprints, iris scans)  
- **Somewhere you are** (geolocation-based)  

Authentication methods may involve one or more of these factors, depending on the **sensitivity of the system** and the **required level of security**.  

---

## 🔐 The Use of Passwords  

The most common and widely used authentication method is still the **password**.  

- **Password**: A string consisting of a combination of **letters**, **numbers**, and **symbols** used for identity validation.  

However, passwords remain a **prime target** for attackers due to:  
- Weak or predictable choices (e.g., `123456`, `password`)  
- Reuse across multiple accounts  
- Theft via phishing, malware, or breaches  
- Vulnerability to guessing and cracking attacks  

---

## ⚔️ Password Attacks  

Since passwords are the primary authentication method, attackers employ multiple techniques to compromise them:  

- **Brute Force** → Trying every possible combination.  
- **Dictionary Attack** → Using wordlists of common passwords.  
- **Rainbow Table Attack** → Using precomputed hash tables to crack passwords.  
- **Credential Stuffing** → Reusing leaked username/password pairs on other services.  
- **Social Engineering (Phishing)** → Tricking users into revealing passwords.  

---

## 📖 Summary  

- The **CIA triad** forms the foundation of information security.  
- **Authentication** ensures only valid users gain access.  
- **Passwords** are widely used but inherently weak.  
- **Password attacks** exploit these weaknesses using technical and social methods.  

---

## 🚀 Next Steps  

This repository will expand with:  
- Examples of password cracking techniques (educational only).  
- Best practices for password security.  
- Demonstrations of defensive mechanisms like MFA, password hashing, and monitoring.  
