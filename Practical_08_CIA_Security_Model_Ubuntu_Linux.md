# Practical 7 — Demonstration of the CIA Security Model Using Ubuntu/Linux

## 1. Aim

To demonstrate the three principles of the CIA security model—Confidentiality, Integrity, and Availability—using practical commands on an Ubuntu/Linux system.

## 2. Source and scope note

The available Cloud Computing practical guide identifies this experiment as **CIA Security Model (Ubuntu/Linux)**. The steps below demonstrate the three CIA principles using file encryption, a checksum, and a local Apache web server. If your official question lists different tools or commands, follow that exact question.

These steps are intended for an Ubuntu system or Ubuntu virtual machine that you are authorized to administer. They do not require an AWS account for the basic demonstration.

## 3. Requirements

- An Ubuntu/Linux machine or virtual machine.
- A user account with `sudo` access.
- Terminal access.
- Internet access for installing packages, if they are not already installed.
- Packages:
  - `openssl` for encryption/decryption.
  - `apache2` for the local web-server availability demonstration.
  - Standard utilities such as `sha256sum`, `cat`, `chmod`, and `systemctl`.

## 4. CIA model overview

### Confidentiality
Only authorized people should be able to read protected information. In this practical, a sample file is encrypted with OpenSSL. The encryption password must be kept private.

### Integrity
Data should remain accurate and unmodified unless an authorized change is made. In this practical, a SHA-256 checksum is created and checked. Modifying the file should make the check fail.

### Availability
Authorized users should be able to access a service when it is needed. In this practical, the Apache web server is started and tested locally.

**Important:** A checksum can detect a change when compared with a trusted checksum, but it does not by itself prove who created the file. Similarly, a local Apache test demonstrates that the service is running on the machine; it is not a complete production availability or disaster-recovery design.

## 5. Step 1 — Update Ubuntu and install required tools

Open a terminal and run:

```bash
sudo apt update
sudo apt install openssl apache2 -y
```

Enter your Ubuntu password if prompted. The password will not appear as you type.

Verify OpenSSL:

```bash
openssl version
```

Verify Apache service availability:

```bash
systemctl --version
```

Expected:
- The OpenSSL version is printed.
- The systemd version is printed.

If your lab machine does not use systemd, the service-management commands may differ. The procedure here is for a standard Ubuntu installation using systemd.

## 6. Step 2 — Create a working directory and sample file

Use a separate directory so the practical's sample files do not get mixed with other work.

```bash
mkdir -p ~/cia-practical
cd ~/cia-practical
```

Create the sample file:

```bash
printf '%s\n' 'Secret cloud data for CIA practical.' > secret.txt
```

Display the original content:

```bash
cat secret.txt
```

Expected output:

```text
Secret cloud data for CIA practical.
```

Confirm the current directory and files:

```bash
pwd
ls -l
```

Expected: the current directory is under your home folder, and `secret.txt` is listed.

## 7. Part A — Demonstrate Confidentiality with encryption

### Step 1 — Encrypt the file

Run:

```bash
openssl enc -aes-256-cbc -salt -pbkdf2 -in secret.txt -out secret.enc
```

OpenSSL prompts for an encryption password and then asks you to verify it. Use a password suitable for this classroom demonstration and remember it for decryption. The password is not displayed as you type.

Do not put the password directly into the command or include it in screenshots or your report.

Expected: the command finishes without an error and creates `secret.enc`.

Check both files:

```bash
ls -l secret.txt secret.enc
file secret.txt secret.enc
```

The encrypted file will not display the original readable sentence when viewed as plain text.

### Step 2 — Inspect the encrypted file

You can inspect the beginning of the file in hexadecimal:

```bash
xxd -l 32 secret.enc
```

If `xxd` is not installed, skip this optional inspection; it is not needed to complete the practical.

Do not use `cat secret.enc` as proof of secure encryption. It may print unreadable binary data and is not a meaningful security test.

### Step 3 — Decrypt the file

Run:

