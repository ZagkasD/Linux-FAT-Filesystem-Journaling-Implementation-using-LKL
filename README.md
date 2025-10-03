# Linux FAT Filesystem Journaling Implementation using LKL

---

## Overview

This repository documents a project for the **Operating Systems** course. The primary objective was to enhance the reliability of the legacy **FAT filesystem** by implementing a rudimentary **journaling/logging mechanism**.

The modifications were carried out within the Linux kernel filesystem layer, utilizing the **Linux Kernel Library (LKL)**. LKL allowed the modified kernel filesystem code to run in user-space, enabling easier development, testing, and debugging of the changes.

---

## Project Goal

The core goal was to safeguard the integrity of the FAT filesystem against unexpected crashes (e.g., power loss) during file operations. This was achieved by:

1.  **Implementing a Transaction Log:** Creating a designated **journal file** (e.g., `journal.txt`) to log critical **metadata changes** before they are committed to the disk.
2.  **Ensuring Atomicity:** Guaranteeing that key metadata operations (like directory entry creation, deletion, or renaming) are performed atomically or can be safely recovered upon reboot.
3.  **FAT Driver Modification:** Integrating the logging calls directly into the relevant functions of the kernel's FAT driver.

---

## Technical Implementation

The work involved modifying the FAT filesystem code within the context of the Linux Kernel's **Virtual Filesystem (VFS)** layer. Key aspects of the implementation included:

* **Linux Kernel Library (LKL):** Used as the primary environment to compile and run the modified FAT driver in user-space.
* **FAT Structure Analysis:** Deep dive into the structure of FAT, including the **File Allocation Table (FAT)**, **Superblocks**, and **Inodes**, to identify the exact points where metadata updates occur.
* **System Call Interface:** Exploration of how user applications interact with the kernel through the **System Call Interface**, specifically focusing on functions like `sys_open` and `sys_write` to understand file manipulation flow.
* **Journaling Logic:** Implementing logic to write transaction records to the journal file for critical operations related to directory entries and file allocation chains.

---

## Known Challenges & Development Notes

A significant challenge encountered during development was the handling of the journal file from within the kernel context:

* **Journal Write/Read Issues:** Attempts to write certain metadata structures (like directory entry slot information) to the `journal.txt` file from the modified FAT functions were successful, but reading this data back via the standard VFS/`inode.c` mechanisms proved unreliable.
* **Data Integrity Risk:** Directly calling `read` from the logging function led to undesirable side effects, including **data corruption/loss** in the `inode.c` context.
