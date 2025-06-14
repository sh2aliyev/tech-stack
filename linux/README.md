<p align="center">
  <img src="./logo.png" height="150">
</p>

<h1 align="center">Linux (Debian)</h1>

<p align="center">
  Linux is an open-source kernel that powers many operating systems, called distributions (or distros). This documentation mainly focuses on Debian, one of the Linux distros that is free and widely used.
</p>

<p align="right">
  <a href="../#tech-stack">Main Page ↖</a>
</p>

### Contents

1. [Basics](#-basics)
   - [Commands](#-commands)
     - [Informational Commands](#-informational-commands)
     - [File & Directory Commands](#-file--directory-commands)
     - [Process & Monitoring Commands](#-process--monitoring-commands)
   - [Command Chaining](#-command-chaining)
2. [Permissions](#-permissions)
   - [Overview](#-overview)
     - [Permission Types](#-permission-types)
     - [Ownership](#-ownership)
   - [Structure](#-structure)
     - [Octal Representation](#-octal-representation)
   - [Permission Commands](#-permission-commands)
   - [User & Group Commands](#-user--group-commands)

<br>

> [!NOTE]
> This is not a complete guide. It's just a collection of specific concepts and commands.

---

## 🔶 Basics

Basic Linux commands and concepts.

<br>

### 🔷 Commands

Definitions:

- ` `, `.`, `./`: Current directory.
- `<variable>`: A variable.
- `<variable>...`: Multiple variables.

<br>

#### 🔻 Informational Commands

- `which <command>...`: Show the path of a command.
- `whatis <command>...`: Briefly describe a command in one line.
- `man <command>`: Display instructions (help) for a command.
- `wc -<option>... <file>...`: Count lines, words, and characters.
- `find <directory>... -name <pattern> -<option>`: Search for files and directories.
  ```sh
  find ./dir1 -name file.txt # A specific file in dir1 and its subdirectories
  find ./dir1 -maxdepth 1 -name file.txt # Look for only in the given directory
  find ./dir1 -name "*.txt" # All .txt files
  find ./dir1 -name file.txt -delete # Find and delete
  find ./dir1 -name "my*" # Anything starts with "my" (files/directories)
  ```
- `grep -<option>... <pattern> <file>...`: Search for patterns in a file and return matching lines.
  - `-i`: Case-insensitive search.
  - `-r`: Recursive search in directories.
  ```sh
  grep "hello" file.txt # Search for "hello"
  ```
- `less`: Open output in a controllable pager.
  > - `q`: Quit.
  > - `space`: Scroll down (next page).
  > - `b`: Scroll up (previous page).
  > - `g`: Go to the beginning.
  > - `G`: Go to the end.
  > - `/<pattern>`: Search forward (down).
  > - `?<pattern>`: Search backward (up).
  > - `n`: Repeat search in the same direction (next result).
  > - `N`: Repeat search in the opposite direction (previous result).
  > - `h`: Help.
  ```sh
  less file.txt # View a file in a pager
  find ./dir1 -name "*.txt" | less # View the output of find in a pager
  ```
- `cat -<option>... <file>...`: Print file contents to the standard output (terminal).
  - `-n`: Number all lines.
  - `-b`: Number non-empty lines.
  - `-s`: Squeeze blank lines into one.
- `getent <database> <key>`: Get entries from system databases.
  ```sh
  getent group # All groups and their members
  getent group docker # Select a specific group (docker in this case)
  getent services # All services and their ports
  getent hosts # All hosts and their IP addresses
  ```
- `file <file>...`: Determine the type of a file.
  ```sh
  file ./my_golang_executable
  ```
  > Output:
  >
  > `./my_golang_executable: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), statically linked, BuildID[sha1]=285f6bad..., with debug_info, not stripped`
  >
  > To remove symbols and debug information from an executable, use:
  >
  > ```sh
  > strip ./my_golang_executable
  > ```
  >
  > This reduces the file size and removes debugging symbols, making it more suitable for production use.

<br>

#### 🔻 File & Directory Commands

- `OUTPUT > <file>...`: Redirect output to a file.
  > - `>`: Overwrite.
  > - `>>`: Append.
  ```sh
  > file.txt # Create/empty a file and start writing in it
  pwd > file.txt # Save the command (pwd) output to file
  cat file1.txt > file2.txt # Copy contents of file1.txt to file2.txt
  cat file1.txt file2.txt >> file3.txt # Append contents of file1.txt and file2.txt to file3.txt
  ```
- `mkdir -<option>... <directory>...`: Create a directory.
  - `-p`: Create parent directories as needed.
  ```sh
  mkdir -p ./sub1/sub2 # Create a nested directory structure
  mkdir -p ./new_dir/{dir1,dir2,dir3} # Create a directory with multiple subdirectories in it
  ```
- `rm -<option>... <file/directory>...`: Delete a file/directory.
  - `-i`: Prompt before each removal.
  - `-r`: Recursively remove a directory and its contents.
  - `-f`: Force delete without prompt.
  - `-v`: Verbose mode (show what is being done).
- `cp -<option>... <source>... <destination>`: Copy files and directories (`mv` to move).
  > Overwrites if already exists.
  - `-i`: Prompt before overwrite.
  - `-r`: Recursively copy directories and their contents.
  ```sh
  cp file.txt ./dir1 # Copy a file to dir
  cp file.txt ./dir1/file2.txt # Copy and rename in destination
  ```
- `touch <file>...`: Create an empty file.
- `tar -<option>... <archive> <file/directory>...`: Create or extract tar archives.
  - `-c`: Create a new archive.
  - `-x`: Extract an archive.
  - `-f`: Specify the archive file (must come right before the file name).
  - `-v`: Verbose mode (show files being processed).
  - `-z`: Compress with gzip (a `.tar.gz` file).
  ```sh
  tar -cvf archive.tar ./dir1 # Create an archive
  tar -xvf archive.tar # Extract an archive
  tar -tvf archive.tar # List contents of an archive without extracting
  tar -czvf archive.tar.gz ./dir1 # Create a compressed archive
  tar -xzvf archive.tar.gz ./dir1 # Create a compressed archive
  ```
- `ln -s <target> <link_name>`: Create a symlink (shortcut) to a file or directory.

<br>

#### 🔻 Process & Monitoring Commands

- `du -<option>... <file/directory>...`: Show disk usage.
  - `-a`: Show all files and directories.
  - `-h`: Human-readable format (e.g., KB, MB).
  - `-s`: Summary total for each argument.
- `pidof <program_name>...`: Show the PID(s) of a running process.
  ```sh
  pidof bash # PID of bash
  pidof docker # PID of Docker
  ```
- `ps -<option>...`: View running processes.
  - `-u <user>`: Show processes for a specific user.
  - `-ef`: More detailed list.
- `lsof -<option>... <pattern>...`: List open files and their processes.
  - `-p <PID>`: Show files opened by a specific process.
  - `-i`: Show network connections.
  ```sh
  lsof -i tcp # TCP connections
  lsof -i tcp:3000 # TCP on port 3000
  ```
- `kill -<signal_num> <PID>`: Send a signal to a process.

  | Signal    | Number | Description                                                                                              |
  | --------- | ------ | -------------------------------------------------------------------------------------------------------- |
  | `SIGHUP`  | `1`    | Reload config. Tells a program to restart or reload its settings. Often used after editing config files. |
  | `SIGINT`  | `2`    | Graceful stop. Sent when you press Ctrl + C to stop a program.                                           |
  | `SIGQUIT` | `3`    | Like `SIGINT` (`2`), but also creates a debug log (core dump).                                           |
  | `SIGABRT` | `6`    | Tells a program to abort/cancel (used when something goes wrong).                                        |
  | `SIGKILL` | `9`    | Force-stops a program immediately.                                                                       |
  | `SIGTERM` | `15`   | Asks a program to stop nicely. (Default signal for `kill`)                                               |

  ```sh
  kill -9 <PID> # Force kill a process with given PID
  kill -9 $(pidof nginx) # Force kill all processes with the name nginx
  ```

- `source <file>...`: Execute commands from a file in the current shell.
  > Apply changes without needing to start a new terminal session.
  ```sh
  source ~/.bashrc # Apply changes made to .bashrc
  ```
- `watch -<option>... <command -option>...`: Re-run a command periodically.
  ```sh
  watch -n 5 free -h # Show memory usage with a 5 seconds of refresh rate
  ```

<br>

### 🔷 Command Chaining

- `cmd1 ; cmd2 ; cmd3`: Run commands in order, regardless of success.

- `cmd1 && cmd2 && cmd3`: Run next only if the previous succeeds.

- `cmd1 || cmd2 || cmd3`: Run next only if the previous fails.

- `cmd1 | cmd2 | cmd3`: Pipe output of one command to the next.
  ```sh
  find . *.* | less # View the command result in a pager
  ```

<br>

Extra:

- `tr -<option>... "<turn_this>" "<into_this>"`: Translate characters.
  ```sh
  echo "a,b,c" | tr ',' ' ' # Turn all `,` into whitespace: a b c
  echo "a,b,c" | tr -d ',' # Delete all `,`: abc
  echo "a,b2,c,d12,23" | tr -d '0-9' # Delete all numbers: a,b,c,d,
  echo "a,b,c" | tr 'a-z' 'A-Z' # Convert lowercase to uppercase: A,B,C
  ```
  > `tr` doesn't work with strings; it works only with single characters, char-by-char, or sets of characters.
  ```sh
  echo "Hello World" | tr -d 'ello' # H Wrd
  ```
- `xargs -<option>... <command>`: Build and execute command lines from standard input.

  > Takes input (a list of items) and passes it as arguments to another command. It can handle input separated by spaces, tabs, or newlines.

  > Example files.txt file:
  >
  > ```txt
  > a.txt b.txt c.txt
  > d.txt
  > e.txt
  > f.txt
  > ```

  ```sh
  cat files.txt | xargs rm # Remove all files listed in the file
  find . -name "*.log" | xargs rm # Find and remove all .log files
  ```

  - `-n <count>`: Run the command with the specified number of arguments at a time.
    ```sh
    cat files.txt | xargs -n 1 echo # Runs 'echo' 6 times, taking 1 argument each time instead of passing all 6 at once
    ```
  - `-I {}`: Placeholder for each item (`{}` gets replaced by the actual item).

    > Each command runs separately for each input line.

    ```sh
    cat files.txt | xargs -I {}  echo File: {} # Print each line by adding "File:" in front

    # File: a.txt b.txt c.txt
    # File: d.txt
    # File: e.txt
    # File: f.txt
    ```

    ```sh
    cat files.txt | tr ' ' '\n' | xargs -I {} touch {} . # Create the files listed in the file
    ```

    ```sh
    find . -name '*.go' | xargs -I {} cat {} | wc -l # Count total lines across all .go files found
    ```

<p align="right">
  <a href="#linux-debian">back to top ⬆</a>
</p>

<br>
<br>

## 🔶 Permissions

Understanding the file and directory permission system in Linux.

<br>

### 🔷 Overview

Permissions determine who can access, modify, or execute files and directories, and ensure that users and programs can only interact with the system in permitted ways.

<br>

#### 🔻 Permission Types

Permission types determine what kind of access a user (or program) has to a file or directory.

- Read (`r`):
  > View file contents or list a directory's contents.
- Write (`w`):
  > Modify file contents or add/remove files in a directory.
- Execute (`x`):
  > Run a file as a program or enter a directory.

<br>

#### 🔻 Ownership

Ownership determines who has control over a file or directory.

- User (`u`):
  > The owner of the file or directory (usually the creator).
- Group (`g`):
  > Members of a group associated with the file or directory.
- Others (`o`):
  > Everyone else on the system.

<br>

### 🔷 Structure

The permission structure is represented in a 10-character string, such as `-rwxr-xr--`, which can be broken down as follows:

<p align="center">
  <b>[identifier] [owner] [group] [others]</b>
</p>

<p align="center">
  <b>- rwx r-x r--</b>
</p>

- Identifier: The 1st char.
  - `-`: File.
  - `d`: Directory.
  - `l`: Symbolic link.
- User/Group/Others: Combination of 3 chars each.
  - `r`: Read.
  - `w`: Write.
  - `x`: Execute.
  - `-`: No permission.

<br>

#### 🔻 Octal Representation

The permission structure can also be represented in octal format, where each permission type is assigned a number:

- `r`: 4
- `w`: 2
- `x`: 1
- `-`: 0

A complete table:

| Octal | Binary | File Mode |
| ----- | ------ | --------- |
| 0     | 000    | ---       |
| 1     | 001    | --x       |
| 2     | 010    | -w-       |
| 3     | 011    | -wx       |
| 4     | 100    | r--       |
| 5     | 101    | r-x       |
| 6     | 110    | rw-       |
| 7     | 111    | rwx       |

> Example: `-rwxr-xr--` translated as `754`.
>
> - `7` for the owner (`rwx`).
> - `5` for the group (`r-x`).
> - `4` for others (`r--`).

<br>

### 🔷 Permission Commands

- `chmod -<option>... <mode> <file/directory>...`: Change file/directory permissions.
  - `<mode>`:
    > - Symbolic: `<targets><actions><permissions>`.
    >   - Targets:
    >     - `u`: User.
    >     - `g`: Group.
    >     - `o`: Others.
    >     - `a`: Everyone (user, group, others).
    >   - Actions:
    >     - `+`: Add permission.
    >     - `-`: Remove permission.
    >     - `=`: Set permission.
    >   - Permissions: `r`, `w`, `x`.
    > - Octal.
  ```sh
  chmod g+x file.txt # Add execute permission for group
  chmod u-rw file.txt # Remove read and write permissions for user
  chmod a=rwx file.txt # Set all permissions for everyone
  chmod ug-x file.txt # Remove execute permission for user and group
  chmod +x file.txt # Add execute permission for everyone (when the target is not specified it defaults to all)
  chmod 755 file.txt # Set permissions to rwxr-xr-x
  chmod -R 755 ./dir1 # Set permissions for all files and directories in dir1 recursively
  ```
- `chown -<option>... <user>:<group> <file/directory>...`: Change file/directory ownership.
  > `$USER` refers to the current user.
  - `-R`: Change ownership of all files and directories recursively.
  ```sh
  chown $USER:$USER file.txt # To the current user and group
  chown john ./dir1 # To user john and group staff
  ```
- `chgrp -<option>... <group> <file/directory>...`: Change file/directory group ownership.
  - `-R`: Change group ownership of all files and directories recursively.

<br>

### 🔷 User & Group Commands

- `groups <user>`: List groups a user belongs to.
- `usermod -aG <group> <user>`: Add a user to a group.
- `gpasswd -d <user> <group>`: Remove a user from a group.
- `passwd -<option>... <user>`: Change a user's password.
  - `-d`: Delete a user's password.
  - `-e`: Expire a user's password (force change on next login).

<p align="right">
  <a href="#linux-debian">back to top ⬆</a>
</p>

<br>
<br>
