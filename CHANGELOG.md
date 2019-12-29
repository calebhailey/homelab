# Changelog

All notable changes to this project will be documented in this file.

This changelog is based on [Keep a Changelog](http://keepachangelog.com/en/1.0.0/).
This project adheres to [Semantic Versioning](http://semver.org/spec/v2.0.0.html).

## NEXT UP

- [Use my YubiKey for SSH?!][next-0]
- [Configure the Raspberry Pi 4 as a PXE server?!][next-1]

[next-0]: https://github.com/drduh/YubiKey-Guide
[next-1]: https://twitter.com/calebhailey/status/1211090177499598851?s=21

## [0.0.5] - 2019-12-29 - "Untitled"

### Added

### Changed

- [#3](https://github.com/calebhailey/homelab/issues/3) Fixed Kubernetes Node
  Taint configuration; I had skipped this step in the original [installation
  guide][0.0.5-1]. The fix was really simple:

  ```
  $ kubectl taint nodes --all node-role.kubernetes.io/master-
  node/homelab untainted
  ```

[0.0.5-1]:  https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/#control-plane-node-isolation

## [0.0.4] - 2019-12-28 - "The Beautiful Snowflake"

The home lab is alive! I can `tmux` from the iPad via `mosh`! :100:

### Added

- Installed a few missing utilities, including `mosh`, `iptables-persistent`,
  `telnet`, and `netcat`.

- Installed the Docker APT repositories and Docker CE packages, following
  [this guide][0.0.4-1]

  ```
  $ sudo apt-get remove docker docker-engine docker.io containerd runc
  $ sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg2 \
    software-properties-common
  $ curl -fsSL https://download.docker.com/linux/debian/gpg | sudo apt-key add
  $ sudo apt-key fingerprint 0EBFCD88
  $ sudo add-apt-repository \
    "deb [arch=amd64] https://download.docker.com/linux/debian \
    $(lsb_release -cs) \
    stable"
  $ sudo apt-get update
  $ sudo apt-get install docker-ce docker-ce-cli containerd.io
  ```

- Installed Kubernetes! 

  Followed these guides to install Kubernetes using `kubeadm`: 
  
  1. [Installing `kubeadm`][0.0.4-2]
  2. [Creating a single control-plane cluster with `kubeadm`][0.0.4-3]
  
  In the end it was pretty simple to setup! 

  ```
  $ sudo apt-get update && sudo apt-get install -y apt-transport-https curl
  $ curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
  $ cat <<EOF | sudo tee /etc/apt/sources.list.d/kubernetes.list
    deb https://apt.kubernetes.io/ kubernetes-xenial main
    EOF
  $ sudo apt-get update
  $ sudo apt-get install -y kubelet kubeadm kubectl
  $ sudo apt-mark hold kubelet kubeadm kubectl
  $ kubeadm config images pull
  $ sudo swapoff -a 
  $ sudo kubeadm init
  $ sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  $ sudo chown ${USER}:${USER} $HOME/.kube/config 
  $ chmod 644 $HOME/.kube/config 
  ```

- Installed a Kubernetes network plugin ([Flannel][0.0.4-4])

  ```
  $ kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
  ```

- I [live tweeted][0.0.4-5] about my progress! 

- [A Raspberry Pi 4][0.0.4-6]! :nerd_face:

### Changed 

- Configured some iptables rules! 

  ```
  $ sudo iptables -S 
  -P INPUT ACCEPT
  -P FORWARD ACCEPT
  -P OUTPUT ACCEPT  
  -A INPUT -i lo -j ACCEPT
  -A INPUT -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT
  -A INPUT -p tcp -m tcp --dport 22 -j ACCEPT
  -A INPUT -p udp -m udp --dport 60000:61000 -j ACCEPT
  
  $ sudo iptables -S 
  -P INPUT ACCEPT
  -P FORWARD ACCEPT
  -P OUTPUT ACCEPT  
  -A INPUT -i lo -j ACCEPT
  -A INPUT -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT
  -A INPUT -p tcp -m tcp --dport 22 -j ACCEPT
  -A INPUT -p udp -m udp --dport 60000:61000 -j ACCEPT  
  ```
  
  _TIL: you can run `dpkg-reconfigure iptables-persistent` to save firewall rules._
  
- Disabled system swap (as recommended by `kubeadm`)

  ```
  $ sudo swapoff -a
  ```
  
  I also edited `/etc/fstab` and commented out the swap partition to prevent it
  from be mounted after a reboot. 
  
[0.0.4-1]: https://docs.docker.com/install/linux/docker-ce/debian/
[0.0.4-2]: https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/
[0.0.4-3]: https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/
[0.0.4-4]: https://github.com/coreos/flannel
[0.0.4-5]: https://twitter.com/calebhailey/status/1211006296100524032?s=21 
[0.0.4-6]: https://twitter.com/calebhailey/status/1211090177499598851?s=21


## [0.0.3] - 2019-12-26 - "The Detour"

### Added

- Installed Debian Buster! Not to be dismayed by my not-so-smart-os woes, I
  decided to take the ~~less interesting~~ _basic_ route and just install linux.
  I grew up on Ubuntu Linux, but I've grown to prefer an even less opinionated
  Linux, so I grabbed the latest Debian image, flashed my USB drive, and got up
  and running in minutes.

- I chose not to install a desktop environment to keep things lean, and I also
  opted not to install any of the package bundles (e.g. what does "basic system
  utilities") even mean?

- Installed `ssh`, `sudo`, `vim`, `curl`, `tmux`, `git`, `htop`, and the drivers
  for my WiFi card (`firmware-iwlwifi`). Keeping things lightweight!

- Added my user to the sudoers `usermod -aG sudo calebhailey`

- Added [`/scripts/ddns`][0.0.3-1] for easy remote access via a Google Domains
  hosted domain name ([documentation][0.0.3-2]).

- Synced my [dotfiles][0.0.3-3].

### Changed

- Modified `/etc/ssh/sshd_config` to disable password authentication and PAM.

- Modified the root user crontab to run the `ddns` script every 5 minutes:

  ```
  */5 * * * *     /usr/local/bin/ddns >> /var/log/ddns.log 2>&1
  ```

  Works like a charm! I get a nice clean log output like:

  ```
  $ $ tail -f /var/log/ddns.log
  Thu 26 Dec 2019 11:55:01 PM PST nochg 123.123.123.123
  Fri 27 Dec 2019 12:00:02 AM PST nochg 123.123.123.123
  ```

  Now I can easily pull up a log to see how often my ISP is changing my IP
  address. Things only a :nerd_face: would be excited about.

- Modified my user's `$HOME/.ssh/authorized_keys` so I can SSH.

  ```
  $ curl -s https://github.com/calebhailey.keys > $HOME/.ssh/authorized_keys
  ```

  Perhaps I should hook this up as a cron job too?

[0.0.3-1]: /blob/master/scripts/ddns
[0.0.3-2]: https://support.google.com/domains/answer/6147083?hl=en
[0.0.3-3]: https://github.com/calebhailey/dotfiles

## [0.0.2] - 2019-12-25 - "Reality Bites"

Well, that was short lived. The SmartOS installer hangs immediately after
loading the kernel modules. Some intense Googling led me to some GitHub issues
about similar issues on NUC7 series systems, which were eventually fixed; I
attempted some of the troubleshooting steps from those GitHub issues without
success. Elsewhere (Reddit maybe?) I found comments suggesting that NUC7 owners
were having success (thanks to the above mentioned fixes) where NUC8 owners were
not. So perhaps my NUC (model number NUC8i7BEH) isn't supported by SmartOS
(yet)... so abort mission?

### Added

- Installed SmartOS following the [Quick Start Guide][0.0.2-2]

- [Live tweeted][0.0.2-3], like a nerd :nerd_face:

- Added Raspberry Pi 4 to the mix! Some of my Googling suggested that the
  installer hanging was an ACPI issue, so the USB ports would go dead. So maybe
  a PXE boot would work? Is that a good enough excuse to order a Raspberry Pi 4?
  Sure. Why not. The Raspberry Pi should arrive in a few days, and shortly
  thereafter is shall become a PXE server... and whatever else I need to init my
  home lab? Yay.

### Changed

- [#1](https://github.com/calebhailey/homelab/issues/1) Fixed image
  authorization failure ([disabled smart boot in the Intel NUC BIOS][0.0.2-1])
- Tried a few dozen different

[0.0.2-1]: https://www.intel.com/content/www/us/en/support/articles/000038401/intel-nuc/intel-nuc-kits.html
[0.0.2-2]: https://wiki.smartos.org/smartos-quick-start-guide/
[0.0.2-3]: https://twitter.com/calebhailey/status/1209961062788780033

## [0.0.1] - 2019-12-25 - "The Innocent Naivety"

### Added

- Started a README(.md)
- Started this CHANGELOG(.md) to document my journey
- Added a `.gitignore` file to ignore my local `downloads/` directory
- Built a SmartOS Bootable USB Key following [this guide][0.0.1-1]

  Downloaded the [latest SmartOS][0.0.1-2] tarball... ran some commands I found
  on the internet (what's the worst that could happen???):

  ```
  $ diskutil list
  /dev/disk0 (internal, physical):
     #:                       TYPE NAME                    SIZE       IDENTIFIER
     0:      GUID_partition_scheme                        *1.0 TB     disk0
     1:                        EFI EFI                     314.6 MB   disk0s1
     2:                 Apple_APFS Container disk1         1.0 TB     disk0s2

  /dev/disk1 (synthesized):
     #:                       TYPE NAME                    SIZE       IDENTIFIER
     0:      APFS Container Scheme -                      +1.0 TB     disk1
                                   Physical Store disk0s2
     1:                APFS Volume Macintosh HD - Data     241.7 GB   disk1s1
     2:                APFS Volume Preboot                 125.5 MB   disk1s2
     3:                APFS Volume Recovery                1.6 GB     disk1s3
     4:                APFS Volume VM                      3.2 GB     disk1s4
     5:                APFS Volume Macintosh HD            11.0 GB    disk1s5

  /dev/disk2 (external, physical):
     #:                       TYPE NAME                    SIZE       IDENTIFIER
     0:     FDisk_partition_scheme                        *8.0 GB     disk2
     1:             Windows_FAT_32 NO NAME                 8.0 GB     disk2s1

  $ diskutil unmountDisk /dev/disk2
  Unmount of all volumes on disk2 was successful
  $ gunzip smartos-20191219T093118Z-USB.img.gz
  $ ls
  smartos-20191219T093118Z-USB.img
  $ sudo dd bs=1m if=smartos-20191219T093118Z-USB.img of=/dev/rdisk2
  1907+1 records in
  1907+1 records out
  2000000000 bytes transferred in 157.314476 secs (12713388 bytes/sec)
  $ diskutil eject /dev/disk2
  Disk /dev/disk2 ejected
  ```

  Success?!

- Next: Stick the USB key in a port and boot from it!


[0.0.1-1]: https://wiki.smartos.org/creating-a-smartos-bootable-usb-key/
[0.0.1-2]: https://us-east.manta.joyent.com/Joyent_Dev/public/SmartOS/latest.html

## [0.0.0-2] - 2019-12-25 – "The Prequel"

- No longer able to run all-the-containers on my laptop, I suddenly remembered
  that I have an unused computer on the shelf (reaches for the Intel NUC).

- Installed some DDR4 RAM and NVMe storage in an Intel NUC, turned it on...

  ```
  A bootable device has not been detected.
  ```

  Womp womp.

- Started Googling...

- Quickly determined that it would be helpful to document this journey.

  ```
  $ mkdir homelab && cd homelab && git init
  ```

## [0.0.0-1] – 2019-??-?? – "Failure to Launch"

- Unboxed an Intel NUC, realized it required assembly, set it on a shelf to
  collect dust.
