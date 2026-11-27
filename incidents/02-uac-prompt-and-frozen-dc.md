# Wrong machine, then a frozen domain controller

**What happened:** I started making the HR shared folder on **WS01** (signed in as Gus) instead of on the server.
Gus isn't an admin, so Windows popped up a UAC prompt asking for admin credentials. The screen stayed dimmed and
I couldn't get to the prompt from VirtualBox.

WS01 wouldn't shut down normally with the prompt open, so I forced it off (like holding the power button):

```bash
VBoxManage controlvm "windows 10 " poweroff
```

Then DC01 froze too. The clock was stuck, the Start button didn't work, and it ignored the shutdown signal for a
couple of minutes. Disk space was fine this time, so it wasn't a repeat of the [disk-full problem](01-host-disk-full.md).
I forced it off and started it again, and it was back at the sign-in screen about 10 minutes after it first froze.

**After it came back:** the HR-Share-Modify group I'd made just before the freeze was still there, and AD was fine.
I also deleted the half-made `C:\Shares` folder on WS01.

Two takeaways: shared folders go on the server, not on someone's PC, and force-off is a last resort. DC01 still
ignores the shutdown signal from VirtualBox, so I shut it down from inside Windows now (Start > Power > Shut down).
