ubuntu@vnic-oberfritz:~$ client_loop: send disconnect: Connection reset
PS C:\Users\39000024>  ssh -i "C:\Users\39000024\.ssh\oracle-oberfritz.key" ubuntu@64.181.185.194
Welcome to Ubuntu 22.04.5 LTS (GNU/Linux 6.8.0-1058-oracle aarch64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

 * Strictly confined Kubernetes makes edge and IoT secure. Learn how MicroK8s
   just raised the bar for easy, resilient and secure K8s cluster deployment.

   https://ubuntu.com/engage/secure-kubernetes-at-the-edge

This system has been minimized by removing packages and content that are
not required on a system that users do not log into.

To restore this content, you can run the 'unminimize' command.

Expanded Security Maintenance for Applications is not enabled.

0 updates can be applied immediately.

Enable ESM Apps to receive additional future security updates.
See https://ubuntu.com/esm or run: sudo pro status

Last login: Mon Aug  3 12:57:17 2026 from 187.17.237.77
ubuntu@vnic-oberfritz:~$ which docker || echo "docker não instalado"
docker não instalado
ubuntu@vnic-oberfritz:~$ for pkg in docker.io docker-doc docker-compose podman-docker containerd runc; do sudo apt-get remove -y $pkg; done
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
Package 'docker.io' is not installed, so not removed
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
Package 'docker-doc' is not installed, so not removed
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
Package 'docker-compose' is not installed, so not removed
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
Package 'podman-docker' is not installed, so not removed
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
Package 'containerd' is not installed, so not removed
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
Package 'runc' is not installed, so not removed
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
ubuntu@vnic-oberfritz:~$ sudo apt-get update
Hit:1 http://ports.ubuntu.com/ubuntu-ports jammy-security InRelease
Hit:2 http://sa-saopaulo-1-ad-1.clouds.ports.ubuntu.com/ubuntu-ports jammy InRelease
Hit:3 http://sa-saopaulo-1-ad-1.clouds.ports.ubuntu.com/ubuntu-ports jammy-updates InRelease
Hit:4 http://sa-saopaulo-1-ad-1.clouds.ports.ubuntu.com/ubuntu-ports jammy-backports InRelease
Reading package lists... Done
ubuntu@vnic-oberfritz:~$ sudo apt-get install -y ca-certificates curl gnupg
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
ca-certificates is already the newest version (20260601~22.04.1).
ca-certificates set to manually installed.
curl is already the newest version (7.81.0-1ubuntu1.25).
gnupg is already the newest version (2.2.27-3ubuntu2.5).
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
ubuntu@vnic-oberfritz:~$ sudo install -m 0755 -d /etc/apt/keyrings
ubuntu@vnic-oberfritz:~$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
ubuntu@vnic-oberfritz:~$ sudo chmod a+r /etc/apt/keyrings/docker.gpg
ubuntu@vnic-oberfritz:~$ echo \
    "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
    $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
    sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
ubuntu@vnic-oberfritz:~$ sudo apt-get update
Get:1 https://download.docker.com/linux/ubuntu jammy InRelease [48.5 kB]
Get:2 https://download.docker.com/linux/ubuntu jammy/stable arm64 Packages [83.7 kB]
Hit:3 http://sa-saopaulo-1-ad-1.clouds.ports.ubuntu.com/ubuntu-ports jammy InRelease
Hit:4 http://sa-saopaulo-1-ad-1.clouds.ports.ubuntu.com/ubuntu-ports jammy-updates InRelease
Hit:5 http://ports.ubuntu.com/ubuntu-ports jammy-security InRelease
Hit:6 http://sa-saopaulo-1-ad-1.clouds.ports.ubuntu.com/ubuntu-ports jammy-backports InRelease
Fetched 132 kB in 1s (220 kB/s)
Reading package lists... Done
ubuntu@vnic-oberfritz:~$ sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following additional packages will be installed:
  docker-ce-rootless-extras git git-man less liberror-perl libjansson4 libnftables1 nftables pigz
Suggested packages:
  cgroupfs-mount | cgroup-lite docker-model-plugin git-daemon-run | git-daemon-sysvinit git-doc git-email git-gui gitk gitweb git-cvs git-mediawiki
  git-svn firewalld
The following NEW packages will be installed:
  containerd.io docker-buildx-plugin docker-ce docker-ce-cli docker-ce-rootless-extras docker-compose-plugin git git-man less liberror-perl libjansson4
  libnftables1 nftables pigz
0 upgraded, 14 newly installed, 0 to remove and 0 not upgraded.
Need to get 91.6 MB of archives.
After this operation, 383 MB of additional disk space will be used.
Get:1 https://download.docker.com/linux/ubuntu jammy/stable arm64 containerd.io arm64 2.2.6-1~ubuntu.22.04~jammy [20.2 MB]
Get:2 https://download.docker.com/linux/ubuntu jammy/stable arm64 docker-ce-cli arm64 5:29.7.1-1~ubuntu.22.04~jammy [15.3 MB]
Get:3 http://sa-saopaulo-1-ad-1.clouds.ports.ubuntu.com/ubuntu-ports jammy/main arm64 libjansson4 arm64 2.13.1-1.1build3 [33.6 kB]
Get:4 https://download.docker.com/linux/ubuntu jammy/stable arm64 docker-ce arm64 5:29.7.1-1~ubuntu.22.04~jammy [20.5 MB]
Get:5 https://download.docker.com/linux/ubuntu jammy/stable arm64 docker-buildx-plugin arm64 0.36.0-1~ubuntu.22.04~jammy [14.7 MB]
Get:6 https://download.docker.com/linux/ubuntu jammy/stable arm64 docker-ce-rootless-extras arm64 5:29.7.1-1~ubuntu.22.04~jammy [9118 kB]
Get:7 https://download.docker.com/linux/ubuntu jammy/stable arm64 docker-compose-plugin arm64 5.3.1-1~ubuntu.22.04~jammy [6901 kB]
Get:8 http://sa-saopaulo-1-ad-1.clouds.ports.ubuntu.com/ubuntu-ports jammy-updates/main arm64 libnftables1 arm64 1.0.2-1ubuntu3.1 [323 kB]
Get:9 http://sa-saopaulo-1-ad-1.clouds.ports.ubuntu.com/ubuntu-ports jammy-updates/main arm64 nftables arm64 1.0.2-1ubuntu3.1 [67.4 kB]
Get:10 http://sa-saopaulo-1-ad-1.clouds.ports.ubuntu.com/ubuntu-ports jammy/universe arm64 pigz arm64 2.6-1 [55.7 kB]
Get:11 http://sa-saopaulo-1-ad-1.clouds.ports.ubuntu.com/ubuntu-ports jammy-updates/main arm64 less arm64 590-1ubuntu0.22.04.3 [141 kB]
Get:12 http://sa-saopaulo-1-ad-1.clouds.ports.ubuntu.com/ubuntu-ports jammy/main arm64 liberror-perl all 0.17029-1 [26.5 kB]
Get:13 http://sa-saopaulo-1-ad-1.clouds.ports.ubuntu.com/ubuntu-ports jammy-updates/main arm64 git-man all 1:2.34.1-1ubuntu1.17 [954 kB]
Get:14 http://sa-saopaulo-1-ad-1.clouds.ports.ubuntu.com/ubuntu-ports jammy-updates/main arm64 git arm64 1:2.34.1-1ubuntu1.17 [3222 kB]
Fetched 91.6 MB in 2s (36.7 MB/s)
debconf: delaying package configuration, since apt-utils is not installed
Selecting previously unselected package containerd.io.
(Reading database ... 116122 files and directories currently installed.)
Preparing to unpack .../00-containerd.io_2.2.6-1~ubuntu.22.04~jammy_arm64.deb ...
Unpacking containerd.io (2.2.6-1~ubuntu.22.04~jammy) ...
Selecting previously unselected package docker-ce-cli.
Preparing to unpack .../01-docker-ce-cli_5%3a29.7.1-1~ubuntu.22.04~jammy_arm64.deb ...
Unpacking docker-ce-cli (5:29.7.1-1~ubuntu.22.04~jammy) ...
Selecting previously unselected package libjansson4:arm64.
Preparing to unpack .../02-libjansson4_2.13.1-1.1build3_arm64.deb ...
Unpacking libjansson4:arm64 (2.13.1-1.1build3) ...
Selecting previously unselected package libnftables1:arm64.
Preparing to unpack .../03-libnftables1_1.0.2-1ubuntu3.1_arm64.deb ...
Unpacking libnftables1:arm64 (1.0.2-1ubuntu3.1) ...
Selecting previously unselected package nftables.
Preparing to unpack .../04-nftables_1.0.2-1ubuntu3.1_arm64.deb ...
Unpacking nftables (1.0.2-1ubuntu3.1) ...
Selecting previously unselected package docker-ce.
Preparing to unpack .../05-docker-ce_5%3a29.7.1-1~ubuntu.22.04~jammy_arm64.deb ...
Unpacking docker-ce (5:29.7.1-1~ubuntu.22.04~jammy) ...
Selecting previously unselected package pigz.
Preparing to unpack .../06-pigz_2.6-1_arm64.deb ...
Unpacking pigz (2.6-1) ...
Selecting previously unselected package less.
Preparing to unpack .../07-less_590-1ubuntu0.22.04.3_arm64.deb ...
Unpacking less (590-1ubuntu0.22.04.3) ...
Selecting previously unselected package docker-buildx-plugin.
Preparing to unpack .../08-docker-buildx-plugin_0.36.0-1~ubuntu.22.04~jammy_arm64.deb ...
Unpacking docker-buildx-plugin (0.36.0-1~ubuntu.22.04~jammy) ...
Selecting previously unselected package docker-ce-rootless-extras.
Preparing to unpack .../09-docker-ce-rootless-extras_5%3a29.7.1-1~ubuntu.22.04~jammy_arm64.deb ...
Unpacking docker-ce-rootless-extras (5:29.7.1-1~ubuntu.22.04~jammy) ...
Selecting previously unselected package docker-compose-plugin.
Preparing to unpack .../10-docker-compose-plugin_5.3.1-1~ubuntu.22.04~jammy_arm64.deb ...
Unpacking docker-compose-plugin (5.3.1-1~ubuntu.22.04~jammy) ...
Selecting previously unselected package liberror-perl.
Preparing to unpack .../11-liberror-perl_0.17029-1_all.deb ...
Unpacking liberror-perl (0.17029-1) ...
Selecting previously unselected package git-man.
Preparing to unpack .../12-git-man_1%3a2.34.1-1ubuntu1.17_all.deb ...
Unpacking git-man (1:2.34.1-1ubuntu1.17) ...
Selecting previously unselected package git.
Preparing to unpack .../13-git_1%3a2.34.1-1ubuntu1.17_arm64.deb ...
Unpacking git (1:2.34.1-1ubuntu1.17) ...
Setting up less (590-1ubuntu0.22.04.3) ...
Setting up libjansson4:arm64 (2.13.1-1.1build3) ...
Setting up liberror-perl (0.17029-1) ...
Setting up docker-buildx-plugin (0.36.0-1~ubuntu.22.04~jammy) ...
Setting up containerd.io (2.2.6-1~ubuntu.22.04~jammy) ...
Created symlink /etc/systemd/system/multi-user.target.wants/containerd.service → /lib/systemd/system/containerd.service.
Setting up docker-compose-plugin (5.3.1-1~ubuntu.22.04~jammy) ...
Setting up docker-ce-cli (5:29.7.1-1~ubuntu.22.04~jammy) ...
Setting up pigz (2.6-1) ...
Setting up git-man (1:2.34.1-1ubuntu1.17) ...
Setting up docker-ce-rootless-extras (5:29.7.1-1~ubuntu.22.04~jammy) ...
Setting up libnftables1:arm64 (1.0.2-1ubuntu3.1) ...
Setting up nftables (1.0.2-1ubuntu3.1) ...
Setting up docker-ce (5:29.7.1-1~ubuntu.22.04~jammy) ...
Created symlink /etc/systemd/system/multi-user.target.wants/docker.service → /lib/systemd/system/docker.service.
Created symlink /etc/systemd/system/sockets.target.wants/docker.socket → /lib/systemd/system/docker.socket.
Setting up git (1:2.34.1-1ubuntu1.17) ...
Processing triggers for libc-bin (2.35-0ubuntu3.14) ...
debconf: unable to initialize frontend: Dialog
debconf: (No usable dialog-like program is installed, so the dialog based frontend cannot be used. at /usr/share/perl5/Debconf/FrontEnd/Dialog.pm line 78.)
debconf: falling back to frontend: Readline
Scanning processes...
Scanning linux images...

Running kernel seems to be up-to-date.

No services need to be restarted.

No containers need to be restarted.

No user sessions are running outdated binaries.

No VM guests are running outdated hypervisor (qemu) binaries on this host.
ubuntu@vnic-oberfritz:~$ sudo usermod -aG docker ubuntu
