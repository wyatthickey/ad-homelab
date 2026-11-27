# Active Directory Home Lab

I set up a small Windows domain at home to practice the things a help desk
deals with every day: password resets, locked accounts, groups and shared folders. It all runs in
VirtualBox on my Linux PC. I took notes and screenshots as I went, including the parts where I messed up.

## What I set up

```
          My Linux PC (VirtualBox)
                    |
       +------------+------------+
       |                         |
  DC01 (Server 2022)        WS01 (Windows 10)
  192.168.50.10             192.168.50.20
  domain controller + DNS   the "employee" PC
       |                         |
       +---- private network ----+
```

- A domain called `corp.internal`
- Folders (OUs) for staff by department (IT, HR, Sales), plus one for computers and one for groups
- Three test users: Gus Lee (IT), Tommy Emanuel (HR) and Jason Woo (Sales)
- Both VMs can also reach the internet through VirtualBox's NAT
- Windows Server 2022 is the free 180-day evaluation

## Building it

1. [The server VM](docs/01-building-the-server-vm.md): creating the VM and installing Windows Server
2. [The network](docs/02-lab-network.md): giving both VMs static IPs and getting them to ping each other
3. [The domain controller](docs/03-domain-controller.md): turning the server into DC01 and checking it worked
4. [Joining the PC to the domain](docs/04-joining-the-client.md): pointing WS01 at the DC for DNS and joining it
5. [OUs and users](docs/05-ous-and-users.md): creating the folder structure and user accounts

## Practice tickets

Once the domain was working, I made up tickets and worked through them the way a help desk would.

| # | Ticket | What I practiced |
|---|---|---|
| 1 | [Password reset](tickets/ticket-01-password-reset.md) | Resetting a password and making the user pick a new one |
| 2 | [Account lockout](tickets/ticket-02-account-lockout.md) | Setting a lockout policy, checking the event log to see where the bad passwords came from, unlocking |
| 3 | [Security groups](tickets/ticket-03-security-groups.md) | Making a group per department, and figuring out why a user didn't have their new group yet |
| 4 | [HR shared folder](tickets/ticket-04-hr-shared-folder.md) | A folder only HR can open, tested with an HR user (allowed) and a Sales user (denied) |

## Things that went wrong

- **DNS problem on the DC.** When I checked DC01 after setting it up, `nslookup` showed it had two addresses
  in DNS: the lab one and the NAT one. Clients would sometimes have been sent to an address they can't reach,
  so I removed the wrong one. ([Lab 3](docs/03-domain-controller.md))
- **New group didn't show up.** I added Gus to a group, but after signing in he still didn't have it. AD showed
  it correctly, and `klist` showed Windows had signed him in from cached credentials without checking with the
  DC. Signing out and back in fixed it. ([Ticket #3](tickets/ticket-03-security-groups.md))
- **My PC ran out of disk space.** A VM snapshot filled up my main drive and both VMs crashed. One VM's settings
  file got wiped, so I restored it from VirtualBox's backup copy and moved the VM to my bigger drive. Now I
  check free space before taking snapshots. ([Write-up](incidents/01-host-disk-full.md))
- **Wrong machine, then a frozen server.** I started sharing the HR folder on the employee PC instead of the
  server, and then the server froze and had to be forced off. It came back fine. ([Write-up](incidents/02-uac-prompt-and-frozen-dc.md))

## Screenshots

All screenshots are in [`screenshots/`](screenshots/), numbered in order. Each page above also shows its own
screenshots at the bottom.

## What I want to try next

- Make the HR folder show up as a drive letter automatically using Group Policy
- Fix the cached sign-in problem from ticket #3 with a Group Policy setting
