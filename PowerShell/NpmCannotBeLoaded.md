# Resolve: "npm.ps1 cannot be loaded because running scripts is disabled on this system"

This error occurs when PowerShell's execution policy prevents running scripts (including the npm wrapper script). Here are several methods to resolve it.

## Error message
```
npm : File C:\Users\<YourUsername>\AppData\Roaming\npm\npm.ps1 cannot be loaded because running scripts is disabled on this system.
```

## Quick Fix: Bypass for Current Session

Run **one** of the following in your current PowerShell window (does **not** require admin, lasts only for this session):

```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process
```

After running this, `npm` should work immediately. When you close and reopen PowerShell, you'll need to run it again.

## Permanent Fix (Option 1): Set for Current User

Run in PowerShell (does **not** require admin):

```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
```

This applies to all PowerShell sessions for your user account and persists across restarts.

## Permanent Fix (Option 2): Set for All Users (Admin required)

Run in **Administrator** PowerShell:

```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope LocalMachine
```

This applies globally and persists across restarts. Requires admin privileges.

## What does RemoteSigned mean?

`RemoteSigned` policy allows:
- Locally created scripts to run without a signature.
- Downloaded scripts (from the internet) to run **only** if they are signed by a trusted publisher.

This is a good balance between security and usability for development.

## Check your current execution policy

```powershell
Get-ExecutionPolicy -List
```

Output shows policies at different scopes (Process, CurrentUser, LocalMachine, etc.). The **effective** policy is the most restrictive one in effect.

## Alternative: Run npm via cmd.exe

If you prefer **not** to change the execution policy, you can use `cmd.exe` instead:

```cmd
npm install package-name
npm run build
```

Or use the Node.js installer to add Node to your system PATH, then invoke npm directly from `cmd.exe`.

## Other execution policies (reference)

| Policy | Description |
|--------|-------------|
| `Unrestricted` | No restrictions; any script can run. (Not recommended.) |
| `RemoteSigned` | Local scripts run freely; downloaded scripts need a signature. (Recommended for dev.) |
| `AllSigned` | All scripts must be signed. (Very restrictive.) |
| `Restricted` | No scripts allowed. (Default for many Windows installs.) |
| `Bypass` | No restrictions; nothing is blocked. (Use sparingly.) |
| `Default` | Uses the system default (usually Restricted). |

## Troubleshooting

- After changing the policy, **restart PowerShell** or open a new window to ensure the change takes effect.
- If `Set-ExecutionPolicy` is blocked, you may need to run PowerShell as **Administrator**.
- Corporate / Group Policy: If your organization enforces a group policy, you may not be able to change the execution policy on your machine.

## References

- Microsoft Docs: https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.security/set-executionpolicy
- PowerShell Execution Policies: https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_execution_policies

---
**Recommended approach for development:**  
Use **`RemoteSigned` at `CurrentUser` scope** — it's secure enough for local development and doesn't require admin privileges.
