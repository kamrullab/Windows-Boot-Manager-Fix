
# 🖥️ **Fix Dual Boot Windows 11 (Chinese Locale to English)** 🖥️

This repository provides a **step-by-step guide** for fixing dual boot issues with Windows 11, particularly when the **boot entries** show a **Chinese locale** (`zh-CN`) and **English locale** (`en-US`). The guide explains how to use **bcdedit** to manage and configure boot entries and locale settings, ensuring a smooth and streamlined boot process.

---

## 🚨 **Problem Overview**

When upgrading or installing Windows 11, you may encounter a **dual boot** setup, where one entry shows a **Chinese locale** (`zh-CN`), and the other shows **English locale** (`en-US`). This can be an issue if you prefer your system to always boot into English and remove any unnecessary entries.

---

## 🛠️ **Solution: Steps to Fix the Issue**

### 🔹 **1. List All Boot Entries**
To begin, list all the current boot entries on your system to review the boot configurations and their locales.

```bash
bcdedit
```

```bash
bcdedit /enum all
```

### **Output:**

```plaintext
Windows Boot Manager
--------------------
identifier              {bootmgr}
device                  partition=\Device\HarddiskVolume1
path                    \EFI\Microsoft\Boot\bootmgfw.efi
description             Windows Boot Manager
locale                  zh-CN
inherit                 {globalsettings}
isolatedcontext         Yes
default                 {current}
resumeobject            {6d9f3ddc-34db-11ee-a577-00deabca3a2d}
displayorder            {6d9f3ddd-34db-11ee-a577-00deabca3a2d}
                        {current}
toolsdisplayorder       {memdiag}
timeout                 30

Windows Boot Loader
-------------------
identifier              {6d9f3ddd-34db-11ee-a577-00deabca3a2d}
device                  partition=C:
path                    \$WINDOWS.~BT\NewOS\WINDOWS\system32\winload.efi
description             Windows 11
locale                  zh-CN
inherit                 {bootloadersettings}
restartonfailure        Yes
isolatedcontext         Yes
allowedinmemorysettings 0x15000075
osdevice                partition=C:
systemroot              \$WINDOWS.~BT\NewOS\WINDOWS
resumeobject            {6d9f3ddc-34db-11ee-a577-00deabca3a2d}
nx                      OptIn
bootmenupolicy          Standard

Windows Boot Loader
-------------------
identifier              {current}
device                  partition=C:
path                    \Windows\system32\winload.efi
description             Windows 11
locale                  en-US
inherit                 {bootloadersettings}
recoverysequence        {6d9f3dda-34db-11ee-a577-00deabca3a2d}
displaymessageoverride  Recovery
recoveryenabled         Yes
isolatedcontext         Yes
allowedinmemorysettings 0x15000075
osdevice                partition=C:
systemroot              \Windows
resumeobject            {bbff7836-355c-11ee-afe0-00deabca3a2d}
nx                      OptIn
bootmenupolicy          Legacy
```

### **What to Delete:**
- **Windows Boot Manager entry (`{bootmgr}`)** with **locale `zh-CN`**.
- **Windows Boot Loader entry (`{6d9f3ddd-34db-11ee-a577-00deabca3a2d}`)** pointing to **\$WINDOWS.~BT** (temporary OS installation).

### **What to Keep:**
- **Current Boot Loader entry (`{current}`)** pointing to **Windows 11** installation with **English (`en-US`)** locale.
- **Windows Boot Manager** entry should remain with **English (`en-US`)** locale.

---

### 🔹 **2. Change Locale for the Boot Manager**
To change the **boot manager's locale** to **English** (`en-US`):

```bash
bcdedit /set {bootmgr} locale en-US
```

- The **locale** for the **boot manager** is currently **`zh-CN`**. We want to change it to **`en-US`**.

---

### 🔹 **3. Change Locale for Windows Boot Entries**
Change the locale for the **Windows boot entries**:

```bash
bcdedit /set {6d9f3ddd-34db-11ee-a577-00deabca3a2d} locale en-US
bcdedit /set {current} locale en-US
```

- The first command changes the locale of the **temporary Windows installation** entry (with the identifier `{6d9f3ddd-34db-11ee-a577-00deabca3a2d}`) from **`zh-CN`** to **`en-US`**.
- The second command ensures that the **current boot entry** (which is the correct Windows 11 entry) remains in **English** (`en-US`).

---

### 🔹 **4. Remove Unwanted Boot Entries**
Now, we’ll **remove** the **duplicate or incorrect** boot entries, particularly the entry pointing to **\$WINDOWS.~BT** (temporary OS files).

