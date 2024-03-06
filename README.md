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
### 3. Installing HomeAssistant Supervised
** **
Originally:

https://sprut.ai/article/ustanovka-home-assistant-na-netbuki-i-starye-pk
```
sudo apt install software-properties-common python3.9 python3.9-dev python3.9-venv python3-pip libffi-dev libssl-dev
sudo apt autoremove -y 
export PATH=$PATH:/usr/sbin
```
**Getting dependencies:**
```
sudo apt install apparmor-utils apt-transport-https avahi-daemon ca-certificates curl dbus jq network-manager socat bash 
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
sudo apt install wget unzip udisks2
wget https://github.com/xvrfr/homeassistant/raw/main/os-agent_1.6.0_linux_i386.deb
sudo dpkg -i os-agent_1.6.0_linux_i386.deb
```

**And finally! Execute modified Supervised installation script:**
```
wget https://github.com/xvrfr/homeassistant/raw/main/supervised-installer.fixed.sh
chmod 777 supervised-installer.fixed.sh
sudo /home/nu100/supervised-installer.fixed.sh
```
If the script completes without errors, there will be web address to connect to HomeAssistant.
** **
**Pay attention that the first start will take about 20 minutes, be patient, please.**
** **
Many thanks to Kanga-who for the initial script.
The rest parts found:
```
URL_HA="https://github.com/home-assistant/cli/releases/download/4.15.1/ha_i386"    
URL_BIN_HASSIO="https://raw.githubusercontent.com/remlabm/hassio-installer/master/files/hassio-supervisor"
URL_BIN_APPARMOR="https://raw.githubusercontent.com/remlabm/hassio-installer/master/files/hassio-apparmor"
URL_SERVICE_HASSIO="https://raw.githubusercontent.com/remlabm/hassio-installer/master/files/hassio-supervisor.service"
URL_SERVICE_APPARMOR="https://raw.githubusercontent.com/remlabm/hassio-installer/master/files/hassio-apparmor.service"
```
