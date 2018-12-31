# Linux Installation Checklist

This is based on a pay for hire base build ansible configuration script I built, but
I cannot include the ansible scripts here as I do not own the copyright to them.

Everything here assumes Ubuntu/Debian. Some files may be in different places on different systems.

# Passwords

## Password Complexity.

- Set password rules in `/etc/pam.d/common-password`.
- A line like this will require a password to be at least 10 characters long, one lowercase character, one uppercase character and one digit. 
- `password requisite pam_cracklib.so minlength=10 lcredit=1 ucredit=1 dcredit=1`

## Password Expiry

- Set password expiration in /etc/login.defs
- Look for `PASS_MAX_DAYS` and `PASS_WARN_AGE`

## SSH versions and no password logins

- Make sure only `Protocol 2` is enabled in `/etc/ssh/sshd_config`
- Make sure `PasswordAuthentication no` is in `/etc/ssh/sshd_config`

## Sudo

- Make sure the appropriate users are configured in sudoers with `visudo`
- You should do this by putting the users in a group, and enabled a group in sudoers.

# Boot Loader / Drive Encryption

If there are people who may have physical access to the machine that you do not trust, you will need to secure the bootloader, using a guide like https://selivan.github.io/2017/12/21/grub2-password-for-all-but-default-menu-entries.html

There is no point in securiring the bootloader if you are worried about people removing a disk and stealing the contents. Follow https://help.ubuntu.com/community/ManualFullSystemEncryption, but be aware that you need to enter the unencryption password EVERY time the system boots. This has obvious tradeoffs with security.

## Time

Make sure ntp is installed, and configured to three time sources. Using `0.uk.pool.ntp.org, 1.uk.pool.ntp.org, 2.uk.pool.ntp.org` will be fine for the vast majority of purposes, or use your own NTP servers if you have them. Be aware if this is a virtual machine, you may not want to do this on a guest, as it should, if correctly configured, take time from the hosts and you dont want a fight between NTP and the Hypervisor.

## Virtualisation Tools

You should install the guest tools for whatever hypervisor you are using, assuming you are using one. For example, `apt-get install open-vm-tools` for both VMware guests.

## Remote syslog.

You should configure `/etc/rsyslog.conf` to send logs you want to the remote host. If you want to ship all logs, use

`*.* @remote_hostname:514` to ship logs via UDP, or `*.* @@remote_hostname:514` to ship logs via TCP

# Modern Python Tools

If you want modern Python you should install the latest version of Python3. You can install a Python3 PPA from a trusted source with this. At time of writing 3.7 is the most modern version, but obviously this may change.

```
sudo add-apt-repository ppa:jonathonf/python-3.7
sudo apt-get update
sudo apt-get install python3.7 python3-pip
sudo pip3 install pip --upgrade
``` 

Note after doing this, python3 the binary will still be the system python3, which we don't want to change in case we break OS tools. If you want to use python3.7, you should call it directly (or have lines like #!/usr/bin/env python3.7 on your scripts)

You have wish to symlink python3-latest to this binary, or you may not, depending on your upgrades rituals.