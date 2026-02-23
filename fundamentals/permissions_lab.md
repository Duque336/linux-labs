Linux Permissions Deep Dive Lab

Author: duk33
Environment: Ubuntu / Kali Linux
Objective: Analyze how Linux permission modes (700, 750, 777) affect access control across multiple users.

1. Lab Overview

This lab evaluates Linux Discretionary Access Control (DAC) using standard permission bits.

The experiment includes:

Creating multiple users

Creating a shared directory

Applying different permission modes

Testing access from different accounts

Recording observed behavior

The goal is to understand how ownership, group membership, and permission modes determine access behavior.

2. User Creation
Commands Executed
sudo adduser testuser1
sudo adduser testuser2
System Actions Performed by adduser

Created new UID and GID

Created home directories under /home/

Copied default configuration from /etc/skel

Updated:

/etc/passwd

/etc/shadow

/etc/group

Verification
grep testuser /etc/passwd

Each user received:

Unique UID

Primary group matching username

Default shell (/bin/bash)

Home directory (/home/testuserX)

3. Directory Creation
Command
mkdir labdir
ls -ld labdir
Observation

The directory was owned by the current user (duk33).

Default permissions were influenced by the system umask.

Check umask:

umask

Umask defines which permission bits are removed when new files or directories are created.

4. Permission Testing – Mode 700
Command
chmod 700 labdir
ls -ld labdir
Permission Breakdown

700 = rwx------

Owner: Read, Write, Execute

Group: No access

Others: No access

Testing
su - testuser1
cd labdir

su - testuser2
cd labdir
Result

Both users received:

Permission denied

Analysis

Since neither user was the owner, and group/others had no permissions, access was denied.

Security Implication

700 enforces strict isolation.
Appropriate for private or sensitive directories.

5. Permission Testing – Mode 750 (Without Group Configuration)
Command
chmod 750 labdir
ls -ld labdir

750 = rwxr-x---

Owner: Full access

Group: Read and Execute

Others: No access

Result

Both users still received:

Permission denied

Explanation

The directory’s group ownership had not been modified.
Neither testuser1 nor testuser2 belonged to the directory’s group.

Linux therefore evaluated the “others” permission field (---).

Conclusion

750 alone does not enable collaboration unless group membership is configured properly.

6. Controlled Group-Based Access Testing
Create Shared Group
sudo groupadd labgroup
Add Users to Group
sudo usermod -aG labgroup testuser1
sudo usermod -aG labgroup testuser2
Change Directory Group Ownership
sudo chown :labgroup labdir

Re-login users to refresh group membership.

Apply Permissions
chmod 750 labdir
Result

Both testuser1 and testuser2 were able to enter the directory.

Analysis

Since both users belonged to labgroup, Linux evaluated group permissions (r-x).

Access was granted.

Security Insight

Group-based permissions enable controlled collaboration.

This supports the Principle of Least Privilege.

7. Permission Testing – Mode 777
Command
chmod 777 labdir
ls -ld labdir

777 = rwxrwxrwx

Owner: Full access

Group: Full access

Others: Full access

Testing

Both users were able to:

Enter the directory

Create files

Modify contents

Security Implication

777 is highly permissive.

On multi-user systems, this can allow:

Unauthorized modification

File tampering

Potential privilege escalation

This permission mode should be avoided in production environments.

8. Summary of Results

Permission Mode | Owner | Group | Others | Risk Level
700 | Full | None | None | Low
750 | Full | Limited | None | Moderate (if group controlled)
777 | Full | Full | Full | High

9. Key Technical Takeaways

Linux evaluates permissions in this order:

Owner

Group

Others

chmod alone is insufficient without correct ownership configuration.

Group membership directly influences access behavior.

Misconfigured permissions are a common source of system vulnerabilities.

Secure systems rely on minimal required access, not maximum convenience.
