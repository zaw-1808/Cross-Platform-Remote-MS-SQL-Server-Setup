<div align="center">

![Header](https://capsule-render.vercel.app/api?type=waving&color=gradient&customColorList=6,11,20&height=200&section=header&text=Cross-Platform%20Remote%20SQL%20Setup&fontSize=34&fontColor=ffffff&animation=fadeIn&fontAlignY=35)

<img src="https://readme-typing-svg.demolab.com?font=Fira+Code&size=18&pause=1000&color=2AA9E0&center=true&vCenter=true&width=650&lines=MacBook+(VS+Code)+%E2%86%94+Wi-Fi+%E2%86%94+Windows+(SQL+Server);Two+Machines%2C+One+Database%2C+Zero+Extra+Apps" alt="Typing SVG" />

![Windows](https://img.shields.io/badge/Windows-0078D6?style=for-the-badge&logo=windows&logoColor=white)
![macOS](https://img.shields.io/badge/macOS-000000?style=for-the-badge&logo=apple&logoColor=white)
![SQL Server](https://img.shields.io/badge/SQL_Server-CC2927?style=for-the-badge&logo=microsoftsqlserver&logoColor=white)
![VS Code](https://img.shields.io/badge/VS_Code-007ACC?style=for-the-badge&logo=visualstudiocode&logoColor=white)

*A step-by-step guide to connect a Mac to a Windows SQL Server over the same Wi-Fi.*

![Dr. Zaw](https://img.shields.io/badge/Dr.%20Zaw-MBBS%2C%20MPH-ff69b4?style=for-the-badge&logo=stethoscope&logoColor=white)
![Journey](https://img.shields.io/badge/🩺→💻-Medicine%20to%20Dev%20(WIP)-6a5acd?style=for-the-badge)

</div>

---

## 🏗️ The 4 Components

| # | Tool | Role | Machine |
|---|------|------|---------|
| 1️⃣ | **MS SQL Server 2022 Express** | 🗄️ Database Engine — stores data as `.mdf` | 💻 Acer (Windows) |
| 2️⃣ | **SSMS** | 👀 Visual Tool — browse/edit tables on Acer | 💻 Acer (Windows) |
| 3️⃣ | **VS Code + SQL Extension** | ⌨️ Dev Tool — write code & send data from Mac | 🍎 MacBook Air M4 |
| 4️⃣ | **Wi-Fi + Port 1433** | 🌉 Bridge — connects the two machines | 🌐 Network |

### 🔄 Data Flow
```
🍎 MacBook (VS Code) → 🌐 Wi-Fi / Port 1433 → 💻 Acer (SQL Server .mdf) → 👀 View via SSMS or VS Code
```

---

## ✅ Prerequisites

- [ ] SQL Server 2022 Express installed on **Acer**
- [ ] TCP/IP enabled + **Port 1433** open in Firewall
- [ ] SQL Authentication (Mixed Mode) enabled
- [ ] SSMS installed on **Acer**
- [ ] VS Code + **mssql** extension installed on **MacBook**
- [ ] Both devices on the **same Wi-Fi**
- [ ] Acer's local IP noted (e.g. `192.168.x.x`)

---

## 🖥️ Phase 1 — Set Up Acer (Windows)

<details>
<summary><b>Step 1 — Static IP</b></summary>

1. Run `ipconfig` in Command Prompt → note **IPv4 Address** & **Default Gateway**
2. `Settings` → `Network & Internet` → `Wi-Fi` → `Hardware properties` → **Edit** IP assignment
3. Set to **Manual**, then fill in:

| Field | Value |
|---|---|
| IP address | `192.168.1.100` *(unused number in your range)* |
| Subnet prefix length | `24` |
| Gateway | `192.168.1.1` |
| Preferred DNS | `8.8.8.8` |

</details>

<details>
<summary><b>Step 2 — Install SQL Server & open TCP/IP</b></summary>

1. Install **SQL Server 2022 Express**
2. Open **SQL Server Configuration Manager** → `SQL Server Network Configuration` → `Protocols for SQLEXPRESS`
3. Enable **TCP/IP** → open its properties → **IP Addresses** tab
4. Under **IPAll**: clear *TCP Dynamic Ports*, set *TCP Port* = `1433`
5. Restart **SQL Server (SQLEXPRESS)** service

</details>

<details>
<summary><b>Step 3 — Enable <code>sa</code> login</b></summary>

Run as **Administrator** in Command Prompt, one line at a time:

```sql
sqlcmd -S .\SQLEXPRESS -Q "ALTER LOGIN sa WITH PASSWORD = 'MyPassword123!'; ALTER LOGIN sa ENABLE;"
```
```sql
sqlcmd -S .\SQLEXPRESS -Q "EXEC xp_instance_regwrite N'HKEY_LOCAL_MACHINE', N'Software\Microsoft\MSSQLServer\MSSQLServer', N'LoginMode', REG_DWORD, 2;"
```
```dos
net stop MSSQL$SQLEXPRESS && net start MSSQL$SQLEXPRESS
```

</details>

<details>
<summary><b>Step 4 — Open Firewall Port</b></summary>

```dos
netsh advfirewall firewall add rule name="SQL Server Port 1433" dir=in action=allow protocol=TCP localport=1433
```

</details>

---

## 🍎 Phase 2 — Set Up MacBook

- [ ] Install **VS Code**
- [ ] Install **SQL Server (mssql)** extension (`Cmd+Shift+X`)
- [ ] *(Optional)* Azure Data Studio / DBeaver for a visual browser

---

## 🔗 Phase 3 — Connect Mac → Acer

| # | Field | Value |
|---|---|---|
| 1 | Server | `192.168.1.100,1433` |
| 2 | Database | leave blank |
| 3 | Auth type | SQL Login |
| 4 | Username | `sa` |
| 5 | Password | your Phase 1 password |
| 6 | Trust server certificate | True |
| 7 | Profile name | e.g. `Acer-SQL-Server` |

**✅ Success check:** a 🟢 green dot next to your profile in VS Code's CONNECTIONS panel.

---

<div align="center">

### 🛠️ Quick Reference

| Item | Value |
|---|---|
| Server IP | `192.168.1.100` |
| Port | `1433` |
| Login | `sa` |
| Auth mode | SQL + Windows Authentication |

---

![Dr. Zaw](https://img.shields.io/badge/Dr.%20Zaw-MBBS%2C%20MPH-ff69b4?logo=stethoscope&logoColor=white)
![Journey](https://img.shields.io/badge/🩺→💻-Medicine%20to%20Dev-6a5acd)

![Footer](https://capsule-render.vercel.app/api?type=waving&color=gradient&customColorList=6,11,20&height=120&section=footer)

</div>
