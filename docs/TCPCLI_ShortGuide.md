# TCP CLI Testing Guide (Shortened Version)

## Prerequisites
- Manga Hub built and ready (`.\bin\tcp-server.exe` and `.\bin\mangahub.exe`)
- 3 separate PowerShell terminals

---

## Setup (One-Time)

### Add binaries to PATH (Optional but Recommended)
In any terminal:
```powershell
$env:PATH = "<your-project-path>\bin;$env:PATH"
```
Example:
```powershell
$env:PATH = "D:\GitHub\Manga-Hub-Group13\bin;$env:PATH"
```

Now you can use `mangahub` instead of `.\bin\mangahub.exe`

### Login with Authentication
```powershell
mangahub auth login --username YourUsername
```
You'll be prompted to enter your password.

**Expected output:**
```
✓ Login successful
Token saved to config
```

⚠️ **Important:** You must be logged in before connecting to TCP sync!

---

## Quick Start (3 Steps)

### Step 1: Start TCP Server
**Terminal 1** - Run and leave open:
```powershell
.\bin\tcp-server.exe
```
Expected output: `tcp_server_ready port:9090`

---

### Step 2: Connect Client
**Terminal 2** - Run and **keep this terminal open**:
```powershell
.\bin\mangahub.exe sync connect --device-type desktop --device-name "YourName"
```
Or if you set PATH:
```powershell
mangahub sync connect --device-type desktop --device-name "YourName"
```

**What you'll see:**
```
✓ Connected successfully!
Session ID: sess_yourname_desktop_10112025T214359_a7f3
```

⚠️ **Important:** Don't close this terminal - it maintains your connection!

**Optional Flags:**
- `--device-type` → `mobile`, `desktop`, or `web` (default: `desktop`)
- `--device-name` → Friendly device name (default: your hostname)

---

### Step 3: Check Connection Status
**Terminal 3** - Run anytime:
```powershell
.\bin\mangahub.exe sync status
```
Or with PATH:
```powershell
mangahub sync status
```

**Expected output:**
```
TCP Sync Status:
  Connection: ✓ Active
  Server: localhost:9090
  Session ID: sess_yourname_desktop_10112025T214359_a7f3
  Network Quality: Good
```

---

## Commands Summary

| Command | Description | Example |
|---------|-------------|---------|
| `mangahub auth login --username <name>` | Login (required before sync) | `mangahub auth login --username johndoe` |
| `.\bin\tcp-server.exe` | Start TCP sync server | Run in Terminal 1 |
| `mangahub sync connect` | Connect to server | `mangahub sync connect --device-name "MyLaptop"` |
| `mangahub sync status` | Check connection status with live stats | Shows messages, devices, RTT |
| `mangahub sync monitor` | Watch real-time sync events | See updates as they happen |
| `mangahub sync disconnect` | Disconnect gracefully | Or press Ctrl+C in connect terminal |
| `Ctrl+C` (in Terminal 2) | Disconnect gracefully | In the sync connect terminal |

---

## New Features ✨

### Live Status with Server Query
`sync status` now queries the TCP server for real-time data:
```powershell
mangahub sync status
```

**What you'll see:**
```
TCP Sync Status:

  Connection: ✓ Active
  Server: localhost:9090
  Uptime: 1h 23m 45s
  Last heartbeat: 3 seconds ago

Session Info:
  User: johndoe
  Session ID: sess_mylaptop_desktop_12112025T150000_a1b2
  Devices online: 2

Sync Statistics:
  Messages sent: 15
  Messages received: 8
  Last sync: 45 seconds ago (One Piece ch. 1095)
  Sync conflicts: 0

Network Quality: Excellent (RTT: 12ms)
```

### Real-Time Monitoring
Watch sync events as they happen across all your devices:
```powershell
mangahub sync monitor
```

**What you'll see:**
```
✓ Subscribed to real-time updates
Monitoring sync events... (Press Ctrl+C to exit)

[15:23:45] ← updated: Jujutsu Kaisen (Chapter 248)
[15:24:12] → updated: One Piece (Chapter 1095)
[15:24:50] ← updated: Demon Slayer (Chapter 157)

Stopping monitor...
✓ Monitoring stopped
```

**Legend:**
- `←` = Update from another device
- `→` = Update from this device
- `[HH:MM:SS]` = Timestamp

---

## Troubleshooting

**Problem:** "Not logged in" error  
**Solution:** Run `mangahub auth login --username YourUsername` first

**Problem:** Connection shows "✗ Inactive"  
**Solution:** Make sure Terminal 2 (sync connect) is still running

**Problem:** Cannot connect to server  
**Solution:** Check if Terminal 1 (tcp-server) is running on port 9090

**Problem:** Session ID looks weird  
**Solution:** Make sure you rebuilt binaries after latest code changes:
```powershell
go build -o bin/tcp-server.exe cmd/tcp-server/main.go
go build -o bin/mangahub.exe cmd/main.go
```

**Problem:** "mangahub: command not found"  
**Solution:** Either set PATH or use `.\bin\mangahub.exe` instead

---

## Example Session IDs

- `sess_thuannm_desktop_10112025T214359_b7bb`
- `sess_johns_laptop_mobile_10112025T215530_a3f2`

---

## What Happens Behind the Scenes

1. **Authentication** - JWT token generated and stored in config
2. **TCP Server** listens on port 9090
3. **Client connects** and authenticates with JWT token
4. **Session created** with unique ID
5. **Heartbeats sent** every 30 seconds to keep connection alive
6. **Connection state saved** to `.mangahub/sync_state.yaml`
7. **Status command** reads the state file from another process

---

## Next Steps

- Test progress synchronization
- Test multi-device sync
- Monitor real-time updates: `mangahub sync monitor`

For detailed testing guide, see `TCPCLI_FullGuide.md`