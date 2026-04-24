# Host Ollama on a Server (Beginner-Friendly Setup Guide)

This guide helps you run Ollama on one computer (“Server Computer”) and access it from another computer (“Client Computer”) on your home network.

This is useful if:

* one computer is more powerful and will run the AI
* another computer (like a laptop) will use the AI through a browser app

At the end, you’ll be able to connect using:

```text
http://ai-host.local:11434
```

instead of remembering an IP address like:

```text
http://192.168.1.193:11434
```

---

# Part 1 — Server Computer (The Computer Running the AI)

This is the computer that will host Ollama and run the AI model.

---

## Step 1 — Install Ollama

### Linux

Open Terminal and run:

```bash
curl -fsSL https://ollama.com/install.sh | sh
```

### macOS

Use the same command above, or download it here:

[https://ollama.com/download/mac](https://ollama.com/download/mac)

### Windows

Open PowerShell and run:

```powershell
irm https://ollama.com/install.ps1 | iex
```

Or download it here:

[https://ollama.com/download/windows](https://ollama.com/download/windows)

---

## Step 2 — Allow Other Computers to Access Ollama

By default, Ollama only listens on your own computer.

We need to make it accessible from other devices on your network.

### 2.1 Edit the Ollama Service Settings

Run:

```bash
sudo systemctl edit ollama
```

This opens a text editor called **Vim**.

### 2.2 Enter Insert Mode

Press:

```text
i
```

This lets you type.

### 2.3 Paste This Configuration

Paste this exactly:

```ini
[Service]
Environment="OLLAMA_HOST=0.0.0.0:11434"
Environment="OLLAMA_ORIGINS=*"
```

Paste it under the section that says:

```text
### Editing /etc/systemd/system/ollama.service.d/override.conf
### Anything between here and the comment below will become the contents of the drop-in file
```

### 2.4 Save and Exit

1. Press `Esc`
2. Type:

```text
:wq
```

3. Press Enter

This means **write + quit**.

### 2.5 Reload the Service

Run:

```bash
sudo systemctl daemon-reload
sudo systemctl restart ollama
```

### 2.6 Verify It Worked

Run:

```bash
ss -tulpn | grep 11434
```

You want to see something like:

```text
*:11434
```

or

```text
0.0.0.0:11434
```

That means Ollama is listening on your network.

---

## Step 3 — Download and Run a Model

Example using **Mistral**:

```bash
ollama run mistral
```

This downloads the model the first time and then starts it.

You can replace `mistral` with another model.

Browse available models here:

[https://ollama.com/search](https://ollama.com/search)

---

## Step 4 — Open Firewall Port 11434

Ollama uses port **11434** by default.

You must allow this port through your firewall.

### Ubuntu / Debian

```bash
sudo ufw allow 11434/tcp
```

### Fedora / RHEL / Rocky / AlmaLinux

```bash
sudo firewall-cmd --add-port=11434/tcp --permanent
sudo firewall-cmd --reload
```

### Windows

Open PowerShell as Administrator and run:

```powershell
New-NetFirewallRule -DisplayName "Allow Ollama Port 11434" -Direction Inbound -LocalPort 11434 -Protocol TCP -Action Allow
```

---

## Step 5 — Find Your Server IP Address

Run:

```bash
ip -4 addr
```

Look for something like:

```text
192.168.1.193
```

Usually:

* `wlan0`, `wlp...` = Wi-Fi
* `eth0`, `enp...` = Ethernet

Most home networks use:

```text
192.168.x.x
```

Write this IP down — you’ll need it on the client computer.

---

# Part 2 — Client Computer (The Computer Using the AI)

This is the computer where you’ll open the assignment helper app.

## Step 1 — Create a Friendly Hostname

Instead of typing the IP every time, we’ll create:

```text
ai-host.local
```

## Step 2 — Open Notepad as Administrator (Windows)

1. Search for **Notepad**
2. Right click it
3. Choose **Run as administrator**

## Step 3 — Open the Hosts File

Go to:

```text
File → Open
```

Then open:

```text
C:\Windows\System32\drivers\etc\hosts
```

You may need to change the file type dropdown to **All Files**.

## Step 4 — Add This Line

At the bottom of the file, add:

```text
192.168.1.193    ai-host.local
```

Replace `192.168.1.193` with your actual server IP.

## Step 5 — Save the File

Now your computer can use:

```text
http://ai-host.local:11434
```

instead of the raw IP.

---

# Part 3 — Run the Example Assignment Helper App

## Step 1 — Install Visual Studio Code

[https://code.visualstudio.com/download](https://code.visualstudio.com/download)

## Step 2 — Install Git

[https://git-scm.com/install/windows](https://git-scm.com/install/windows)

## Step 3 — Open Terminal

Start Menu → search for:

```text
Terminal
```

and press Enter.

## Step 4 — Clone the Project

Run:

```bash
mkdir -p ~/Documents/Github
cd ~/Documents/Github
git clone https://github.com/Fooot-Code/ai-host.git
code ai-host
```

## Step 5 — Install Live Server Extension

[https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer)

## Step 6 — Launch the Website

Press:

```text
Ctrl + Shift + P
```

Type:

```text
Live
```

Then choose:

```text
Live Server: Open With Live Server
```

Your browser should open automatically.

---

# Part 4 — Using the Assignment Helper

## Host

Use:

```text
http://ai-host.local:11434
```

or:

```text
http://YOUR-IP:11434
```

Format must be:

```text
http://{host}:{port}
```

## Model

Use:

```text
mistral:latest
```

if following this guide.

## Assignment Topic

Example:

```text
The Effects of Social Media on Student Learning
```

## Rubric / Instructions

Paste your assignment instructions here.

Include:

* grading rubric
* required formatting
* word count
* sources required
* teacher instructions

The more detail you give, the better the output.

## Final Step

Click:

```text
Generate Assignment
```

Then wait while Ollama writes the response.

Large assignments may take a few minutes.

This is normal.

---

# Troubleshooting

## “It says Generating forever”

Usually this means:

* wrong model name
* firewall issue
* Ollama not running
* browser blocked by CORS

Test with:

```bash
curl http://YOUR-IP:11434/api/tags
```

If that works, your setup is probably correct.

## “Host not allowed”

Use:

* `.local`
* `192.168.x.x`
* `10.x.x.x`
* `172.x.x.x`

Public internet addresses are intentionally blocked for safety.

## “Can’t connect”

Double check:

* Ollama is running
* firewall port 11434 is open
* correct IP address
* both computers are on the same network