```bash
bcdedit /delete {6d9f3ddd-34db-11ee-a577-00deabca3a2d}
```

- **DELETE** the boot entry with the identifier `{6d9f3ddd-34db-11ee-a577-00deabca3a2d}` because it points to a temporary installation (`\$WINDOWS.~BT\NewOS`).

---

### 🔹 **5. Set Boot Timeout**
To make sure you have enough time to select the correct boot entry, set a **timeout** for the boot menu:

```bash
bcdedit /timeout 10
```

- This sets a **10-second** timeout so that you can manually select which OS to boot if needed.

---

### 🔹 **6. Set Default Boot Entry**
Set the **default boot entry** to the correct one (the one pointing to **Windows 11** in English):

```bash
bcdedit /default {current}
```

- This will make the **current entry** (Windows 11 with English locale) the default boot entry.

---

### 🔹 **7. Enable Boot Menu (Optional)**
To always show the boot menu when starting Windows (helpful for dual-boot setups), enable it with:

```bash
bcdedit /set {bootmgr} displaybootmenu yes
```

---

### 🔹 **8. Verify Recovery Environment**
Ensure that the **Windows Recovery Environment** (WinRE) is enabled in case you need it for recovery tasks:

```bash
bcdedit /enum {current}
```

If **WinRE** is disabled, enable it with:

```bash
bcdedit /set {current} recoveryenabled yes
```

---

### 🔹 **9. Restart and Verify Changes**

Once you've made all the necessary changes, restart your computer to apply the new settings. Verify that:
- Your system now boots directly into **Windows 11** with **English** as the locale.
- The **boot manager** shows **English** as the default language.
- Unnecessary or duplicate boot entries have been removed.

---

## 📋 **Summary of Commands**

1. **List All Boot Entries**:
   ```bash
   bcdedit /enum all
   ```

2. **View Locale for Boot Manager and Entries**:
   ```bash
   bcdedit /enum {bootmgr}
   bcdedit /enum {current}
   bcdedit /enum {identifier-of-other-boot-entry}
   ```

3. **Change Locale for Boot Manager**:
   ```bash
   bcdedit /set {bootmgr} locale en-US
   ```

4. **Change Locale for Windows Boot Entries**:
   ```bash
   bcdedit /set {6d9f3ddd-34db-11ee-a577-00deabca3a2d} locale en-US
   bcdedit /set {current} locale en-US
   ```

5. **Delete Unwanted Boot Entries**:
   ```bash
   bcdedit /delete {6d9f3ddd-34db-11ee-a577-00deabca3a2d}  # Delete temporary Windows entry
   ```

6. **Set Boot Timeout**:
   ```bash
   bcdedit /timeout 10
   ```

7. **Set Default Boot Entry**:
   ```bash
   bcdedit /default {current}
   ```

8. **Enable Boot Menu**:
   ```bash
   bcdedit /set {bootmgr} displaybootmenu yes
   ```

9. **Enable Recovery Environment**:
   ```bash
   bcdedit /set {current} recoveryenabled yes
   ```

---

## 📝 **Conclusion**

By following these steps, you have successfully:

- Changed the **locale** to **English** (`en-US`) for both the **Boot Manager** and **Windows boot entries**.
- Removed **duplicate** or **incorrect locale** boot entries, particularly the one pointing to **\$WINDOWS.~BT**.
- Configured the **boot manager** for an English-language startup.
- Optionally configured **boot timeout** and **display boot menu** settings.

Now your system should be set up to **boot** directly into

 **Windows 11** with **English** as the default language.

---

## ⚙️ **Useful Links & Resources**

- [Microsoft Documentation: BCDEdit](https://learn.microsoft.com/en-us/windows-hardware/drivers/devtest/bcdedit)
- [Windows 11 Installation Issues](https://support.microsoft.com/en-us/windows/windows-11)

---

## 📅 **License**

This guide is **open-source** and free to use. Feel free to **contribute** by submitting **issues** or **pull requests**.


---

### Changes Made:
- **Deleted** unnecessary entries, particularly the ones pointing to **\$WINDOWS.~BT**.
- **Kept** only the required boot entries for Windows 11 with **English** locale.

---

With this README, you now have a comprehensive guide for fixing your **dual boot** issues and configuring your **boot entries** properly.

---

### Contributed by:  
[GitHub: kamrulab](https://github.com/kamrulab)  
[Facebook: elitekamrul](https://www.facebook.com/elitekamrul)

