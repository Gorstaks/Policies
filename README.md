# Policies

A Windows security baseline stored as a local Group Policy backup, with the tools to install and re-export it.

> **Warning:** installing the baseline overwrites local machine and user policy. It can restrict software, change UAC behavior and audit settings, and break applications. Test in a virtual machine and export your current policy first.

## Contents

| Item | Description |
| --- | --- |
| `{F23F984B-...}\` | The GPO backup. Contains machine and user `registry.pol`, `audit.csv` (advanced audit policy) and `GptTmpl.inf` (security settings), plus `Backup.xml` and `Bkupinfo.xml`. |
| `LGPO.exe` | Microsoft Local Group Policy Object utility. Imports and exports policy. |
| `PolicyPlusLatest.exe` | Policy Plus, a third-party local policy editor. Use it to inspect or edit the baseline, including on Windows Home. |
| `PoliciesInstall.cmd` | Imports the baseline with `lgpo /g ./`. |
| `PoliciesExport.cmd` | Exports the current local policy into this folder with `lgpo /b <folder> /n GSecurity`. |

## What the baseline enforces

- `registry.pol` holds 1,319 settings (915 machine, 404 user): the original policy plus every setting from GSecurity's `GSecurity.reg`. Group Policy re-applies them at startup, logon and each refresh (about every 90 minutes), so they cannot drift. That includes ordinary system and user keys, not only `Software\Policies`: a value removed from this file later is not undone on machines that already applied it.
- User settings apply to every account that signs in, not only the account that ran setup.
- Three whole-key deletions in `GSecurity.reg` (`MountPoints2` and two Explorer namespace entries) cannot be expressed in `registry.pol` and stay in the `.reg`.
- PowerShell script block logging is on. Transcription, module logging and invocation logging are off: with Gnyida installed they recorded every run of its PowerShell firewall check, about 3,700 a day.

## Compatibility with Gnyida

The baseline uses the same values Gnyida writes, so neither keeps overwriting the other: ASR rules (`c1db55ab` is not set, because it blocks installers started from `%TEMP%`), `AlwaysInstallElevated=0`, `CachedLogonsCount="2"`, UAC prompt behaviour, `AuditBaseObjects`, SMB `AutoDisconnect`, `SeDebugPrivilege` granted to no one, and firewall `AllowLocalPolicyMerge=1` on every profile so Gnyida's local firewall rules take effect. Kernel Object auditing is on so Gnyida's LSASS access monitor receives events 4656/4663.

## Install the baseline

1. Run `PoliciesInstall.cmd` as administrator. It elevates itself and imports the backup folder next to it.
2. Restart, or run `gpupdate /force`.

## Export current policy

Run `PoliciesExport.cmd`. It creates a new GUID-named backup folder named `GSecurity` in this directory. Run it before installing if you want a way back: import the saved folder with `lgpo /g <folder>`.

## Notes

- `LGPO.exe` is from Microsoft's Security Compliance Toolkit. `PolicyPlusLatest.exe` is third-party. Check both against their publishers before running them.
- `Releases/0.0.1/Policies.zip` holds the released copy.
- Related: `INF` (SCE templates), `Provisioning` (provisioning package), `Batch` (script-based hardening).

## Author

Gorstak
