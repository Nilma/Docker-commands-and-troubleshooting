# Docker Troubleshooting Guide For Windows

> This guide helps troubleshoot Docker Desktop problems on Windows related to **hardware virtualization**, the **Hypervisor**, **Windows virtualization features**, and the **`docker-users`** group.

---

## 1. Check Virtualization in BIOS/UEFI

Docker Desktop relies on hardware virtualization. If virtualization is disabled in your computer's BIOS/UEFI settings, Docker may not be able to start correctly.

### Step 1: Check Whether Virtualization Is Already Enabled

Before changing anything in BIOS/UEFI, you can check the current status in Windows.

Open **Task Manager** with:

```text
Ctrl + Shift + Esc
```

Select:

**Performance → CPU**

Look for:

> **Virtualization: Enabled**

If it already says **Enabled**, you can continue to the next section.

If it says **Disabled**, virtualization needs to be enabled in BIOS/UEFI.

### Step 2: Enter BIOS/UEFI

Restart your computer and enter the BIOS/UEFI setup.

The key used to enter BIOS/UEFI depends on the computer manufacturer. Common keys include:

- **Delete**
- **F1**
- **F2**
- **F10**
- **F12**
- **Esc**

You may need to press the key repeatedly immediately after turning on the computer.

> **Tip:** If you are unsure which key to use, check your computer or motherboard manufacturer's instructions.

### Step 3: Enable CPU Virtualization

Look through the BIOS/UEFI settings for a virtualization option.

The name depends on your CPU.

**Intel systems** may use names such as:

- **Intel Virtualization Technology**
- **Intel VT-x**
- **Virtualization Technology**

**AMD systems** may use names such as:

- **SVM Mode**
- **AMD-V**

Set the virtualization option to:

> **Enabled**

### Step 4: Save and Restart

Save your BIOS/UEFI changes and restart the computer.

Once Windows has started, open **Task Manager → Performance → CPU** again and check that:

> **Virtualization: Enabled**

You can then continue with the next section.

---

## 2. Force the Hypervisor to Start at Boot

If Docker Desktop reports that the **Hypervisor launch type may be disabled**, Windows may not be configured to start the Hypervisor automatically.

### Step 1: Open an Administrator Terminal

Open **Terminal (Admin)** or **PowerShell (Admin)**.

### Step 2: Enable the Hypervisor

Run the following command:

```text
bcdedit /set hypervisorlaunchtype auto
```

### Step 3: Restart Windows

Restart your computer after running the command.

> **Important:** The Hypervisor setting will not take effect until Windows has been restarted.

---

## 3. Enable the Required Windows Features

Docker Desktop requires several Windows virtualization features to be enabled.

### Step 1: Open Windows Features

Press:

```text
Win + R
```

Then type:

```text
optionalfeatures
```

and press **Enter**.

### Step 2: Enable the Following Features

Make sure these features are checked:

- **Hyper-V**
  - Hyper-V Management Tools
  - Hyper-V Platform
- **Virtual Machine Platform**
- **Windows Hypervisor Platform**
- **Windows Subsystem for Linux**

### Step 3: Apply the Changes

Click **OK** and then restart the computer.

### Step 4: Switch Docker's Virtual Machine Manager

Open **Docker Desktop** and switch to:

> **Docker VMM**

Then restart your computer.

---

## 4. Create the `docker-users` Group

Docker Desktop may require your Windows account to belong to the **`docker-users`** group.

### Step 1: Open PowerShell as Administrator

Search for **PowerShell**, right-click it, and select:

> **Run as administrator**

### Step 2: Create the Group

Run:

```text
net localgroup docker-users /add
```

This creates the `docker-users` group if it does not already exist.

### Step 3: Add Your Windows User

Open PowerShell as Administrator and run:

```text
net localgroup docker-users [USERNAME] /add
```

Replace `[USERNAME]` with your Windows username.

For example:

```text
net localgroup docker-users Mark /add
```

> **Note:** The username in the command should be your actual Windows account name.

---

## 5. Check the `docker-users` Group

You can check which Windows accounts are members of the `docker-users` group using PowerShell.

Open PowerShell as Administrator and run:

```text
Get-LocalGroupMember -Group "docker-users"
```

Look through the output and check whether your Windows user is listed.

---

## 6. Restart After Making Changes

After changing BIOS/UEFI settings, Windows virtualization settings, or adding your account to the `docker-users` group, restart Windows before testing Docker Desktop again.

A useful troubleshooting sequence is:

```text
Check BIOS/UEFI virtualization
        ↓
Enable Windows virtualization features
        ↓
Configure the Hypervisor
        ↓
Configure docker-users
        ↓
Restart Windows
        ↓
Start Docker Desktop
        ↓
Test Docker
```

---

## 7. Quick Troubleshooting Checklist

If Docker Desktop is not starting, work through these checks in order:

- [ ] Hardware virtualization is enabled in BIOS/UEFI
- [ ] Hypervisor is configured to start automatically
- [ ] Windows has been restarted
- [ ] **Hyper-V** is enabled
- [ ] **Virtual Machine Platform** is enabled
- [ ] **Windows Hypervisor Platform** is enabled
- [ ] **Windows Subsystem for Linux** is enabled
- [ ] Docker Desktop is using **Docker VMM**
- [ ] Your Windows account belongs to **`docker-users`**
- [ ] Windows has been restarted after changing group membership

If all of these are configured correctly, try starting Docker Desktop again.
