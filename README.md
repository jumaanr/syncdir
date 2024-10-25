# Testing Task 02: Systems (60% Marks)

### Problem Description

You have two files located on two different Linux servers. Initially, both files are identical, but over time, updates are made to either file independently. The requirement is to ensure that changes made to one file are automatically replicated to the other file, and this synchronization should occur in both directions—whenever either file is updated, the changes should be reflected in the other file.

---

## 1. Problem Assessment

To design a professional and reliable solution to keep the files synchronized across two servers, we need to consider several factors, including synchronization triggers, network reliability, conflict resolution, and fault tolerance.

### High-Level Assessment of Requirements and Potential Challenges

**Requirements:**

1. **Bidirectional Synchronization**  
   Any change made to File A on Server A should be reflected in File B on Server B, and vice versa. This synchronization must happen every time a file is updated.

2. **Real-Time Updates**  
   Changes should be propagated either in real-time (immediately after they happen).

3. **Conflict Resolution**  
   In case both files are updated simultaneously, there should be a strategy to resolve conflicts. For instance, either the latest update wins, or a version control system may be employed.

4. **Reliability**  
   The solution should be resilient to network failures or server downtimes. If synchronization fails due to connectivity issues, the system should retry when the connection is restored.

5. **Scalability**  
   While the current scenario involves two files on two servers, the solution should be scalable to accommodate more files or additional servers if needed in the future.

6. **Minimal Resource Usage**  
   The synchronization process should have a minimal performance impact on both servers, using appropriate strategies to minimize bandwidth and processing power (e.g., syncing only the changes rather than the entire file).

---

Before implementing a solution from scratch or reinventing the wheel, research was conducted on available tools that meet these requirements. After researching, the following tools were found to be capable of achieving these requirements:

- **rsync & inotify**  
  Refer to native man pages.

