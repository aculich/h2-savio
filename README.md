# Overview

This repo will document a setup that is standard best practice for shared
research directories on an HPC cluster. It prevents permission conflicts and
ensures seamless collaboration among research group members. 

- ✅ *Collaborative Access* - All files and directories belong to the research group fc_winerev, ensuring smooth collaboration.
- ✅ *Automatic Group Inheritance* - New files/directories automatically belong to fc_winerev (via g+s).
- ✅ *Shared Read/Write Permissions* - Everyone in fc_winerev can read and modify any file.
- ✅ *Prevents Permission Issues* - Avoids problems where files have different owners/groups that prevent modifications.
- ❌ *Private files* - Avoids problems where files are lost due to permission issues in $HOME directories or unreadbale/unwritable in group directories.

These commands are configuring the directory `/global/home/groups/fc_winerev` on
the Savio HPC cluster for a faculty condo group called `fc_winerev`. Below is a
breakdown of what each command does and why it’s useful for collaborative work.

--

## **Graphic Representation of Unix File Permissions**  
Below is a visual representation of a common Unix file permission mode, similar to the style in your attached screenshot.  

### **Visual Representation of `rwx rwx rwx` (Octal: `777`)**  

![image](https://github.com/user-attachments/assets/ca6dd013-064e-491f-ae03-c3e7e474f96f)

Taken from: [Day 6: File Permissions and Access Control Lists🐧](url)

---
## **Table 0: Most Often Used Unix File and Directory Modes**  

| **Octal** | **Mode**    | **Description** |
|-----------|------------|------------------------------------------------------|
| **644**   | rw-r--r--  | Regular file: owner can read/write, others can only read. Common for text files and configuration files. |
| **600**   | rw-------  | Private file: only owner can read/write. Used for sensitive files like SSH keys (`~/.ssh/id_rsa`). |
| **666**   | rw-rw-rw-  | World-writable file (not recommended). All users can read/write, but not execute. |
| **755**   | rwxr-xr-x  | Executable file: owner can read/write/execute, others can read/execute. Used for scripts and binaries. |
| **700**   | rwx------  | Private executable file: only owner can read/write/execute. Useful for private scripts. |
| **700**   | rwx------  | Private directory: only owner can access. Used for home directories like `~/.ssh/`. |
| **750**   | rwxr-x---  | Directory accessible by owner and group. Others have no access. |
| **755**   | rwxr-xr-x  | Public directory: owner can write, others can read/execute. Common for web server directories (`/var/www/html`). |
| **777**   | rwxrwxrwx  | Full access (not recommended). Everyone can read, write, and execute. Used temporarily for troubleshooting. |
| **1777**  | rwxrwxrwt  | Sticky bit: everyone can write, but only the owner can delete their own files (common for `/tmp`). |
| **2770**  | rwxrws---  | **(Recommended for research groups)**: Full read/write/execute access for the owner and group, but no access for others. The SetGID bit ensures that all newly created files inherit the group ownership of the directory. This is ideal for research groups collaborating on files, as it prevents accidental permission mismatches. |
| **2775**  | rwxrwsr-x  | **(Alternative for mixed access teams)**: Similar to `2770`, but allows read and execute access for others, useful if non-group members need to browse the directory without modifying files. |

### Why `2770` is ideal for research groups:
- **Owner and group** can fully read, write, and execute files (`rwxrws---`).
- **SetGID (s on group permissions)** ensures that **all new files inherit the group** automatically.
- **Others have no access** (`---`), maintaining privacy for internal files.
- This setup avoids the common problem of files being created with **wrong group ownership**, which can prevent collaboration.

---

## **Table 1: Unix File Permissions Breakdown**  
This table shows the basic Unix file permissions in octal notation, their corresponding symbolic representation, and what they mean.  

| **Octal** | **String Representation** | **Permissions** |
|---------|----------------------|--------------------|
| **0**  | --- | No Permission |
| **1**  | --x | Execute |
| **2**  | -w- | Write |
| **3**  | -wx | Write + Execute |
| **4**  | r-- | Read |
| **5**  | r-x | Read + Execute |
| **6**  | rw- | Read + Write |
| **7**  | rwx | Read + Write + Execute |

---

## **Table 2: Common Unix File and Directory Modes**  
This table lists common octal permission modes for files and directories, explaining their usage.  

| **Octal** | **Mode**    | **Description** |
|-----------|------------|------------------------------------------------------|
| **000**   | ---------  | No permissions for anyone |
| **644**   | rw-r--r--  | Regular file: owner can read/write, others can only read |
| **600**   | rw-------  | Private file: only owner can read/write |
| **666**   | rw-rw-rw-  | All users can read/write (not recommended) |
| **755**   | rwxr-xr-x  | Executable file: owner can read/write/execute, others can read/execute |
| **700**   | rwx------  | Private executable file: only owner can read/write/execute |
| **777**   | rwxrwxrwx  | Full access (read/write/execute) for everyone (not recommended) |
| **700**   | rwx------  | Private directory: only owner can access |
| **750**   | rwxr-x---  | Directory accessible by owner and group |
| **755**   | rwxr-xr-x  | Public directory: owner can write, others can read/execute |

---

## **Table 3: Special Unix File and Directory Modes**  
This table lists special octal permission modes, explaining their effects.

| **Octal** | **Mode**    | **Description** |
|-----------|------------|------------------------------------------------------|
| **1777**  | rwxrwxrwt  | Sticky bit: everyone can write, but only the owner can delete their own files (common for `/tmp`). |
| **2755**  | r-xr-sr-x  | **SetGID**: Executable runs with the group’s permissions. Useful for shared group executables. |
| **2700**  | r-xr-s---  | **Private directory with SetGID**: only group members can access. Used to restrict access while preserving group ownership. |
| **4755**  | r-sr-xr-x  | **SetUID**: Executable runs with the owner's permissions. Used for programs needing elevated privileges. |
| **4000**  | r-s------  | **SetUID**: File executes as the file owner, regardless of the user running it. |
| **2000**  | r--r-s---  | **SetGID**: File executes as the group owner, preserving group identity when run. |
| **1000**  | r--r--r-T  | **Sticky bit**: Restricts file deletion in shared directories, preventing non-owners from deleting each other's files. |
| **2770**  | rwxrws---  | **(Recommended for research groups)**: Full read/write/execute access for the owner and group, but no access for others. The SetGID bit ensures that all newly created files inherit the group ownership of the directory. This is ideal for research groups collaborating on files, as it prevents accidental permission mismatches. |
| **2775**  | rwxrwsr-x  | **(Alternative for mixed access teams)**: Similar to `2770`, but allows read and execute access for others, useful if non-group members need to browse the directory without modifying files. |

### Why `2770` is ideal for research groups:
- **Owner and group** can fully read, write, and execute files (`rwxrws---`).
- **SetGID (s on group permissions)** ensures that **all new files inherit the group** automatically.
- **Others have no access** (`---`), maintaining privacy for internal files.
- This setup avoids the common problem of files being created with **wrong group ownership**, which can prevent collaboration.

---


🚀 Explanation of Each Command and Its Impact on an HPC Cluster Research Group

## 1. Change Group Ownership Recursively

`chgrp -R fc_winerev /global/home/groups/fc_winerev`

What It Does:
- Changes the group ownership of all files and directories inside `/global/home/groups/fc_winerev` to the research group `fc_winerev`.
- The `-R` (recursive) option ensures it applies to all files and subdirectories.

Why?
- Ensures that all files in this shared space belong to the fc_winerev group.
- Any user in fc_winerev will have group-level access to these files (depending on permissions).

## 2. Set Group Permissions on Directories

`find /global/home/groups/fc_winerev -type d -exec chmod g+o,g+s {} +`

What It Does:
- `find ... -type d`: Finds all directories inside `/global/home/groups/fc_winerev`.
- `chmod g+o,g+s {}:`
- `g+o`: Ensures group members get the same permissions as the owner (group ownership matches the user’s permissions).
- `g+s`: SetGID (Set Group ID) on directories, ensuring that:
- New files and subdirectories inside will inherit the group fc_winerev instead of the user’s primary group.
- This is essential in collaborative research environments so files don’t end up owned by different groups.

Why?
- Maintains consistent group ownership when users create files, avoiding permission issues where only the file creator can modify files.
- Ensures everyone in the group can access shared directories properly.

## 3. Set Group Permissions on Files

`find /global/home/groups/fc_winerev -type f -exec chmod g+o,g+rw {} +`

What It Does:
- `find ... -type f`: Finds all files inside `/global/home/groups/fc_winerev`.
- `chmod g+o,g+rw {}:`
- `g+o`: Makes sure group permissions match the owner’s permissions.
- `g+rw`: Grants read (r) and write (w) access to the fc_winerev group.

Why?
- Ensures that any file created inside this shared research space is readable and writable by all members of the research group.
- Prevents scenarios where one user creates a file that others cannot modify.
