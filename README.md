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
