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
