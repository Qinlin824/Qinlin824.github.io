## Changing software sources (mirrors) is a fundamental skill for any Linux user, especially when dealing with restricted or slow international bandwidth. Since different distributions use different package managers, the process varies. Here is a comprehensive guide to switching to Chinese mirrors (such as TUNA, Alibaba, or Huawei Cloud) for the most common Linux families.

## 1. Debian / Ubuntu Family
These distributions use the `apt` package manager. Configuration is primarily stored in `/etc/apt/sources.list`.

### **Step-by-Step Instructions**

1.  **Backup your current config:**
    ```bash
    sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak
    ```

2.  **Edit the file:**
    ```bash
    sudo nano /etc/apt/sources.list
    ```

3.  **Replace the content:**
    Delete the existing lines and replace them with a mirror endpoint. For example, for **Ubuntu 22.04 (Jammy)**:
    ```text
    deb [https://mirrors.tuna.tsinghua.edu.cn/ubuntu/](https://mirrors.tuna.tsinghua.edu.cn/ubuntu/) jammy main restricted universe multiverse
    deb [https://mirrors.tuna.tsinghua.edu.cn/ubuntu/](https://mirrors.tuna.tsinghua.edu.cn/ubuntu/) jammy-updates main restricted universe multiverse
    deb [https://mirrors.tuna.tsinghua.edu.cn/ubuntu/](https://mirrors.tuna.tsinghua.edu.cn/ubuntu/) jammy-backports main restricted universe multiverse
    deb [https://mirrors.tuna.tsinghua.edu.cn/ubuntu/](https://mirrors.tuna.tsinghua.edu.cn/ubuntu/) jammy-security main restricted universe multiverse
    ```

4.  **Update the database:**
    ```bash
    sudo apt update
    ```

> **Note for Ubuntu 24.04+:** Modern versions use the DEB822 format located at `/etc/apt/sources.list.d/ubuntu.sources`. You should modify the `URIs` field within that specific file.

---

## 2. Arch Linux
Arch Linux uses `pacman`. Mirror priority is determined by the order of entries in `/etc/pacman.d/mirrorlist`.

### **Manual Configuration**

1.  **Edit the mirrorlist:**
    ```bash
    sudo nano /etc/pacman.d/mirrorlist
    ```

2.  **Add the mirror to the top:**
    The first working mirror in the list is the one used. Add this at the very top:
    ```text
    Server = [https://mirrors.tuna.tsinghua.edu.cn/archlinux/$repo/os/$arch](https://mirrors.tuna.tsinghua.edu.cn/archlinux/$repo/os/$arch)
    ```

3.  **Sync system:**
    ```bash
    sudo pacman -Syu
    ```

---

## 3. Red Hat / Fedora / CentOS / AlmaLinux
These systems use `dnf` or `yum`. Configuration files are located in `/etc/yum.repos.d/`.

### **The "Clean Slate" Method**

1.  **Backup and remove old repos:**
    ```bash
    sudo mkdir /etc/yum.repos.d/bak
    sudo mv /etc/yum.repos.d/*.repo /etc/yum.repos.d/bak/
    ```

2.  **Download the new repo file (Example for CentOS Stream 9):**
    ```bash
    sudo curl -o /etc/yum.repos.d/centos.repo [https://mirrors.aliyun.com/repo/Centos-9.repo](https://mirrors.aliyun.com/repo/Centos-9.repo)
    ```

3.  **Refresh cache:**
    ```bash
    sudo dnf clean all
    sudo dnf makecache
    ```

---

## 4. Alpine Linux
Commonly used in Docker containers and lightweight SBC (Single Board Computer) environments like the Raspberry Pi.

1.  **Modify the repositories file:**
    ```bash
    sudo sed -i 's/dl-cdn.alpinelinux.org/mirrors.tuna.tsinghua.edu.cn/g' /etc/apk/repositories
    ```

2.  **Update:**
    ```bash
    apk update
    ```

---

## 5. SBC Specifics (Raspberry Pi)
If you are running Raspberry Pi OS, the mirror URL is different from standard Debian. Use:
`http://mirrors.tuna.tsinghua.edu.cn/raspberrypi/`

---

## 6. Quick Reference: Top Chinese Mirrors

| Provider | URL | Best For |
| :--- | :--- | :--- |
| **Tsinghua (TUNA)** | `https://mirrors.tuna.tsinghua.edu.cn` | Academic & General |
| **Alibaba Cloud** | `https://mirrors.aliyun.com` | Enterprise & Stability |
| **USTC** | `https://mirrors.ustc.edu.cn` | Speed & Reliability |

---

## Technical Analysis & Tips

### **1. Permissions**
Since you are modifying system files in `/etc`, you must use `sudo`. Without it, you will receive a "Permission Denied" error when trying to save.

### **2. HTTPS Support**
Some minimal base images (especially Debian/Ubuntu "slim" versions) don't support HTTPS out of the box. Install the necessary packages first:
```bash
sudo apt install apt-transport-https ca-certificates