# 🕵️ Forensics – Category Overview

Welcome to the Forensics section of this CTF repository.
This category focuses on analyzing digital artefacts, uncovering hidden data, and reconstructing information from suspicious files, memory dumps, network captures, and more.

The goal of forensics challenges is simple:
👉 Find out what happened, how it happened, and what was hidden.

# 🔍 What You’ll Do in Forensics Challenges
1. File Structure Analysis

Identify the true nature of a file by inspecting:

headers and magic bytes

embedded files

container formats (zip, docx, pptx, pdf, etc.)

# Tools commonly used:

xxd

file

binwalk

foremost

# 2. Steganography Detection

Hidden data inside:

images (JPG, PNG)

audio files

documents

# Techniques may involve:

LSB stego

metadata manipulation

appended/overlay data

embedded archives

# Tools:

stegseek

steghide

zsteg

exiftool

# 3. Network Forensics

Extracting intel from PCAPs such as:

HTTP exfiltration

encoded payloads

suspicious tunneling

credential leaks

command & control patterns

# Tools:

Wireshark

tshark

strings / grep

NetworkMiner

# 4. Memory & Disk Forensics

Includes:

carving executable code

recovering deleted strings/files

analyzing memory dumps

timeline reconstruction

# Tools:

Volatility

Autopsy

Sleuth Kit

# 5. Encoding / Obfuscation

Challenges may use:

Base64 / Hex / ROT

XOR

custom encodings

obfuscated scripts

Your mission is to reverse these transformations and reveal hidden content.

# 🧠 Skills Gained in Forensics Category

Deep understanding of file formats

Recognizing anomalies in network traffic

Extracting and decoding hidden information

Investigating encryption vs. obfuscation

Applying OSINT + forensic logic together

Reconstructing attacker behavior

# 🛠️ Recommended Toolkit

Here’s a solid baseline toolkit for forensic challenges:

stegseek
binwalk
exiftool
strings
xxd
file
zsteg
wireshark
tshark
volatility
autopsy
foremost


A good forensic investigator always checks everything — even things that look harmless.
