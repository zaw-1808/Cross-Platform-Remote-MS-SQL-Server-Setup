# Cross-Platform-Remote-MS-SQL-Server-Setup
MacBook Air M4 (macOS) ပေါ်ရှိ VS Code မှတစ်ဆင့် Local Network ထဲရှိ Windows Laptop (Acer) ပေါ်တွင် Host လုပ်ထားသော MS SQL Server Express သို့ အဝေးမှ အောင်မြင်စွာ ချိတ်ဆက်ပြီး Database စီမံခန့်ခွဲမှု ပတ်ဝန်းကျင်တစ်ခုကို တည်ဆောက်ခဲ့သည်။
# ⚡ macOS (VS Code) မှ Windows Remote MS SQL Server သို့ ချိတ်ဆက်နည်း (Quick Reference)

macOS ပေါ်ရှိ VS Code မှတစ်ဆင့် Windows Server ပေါ်ရှိ MS SQL Server ထို့ အောင်မြင်စွာ ချိတ်ဆက်ခဲ့သော အဆင့်များကို တစ်နေရာတည်းတွင် စုစည်းထားသော လမ်းညွှန်ချက်ဖြစ်ပါသည်။

---

```cmd
:: =================================================================
:: PHASE 1: Windows Host Setup (Acer Laptop - cmd ကို Admin ဖြင့်ဖွင့်ပါ)
:: =================================================================

:: ၁။ Mixed Mode Authentication (SQL + Windows Auth) ကို ဖွင့်ပါ
REG ADD "HKLM\SOFTWARE\Microsoft\Microsoft SQL Server\MSSQL16.SQLEXPRESS\MSSQLServer" /v LoginMode /t REG_DWORD /d 2 /f

:: ၂။ Administrator 'sa' account ကို ဖွင့်ပြီး Password သစ် သတ်မှတ်ပါ
sqlcmd -S .\SQLEXPRESS -Q "ALTER LOGIN sa ENABLE; ALTER LOGIN sa WITH PASSWORD = 'YourStrongPassword123!';"

:: THREE။ ပြောင်းလဲမှုများ အလုပ်လုပ်ရန် SQL Server Service ကို Restart လုပ်ပါ
net stop MSSQL$SQLEXPRESS
net start MSSQL$SQLEXPRESS

:: ၄။ Windows Firewall တွင် TCP Port 1433 ကို ဝင်ရောက်ခွင့်ပြုပါ
netsh advfirewall firewall add rule name="SQL Server Port 1433" dir=in action=allow protocol=TCP localport=1433

:: ၅။ မိမိ စက်၏ IP Address ကို ကြည့်ပါ (IPv4 Address ကို မှတ်ထားပါ၊ ဥပမာ - 192.168.0.xxx)
ipconfig


:: =================================================================
:: PHASE 2: Windows ပေါ်တွင် Sample Database နှင့် Data ဖန်တီးခြင်း
:: =================================================================

:: ၁။ Test Database ဆောက်ပါ
sqlcmd -S .\SQLEXPRESS -Q "CREATE DATABASE TestStore;"

:: ၂။ Products Table ဆောက်ပါ
sqlcmd -S .\SQLEXPRESS -d TestStore -Q "CREATE TABLE Products (ID INT PRIMARY KEY IDENTITY(1,1), ItemName NVARCHAR(50), Price DECIMAL(10,2));"

:: THREE။ Data သစ်များ ထည့်သွင်းပါ
sqlcmd -S .\SQLEXPRESS -d TestStore -Q "INSERT INTO Products (ItemName, Price) VALUES ('MacBook Air M4', 1200.00), ('Acer Laptop', 800.00);"

:: ၄။ Data ရောက်မရောက် စစ်ဆေးပါ
sqlcmd -S .\SQLEXPRESS -d TestStore -Q "SELECT * FROM Products;"