- **Unison & inotify**  
  [GitHub Repository](https://github.com/bcpierce00/unison)

- **Syncthing**  
  [Getting Started Guide](https://docs.syncthing.net/intro/getting-started.html)

---

## 2. Design Considerations

The following considerations are essential for designing a solution:

- Ease of implementation and support documentation for tools
- Bidirectional Synchronization
- Real-Time Updates
- Conflict Resolution Strategy
- Reliability
- Scalability
- Minimal Resource Usage

---

## 3. Comparison of Syncthing, Unison with inotify, and rsync with inotify for Bidirectional File Synchronization


### **1. rsync with inotify**

**Overview:**
`rsync` is a widely used utility for synchronizing files between systems, known for its efficiency in transferring only the differences between files. `inotify` is a Linux kernel subsystem that monitors file system events.

**Assessment:**

- **Bidirectional Synchronization:**
  - *Challenge:* `rsync` is inherently unidirectional. Achieving bidirectional synchronization requires setting up scripts on both servers to monitor changes and trigger `rsync` commands to the opposite server.
  - *Impact:* Increases complexity and potential for synchronization conflicts.

- **Real-Time Updates:**
  - *Implementation:* Using `inotify`, scripts can be configured to trigger `rsync` immediately upon detecting file changes.
  - *Limitation:* Potential latency between detection and synchronization, especially under heavy load.

- **Conflict Resolution:**
  - *Challenge:* Lacks built-in conflict resolution. Simultaneous edits can lead to overwriting changes.
  - *Workaround:* Requires additional scripting or manual intervention to handle conflicts.

- **Reliability:**
  - *Concern:* No inherent retry mechanism for network failures. Scripts must handle exceptions and implement retries.
  - *Impact:* Increases complexity and potential for unsynchronized states during outages.

- **Scalability:**
  - *Limitation:* Managing multiple servers and files increases script complexity exponentially.
  - *Maintenance:* Becomes cumbersome with the addition of more files or servers.

- **Minimal Resource Usage:**
  - *Advantage:* Efficient in data transfer due to delta encoding.
  - *Concern:* Continuous monitoring and frequent synchronization can increase CPU and network usage.

---

### **2. Unison with inotify**

**Overview:**
Unison is a synchronization tool designed for bidirectional file syncing. It ensures consistency between directories on different hosts.

**Assessment:**

- **Bidirectional Synchronization:**
  - *Strength:* Built-in support for bidirectional synchronization.
  - *Benefit:* Automatically detects and synchronizes changes on both servers.

- **Real-Time Updates:**
  - *Implementation:* Can be triggered using `inotify` to run Unison upon file changes.
  - *Limitation:* Not originally designed for real-time syncing; may experience slight delays.

- **Conflict Resolution:**
  - *Strength:* Provides robust conflict detection and resolution options.
  - *Customization:* Allows configuration for automatic conflict resolution or user prompts.

- **Reliability:**
  - *Strength:* Handles interrupted synchronizations gracefully.
  - *Concern:* Requires additional scripting to handle network failures and retries when using `inotify`.

- **Scalability:**
  - *Limitation:* Configuration becomes complex with multiple files or servers.
  - *Maintenance:* Each new node requires careful setup to ensure consistency.

- **Minimal Resource Usage:**
  - *Concern:* Resource-intensive when scanning large directories.
  - *Impact:* Frequent synchronizations can lead to higher CPU and disk I/O usage.

---

### **3. Syncthing**

**Overview:**
Syncthing is an open-source, decentralized synchronization tool designed for continuous, real-time file synchronization between devices.

**Assessment:**

- **Bidirectional Synchronization:**
  - *Strength:* Natively supports bidirectional synchronization.
  - *Benefit:* Automatically syncs changes in both directions without additional configuration.

- **Real-Time Updates:**
  - *Strength:* Designed for real-time syncing using built-in file system watchers.
  - *Benefit:* Changes are propagated almost immediately after they occur.

- **Conflict Resolution:**
  - *Strength:* Includes built-in conflict handling and versioning.
  - *Customization:* Users can configure conflict resolution strategies, including keeping versions of conflicting files.

- **Reliability:**
  - *Strength:* Handles network interruptions gracefully by queuing changes and retrying synchronization when the connection is restored.
  - *Security:* Uses TLS for secure communication between nodes.

- **Scalability:**
  - *Strength:* Easily scales to multiple files and servers.
  - *Benefit:* Simple configuration for additional nodes enhances flexibility.

- **Minimal Resource Usage:**
  - *Advantage:* Efficient resource utilization with delta transfers and minimal overhead.
  - *Optimization:* Designed to minimize CPU and memory usage even with large datasets.

---

### **Conclusion and Recommendation**

Considering the requirements and the assessment of each tool:

- **rsync with inotify** introduces significant complexity for bidirectional synchronization and lacks built-in conflict resolution and reliability mechanisms.
  
- **Unison with inotify** offers bidirectional synchronization and conflict resolution but is not inherently designed for real-time updates and can become resource-intensive and complex to scale.

- **Syncthing** excels in all required areas:
  - **Bidirectional Synchronization:** Out-of-the-box support without additional scripting.
  - **Real-Time Updates:** Immediate propagation of changes using efficient file system monitoring.
  - **Conflict Resolution:** Built-in mechanisms with customizable strategies.
  - **Reliability:** Robust handling of network issues with automatic retries.
  - **Scalability:** Simple addition of files and servers with minimal configuration changes.
  - **Minimal Resource Usage:** Optimized for low resource consumption even in real-time operation.

**Recommendation:**

Based on the analysis, **Syncthing** is the most suitable solution for the given requirements. It provides a professional, reliable, and efficient means to keep the files synchronized across two servers with minimal administrative overhead.

**Benefits of Choosing Syncthing:**

- **Ease of Setup and Use:** User-friendly interface and straightforward configuration.
- **Security:** Encrypted communication ensures data integrity and confidentiality.
- **Active Community and Support:** Well-documented with an active user community for assistance.

**Implementation Notes:**

- **Installation:** Available in repositories for most Linux distributions or can be installed manually.
- **Configuration:**
  - Set up Syncthing on both servers.
  - Share the specific folder containing the file to be synchronized.
  - Configure synchronization settings and conflict resolution policies as needed.
- **Monitoring and Maintenance:**
  - Minimal ongoing maintenance once configured.
  - Provides logs and a web interface for monitoring synchronization status.

---

| Tool Combination       | Bidirectional Sync | Real-Time Updates | Conflict Resolution | Reliability | Scalability | Resource Usage |
|------------------------|--------------------|-------------------|---------------------|-------------|-------------|----------------|
| **rsync & inotify**    | Limited           | Yes              | Manual              | High        | Moderate    | Moderate       |
| **Unison & inotify**   | Yes               | Limited          | Automated           | High        | High        | Moderate       |
| **Syncthing**          | Yes               | Yes              | Automated           | High        | High        | Low            |

---

## 4. Environment and Provisioning

### Testing Environment

The testing environment consists of two Ubuntu Servers running Ubuntu 22.04 LTS, connected to the same network:
Default GW: 10.0.0.1 , DNS: 8.8.8.8

| Hostname | IP Address     |
|----------|----------------|
| vm01     | 10.0.0.100/24 |
| vm02     | 10.0.0.101/24 |


## Provisioning

The following steps outline the setup and provisioning process for both servers (`vm01` and `vm02`).


### 1. Initial Configuration on Both Servers

```bash
# Update and upgrade packages
sudo apt update
sudo apt list --upgradable
sudo apt upgrade

# Install vim and set default editor
sudo apt install vim
sudo update-alternatives --config editor

# Set hostname to localhost for initial setup
sudo hostnamectl set-hostname localhost

# Configure sudo privileges for devops user
sudo visudo
# Add the following line:
# devops ALL=(ALL) NOPASSWD:ALL

# Install net-tools and reboot
sudo apt install net-tools
sudo systemctl reboot
```

### 2. Network Configuration

**For `vm01` Network Provisioning:**

```bash
# Check network connections
sudo nmcli con show

# Configure static IP for vm01
sudo nmcli connection modify "Wired connection 1" \
  ipv4.addresses 10.0.0.100/24 \
  ipv4.gateway 10.0.0.1 \
  ipv4.dns 8.8.8.8 \
  ipv4.method manual

# Enable autoconnect and apply changes
sudo nmcli connection modify "Wired connection 1" connection.autoconnect yes
sudo nmcli connection down "Wired connection 1"
sudo nmcli connection up "Wired connection 1"
```

**For `vm02` Network Provisioning:**

```bash
# Configure static IP for vm02
sudo nmcli connection modify "Wired connection 1" \
  ipv4.addresses 10.0.0.101/24 \
  ipv4.gateway 10.0.0.1 \
  ipv4.dns 8.8.8.8 \
  ipv4.method manual

# Enable autoconnect and apply changes
sudo nmcli connection modify "Wired connection 1" connection.autoconnect yes
sudo nmcli connection down "Wired connection 1"
sudo nmcli connection up "Wired connection 1"
```

### 3. Hostname Configuration

**Set Hostnames and Reboot**

On `vm01`:
```bash
sudo hostnamectl set-hostname vm01
sudo systemctl reboot
```

On `vm02`:
```bash
sudo hostnamectl set-hostname vm02
sudo systemctl reboot
```

**Update `/etc/hosts` File on Both Servers:**

```bash
echo "10.0.0.100 vm01" | sudo tee -a /etc/hosts
echo "10.0.0.101 vm02" | sudo tee -a /etc/hosts
```

### 4. Firewall Configuration

```bash
# Allow necessary services through the firewall
sudo ufw allow http
sudo ufw allow https
sudo ufw allow ssh

# Enable and verify firewall status
sudo ufw enable
sudo ufw status
sudo ufw reload
sudo ufw status verbose
```

### 5. Connectivity Check

Verify connectivity by pinging each server from the other:

```bash
ping vm01
ping vm02
```

### 6. SSH Configuration

Install and configure SSH on both servers:

```bash
# Update and install SSH server
sudo apt update
sudo apt install openssh-server
sudo systemctl status ssh
sudo systemctl start ssh
sudo systemctl enable ssh
```

**Generate and Exchange SSH Keys:**

On `vm01`:
```bash
ssh-keygen
ssh-copy-id devops@vm02
sudo systemctl reboot
```

On `vm02`:
```bash
ssh-keygen
ssh-copy-id devops@vm01
sudo systemctl reboot
```

### 7. Install Curl

```bash
sudo apt install curl
```
This completes the environment setup and provisioning. All configurations ensure network connectivity, user access, and SSH key exchange for secure communication between the two servers.

---
# Syncthing Installation and Configuration without GUI

This guide provides step-by-step instructions for installing and configuring Syncthing on two servers, `vm01` and `vm02`, without using the graphical user interface (GUI). Both servers will synchronize a directory called `/syncdir`.

## Table of Contents

1. [Introduction](#introduction)
2. [Prerequisites](#prerequisites)
3. [Installation](#installation)
4. [Running Syncthing without GUI](#running-syncthing-without-gui)
5. [Creating the Synchronization Directory](#creating-the-synchronization-directory)
6. [Configuration Steps](#configuration-steps)
   - [Add Synchronization Directory](#add-synchronization-directory)
   - [Add Device](#add-device)
   - [Initialize Synchronization](#initialize-synchronization)
7. [Obtaining Syncthing Critical Information](#obtaining-syncthing-critical-information)
   - [Device ID](#device-id)
   - [Configuration File Paths](#configuration-file-paths)
8. [Modifying Configuration Files](#modifying-configuration-files)
   - [Adding a Shared Folder](#adding-a-shared-folder)
   - [Adding a Device](#adding-a-device)
   - [Sharing a Folder with a Device](#sharing-a-folder-with-a-device)
9. [Using Syncthing CLI Commands](#using-syncthing-cli-commands)
   - [Adding Devices](#adding-devices)
   - [Adding Sync Folders to a Device](#adding-sync-folders-to-a-device)
   - [Configuring Automatic Folder Acceptance](#configuring-automatic-folder-acceptance)
10. [Running Syncthing as a Systemd Service](#running-syncthing-as-a-systemd-service)
11. [Syncthing Conflict Resolution](#syncthing-conflict-resolution)
12. [Conclusion](#conclusion)

---

# Installation and Implementation

## Prerequisites

- Two servers (`vm01` and `vm02`) running a Debian-based Linux distribution.
- User with `sudo` privileges.
- Basic understanding of editing XML configuration files.

## Installation

Install Syncthing on both `vm01` and `vm02` using the official APT repository.

1. **Install required certificates:**

   ```bash
   sudo apt-get install ca-certificates
   ```

2. **Add the release PGP keys:**

   ```bash
   sudo mkdir -p /etc/apt/keyrings
   sudo curl -L -o /etc/apt/keyrings/syncthing-archive-keyring.gpg https://syncthing.net/release-key.gpg
   ```

3. **Add the "stable" channel to your APT sources:**

   ```bash
   echo "deb [signed-by=/etc/apt/keyrings/syncthing-archive-keyring.gpg] https://apt.syncthing.net/ syncthing stable" | sudo tee /etc/apt/sources.list.d/syncthing.list
   ```

4. **Update package lists and install Syncthing:**

   ```bash
   sudo apt-get update
   sudo apt-get install syncthing
   ```

5. **Allow required firewall exceptions:**

   ```bash
   sudo ufw allow syncthing
   sudo ufw allow syncthing-gui
   sudo ufw reload
   sudo ufw status verbose
   ```

## Running Syncthing without GUI

By default, Syncthing starts a GUI accessible via a web browser at `http://127.0.0.1:8384`. Since the aim to configure Syncthing without a GUI, it can start without opening the browser.

**Run Syncthing without opening a browser:**

```bash
syncthing -no-browser -logfile=/var/log/syncthing.log &
```

This command starts Syncthing in the background, logs output to `/var/log/syncthing.log`, and prevents the automatic opening of a web browser.

## Creating the Synchronization Directory

Create a directory `/syncdir` on both `vm01` and `vm02` to be synchronized.

1. **Create the directory:**

   ```bash
   sudo mkdir -p /syncdir
   ```

2. **Change ownership to your user (replace `devops` with your username):**

   ```bash
   sudo chown devops:devops /syncdir
   ```

3. **Set permissions to allow read/write access:**

   ```bash
   sudo chmod 777 /syncdir
   ```

   > **Note:** Setting permissions to `777` allows everyone to read, write, and execute, which may not be secure. Adjust permissions according to your security requirements.

## Configuration Steps

The main steps to configure Syncthing are:

1. Add synchronization directory.
2. Add device.
3. Initialize synchronization.

### Add Synchronization Directory

We need to define the directory to be synchronized in Syncthing's configuration file.

### Add Device

Each Syncthing instance has a unique Device ID. To synchronize with another device, you need to add its Device ID to your configuration.

### Initialize Synchronization

Once the devices and directories are configured, Syncthing will begin synchronizing the specified directories between the devices.

## Obtaining Syncthing Critical Information

### Device ID

The Device ID is a unique identifier for each Syncthing instance.

**Get your Device ID:**

```bash
syncthing --device-id
```

### Configuration File Paths

Syncthing's configuration files are essential for non-GUI configuration.

**Get configuration file paths:**

```bash
syncthing -paths
```

This command outputs various paths used by Syncthing, including the location of the `config.xml` file.

## Modifying Configuration Files

To configure Syncthing without the GUI, we need to edit the `config.xml` file directly.

### Adding a Shared Folder

Add a `<folder>` block to the `config.xml` file to define the synchronization directory.

**Example `<folder>` block:**

```xml
<!-- Add this block to define the shared folder -->
<folder id="syncdir" label="syncdir" path="/syncdir" type="sendreceive" rescanIntervalS="3600" fsWatcherEnabled="true">
    <device id="DEVICE_ID_OF_PEER" introducedBy="">
        <encryptionPassword></encryptionPassword>
    </device>
    <!-- Additional settings can be included here -->
</folder>
```

#### Explanation of the `<folder>` Block

- **`id`**: A unique identifier for the folder within Syncthing.
- **`label`**: A human-readable name for the folder.
- **`path`**: The filesystem path to the folder.
- **`type`**: Synchronization type (`sendreceive`, `sendonly`, `receiveonly`).
- **`rescanIntervalS`**: Interval in seconds for rescanning the folder for changes.
- **`fsWatcherEnabled`**: Enables filesystem watching for immediate synchronization.

**Device Block within Folder:**

```xml
<device id="DEVICE_ID_OF_PEER" introducedBy="">
    <encryptionPassword></encryptionPassword>
</device>
```

- **`id`**: Device ID of the peer with whom the folder is shared.
- **`introducedBy`**: Used when devices are introduced via an introducer.

### Adding a Device

Add a `<device>` block to the `config.xml` file to define the peer device.

**Example `<device>` block:**

```xml
<!-- Add this block to define the peer device -->
<device id="DEVICE_ID_OF_PEER" name="vm02" compression="metadata" introducer="false">
    <address>dynamic</address>
    <!-- Additional settings can be included here -->
</device>
```

#### Explanation of the `<device>` Block

- **`id`**: Device ID of the peer.
- **`name`**: A human-readable name for the device.
- **`compression`**: Type of compression (`always`, `metadata`, `never`).
- **`introducer`**: If `true`, the device acts as an introducer for other devices.
- **`address`**: Connection address. `dynamic` allows Syncthing to discover the address automatically.

### Sharing a Folder with a Device

To share a folder with a device, include a `<device>` block within the `<folder>` block.

**Example:**

```xml
<!-- Folder shared between vm01 and vm02 -->
<folder id="syncdir-root" label="syncdir" path="/syncdir" type="sendreceive">
    <device id="DEVICE_ID_OF_VM01" introducedBy="">
        <encryptionPassword></encryptionPassword>
    </device>
    <device id="DEVICE_ID_OF_VM02" introducedBy="">
        <encryptionPassword></encryptionPassword>
    </device>
    <!-- Additional settings can be included here -->
</folder>

<!-- Define devices -->
<device id="DEVICE_ID_OF_VM01" name="vm01" compression="metadata" introducer="false">
    <address>dynamic</address>
</device>
<device id="DEVICE_ID_OF_VM02" name="vm02" compression="metadata" introducer="false">
    <address>dynamic</address>
</device>
```

**Explanation:**

- The `<folder>` block defines the shared directory `/syncdir`.
- The `<device>` blocks within the folder specify which devices share this folder.
- Separate `<device>` blocks outside the folder define device settings.

## Using Syncthing CLI Commands

Alternatively, you can use Syncthing's CLI to manage devices and folders.

### Adding Devices

**Add a device:**

```bash
syncthing cli config devices add --device-id DEVICE_ID_OF_PEER
```

### Adding Sync Folders to a Device

**Add a sync folder to a device:**

```bash
syncthing cli config folders FOLDER_ID devices add --device-id DEVICE_ID_OF_PEER
```

Replace `FOLDER_ID` with the ID of your folder (e.g., `syncdir`).

### Configuring Automatic Folder Acceptance

To configure a device to automatically accept folder share requests:

```bash
syncthing cli config devices DEVICE_ID auto-accept-folders set true
```

## Running Syncthing as a Systemd Service

Running Syncthing as a systemd service ensures it starts automatically on boot and restarts if it crashes.

1. **Download the Syncthing systemd service file:**

   ```bash
   sudo wget https://raw.githubusercontent.com/syncthing/syncthing/main/etc/linux-systemd/system/syncthing@.service -O /etc/systemd/system/syncthing@.service
   ```

2. **Reload systemd manager configuration:**

   ```bash
   sudo systemctl daemon-reload
   ```

3. **Enable the Syncthing service for your user (replace `devops` with your username):**

   ```bash
   sudo systemctl enable syncthing@devops.service
   ```

4. **Start the Syncthing service:**

   ```bash
   sudo systemctl start syncthing@devops.service
   ```

5. **Check the status of the Syncthing service:**

   ```bash
   sudo systemctl status syncthing@devops.service
   ```

## Syncthing Conflict Resolution

When multiple devices modify the same file simultaneously, conflicts can occur. Syncthing handles conflicts using a "last-writer-wins" strategy but also keeps conflicting copies to prevent data loss.

### How Syncthing Handles Conflicts

- **Conflict Detection:** Syncthing monitors file modifications across all synchronized devices.
- **Conflict Resolution:** If a file is modified on two devices before synchronization occurs, Syncthing identifies a conflict.
- **Conflict Files:** Syncthing preserves both versions by renaming the conflicting file with a special suffix.

**Conflict File Naming Convention:**

```
filename.sync-conflict-YYYYMMDD-HHMMSS-DEVICEID.ext
```

- `YYYYMMDD-HHMMSS`: Timestamp of the conflict.
- `DEVICEID`: Shortened Device ID of the device where the conflicting change originated.

### Resolving Conflicts Manually

1. **Identify Conflict Files:**

   - Look for files with the `.sync-conflict` suffix in your synchronized directories.

2. **Compare Versions:**

   - Use tools like `diff`, `vimdiff`, or graphical diff tools to compare the original and conflicting files.

3. **Merge Changes:**

   - Manually merge the necessary changes into a single file that incorporates updates from both versions.

4. **Replace the Original File:**

   - Save the merged file over the original file, ensuring it's named correctly.

5. **Cleanup:**

   - Delete the `.sync-conflict` files after resolving to avoid confusion.

6. **Allow Synchronization:**

   - Syncthing will detect the changes and synchronize the resolved file across all devices.

### Preventing Conflicts

- **Avoid Simultaneous Edits:**

  - Coordinate with other users to prevent editing the same file at the same time.

- **Use File Locking Mechanisms:**

  - Implement file locking if supported by the application to prevent concurrent modifications.

- **Enable Versioning:**

  - Configure Syncthing to keep historical versions of files, providing a safety net against accidental overwrites.

### Enabling Versioning in Syncthing

Enable versioning in the `<folder>` configuration to keep backups of changed or deleted files.

**Example Configuration:**

```xml
<versioning>
    <cleanupIntervalS>3600</cleanupIntervalS>
    <fsPath></fsPath>
    <fsType>staggered</fsType>
</versioning>
```

- **`fsType`**: Type of versioning strategy (e.g., `staggered`, `simple`, `trashcan`).
- **`cleanupIntervalS`**: Interval in seconds for cleaning up old versions.

**Versioning Strategies:**

- **Staggered Versioning:**

  - Keeps multiple old versions with increasing intervals between them.

- **Simple Versioning:**

  - Keeps a fixed number of old versions.

- **Trash Can Versioning:**

  - Moves deleted or replaced files to a `.stversions` directory for manual recovery.

### Configuring Versioning via CLI

**Enable Versioning:**

```bash
syncthing cli config folders FOLDER_ID versioning set --type staggered
```

**Set Cleanup Interval:**

```bash
syncthing cli config folders FOLDER_ID versioning cleanup-interval set 3600
```

## Conclusion

By following this guide, I have installed and configured Syncthing on two servers without using the GUI. You have set up synchronization for the `/syncdir` directory, added devices, and learned how to manage configurations using the command line and by editing the `config.xml` file directly.

Understanding conflict resolution in Syncthing ensures data integrity and helps prevent data loss when multiple users modify the same files. Always monitor for conflict files and resolve them promptly.

---
