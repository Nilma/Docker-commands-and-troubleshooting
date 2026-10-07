# Docker Troubleshooting Guide

> This guide helps troubleshoot Docker Desktop problems on Windows related to the **Hypervisor**, **Windows virtualization features**, and the **`docker-users`** group.

---

## 1. Force the Hypervisor to Start at Boot

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

## 2. Enable the Required Windows Features

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

## 3. Create the `docker-users` Group

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

## 4. Check the `docker-users` Group

You can check which Windows accounts are members of the `docker-users` group using PowerShell.

Open PowerShell as Administrator and run:

```text
Get-LocalGroupMember -Group "docker-users"
```

Look through the output and check whether your Windows user is listed.

---

## 5. Restart After Making Changes

After changing Windows virtualization settings or adding your account to the `docker-users` group, restart Windows before testing Docker Desktop again.

A useful troubleshooting sequence is:

```text
Change Windows settings
        ↓
Restart Windows
        ↓
Start Docker Desktop
        ↓
Test Docker
```

---

## 6. Quick Troubleshooting Checklist

If Docker Desktop is not starting, work through these checks in order:

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
