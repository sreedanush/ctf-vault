# XOR-Based Stream Cipher Challenge

This section contains the solution and methodology for a CTF challenge that used
a custom XOR-based stream cipher. The encryption relied on a generated keystream
derived from a simple linear recurrence of the form:

    s[n+1] = (A * s[n] + C) mod 256

Since the plaintext for one ciphertext was provided, the keystream values could
be recovered directly. From the recovered keystream bytes, the parameters `A`
and `C` of the recurrence were solved using modular arithmetic. Once the
recurrence was reconstructed, all possible seed values (0–255) were tested to
find the one that produced the matching ciphertext.

After identifying the correct seed, the final ciphertext was decrypted by
recomputing the keystream and applying XOR to reveal the original message.

This folder stores the script and logic used to perform the attack, along with
any notes needed to reuse or adapt the technique in future challenges.
