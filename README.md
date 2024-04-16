# Home Assistant Supervised installation for i386


###### on Atom N270 Netbook MSI Wind U100

[![hassio_badge](https://img.shields.io/badge/home%20assistant-%2341BDF5.svg?&style=for-the-badge&logo=home%20assistant&logoColor=white)](https://www.home-assistant.io/)

[![hass_inst_badge](https://img.shields.io/badge/HomeAssistant-Installer-blue.svg)](https://www.home-assistant.io/)

![Supports i386 Architecture](https://img.shields.io/badge/i386-yes-green.svg)
** **

---

### 1. Installing Debian 12

** **

**Disk partitioning:**

_Automatic, separate partitions for /, /var, /tmp, /home_

** **

### 2. First console login (as root)

** **

**Advanced system setup**

We need to install `sudo` and `openssh-server`:
```
apt update
apt upgrade
apt install sudo acpi-support vbetool openssh-server 
```
---

**Tweaks**

For user system folders to be named in English ("Desktop" but not "Рабочий стол") we need to execute re-creating by deleting shell command for every user:
```
LC_ALL=C xdg-user-dirs-update --force
```
_But this command is to be executed only after GUI (Gnome, KDE, Xfce и др.) being installed, something we are not going to do in this manual._

---

**🌜 For our laptop not to go sleep after closing display shell (lid):**

```
curl -sL "https://github.com/xvrfr/homeassistant/raw/main/files/system/logind.conf" > /etc/systemd/logind.conf
```
<h6><details><summary>Alternative file installation method via <code>echo</code> command
</summary>

```
echo "# /etc/systemd/logind.conf" > /etc/systemd/logind.conf
echo "[Login]" >> /etc/systemd/logind.conf
echo "HandleLidSwitch=ignore" >> /etc/systemd/logind.conf
echo "HandleLidSwitchDocked=ignore" >> /etc/systemd/logind.conf
echo "LidSwitchIgnoreInhibited=no" >> /etc/systemd/logind.conf
```
</details></h6>

---

**🔅 For our laptop to turn off display LED after closing display shell (lid):**

```
curl -sL "https://github.com/xvrfr/homeassistant/raw/main/files/system/lid-button" > /etc/acpi/events/lid-button
```

Next step:

```
touch /etc/acpi/lid.sh
chmod +x /etc/acpi/lid.sh
```
```
curl -sL "https://github.com/xvrfr/homeassistant/raw/main/files/system/lid.sh" > /etc/acpi/lid.sh
```
<h6><details><summary>Alternative file installation method via <code>echo</code> command
</summary>

```
echo "event=button/lid.*" > /etc/acpi/events/lid-button
echo "action=/etc/acpi/lid.sh" >> /etc/acpi/events/lid-button
```
```
touch /etc/acpi/lid.sh
chmod +x /etc/acpi/lid.sh
```
```
echo '#!/bin/bash' >  /etc/acpi/lid.sh
echo "" >> /etc/acpi/lid.sh
echo "grep -q close /proc/acpi/button/lid/*/state" >> /etc/acpi/lid.sh
echo "" >> /etc/acpi/lid.sh
echo "if [ $? = 0 ]; then" >> /etc/acpi/lid.sh
echo "    sleep 0.2" >> /etc/acpi/lid.sh
echo "echo \"vbetool dpms off\"" >> /etc/acpi/lid.sh
echo "fi" >> /etc/acpi/lid.sh
echo "" >> /etc/acpi/lid.sh
echo "grep -q open /proc/acpi/button/lid/*/state" >> /etc/acpi/lid.sh
echo "" >> /etc/acpi/lid.sh
echo "if [ $? = 0 ]; then" >> /etc/acpi/lid.sh
echo "    vbetool dpms on" >> /etc/acpi/lid.sh
echo "fi" >> /etc/acpi/lid.sh
```
To check file creation (optional) use command:
```
nano /etc/acpi/lid.sh
```
</details></h6>

** **
### 3. Install OS Agent, Docker and Dependencies
** **
You will now install the OS Agent for Home Assistant. It is used for Home Assistant OS and Home Assistant Supervised installation types and it allows the Home Assistant Supervisor to communicate with the host operating system.

2.1) In terminal (or connected to your machine via SSH using Putty - see Step 3.3 for info), run the following commands to update the Debian OS, install Docker and the required dependencies for the OS Agent and the Supervised installer. Execute the following commands one at a time.

```
sudo -i

apt update && sudo apt upgrade -y && sudo apt autoremove -y

apt --fix-broken install

apt install apparmor jq wget curl udisks2 libglib2.0-bin network-manager dbus lsb-release systemd-journal-remote systemd-resolved -y

sudo apt-get install docker.io

```
**Getting dependencies:**
```
sudo apt install apparmor-utils apt-transport-https avahi-daemon ca-certificates curl dbus jq network-manager socat bash 
```
```
apt install \
apparmor \
cifs-utils \
curl \
dbus \
jq \
libglib2.0-bin \
lsb-release \
network-manager \
nfs-common \
systemd-journal-remote \
systemd-resolved \
udisks2 \
wget -y

```

Disabling ModemManager
```
systemctl disable ModemManager 
```
```
systemctl stop ModemManager 
```
**Installing Docker:**
```
sudo apt install -y docker.io
```
**Installing HomeAssistant OS Agent:**
```
wget https://github.com/home-assistant/os-agent/releases/download/1.6.0/os-agent_1.6.0_linux_i386.deb

dpkg -i os-agent_1.6.0_linux_i386.deb
```

**And finally! Execute modified Supervised installation:**
```
wget -O homeassistant-supervised.deb https://github.com/oleg-antoniuk/HA-i386/releases/download/HA/homeassistant-supervised.deb
apt install ./homeassistant-supervised.deb
```
### TROUBLESHOOTING

-Not Privileged-

Ignore the unhealthy check by using: " ha jobs options --ignore-conditions healthy " in the terminal
    Reboot
    Update as usual.
    Reset the settings: " ha jobs reset " and " ha supervisor restart. " in the terminal.
