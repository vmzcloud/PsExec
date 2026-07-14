# PsExec Panel

Lightweight Windows HTA UI for running [Sysinternals PsExec](https://learn.microsoft.com/sysinternals/downloads/psexec) against a list of PCs with reusable command templates.

## Requirements

- Windows with MSHTA (built-in)
- [PsExec](https://learn.microsoft.com/sysinternals/downloads/psexec) installed locally
- Network access and permissions to administer target machines

## Project layout

| File / folder | Purpose |
|---------------|---------|
| `PsExecPanel.hta` | Main UI and logic |
| `config.ini` | PsExec path and optional credentials |
| `pcs.csv` | Host inventory (`Name,Notes`) |
| `templates.csv` | Named remote commands (`Name,Command`) |
| `Log\` | Per-run log files (created automatically) |

## Quick start

1. Edit `config.ini` and set the path to `PsExec.exe` (optional `Username` / `Password`).
2. Edit `pcs.csv` with your hostnames.
3. Optionally edit `templates.csv` to add or change remote commands.
4. Double-click `PsExecPanel.hta`.
5. Select shell, template, flags, and target PC(s), then click **Connect**.

## Configuration

### `config.ini`

```ini
[PsExec]
Path=C:\Tools\PSTools\PsExec.exe
Username=
Password=
```

- `Path` — full path to `PsExec.exe` (required)
- `Username` / `Password` — optional credentials passed as `-u` / `-p`

### `pcs.csv`

```csv
Name,Notes
PC-001,Reception desk
PC-002,Warehouse office
```

### `templates.csv`

```csv
Name,Command
Interactive shell,
Whoami,whoami
IP config,ipconfig /all
```

- Empty `Command` opens an interactive remote shell (PowerShell or CMD).
- Non-empty commands run once, then the console pauses so you can read the output.
- Quote fields that contain commas.

## Usage

| Control | Description |
|---------|-------------|
| **Shell** | Remote shell: PowerShell or CMD |
| **Template** | Command from `templates.csv` |
| **-s** | Run as system account |
| **-i** | Interactive session on the remote desktop |
| **-h** | Elevated token (if available) |
| **Wait** | Wait for each target to finish before starting the next |
| **Search** | Filter the PC list by name or notes |
| **Connect** | Launch PsExec for selected (or focused) PC(s) |

- Checkboxes select multiple PCs; if none are checked, the focused row is used.
- Multi-PC runs ask for confirmation first.
- The preview line shows the remote payload that will be executed.

## Logging

Every Connect creates a file under `Log\`:

```text
Log\{hostname}_{yyyyMMdd_HHmmss}.log
```

Example: `Log\PC-001_20260714_153045.log`

Each log starts with a header (time, host, template, redacted command).

| Run type | Log contents | Console after run |
|----------|--------------|-------------------|
| Command template | Header + PsExec/remote console output | Output is shown, then **Press any key to continue** |
| Interactive shell | Header only (session output not captured) | Live shell until you close it |

Passwords in logged command lines are replaced with `********`.

**Note:** With **-i**, some remote output may appear on the remote session instead of the local console/log. For full local capture of template output, leave **-i** unchecked when appropriate.

## Notes

- This is an admin/remote-management tool. Use only on systems you are authorized to manage.
- Do not commit real passwords in `config.ini` to shared repositories.
- `Log\` is created next to the HTA on first run; clean it up as needed.
