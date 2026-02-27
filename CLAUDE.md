# CLAUDE.md — IT Consultant / MSP Technician Context

## Role & Work Style

I am an IT technician/consultant working across multiple customers in enterprise environments.
My work spans a wide range of tasks: Active Directory, Windows Server, networking, scripting, cloud administration, and end-user support.
I use **PowerShell heavily** — for automation, bulk operations, reporting, and administration.
I value efficient, reusable, well-commented scripts over one-liners that are hard to maintain.

---

## How I Want Claude to Behave

- **Default to PowerShell** for any Windows/AD/cloud task unless I specify otherwise.
- Write scripts that are **production-safe**: include error handling (`try/catch`), verbose logging, and `-WhatIf` support where applicable.
- **Always ask or clarify** if a script will make destructive or irreversible changes (deletions, bulk modifications, AD account changes, etc.).
- Prefer **modular, reusable code** — functions over monolithic scripts.
- Add **inline comments** explaining non-obvious logic. I may hand scripts to customers or colleagues.
- When providing CLI commands, show the **full command** — not abbreviated or shorthand that requires guessing.
- If something has multiple valid approaches, briefly explain the tradeoff so I can pick.

---

## Customer Context Management

I work with **multiple customers** simultaneously, each with their own:
- Domain names (e.g., `customer1.local`, `customer2.com`)
- Naming conventions (servers, OUs, groups, GPOs)
- Environment quirks (on-prem only, hybrid, cloud-only, etc.)
- Ticketing and documentation systems

**When I mention a customer by name or shorthand**, remember the context for that session.
If I switch customers mid-conversation, acknowledge it so we stay in the right context.

When writing scripts intended for a specific customer, use **placeholder variables at the top** (e.g., `$Domain`, `$OUPath`, `$CustomerName`) so they're easy to adapt.

---

## Technology Stack

### Microsoft / Windows
- Active Directory (AD DS, ADUC, ADAC, PowerShell AD module)
- Windows Server 2016 / 2019 / 2022
- Group Policy (GPOs)
- DHCP / DNS (Windows Server roles)
- Hyper-V
- Windows Admin Center
- File Servers / DFS
- Print Servers
- BitLocker / LAPS

### Microsoft 365 / Azure
- Exchange Online / EXO PowerShell
- Entra ID (Azure AD) — users, groups, Conditional Access
- Intune / MEM
- Microsoft Teams administration
- SharePoint Online
- Microsoft Graph API / PowerShell SDK

### Networking
- UniFi (Ubiquiti)
- Meraki
- Cisco (IOS, switches)
- Omada (TP-Link)
- VLANs, DHCP scopes, firewall rules

### Security & Monitoring
- Zscaler (including SSL inspection quirks)
- Penetration testing tools (nmap, nikto, sqlmap — for authorized work only)
- Kali Linux
- Windows Defender / Endpoint security

### Databases
- SQL Server (clustering, Availability Groups, maintenance)
- MySQL / MariaDB

### Virtualization / Infrastructure
- Proxmox (home lab and some customer work)
- Docker / containers
- VMware (basic)

### Scripting / Dev
- PowerShell (primary)
- Python (learning / intermediate)
- Bash
- Git (basic)

---

## PowerShell Preferences

```powershell
# Preferred script structure:
#Requires -Version 5.1
#Requires -Modules ActiveDirectory

<#
.SYNOPSIS
    Short description of what the script does.
.DESCRIPTION
    Longer description. Mention the customer/use case.
.PARAMETER CustomerDomain
    FQDN of the customer's domain.
.EXAMPLE
    .\ScriptName.ps1 -CustomerDomain "customer.local"
#>

[CmdletBinding(SupportsShouldProcess)]
param(
    [Parameter(Mandatory)]
    [string]$CustomerDomain
)

# --- Variables ---
$LogPath = "C:\Logs\ScriptName_$(Get-Date -Format 'yyyyMMdd_HHmm').log"

# --- Functions ---
function Write-Log {
    param([string]$Message, [string]$Level = "INFO")
    $entry = "[$(Get-Date -Format 'yyyy-MM-dd HH:mm:ss')] [$Level] $Message"
    Write-Verbose $entry
    Add-Content -Path $LogPath -Value $entry
}

# --- Main ---
try {
    Write-Log "Script started for $CustomerDomain"
    # ...
}
catch {
    Write-Log "ERROR: $_" -Level "ERROR"
    throw
}
```

- Use `Write-Verbose` / `Write-Log` over `Write-Host` for automation-friendly output
- Always include `-ErrorAction Stop` when I need terminating errors in a try/catch
- Use `[CmdletBinding(SupportsShouldProcess)]` + `$PSCmdlet.ShouldProcess()` for destructive actions
- Prefer `Get-ADUser -Filter` over `-LDAPFilter` unless LDAP is clearly better
- Always output objects, not just strings — makes piping and further processing easy

---

## Common Tasks I Do Regularly

- AD user lifecycle: create, modify, disable, delete (bulk and single)
- Group and OU management
- Exchange Online: mailbox setup, permissions, shared mailboxes, quarantine
- DHCP scope migrations between servers
- DNS record management and troubleshooting
- GPO creation, linking, troubleshooting (RSoP, gpresult)
- FSMO role transfers
- SSL certificate issues (especially with proxy interception like Zscaler)
- Printer management and driver deployment
- Network troubleshooting (VLAN, routing, connectivity)
- SQL Server AG sync issues and cluster troubleshooting
- BitLocker key recovery
- Intune device enrollment and policy deployment
- MCP server configuration and Claude Desktop integration
- Penetration testing (authorized — nmap scans, vulnerability assessment)

---

## Output Preferences

- **Code blocks** for all scripts and commands — always specify the language (`powershell`, `bash`, `sql`, etc.)
- **Short explanation** before a script block, longer notes after if needed
- **Warnings** clearly marked if a command/script is destructive or irreversible
- No excessive padding or filler text — get to the point
- If a script is long, structure it with clear section headers as comments
- When troubleshooting, present steps in logical order (quick wins first)

---

## Things to Avoid

- Don't write scripts that silently swallow errors
- Don't assume a specific OU path or domain — ask or use a variable
- Don't suggest GUI-only solutions when PowerShell can do the job
- Don't over-engineer simple tasks, but don't under-engineer tasks that run in production
- Avoid deprecated cmdlets (e.g., use `Get-MgUser` over `Get-AzureADUser` for Graph)

---

## Language

- I communicate in **English** with Claude
- Some customers and documentation may be in **Swedish** — I may paste Swedish text and ask you to work with it; handle that naturally

---

*Last updated: February 2026*
