# Policies

A Windows security baseline stored as a local Group Policy backup, with the tools to install and re-export it.

> **Warning:** installing the baseline overwrites local machine and user policy. It can restrict software, change UAC behavior and audit settings, and break applications. Test in a virtual machine and export your current policy first.

## Contents

| Item | Description |
| --- | --- |
| `{09EC67FF-...}\` | The GPO backup. Contains machine and user `registry.pol`, `audit.csv` (advanced audit policy) and `GptTmpl.inf` (security settings), plus `Backup.xml` and `Bkupinfo.xml`. |
| `LGPO.exe` | Microsoft Local Group Policy Object utility. Imports and exports policy. |
| `PolicyPlusLatest.exe` | Policy Plus, a third-party local policy editor. Use it to inspect or edit the baseline, including on Windows Home. |
| `PoliciesInstall.cmd` | Imports the baseline with `lgpo /g ./`. |
| `PoliciesExport.cmd` | Exports the current local policy into this folder with `lgpo /b <folder> /n GSecurity`. |

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
