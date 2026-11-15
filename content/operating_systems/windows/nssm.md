+++
title = "NSSM (the Non-Sucking Service Manager)"
date = 2026-11-15T09:00:00+11:00
draft = false
tags = ["windows", "nssm", "winget", "sysadmin", "services"]
categories = ["System Administration", "Windows"]
+++

A guide on installing NSSM and using it to create, edit, disable, and remove Windows Services—from simple executables to complex CLI tools.
<!--more-->

Running background applications, scripts, or cross-platform binaries (like Node.js, Python, or Go apps) on Windows often 
requires wrapping them in a native Windows Service. While Windows includes the built-in `sc.exe` utility, 
it expects executables to natively implement the Windows Service Control Manager (SCM) API. 

**NSSM (the Non-Sucking Service Manager)** solves this problem by acting as a lightweight wrapper that monitors your application, 
restarts it if it crashes, and redirects `stdout`/`stderr` to log files.

---

## Prerequisites & Installation

Before installing or managing services, open **Windows Terminal**, **PowerShell**, or **Command Prompt** as an **Administrator**.

### Installing NSSM via Winget

The fastest way to install NSSM on modern Windows systems (Windows 10/11 and Windows Server) is using the 
Windows Package Manager (`winget`).

```powershell
winget install --id=NSSM.NSSM -e
```

Once installed, restart your terminal session (or refresh your environment variables) so that `nssm` is available in 
your system `PATH`. Verify the installation:

```powershell
nssm version
```

---

## Creating Windows Services

NSSM supports both a graphical user interface (GUI) and a purely command-line interface (CLI) for automation.

### 1. Simple Example (Interactive GUI)

If you prefer a visual setup or want to explore all available options, pass the `install` command followed only by your 
desired service name:

```powershell
nssm install MySimpleApp
```

This opens the NSSM Service Installer dialog:
1. **Path:** Browse to your executable (e.g., `C:\Tools\my-app.exe`).
2. **Startup directory:** Automatically populates with the executable's folder.
3. **Arguments:** Leave blank if none are needed.
4. Click **Install service**.

### 2. Simple Example (Command Line)

To install a basic executable without arguments directly from the terminal:

```powershell
nssm install MySimpleApp "C:\Tools\my-app.exe"
```

### 3. Complex Example (Executables with CLI Parameters)

When wrapping runtimes like Python, Node.js, or specialized CLI tools (such as `rclone`, `prometheus`, or `caddy`), you 
must separate the **application binary** from its **arguments**, and explicitly configure the **working directory** and 
**logging**.

#### Scenario A: Running a Python Script with Arguments

Suppose you want to run a Python HTTP server or worker script inside a virtual environment:

```powershell
# 1. Install the service pointing to the virtual environment's python.exe
nssm install DataWorkerService "C:\Apps\DataWorker\.venv\Scripts\python.exe"

# 2. Pass the script path and CLI flags as AppParameters
nssm set DataWorkerService AppParameters "worker.py --env production --workers 4"

# 3. Set the working directory so relative paths resolve properly
nssm set DataWorkerService AppDirectory "C:\Apps\DataWorker"

# 4. Configure human-readable display details
nssm set DataWorkerService DisplayName "Data Worker Background Service"
nssm set DataWorkerService Description "Processes background ingestion queues using Python."
```

#### Scenario B: Complex CLI Tool with Quoted Arguments & Logging

When passing parameters that contain spaces or nested quotes (such as mounting cloud storage with `rclone` or running a 
reverse proxy), escape quotes carefully in PowerShell using `--%` (the stop-parsing token) or backticks:

```powershell
# Install the base executable
nssm install CaddyProxy "C:\Apps\Caddy\caddy.exe"

# Use --% in PowerShell to pass complex arguments cleanly to NSSM
nssm --% set CaddyProxy AppParameters run --config "C:\Apps\Caddy\Caddyfile" --adapter caddyfile

# Set the working directory
nssm set CaddyProxy AppDirectory "C:\Apps\Caddy"

# Redirect stdout and stderr to log files (Crucial for debugging!)
nssm set CaddyProxy AppStdout "C:\Apps\Caddy\logs\service-out.log"
nssm set CaddyProxy AppStderr "C:\Apps\Caddy\logs\service-err.log"

# Enable automatic log rotation (rotate when files exceed 5MB)
nssm set CaddyProxy AppRotateFiles 1
nssm set CaddyProxy AppRotateOnline 1
nssm set CaddyProxy AppRotateBytes 5242880
```

> **Tip:** Always configure `AppStdout` and `AppStderr` for CLI applications. Without log redirection, error messages 
> printed to the console on startup will be lost, making troubleshooting difficult.

---

## Managing Service Lifecycle

Once created, you can control the service using either `nssm` or native PowerShell commands 
(`Start-Service`, `Stop-Service`, `Restart-Service`).

```powershell
# Start the service
nssm start CaddyProxy

# Check the current status
nssm status CaddyProxy

# Restart the service (useful after updating config files)
nssm restart CaddyProxy

# Stop the service
nssm stop CaddyProxy
```

---

## Editing an Existing Service

You do not need to delete and recreate a service to change its parameters, environment variables, or executable path.

### Editing via GUI
Launch the configuration window for an existing service:

```powershell
nssm edit CaddyProxy
```

Make your changes across the tabs (Application, Details, Log on, I/O, Environment) and click **Edit service**.

### Editing via CLI
Use `nssm set` to modify specific properties, or `nssm get` to inspect current values:

```powershell
# View current parameters
nssm get CaddyProxy AppParameters

# Update parameters
nssm set CaddyProxy AppParameters "run --config C:\Apps\Caddy\NewCaddyfile"

# Add environment variables (Note: AppEnvironmentExtra overwrites previous extras unless specified together)
nssm set CaddyProxy AppEnvironmentExtra "NODE_ENV=production" "PORT=8080"

# Restart to apply changes
nssm restart CaddyProxy
```

---

## Disabling a Service

If you want to prevent a service from starting automatically on boot without deleting its configuration, change its 
`Start` parameter to `SERVICE_DISABLED` (or `SERVICE_DEMAND_START` for manual startup).

```powershell
# Stop the running service first
nssm stop CaddyProxy

# Disable the service completely
nssm set CaddyProxy Start SERVICE_DISABLED
```

To set it to **Manual** startup instead of completely disabled:

```powershell
nssm set CaddyProxy Start SERVICE_DEMAND_START
```

To re-enable **Automatic** startup later:

```powershell
nssm set CaddyProxy Start SERVICE_AUTO_START
nssm start CaddyProxy
```

---

## Removing a Service

When you no longer need a service, stop it and remove it cleanly from the Windows Service Control Manager.

### Interactive Removal (With Confirmation Prompt)

```powershell
nssm remove CaddyProxy
```

### Silent Removal (For Scripts & Automation)

Pass the `confirm` keyword at the end to bypass the graphical confirmation dialog:

```powershell
# Stop the service first to ensure a clean exit
nssm stop CaddyProxy

# Remove immediately without prompts
nssm remove CaddyProxy confirm
```

> **Note:** Removing a service via NSSM only unregisters the Windows Service; it does **not** delete your application 
> files, configuration, or log files on disk.