# My PC ran out of disk space and both VMs crashed

**What happened:** WS01 crashed while I was opening Command Prompt. VirtualBox listed it as `<inaccessible>`,
and DC01 had crashed too.

**Why:** my Windows 10 VM was still stored on my small main drive. I took a snapshot while it was running, which
saves the VM's memory to a file (over 1 GB). That used up the last of the space and the drive hit 100%. With no
space left, VirtualBox couldn't save the VM's settings file, and it ended up empty (0 bytes).

```bash
df -h /              # 100% used
VBoxManage list vms  # showed "<inaccessible>"
```

**How I fixed it:**

1. Copied the whole VM folder to my big drive (`/mnt/storage/vms`).
2. Checked the copy matched the original before deleting anything.
3. Deleted the original to free up the main drive.
4. VirtualBox keeps a backup of the settings file (`.vbox-prev`). I copied that over the empty `.vbox` file.
5. Removed the VM from VirtualBox and added it back from the new location.

Both VMs booted again and all the snapshots were still there.

**What I do differently now:** all the VMs live on the big drive, and I run `df -h` before taking snapshots.
