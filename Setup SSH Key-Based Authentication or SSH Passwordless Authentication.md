## Step-by-Step Guide: SSH Passwordless Authentication  - Using SSH Key

**Prerequisite: Location**  
Perform **Step 1** and **Step 2** on your **local computer** (the one you are connecting **from**), not inside the server.

---

### Step 1: Generate SSH Keys (Local Machine)  
First, check if you already have keys or generate a new pair.  
Open your terminal (Mac/Linux) or PowerShell (Windows) and run:

```bash
ssh-keygen -t rsa -b 4096
```

- When prompted to *"Enter file in which to save the key,"* just press **Enter** (this saves it to the default location). Optionally you can enter the Name too. 
- When prompted for a passphrase, press **Enter** twice (leave it empty) to ensure true passwordless login.

---

### Step 2: Copy the Key to Your Server  
The easiest way to send your public key to the server is using the `ssh-copy-id` command.

Run the following command (you will be asked for your password one last time):

```bash
ssh-copy-id -p 21098 khabqcgb@198.187.29.237
```
Accept Fingerprint by typing yes
then you will see option to Enter SSH password for user khabqcgb

> **Note:** If you prefer using the domain and DNS is already propagated, you can replace the IP with `khabribaba.com`.

Here's the Markdown version of Step 3 and Troubleshooting:

---

### Step 3: Connect from Your Computer
Now that cPanel knows your key, go back to your computer's terminal. You need to specify the Namecheap port (likely **21098**).

Run this command:

```bash
ssh -p 21098 khabqcgb@198.187.29.236
```

> **Note:** If your port in Step 1 was different, change `21098` to that number.

---
This Process is known as
SSH Key Authentication - The most accurate technical term
Passwordless SSH Login - Describes the user experience
Public Key Authentication - Refers to the cryptographic method
SSH Key Pair Authentication

**"Permission denied"**: If it still asks for a password, ensure you clicked **Authorize** in Step 2.

**"Connection refused"**: This means you are using the wrong port. Double-check the port in the **"Manage Shell"** section.