```bash
openssl enc -d -aes-256-cbc -pbkdf2 -in secret.enc -out decrypted.txt
```

Enter the same password used during encryption.

Display the decrypted result:

```bash
cat decrypted.txt
```

Expected output:

```text
Secret cloud data for CIA practical.
```

This demonstrates that the encrypted file can be recovered by a person who has the required password.

### Step 4 — Confidentiality result

- `secret.txt` is the original readable file.
- `secret.enc` is the encrypted output.
- `decrypted.txt` contains the recovered text after correct decryption.

Encryption helps protect confidentiality when the password is kept secure. This small lab example is not a complete file-management or key-management solution for production systems.

## 8. Part B — Demonstrate Integrity with SHA-256

### Step 1 — Create a checksum

First ensure that `secret.txt` contains the original sample sentence. Run:

```bash
printf '%s\n' 'Secret cloud data for CIA practical.' > secret.txt
sha256sum secret.txt > secret.sha256
```

View the stored checksum:

```bash
cat secret.sha256
```

Expected: a long hexadecimal SHA-256 digest followed by the filename.

### Step 2 — Verify the unchanged file

Run:

```bash
sha256sum -c secret.sha256
```

Expected output:

```text
secret.txt: OK
```

This means the current file matches the checksum recorded in `secret.sha256`.

### Step 3 — Modify the file

Append a new line:

```bash
printf '%s\n' 'This line changes the file.' >> secret.txt
```

Check it again:

```bash
sha256sum -c secret.sha256
```

Expected: the check reports `FAILED` for `secret.txt` and the command returns a non-zero status. The exact wording may vary slightly by system.

This demonstrates that a content change produces a checksum mismatch.

### Step 4 — Restore the original sample for a clean final state

To return the sample to its original contents, run:

```bash
printf '%s\n' 'Secret cloud data for CIA practical.' > secret.txt
sha256sum -c secret.sha256
```

Expected:

```text
secret.txt: OK
```

**Do not edit `secret.sha256` to match the modified file before the failed-check demonstration.** The point is to compare the modified file against the original checksum.

### Step 5 — Integrity result

The unchanged file passes the checksum test. After the file is modified, it fails the test. Restoring the original contents makes it pass again.

A checksum is only useful for this purpose if the expected checksum itself is trusted. An attacker who can alter both the file and the checksum may be able to hide a change; a keyed MAC or digital signature is needed for stronger authenticity guarantees.

## 9. Part C — Demonstrate Availability with Apache

### Step 1 — Start Apache

Run:

```bash
sudo systemctl enable --now apache2
```

This starts Apache now and enables it to start automatically when the system boots.

### Step 2 — Check the service status

Run:

```bash
sudo systemctl status apache2 --no-pager
```

Expected: the service status indicates `active (running)`.

If the service is not running, read the error shown by `systemctl status` and inspect the logs before proceeding.

### Step 3 — Test the local web server

Run:

```bash
curl -I http://localhost
```

Expected: HTTP response headers are printed. A successful default Apache response commonly begins with a status line such as `HTTP/1.1 200 OK`; exact headers may differ.

You can also fetch the page content:

```bash
curl http://localhost
```

Expected: HTML for the default Apache page is printed.

If `curl` is not installed and your lab permits it, install it:

```bash
sudo apt install curl -y
```

### Step 4 — Optional browser test

If you have a graphical Ubuntu desktop, open a browser and visit:

```text
http://localhost
```

The default Apache page should appear if the service is working.

### Step 5 — Availability result

Apache is running and responds to a request from the local machine. This demonstrates basic service availability. It does not test access from another computer or prove high availability.

### Optional cleanup

If you do not need Apache running after the practical, you may stop it:

```bash
sudo systemctl stop apache2
```

If you want it to continue running for later lab work, leave it enabled. Follow your instructor's requirements.

## 10. Optional Part D — Demonstrate basic file access control

Only perform this if your question or teacher expects an access-control demonstration in addition to the three CIA principles.

Create a file and restrict its permissions:

