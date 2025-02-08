# Rock Pi 4 configuration

This repository contains information about how to configure my Rock Pi 4 to run Pi Hole and Netdata to monitor it.

## Manual setup

Get an Armbian minimal image. I used [this release](https://github.com/armbian/community/releases/download/25.5.0-trunk.4/Armbian_community_25.5.0-trunk.4_Rockpi-4bplus_bookworm_current_6.12.12_minimal.img.xz) at the time.

Once flashed and booted, connect it to a network cable. The device will fetch an IP using DHCP, so you might have to scan your network in order to find the IP:

```
nmap -sP 192.168.1.*
```

Once you found the device, you can login using SSH with username root and password `1234`.

The device will then ask you to customize the root password. Choose whatever seems fit to you. You do not have to create a separate user account.

Last, configure a static IP for the system. Run `armbian-config`, go to `Network`, choose `Basic Network Setup` and then customize the IP setup for eth0. Then, remove the "fallback DHCP" configuration in order to activate the static IP address.

## Ansible setup

Install the required roles and collections:

```
ansible-galaxy install -r requirements.yml
```

Configure an `hosts` file that looks something like this:

```yaml
all:
  hosts:
    rock:
      ansible_host: 1.2.3.4
      ansible_user: myrockuser
      ansible_become_method: sudo
      ansible_become_pass: myrockpassword
      pihole_api_password: mypiholepassword
      pihole_web_password: mypiholepassword
```

The `pihole_api_password` can only be retrieved after the initial provisioning. You can find it in the Pihole GUI under `Settings` / `API`. For the initial run, just enter something random and update the value afterward. Then run the playbook a second time.

Run the playbook:

```
ansible-playbook -i hosts setup-pi-hole.yml
```

## What has been done?

* Pihole is up-and-running, its dashboard is accessible at `http://pihole.home.arpa/admin/`.
* Netdata is up-and-running, its dashboard is accessible at `http://netdata.home.arpa/`
