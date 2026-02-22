# Filesystem Exploration

## Overview

This lab explores the Linux filesystem layout and basic inspection using `ls -lah`.  
The goal is to understand file visibility, permissions, directory roles, and why certain directories behave differently in production systems.

---

## What `ls -lah` Reveals

The command:

ls -lah


combines three important flags:

- `-l` → Long listing format (permissions, ownership, size, timestamps)  
- `-a` → Shows hidden files (including dotfiles)  
- `-h` → Human-readable file sizes (KB, MB, GB)

From this output we can observe:

- File permissions (read, write, execute