```bash
cd ~/cia-practical
printf '%s\n' 'Private information for access-control test.' > private.txt
chmod 600 private.txt
ls -l private.txt
```

Expected permissions begin with:

```text
-rw-------
```

This means the file owner has read/write permissions, while group and other users have no permissions.

To test denial, try reading it as a different unprivileged account:

```bash
sudo -u nobody cat "$PWD/private.txt"
```

Expected: `Permission denied` on a standard Linux setup where `nobody` is a different user and the home-directory path is traversable. If the test behaves differently due to the machine's account or directory configuration, report the actual result rather than claiming it failed.

The access-control example illustrates restricting file access; it is not a replacement for encryption.

## 11. Summary of commands

| Principle | Command or operation | Evidence |
|---|---|---|
| Confidentiality | OpenSSL encrypt/decrypt | Encrypted file exists; decrypted content matches original |
| Integrity | `sha256sum` and `sha256sum -c` | Original passes, modified file fails, restored file passes |
| Availability | Start Apache and run `curl` | Service is active and local HTTP request responds |
| Optional access control | `chmod 600` | File permissions restrict access |

## 12. Expected results

- **Confidentiality:** `secret.txt` is encrypted to `secret.enc`; correct decryption restores the original text.
- **Integrity:** the original file passes the SHA-256 check; after modification, the check fails; after restoration, it passes.
- **Availability:** Apache is active and `curl http://localhost` returns a response.
- **Optional access control:** `private.txt` has owner-only read/write permissions and another unprivileged user cannot read it under the expected setup.

## 13. Screenshots/evidence to capture

Capture screenshots from your own Ubuntu/Linux environment:

1. Terminal showing `openssl version` and the working directory.
2. Encryption command and the generated `secret.enc` file. Do not show or record the password.
3. Decryption command and `cat decrypted.txt` showing the recovered sentence.
4. `sha256sum -c secret.sha256` showing `secret.txt: OK`.
5. The integrity check after modification showing a failure.
6. The integrity check after restoring the file showing `OK`.
7. `systemctl status apache2` showing `active (running)`.
8. `curl -I http://localhost` showing a successful response.
9. Optional: `ls -l private.txt` showing the restricted permissions.

Do not include passwords or other secrets in screenshots.

## 14. Record-book write-up

### Aim
To demonstrate Confidentiality, Integrity, and Availability (CIA) on Ubuntu/Linux.

### Requirements
Ubuntu/Linux terminal, `openssl`, `apache2`, `sha256sum`, and `curl` for convenient HTTP testing.

### Procedure
1. Update package lists and install OpenSSL and Apache.
2. Create a sample text file in a dedicated working directory.
3. Encrypt the file using OpenSSL and decrypt it with the correct password.
4. Create a SHA-256 checksum for the original file.
5. Verify that the unchanged file passes the checksum check.
6. Modify the file and verify that the checksum check fails.
7. Restore the original file and verify the checksum again.
8. Start Apache, check its status, and test it using `curl`.
9. If required, demonstrate file access restrictions using `chmod 600`.

### Output
The original text was recovered after decryption, a modified file failed the checksum check, and Apache responded to a local HTTP request.

### Result
The three CIA security principles were demonstrated using encryption, checksum verification, and a local web service on Ubuntu/Linux.

## 15. Troubleshooting

### `openssl: command not found`
Install it using:

```bash
sudo apt update
sudo apt install openssl -y
```

### Decryption reports a bad password or invalid data
- Use the same password entered during encryption.
- Ensure that you are decrypting the correct `secret.enc` file.
- Do not overwrite `secret.enc` with the decrypted output.
- Repeat encryption if the sample encrypted file is damaged.

### `xxd: command not found`
The optional hex inspection is not necessary. Skip it or install the package providing `xxd` if permitted.

### `sha256sum -c` reports failure before you intended to modify the file
Check whether `secret.txt` was changed, moved, or recreated with different contents. Restore the exact original line and recreate the checksum only when beginning a fresh integrity demonstration.

