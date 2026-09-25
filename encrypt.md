The encryption itself is strong. **The weak point is your password.** With a good password it can't realistically be broken. With a weak one it can.

**What it uses (from the page's code):**

- **The file:** your file is encrypted with **AES-256-GCM** under a random key that's generated fresh for each file. This is the current industry standard; it both encrypts the data and detects tampering. No practical attack on it is known.
- **The password:** that random key is then locked with your password. The password goes through **PBKDF2 with SHA-512**, set to **16,777,216 iterations** by default, which gives an AES-256 key. That key wraps the file key in the standard CMS password-recipient format. Salts come from the browser's secure random generator.
- **The file format:** it's CMS, the standardized format also used by S/MIME. So this isn't homemade crypto.

**Why the iteration count matters:** every password guess has to run PBKDF2 16.7 million times. For comparison, OWASP recommends about 210,000 iterations for PBKDF2-SHA512, so this default is roughly 80 times higher. That makes each guess very slow, even on GPUs.

**How easy is it to break?**

| Password | Realistic outcome |
|---|---|
| Common word or short password (`qatar2024`, `P@ssw0rd`) | Can be cracked, because dictionary attacks try these first |
| 8 random characters | Hard, but a determined attacker with lots of hardware might manage it |
| 12+ random characters, or a 5–6 word passphrase | Not breakable in practice |

**Other things to keep in mind:**

- There's no way to recover a forgotten password. If you lose it, the file is gone.
- Don't use the **Hint** field for anything that gives the password away, because the hint is stored unencrypted.
- The file name is visible unless you tick **"No file name"** or override it.
- The file is only as safe as the device you decrypt it on. Malware or a keylogger defeats any encryption.
- If you rely on this for sensitive work, use the **offline download** and check its PGP signature. That way you aren't trusting whatever the website serves you on a given day.

I read the code but didn't run a formal audit. Still, the algorithms and settings it uses are all sound, modern choices.
