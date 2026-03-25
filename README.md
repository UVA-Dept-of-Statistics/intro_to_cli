# Getting Started with UVA's Rivanna HPC Cluster

This guide walks you through connecting to UVA's Rivanna high-performance computing cluster and learning the essential Linux commands you'll use every day. No prior Linux experience required.

---

## Table of Contents

1. [What is Rivanna?](#what-is-rivanna)
2. [How SSH Works](#how-ssh-works)
3. [Connecting to Rivanna](#connecting-to-rivanna)
   - [Mac and Linux](#mac-and-linux)
   - [Windows](#windows)
4. [Essential Linux Commands](#essential-linux-commands)
5. [Running Jobs with SLURM](#running-jobs-with-slurm)
6. [Tips and Next Steps](#tips-and-next-steps)

---

## What is Rivanna?

Rivanna is UVA's primary high-performance computing (HPC) cluster. It runs Linux (specifically a distribution called RedHat/Rocky Linux) and gives you access to thousands of CPU cores, large memory nodes, and GPUs for research computing. Even if your personal machine runs Windows or macOS, you interact with Rivanna through a terminal using a protocol called SSH.

---

## How SSH Works

**SSH (Secure Shell)** is a network protocol that lets you securely log into a remote computer and run commands on it as if you were sitting in front of it.

Here's what's happening under the hood:

```
Your computer  ──── encrypted tunnel ────▶  Rivanna login node
(any OS)             (SSH protocol)          (Linux)
```

- **Everything you type is encrypted** before it leaves your machine.
- You authenticate with a **username + password** (or optionally, an SSH key pair).
- Once connected, your terminal is now running a **shell on Rivanna**, not on your local machine. Commands you type execute there.
- The connection goes to a **login node** — a shared entry point. Do not run heavy computations here; use the job scheduler (SLURM) for that.

### Key SSH concepts

| Term | Meaning |
|------|---------|
| **Host** | The remote machine you're connecting to (`rivanna.hpc.virginia.edu`) |
| **Port** | SSH uses port 22 by default |
| **Shell** | The command interpreter on the remote side (Rivanna uses Bash) |
| **Session** | Your connection from login to logout |
| **stdin / stdout / stderr** | Standard input, output, and error — how programs send and receive text |

---

## Connecting to Rivanna

Before connecting, you must be on UVA's network or connected via the **UVA VPN**. Off-campus users need to install and activate the Cisco AnyConnect VPN first.

Full login documentation: https://www.rc.virginia.edu/userinfo/hpc/login/

### Mac and Linux

Both macOS and Linux come with SSH built into the terminal. No extra software needed.

1. Open your terminal:
   - **Mac**: Press `Cmd + Space`, type `Terminal`, press Enter. Or use iTerm2 if installed.
   - **Linux**: Press `Ctrl + Alt + T` or search for "Terminal" in your app launcher.

2. Run the SSH command:

   ```bash
   ssh your_computing_id@rivanna.hpc.virginia.edu
   ```

   Replace `your_computing_id` with your UVA computing ID (the part before `@virginia.edu` in your email).

3. When prompted, enter your UVA password. You won't see characters as you type — this is normal.

4. Complete Duo two-factor authentication if prompted.

5. You're in! You should see a welcome banner and a prompt like:

   ```
   [abc2de@login01 ~]$
   ```

**To disconnect**, type `exit` or press `Ctrl + D`.

---

### Windows

Windows 10 and 11 include a built-in SSH client in PowerShell and Command Prompt. You can also use a dedicated SSH client.

#### Option 1: PowerShell or Command Prompt (built-in, Windows 10/11)

1. Press `Win + R`, type `powershell`, press Enter.
2. Run the same command as Mac/Linux:

   ```powershell
   ssh your_computing_id@rivanna.hpc.virginia.edu
   ```

#### Option 2: MobaXterm (recommended for Windows — free)

MobaXterm gives you a full Linux-like terminal experience on Windows, including built-in file transfer.

1. Download MobaXterm Home Edition from https://mobaxterm.mobatek.net/
2. Open MobaXterm and click **Session** → **SSH**.
3. Enter:
   - Remote host: `rivanna.hpc.virginia.edu`
   - Username: your UVA computing ID
4. Click OK and enter your password when prompted.

#### Option 3: PuTTY (classic SSH client)

1. Download PuTTY from https://www.putty.org/
2. In the "Host Name" field enter: `rivanna.hpc.virginia.edu`
3. Port: `22`, Connection type: `SSH`
4. Click **Open**, then enter your username and password.

---

## Essential Linux Commands

Once you're logged in, you're working in a **Bash shell**. Here are the commands you'll use constantly. The general syntax for most commands is:

```
command [options] [arguments]
```

- **Options** (also called flags) modify behavior and usually start with `-` or `--`
- **Arguments** are the files, directories, or text the command acts on
- Items in `[brackets]` are optional; items in `<angle brackets>` are required

---

### Navigating the Filesystem

#### `pwd` — Print Working Directory
Shows where you currently are.

```bash
pwd
# Output: /home/abc2de
```

#### `ls` — List directory contents

```bash
ls                  # list files in current directory
ls -l               # long format: permissions, size, date
ls -a               # show hidden files (those starting with .)
ls -lh              # long format with human-readable sizes (KB, MB)
ls -lt              # sort by modification time, newest first
ls /path/to/dir     # list a specific directory
```

Example output of `ls -lh`:
```
drwxr-xr-x 2 abc2de users 4.0K Mar 10 09:15 data/
-rw-r--r-- 1 abc2de users  12K Mar 10 09:14 results.txt
```

#### `cd` — Change Directory

```bash
cd /scratch/abc2de      # go to an absolute path (starts from root /)
cd data                 # go to a relative path (relative to where you are now)
cd ..                   # go up one directory
cd ~                    # go to your home directory
cd -                    # go back to the previous directory
```

> **Absolute vs. relative paths**: An absolute path starts with `/` and works from anywhere. A relative path starts from your current location.

---

### Working with Files and Directories

#### `mkdir` — Make Directory

```bash
mkdir my_project                     # create a directory
mkdir -p my_project/data/raw         # create nested directories all at once
```

#### `touch` — Create an empty file (or update timestamps)

```bash
touch notes.txt
```

#### `cp` — Copy files or directories

```bash
cp file.txt copy_of_file.txt         # copy a file
cp file.txt /scratch/abc2de/         # copy to another directory
cp -r my_folder/ backup_folder/      # copy a directory (-r means recursive)
```

#### `mv` — Move or rename files

```bash
mv old_name.txt new_name.txt         # rename a file
mv file.txt /scratch/abc2de/         # move a file to another directory
```

#### `rm` — Remove files or directories

```bash
rm file.txt                          # delete a file
rm -r my_folder/                     # delete a directory and everything in it
rm -i file.txt                       # ask for confirmation before deleting
```

> **Warning**: There is no Recycle Bin on Linux. `rm` is permanent. Use `-i` when unsure.

---

### Viewing File Contents

#### `cat` — Print entire file contents

```bash
cat file.txt                         # print the whole file
cat file1.txt file2.txt              # concatenate and print two files
```

#### `less` — View large files one page at a time

```bash
less large_file.txt
```

Inside `less`:
- `Space` or `f` — next page
- `b` — previous page
- `/pattern` — search for text
- `q` — quit

#### `head` and `tail` — View the beginning or end of a file

```bash
head file.txt                        # first 10 lines (default)
head -n 20 file.txt                  # first 20 lines
tail file.txt                        # last 10 lines
tail -n 50 file.txt                  # last 50 lines
tail -f logfile.txt                  # follow a file as it grows (great for logs)
```

---

### Searching and Filtering

#### `grep` — Search for text patterns

```bash
grep "error" logfile.txt             # find lines containing "error"
grep -i "error" logfile.txt          # case-insensitive search
grep -r "TODO" my_project/           # search recursively through a directory
grep -n "error" logfile.txt          # show line numbers
grep -v "debug" logfile.txt          # show lines that do NOT match
```

#### `find` — Search for files by name or properties

```bash
find . -name "*.txt"                 # find all .txt files from current directory down
find /scratch -name "results*"       # find files starting with "results"
find . -type d                       # find only directories
find . -mtime -1                     # find files modified in the last 1 day
```

---

### Redirects and Pipes

These are some of the most powerful features of the Linux shell.

#### Redirect output to a file with `>`

```bash
ls -l > file_list.txt                # save ls output to a file (overwrites)
ls -l >> file_list.txt               # append output to a file
```

#### Pipe output from one command into another with `|`

```bash
ls -l | grep ".txt"                  # list files, then filter for .txt
cat data.csv | grep "2024" | head    # filter a CSV, show first 10 matches
```

#### Combine them

```bash
grep -r "error" logs/ | wc -l        # count how many lines contain "error"
```

---

### Permissions

#### `chmod` — Change file permissions

Linux permissions look like `-rwxr-xr--` and control who can read, write, and execute a file.

```bash
chmod +x script.sh                   # make a script executable
chmod 755 script.sh                  # rwx for owner, rx for group and others
chmod 600 private_key                # rw for owner only (good for SSH keys)
```

Permission number breakdown:
| Number | Permissions |
|--------|-------------|
| 7 | read + write + execute |
| 6 | read + write |
| 5 | read + execute |
| 4 | read only |
| 0 | no permissions |

---

### Processes and Jobs

#### `ps` and `top` — See running processes

```bash
ps aux                               # list all running processes
top                                  # live process monitor (press q to quit)
htop                                 # nicer version of top (if available)
```

#### `kill` — Stop a process

```bash
kill 12345                           # send termination signal to process ID 12345
kill -9 12345                        # force kill (use as a last resort)
```

#### Running jobs in the background

```bash
long_running_command &               # run in background
jobs                                 # list background jobs
fg                                   # bring background job to foreground
Ctrl + C                             # interrupt/kill the current foreground process
Ctrl + Z                             # pause (suspend) the current process
```

---

### Other Frequently Used Commands

#### `echo` — Print text

```bash
echo "Hello, Rivanna"
echo $HOME                           # print the value of an environment variable
```

#### `wc` — Word/line/character count

```bash
wc -l file.txt                       # count lines
wc -w file.txt                       # count words
```

#### `sort` — Sort lines

```bash
sort file.txt                        # sort alphabetically
sort -n numbers.txt                  # sort numerically
sort -r file.txt                     # reverse sort
```

#### `history` — Show command history

```bash
history                              # show recent commands
history | grep ssh                   # search your history
!!                                   # repeat the last command
!grep                                # repeat the last command starting with "grep"
```

#### `man` — Manual pages (built-in documentation)

```bash
man ls                               # read the manual for ls
man grep                             # read the manual for grep
```

Press `q` to exit a man page. When in doubt, `man <command>` is your best friend.

---

## Tips and Next Steps

### Keyboard shortcuts in the terminal

| Shortcut | Action |
|----------|--------|
| `Tab` | Autocomplete file/command names |
| `Tab Tab` | Show all possible completions |
| `Ctrl + C` | Cancel the current command |
| `Ctrl + D` | Exit the shell (logout) |
| `Ctrl + L` | Clear the screen |
| `Ctrl + A` | Jump to beginning of line |
| `Ctrl + E` | Jump to end of line |
| `Ctrl + R` | Search command history interactively |
| `Up / Down arrows` | Scroll through previous commands |

### Where to store your files on Rivanna

| Location | Purpose | Notes |
|----------|---------|-------|
| `/home/your_id/` | Personal files, scripts, configs | Limited space (~50 GB) |
| `/scratch/your_id/` | Large temporary data | Not backed up; purged periodically |
| `/project/your_group/` | Shared project storage | Ask your PI for access |

---

## Running Jobs with SLURM

On Rivanna, you **never run computations directly in your terminal**. The login nodes are shared and not meant for heavy work. Instead, all jobs — whether a 5-minute Python script or a multi-day simulation — are submitted to **SLURM**, a job scheduler that manages the cluster's compute resources.

### What SLURM does

SLURM (Simple Linux Utility for Resource Management) sits between you and the compute nodes. You describe what you need (CPUs, memory, time, GPUs) and what you want to run, and SLURM queues your job and dispatches it when resources are available. You don't log into a compute node directly — SLURM handles that for you.

Your typical workflow looks like this:

```
Write your code  →  Write a SLURM script  →  Submit it  →  Check status  →  View output
```

### Key SLURM commands

```bash
sbatch job_script.sh        # submit a job
squeue -u your_computing_id # check the status of your jobs
scancel <job_id>            # cancel a job
sacct -j <job_id>           # view resource usage after a job completes
```

### Example SLURM script

Save this as `job.sh`, then submit it with `sbatch job.sh`:

```bash
#!/bin/bash
#SBATCH --job-name=my_job         # name shown in the queue
#SBATCH --account=your_account    # your allocation account
#SBATCH --partition=standard      # which queue to use
#SBATCH --nodes=1                 # number of nodes
#SBATCH --ntasks=1                # number of tasks (processes)
#SBATCH --cpus-per-task=4         # CPU cores per task
#SBATCH --mem=8GB                 # total memory
#SBATCH --time=01:00:00           # max wall time (HH:MM:SS)
#SBATCH --output=my_job-%j.out    # stdout log (%j = job ID)
#SBATCH --error=my_job-%j.err     # stderr log

# Load any needed software modules
module load gcc python

# Run your code
python my_script.py
```

Lines starting with `#SBATCH` are directives that tell SLURM what resources to request — they look like comments but SLURM reads them before the job runs.

### Getting help writing SLURM scripts

UVA Research Computing has detailed documentation covering partitions, memory options, GPU jobs, array jobs, and more:

https://www.rc.virginia.edu/userinfo/hpc/slurm/

---

### Getting more help

- UVA Research Computing documentation: https://www.rc.virginia.edu/userinfo/hpc/
- On Rivanna, run `module avail` to see available software
- UVA RC office hours and support: https://www.rc.virginia.edu/support/
