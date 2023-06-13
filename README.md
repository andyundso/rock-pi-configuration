# Rock Pi 4 configuration

This repository contains information about how to configure my Rock Pi 4 to run Pi Hole and Netdata to monitor it.

## Manual setup

Get the Ubuntu Server image from one of the community builds. I used [this release](https://github.com/radxa-build/rock-pi-4b/releases/tag/20221101-0235) at the time. I would have prefered Debian, but they all contained a GUI which I didn't want to manually remove.

Once flashed and booted, connect a monitor and keyboard. Default credentials are `rock` / `rock`.

Get the key for the Radxa apt repository. It's preconfigured on the image, but the key included in the image is outdated.

```
wget -O - https://apt.radxa.com/focal-stable/public.key | sudo apt-key add -
```

Afterward, install `openssh-server` to work from your notebook to provision the rest.

```
sudo apt update && sudo apt install openssh-server
```

Connect to the system via SSH and configure the authorised keys.

```
ssh rock@34.56.78.90
mkdir .ssh
echo "your public key" > .ssh/authorized_keys
```

Last, configure a static IP for the system. I used `sudo nmtui` for it. The user interface explains itself.

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
