# Docker-VHDX-
```markdown
# 🧹 How to Shrink WSL & Docker VHDX File Size (Clean Up Disk Space)

If you're using **WSL (Windows Subsystem for Linux)** and **Docker Desktop** on Windows,
your VHDX (virtual disk) files can grow significantly over time.
This guide shows you how to safely compact those files and reclaim disk space.

---

## 1. 🔻 Stop WSL and Docker

First, shut down WSL and make sure Docker Desktop is not running.

Open **PowerShell** or **Command Prompt** as **Administrator** and run:

```powershell
wsl --shutdown
```

If Docker Desktop is running, you can exit it manually from the system tray icon, or run this command to kill it forcefully:

```powershell
taskkill /f /im "Docker Desktop.exe"
```

---

## 2. 📁 Find the Path to the VHDX File

The VHDX file stores the virtual disk used by WSL and Docker. It can get large over time as you use containers or Linux applications.

For Docker Desktop, the default location is usually:

```
C:\Users\<USERNAME>\AppData\Local\Docker\wsl\main\ext4.vhdx
```

> 🔁 Replace `<USERNAME>` with your actual Windows username.

To list your installed WSL distributions and see which ones you may want to clean:

```bash
wsl --list --verbose
```

---

## 3. 🛠 Shrink the VHDX File Using DiskPart

Now we’ll use **DiskPart**, a native Windows tool, to reclaim unused space in the VHDX file.

> ⚠️ Make sure WSL and Docker are fully shut down before you begin.

### ✅ Step-by-step Instructions

1. Open **PowerShell** or **Command Prompt** as Administrator.
2. Launch DiskPart:

```powershell
diskpart
```

3. Inside DiskPart, run the following commands **one at a time**:

```powershell
select vdisk file="C:\Users\<USERNAME>\AppData\Local\Docker\wsl\main\ext4.vhdx"
attach vdisk readonly
compact vdisk
detach vdisk
exit
```

> 🔒 `attach vdisk readonly` mounts the disk in read-only mode to avoid any changes.  
> 🧽 `compact vdisk` frees up unused space within the VHDX file.  
> ✅ `detach vdisk` unmounts the disk safely.

---

## ✅ Notes & Warnings

- This will **not delete any of your containers, files, or distros**.
- Ensure both **WSL and Docker Desktop are stopped**. Otherwise, the VHDX file will be locked.
- If you're managing other WSL distros manually, find their VHDX files via their install paths and adjust the file path accordingly.
- This reduces the **file size on disk**, but does **not reduce used space inside the virtual filesystem**.

---

## 📌 Bonus Tip (Optional Automation)

If you need to do this regularly, you can create a PowerShell script:

```powershell
wsl --shutdown
taskkill /f /im "Docker Desktop.exe"

Start-Sleep -Seconds 5

diskpart /s shrink-vhdx.txt
```

Where `shrink-vhdx.txt` contains:

```
select vdisk file="C:\Users\<USERNAME>\AppData\Local\Docker\wsl\main\ext4.vhdx"
attach vdisk readonly
compact vdisk
detach vdisk
exit
```

> 💡 You can modify the script to target other distros or custom paths as needed.

---

## 🛑 Final Reminder

- Always double-check the VHDX path before running DiskPart.
- Don’t try this on active disks or while WSL/Docker is running.
- Use `readonly` mode to avoid damaging data.
- No need to reboot after this process.

---

## 📂 Resources

- [Microsoft Docs - DiskPart](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/diskpart)
- [Docker Desktop WSL Integration](https://docs.docker.com/desktop/wsl/)
- [WSL Disk Cleanup Guide (Unofficial)](https://stackoverflow.com/questions/63687052/how-to-shrink-the-size-of-a-wsl2-virtual-disk)

---

Feel free to fork, adapt, or automate this to fit your workflow!
```
