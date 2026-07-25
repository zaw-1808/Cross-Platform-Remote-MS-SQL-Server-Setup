# Cross-Platform-Remote-MS-SQL-Server-Setup
MacBook (macOS) ပေါ်ရှိ VS Code မှတစ်ဆင့် Local Network ထဲရှိ Windows Laptop ပေါ်တွင် Host လုပ်ထားသော MS SQL Server Express သို့ အဝေးမှ အောင်မြင်စွာ ချိတ်ဆက်ပြီး Database စီမံခန့်ခွဲမှု ပတ်ဝန်းကျင်တစ်ခုကို တည်ဆောက်ခဲ့သည်။

 💻 macOS (VS Code) ──── 📡 Wi-Fi ──── 🖥️ Windows (MS SQL)
<div align="center">

# 🚀 Cross-Platform Remote MS SQL Server Setup


![Windows](https://img.shields.io/badge/Windows-0078D6?style=for-the-badge&logo=windows&logoColor=white)
![macOS](https://img.shields.io/badge/macOS-000000?style=for-the-badge&logo=apple&logoColor=white)
![SQL Server](https://img.shields.io/badge/SQL_Server-CC2927?style=for-the-badge&logo=microsoftsqlserver&logoColor=white)
![VS Code](https://img.shields.io/badge/VS_Code-007ACC?style=for-the-badge&logo=visualstudiocode&logoColor=white)

*A step-by-step guide to connect a Mac to a Windows SQL Server over the same Wi-Fi network.*

</div>

---

## 📋 What You'll Do

```
┌─────────────────────┐         Wi-Fi          ┌──────────────────────┐
│                     │ ─────────────────────▶ │    MacBook    
│   Windows           │      Port 1433         │   (VS Code)           
│  SQL Server host    │ ◀───────────────────── │    Client               
└─────────────────────┘                        └──────────────────────┘
```

| Phase | What happens | Where |
|:---:|---|:---:|
| 1️⃣ | Set up the Windows laptop as the database server | 🖥️ Window |
| 2️⃣ | Install tools on the Mac | 🍎 MacBook |
| 3️⃣ | Connect the two | 🔗 Both |

> 💡 **Tip:** Do the phases in order. Each one builds on the last.

---

## 🖥️ Phase 1 — Set Up the Acer Laptop (Windows)

<details>
<summary><b>Step 1 — Give the laptop a fixed (static) IP address</b></summary>

A static IP keeps your laptop's address from changing, so the Mac can always find it.

**1.1 Find your router's IP pattern**
- Open Command Prompt (`cmd`)
- Type:
  ```
  ipconfig
  ```
- Write down your **IPv4 Address** (e.g. `192.168.1.15`) and **Default Gateway** (e.g. `192.168.1.1`)

**1.2 Open Manual IP settings**
- Go to `Settings` → `Network & internet` → `Wi-Fi` → `Hardware properties`
- Next to **IP assignment**, click **Edit**

**1.3 Fill in the static IP details**

Turn on **Manual (IPv4: ON)**, then enter:

| Field | Value |
|---|---|
| IP address | `192.168.1.100` *(pick an unused number that fits your pattern)* |
| Subnet prefix length | `24` *(or Subnet Mask `255.255.255.0`)* |
| Gateway | `192.168.1.1` |
| Preferred DNS | `8.8.8.8` |
| Secondary DNS | `8.8.4.4` |

Click **Save**. ✅

</details>

<details>
<summary><b>Step 2 — Install SQL Server Express & open the TCP/IP port</b></summary>

1. Download **SQL Server 2022 Express Edition** from Microsoft's site and install with the basic setup.
2. Open **SQL Server Configuration Manager** (search for it in the Start Menu).
3. Go to `SQL Server Network Configuration` → `Protocols for SQLEXPRESS`.
4. Right-click **TCP/IP** → **Enable**.
5. Double-click **TCP/IP** → open the **IP Addresses** tab:
   - Scroll to the bottom and find **IPAll**
   - Clear (empty) the **TCP Dynamic Ports** field
   - Set **TCP Port** to `1433`
   - Click **OK**
6. Go to `SQL Server Services` on the left, right-click **SQL Server (SQLEXPRESS)**, and choose **Restart**.

</details>

<details>
<summary><b>Step 3 — Unlock the admin (<code>sa</code>) account & enable SQL Authentication</b></summary>

Open Command Prompt **as Administrator** (right-click → *Run as administrator*), then run these **one at a time**:

**① Set the `sa` password and turn the account on:**
```sql
sqlcmd -S .\SQLEXPRESS -Q "ALTER LOGIN sa WITH PASSWORD = 'MyPassword123!'; ALTER LOGIN sa ENABLE;"
```
> Replace `MyPassword123!` with your own password.

**② Switch to SQL + Windows Authentication mode:**
```sql
sqlcmd -S .\SQLEXPRESS -Q "EXEC xp_instance_regwrite N'HKEY_LOCAL_MACHINE', N'Software\Microsoft\MSSQLServer\MSSQLServer', N'LoginMode', REG_DWORD, 2;"
```

**③ Restart the SQL Server service:**
```dos
net stop MSSQL$SQLEXPRESS && net start MSSQL$SQLEXPRESS
```

</details>

<details>
<summary><b>Step 4 — Allow Port 1433 through Windows Firewall</b></summary>

So the Mac isn't blocked when it connects, run this in an **Admin** Command Prompt:

```dos
netsh advfirewall firewall add rule name="SQL Server Port 1433" dir=in action=allow protocol=TCP localport=1433
```

</details>

---

## 🍎 Phase 2 — Set Up the MacBook Air M4

- [ ] Download and install **VS Code**, with the **C# Dev Kit** extension
- [ ] Open Extensions (`Cmd + Shift + X`) and install the **SQL Server (mssql)** extension
- [ ] *(Optional)* Want a visual database browser? Install **Azure Data Studio** or **DBeaver** (Mac version) — both are free

---

## 🔗 Phase 3 — Connect the Mac to the Windows SQL Server

| # | Action | Enter |
|:---:|---|---|
| 1 | In VS Code, click the **SQL Server icon** in the sidebar, then **+** next to CONNECTIONS | — |
| 2 | Type the server address | `192.168.1.100,1433` |
| 3 | Database name | leave blank, press **Enter** |
| 4 | Authentication type | **SQL Login** |
| 5 | Username | `sa` |
| 5 | Password | the password you set in Phase 1 · Step 3 |
| 6 | Save Password | **Yes** |
| 6 | Trust server certificate | **True** |
| 7 | Profile name | anything you like, e.g. `Acer-SQL-Server` |

---

## ✅ How to Know It Worked

Look under **CONNECTIONS** in VS Code — a 🟢 **green dot** next to your profile name means you're connected!

Click it to start browsing and using the databases on your Windows server. 🎉

---

<div align="center">

### 🛠️ Quick Reference

| Item | Value |
|---|---|
| Server IP | `192.168.1.100` |
| Port | `1433` |
| Login | `sa` |
| Auth mode | SQL Server + Windows Authentication |

**Made for multi-device dev setups — Windows 🖥️ + Mac 🍎, working together.**

</div>