### Apache is not active
Check:

```bash
sudo systemctl status apache2 --no-pager
sudo journalctl -u apache2 --no-pager -n 50
```

Then try starting it:

```bash
sudo systemctl start apache2
```

If startup fails, use the actual error in the service output to guide troubleshooting.

### `curl` cannot connect to localhost
Check that Apache is active and listening. A local request to `localhost` does not require opening a cloud firewall port. Do not open public inbound ports for this local demonstration.

### `Permission denied` in the optional access-control test
Verify the permissions with `ls -l private.txt`, confirm that `nobody` is a different user, and check whether the directory path is accessible. Report what your system actually shows.

## 16. Safety and cleanup

- Use only a machine you are authorized to administer.
- Do not use real confidential data; use the sample text.
- Keep the encryption password private.
- Do not upload sample secret files to public locations.
- If Apache is not needed afterward, stop it with `sudo systemctl stop apache2`.
- The working directory can be retained for later practicals or removed only after you have saved the required evidence and no longer need the files.

## 17. Viva questions and answers

**Q1. What does CIA stand for?**  
Confidentiality, Integrity, and Availability.

**Q2. What is confidentiality?**  
Protecting information so only authorized parties can access or read it.

**Q3. How is confidentiality demonstrated here?**  
By encrypting a sample text file and decrypting it with the correct password.

**Q4. What is encryption?**  
Transforming readable data into a protected form that requires the correct key or password to recover.

**Q5. What is OpenSSL?**  
A toolkit that provides cryptographic functions, including file encryption and decryption.

**Q6. What does AES-256-CBC mean in the command?**  
AES is a symmetric encryption algorithm; 256 refers to the key size in bits, and CBC is a block-cipher mode.

**Q7. What is integrity?**  
Maintaining data accuracy and detecting unauthorized or unexpected changes.

**Q8. What is a checksum?**  
A value computed from data that can be used to check whether the data has changed.

**Q9. What does SHA-256 do?**  
It produces a 256-bit cryptographic hash digest from the file contents.

**Q10. What does `sha256sum -c` do?**  
It compares the current file's hash with the saved expected hash and reports whether they match.

**Q11. Why does the checksum fail after modifying the file?**  
The modified content produces a different hash from the stored checksum.

**Q12. Does a checksum alone prove who created a file?**  
No. It detects a mismatch against a trusted value but does not by itself authenticate the creator.

**Q13. What is availability?**  
Ensuring authorized users can access systems or information when needed.

**Q14. How is availability demonstrated here?**  
By starting Apache and verifying that a local HTTP request receives a response.

**Q15. What is Apache?**  
A widely used web-server software package.

**Q16. What does `systemctl status apache2` show?**  
The service's current status and related information.

**Q17. What does `curl http://localhost` do?**  
It sends an HTTP request to the web server on the same machine and displays the response.

**Q18. What does `chmod 600` do?**  
It gives the file owner read/write permissions and removes permissions for group and other users.

**Q19. Is a running local Apache server proof of full high availability?**  
No. It only demonstrates that the service responds locally; high availability requires additional design and testing.

**Q20. Give one example of each CIA principle.**  
Confidentiality: encrypting a file. Integrity: checking a SHA-256 hash. Availability: keeping a web service running and responsive.

## 18. Final checklist

- [ ] Installed or verified OpenSSL and Apache.
- [ ] Created `~/cia-practical/secret.txt`.
- [ ] Encrypted the file to `secret.enc`.
- [ ] Decrypted it and confirmed the original content.
- [ ] Created a SHA-256 checksum.
- [ ] Verified the original file successfully.
- [ ] Modified the file and observed the failed check.
- [ ] Restored the original file and verified it again.
- [ ] Started Apache and confirmed `active (running)`.
- [ ] Tested the local server using `curl`.
- [ ] Completed the optional access-control test only if required.
- [ ] Captured evidence without revealing passwords.
- [ ] Reviewed the viva questions.
