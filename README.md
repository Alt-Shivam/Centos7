# OS Configuration (Cascade Lake)
###### tags: `ORAN`
- [Reference - OS configuration](https://github.com/intel/FlexRAN#os-configuration)
---
###### tags: `CHT Technical Document`
:::success
**Outline:**
[TOC]
:::

## Background
:::info
kernel-rt are required to run O-DU Low efficiently
This guide will guide you to setup the kernel-rt and all required OS Setting
:::

---

## Install RT Kernel
### 1. Download kernel-rt
:::info
- Here we need use [kernel-rt-3.10.0-1062.12.1.rt56.1042.el7.x86_64.rpm](https://linuxsoft.cern.ch/cern/centos/7/rt/x86_64/Packages/kernel-rt-3.10.0-1062.12.1.rt56.1042.el7.x86_64.rpm)
    - packager source : 
https://linuxsoft.cern.ch/cern/centos/7/rt/x86_64/Packages/
:::
```shell=
### check th current kernel
[oai@ee705-7-ip120 ~]$ uname -a
Linux 121-ip202.et.ntust.edu.tw 3.10.0-1062.el7.x86_64 #1 SMP Wed Aug 7 18:08:02 UTC 2019 x86_64 x86_64 x86_64 GNU/Linux
### download the kernel-rt package
[oai@ee705-7-ip120 ~]$ wget https://linuxsoft.cern.ch/cern/centos/7/rt/x86_64/Packages/kernel-rt-3.10.0-1062.12.1.rt56.1042.el7.x86_64.rpm

```

### 2. Install other packages first
:::info
- Before install the kernel-rt, you need to install the following packages (**must be in order**)
- All these packages are used to tune your OS so that O-DU Low can run more efficiently
- **Follow the package version is reccomended since different version might show problems**
    - packager source : https://linuxsoft.cern.ch/cern/centos/7/rt/x86_64/Packages/
:::
:::danger
- If you have error message while installing, refer to [Failed dependencies](https://hackmd.io/aGWqKPqNTc-Td8h1VL8hCg?view#%E2%9C%93-Failed-dependencies)
:::
#### tuna
```shell=
### install packages by yum
[oai@ee705-7-ip120 ~]$ sudo yum install -y tuna
### check the packages
[oai@ee705-7-ip120 ~]$ rpm -q tuna
tuna-0.13-9.el7.noarch
```
#### tuned
```shell=
### download the packages
[oai@ee705-7-ip120 ~]$ wget 
https://linuxsoft.cern.ch/cern/centos/7/rt/x86_64/Packages/tuned-2.9.0-1.el7fdp.noarch.rpm


### uninstall old package (if other version already installed)
[oai@ee705-7-ip120 ~]$ sudo yum remove tuned.noarch
### use rpm to install packages
[oai@ee705-7-ip120 ~]$ sudo rpm -ivh tuned-2.9.0-1.el7fdp.noarch.rpm
### check the packages
[oai@ee705-7-ip120 ~]$ rpm -q tuned
tuned-2.9.0-1.el7fdp.noarch
```

#### tuned-profiles-realtime
```shell=
### downlaod the packages
[oai@ee705-7-ip120 ~]$ wget http://mirror.centos.org/centos/7/rt/x86_64/Packages/tuned-profiles-realtime-2.9.0-1.el7fdp.noarch.rpm
### use rpm to install packages
[oai@ee705-7-ip120 ~]$ sudo rpm -ivh tuned-profiles-realtime-2.9.0-1.el7fdp.noarch.rpm
### check the packages
[oai@ee705-7-ip120 ~]$ rpm -q tuned-profiles-realtime
tuned-profiles-realtime-2.9.0-1.el7fdp.noarch
```

#### rt-setup
```shell=
### download the packages
[oai@ee705-7-ip120 ~]$ wget http://mirror.centos.org/centos/7/rt/x86_64/Packages/rt-setup-2.0-6.el7.x86_64.rpm
### use rpm to install packages
[oai@ee705-7-ip120 ~]$ sudo rpm -ivh rt-setup-2.0-6.el7.x86_64.rpm
### check the packages
[oai@ee705-7-ip120 ~]$ rpm -q rt-setup
rt-setup-2.0-6.el7.x86_64
```

### 3. Install the kernel-rt
:::info
- If you have error message while installing, refer to [Failed dependencies](https://hackmd.io/aGWqKPqNTc-Td8h1VL8hCg?view#%E2%9C%93-Failed-dependencies)
- **Please make sure your kernel-rt version is the same as in this guide**
:::
```shell=
### download the packages (if you havent downloaded the package)
[oai@ee705-7-ip120 ~]$ wget https://linuxsoft.cern.ch/cern/centos/7/rt/x86_64/Packages/kernel-rt-3.10.0-1062.12.1.rt56.1042.el7.x86_64.rpm

### use rpm to install packages
[oai@ee705-7-ip120 ~]$ sudo rpm -ivh kernel-rt-3.10.0-1062.12.1.rt56.1042.el7.x86_64.rpm


### check the packages
[oai@ee705-7-ip120 ~]$ rpm -q kernel-rt
kernel-rt-3.10.0-1062.12.1.rt56.1042.el7.x86_64


```

### 4. [Real-time Kernel Patching](http://bowlofstew.com/rt-patching)
:::info
- Patch the kernel-rt based on our configuration
- CERN hosts the both Scientific Linux (SLC) 5/6 and CentOS 7 source sets. For the case of CentOS 7, you can add this to the yum-config-manager via
- Add Centos7 repo to yum-config-manager
:::
```shell=
[oai@ee705-7-ip120 ~]$ sudo yum-config-manager --add-repo=http://linuxsoft.cern.ch/cern/centos/7/rt/CentOS-RT.repo
```
:::info
- **Set gpgcheck=1 to gpgcheck=0** for each entry in /etc/yum.repos.d/CentOS-RT.repo
- This turn off gpg-check-pattern when we install something from the repo
:::
```shell=
[oai@ee705-7-ip120 ~]$ sudo vi /etc/yum.repos.d/CentOS-RT.repo 
```
:::info
- Install the yum group RealTime via
:::
```shell=
[oai@ee705-7-ip120 ~]$ sudo yum group install -y RealTime
```
:::info
- Install other packages needed
- Contains programs to test various real time Linux features
:::
```shell=
[oai@ee705-7-ip120 ~]$ sudo yum install -y rt-tests
```
:::info
- Reboot the server
    - If you have error message while rebooting, refer to [Reboot failed when choose the rt kernel](https://hackmd.io/aGWqKPqNTc-Td8h1VL8hCg?view#%E2%9C%93-Reboot-failed-when-choose-the-rt-kernel)
:::
```shell=
[oai@ee705-7-ip120 ~]$ sudo reboot
```
:::info
- use **uname -a** to check the kernel is rt kernel
- **Make sure kernel-rt version is the same  again**
- **Also make sure all installed packages version is the same**
:::
 ```shell=
[oai@ee705-7-ip120 ~]$ uname -a
Linux ee705-7-ip120.et.ntust.edu.tw 3.10.0-1062.12.1.rt56.1042.el7.x86_64 #1 SMP PREEMPT RT Wed Feb 5 10:31:05 CET 2020 x86_64 x86_64 x86_64 GNU/Linux
```
![](https://i.imgur.com/b9dL3VM.png)

## Install other packages
### 1. Install NFV packages
:::info
- **Must install the packages in order**
    - packager source : http://ftp.riken.jp/Linux/cern/centos/7/rt/x86_64/Packages/
:::
#### qemu-kvm-tools-ev
:::info
-  This is an open source virtualizer that provides hardware emulation for the KVM hypervisor
:::
```shell=
### downlaod the packages
[oai@ee705-7-ip120 ~]$ wget http://ftp.ksu.edu.tw/FTP/CentOS/7/virt/x86_64/kvm-common/Packages/q/qemu-kvm-tools-ev-2.12.0-33.1.el7_7.4.x86_64.rpm
### use rpm to install packages
[oai@ee705-7-ip120 ~]$ sudo rpm -ivh qemu-kvm-tools-ev-2.12.0-33.1.el7_7.4.x86_64.rpm
### check the packages
[oai@ee705-7-ip120 ~]$ rpm -q qemu-kvm-tools-ev
qemu-kvm-tools-ev-2.12.0-33.1.el7_7.4.x86_64
```
#### tuned-profiles-nfv
:::info
-  Additional tuned profile(s) targeted to Network Function Virtualization
-  profile used to tune OS
:::
```shell=
### downlaod the packages
[oai@ee705-7-ip120 ~]$ wget http://mirror.centos.org/centos/7/rt/x86_64/Packages/tuned-profiles-nfv-2.9.0-1.el7fdp.noarch.rpm
### use rpm to install packages
[oai@ee705-7-ip120 ~]$ sudo rpm -ivh tuned-profiles-nfv-2.9.0-1.el7fdp.noarch.rpm
### check the packages
[oai@ee705-7-ip120 ~]$ rpm -q tuned-profiles-nfv
tuned-profiles-nfv-2.9.0-1.el7fdp.noarch
```
#### tuned-profiles-nfv-guest
:::info
- Provide the Network Function Virtualization (NFV) guest profiles for tuning
:::
```shell=
### downlaod the packages
[oai@ee705-7-ip120 ~]$ wget http://mirror.centos.org/centos/7/rt/x86_64/Packages/tuned-profiles-nfv-guest-2.9.0-1.el7fdp.noarch.rpm
### use rpm to install packages
[oai@ee705-7-ip120 ~]$ sudo rpm -ivh tuned-profiles-nfv-guest-2.9.0-1.el7fdp.noarch.rpm
### check the packages
[oai@ee705-7-ip120 ~]$ rpm -q tuned-profiles-nfv-guest
tuned-profiles-nfv-guest-2.9.0-1.el7fdp.noarch
```
#### tuned-profiles-nfv-host
:::info
- Providethe Network Function Virtualization (NFV) host profiles for tuning
:::
```shell=
### downlaod the packages
[oai@ee705-7-ip120 ~]$ wget http://mirror.centos.org/centos/7/rt/x86_64/Packages/tuned-profiles-nfv-host-2.9.0-1.el7fdp.noarch.rpm
### use rpm to install packages
[oai@ee705-7-ip120 ~]$ sudo rpm -ivh tuned-profiles-nfv-host-2.9.0-1.el7fdp.noarch.rpm
### check the packages
[oai@ee705-7-ip120 ~]$ rpm -q tuned-profiles-nfv-host
tuned-profiles-nfv-host-2.9.0-1.el7fdp.noarch
```

### 2. Install other RT packages
:::info
- **Packages version Must be installed same as kernel-rt**
(3.10.0-1062.12.1.rt56.1042.el7)
    - packager source : https://linuxsoft.cern.ch/cern/centos/7/rt/x86_64/repoview/kernel-rt-devel.html
:::
#### kernel-rt-devel
:::info
- Development package for building kernel modules
:::
```shell=
### downlaod the packages
[oai@ee705-7-ip120 ~]$ wget https://linuxsoft.cern.ch/cern/centos/7/rt/x86_64/Packages/kernel-rt-devel-3.10.0-1062.12.1.rt56.1042.el7.x86_64.rpm
### use rpm to install packages
[oai@ee705-7-ip120 ~]$ sudo rpm -ivh kernel-rt-devel-3.10.0-1062.12.1.rt56.1042.el7.x86_64.rpm 
### check the packages
[oai@ee705-7-ip120 ~]$ rpm -q kernel-rt-devel
kernel-rt-devel-3.10.0-1062.12.1.rt56.1042.el7.x86_64
```
#### kernel-rt-kvm
:::info
- Provides KVM modules for package kernel-rt
:::
```shell=
### downlaod the packages
[oai@ee705-7-ip120 ~]$ wget https://linuxsoft.cern.ch/cern/centos/7/rt/x86_64/Packages/kernel-rt-kvm-3.10.0-1062.12.1.rt56.1042.el7.x86_64.rpm
### use rpm to install packages
[oai@ee705-7-ip120 ~]$ sudo rpm -ivh kernel-rt-kvm-3.10.0-1062.12.1.rt56.1042.el7.x86_64.rpm
### check the packages
[oai@ee705-7-ip120 ~]$ rpm -q kernel-rt-kvm
kernel-rt-kvm-3.10.0-1062.12.1.rt56.1042.el7.x86_64
```
#### rtctl
:::info
- Used to manipulate real-time attributes for groups of processes or individual threads
:::
```shell=
### downlaod the packages
[oai@ee705-7-ip120 ~]$ wget https://linuxsoft.cern.ch/cern/centos/7/rt/x86_64/Packages/rtctl-1.13-2.el7.noarch.rpm
### use rpm to install packages
[oai@ee705-7-ip120 ~]$ sudo rpm -ivh rtctl-1.13-2.el7.noarch.rpm
### check the packages
[oai@ee705-7-ip120 ~]$ rpm -q rtctl
rtctl-1.13-2.el7.noarch
```

## Tune the RT kernel performance
:::info
- The setting is based on the server enable hyperthreading (HT), if your server didn't enable HT, please follow the reference from [Intel Flexran](https://github.com/intel/FlexRAN#os-configuration) 
  - P.S.1 : For the 1. in the below section, there's the way to check if your server enable HT or not
  - ==P.S.2 : [If you follow Flexran setting, your server may not boot, you can try to debug by yourself or follow the solution here to change the grub setting back](https://hackmd.io/903nREczSNmNhjH6IyTVHQ#Disable-HT)==

- Here we want to edit the real-time profile and use it on our OS 
:::
### 1. Edit real-time profiles to **add isolated_cores**:
:::info
- CPU isolation is a way to force the system scheduler to use only some logical CPU cores for processes
- In our environment : 2 CPU, each has 10 cores, since enable HT, there are totally 40 cores
    - From [this reference](https://blog.csdn.net/qq_33468857/article/details/85092861?fbclid=IwAR0Oj1iLj_3wLgOEMpWe4_dsPOPZ5M0aHMmsHQ4YzsrPb5YlMF-fRguu1ag), here's some equation to make sure if your server already enabled HT or not
         - ***Equation 1*** : `grep 'physical id' /proc/cpuinfo | sort -u | wc -l`
         - ***Equation 2*** : `grep 'core id' /proc/cpuinfo | sort -u | wc -l`
         - ***Equation 3*** : `grep 'processor' /proc/cpuinfo | sort -u | wc -l`
         - If the result of ***Equation 3*** = ***Equation 1*** * ***Equation 2*** 
           - Then your server disable HT, else your server enable
:::
```shell=
[oai@ee705-7-ip120 ~]$ sudo vi /etc/tuned/realtime-virtual-host-variables.conf 

---

# Examples: Here's the example of setting isolated_cores while having two CPUs and enable HT
# isolated_cores=1- <Equation 1*Equation 2 -1>
# 
isolated_cores=1-19
```



<!--```shell=
[oai@ee705-7-ip120 ~]$ sudo vi /etc/tuned/realtime-virtual-host-variables.conf 
```
Example if n=18
![](https://i.imgur.com/eKrgJj9.png)
-->
### 2. Activate Real Time Profile
```shell=
[oai@ee705-7-ip120 ~]$ sudo tuned-adm profile realtime-virtual-host
```
### 3. Edit /etc/default/grub
:::info
- **Append** the following to the **GRUB_CMDLINE_LINUX**
:::
```shell=
[oai@ee705-7-ip120 ~]$ sudo vi /etc/default/grub

###append the following satting to the GRUB_CMDLINE_LINUX:
"crashkernel=auto spectre_v2=retpoline rd.lvm.lv=centos/root rd.lvm.lv=centos/swap rhgb quiet processor.max_cstate=1 intel_idle.max_cstate=0 intel_pstate=disable cgroup_memory=1 cgroup_enable=memory idle=poll default_hugepagesz=1G hugepagesz=1G hugepages=32 intel_iommu=on iommu=pt usbcore.autosuspend=-1 selinux=0 enforcing=0 nmi_watchdog=0 softlockup_panic=0 audit=0 mce=off kthread_cpus=0 irqaffinity=0"
```

<!-- ![](https://i.imgur.com/q07Ji3L.png) -->
:::info
- Add the following setting to the **GRUB_CMDLINE_LINUX_DEFAULT**
:::
```shell=
"${GRUB_CMDLINE_LINUX_DEFAULT:+$GRUB_CMDLINE_LINUX_DEFAULT }\$tuned_params"
```
![](https://i.imgur.com/EtefpgE.png)

:::info
- Add the following setting to the **GRUB_INITRD_OVERLAY**:
:::
```shell=
"${GRUB_INITRD_OVERLAY:+$GRUB_INITRD_OVERLAY }\$tuned_initrd"
```
![](https://i.imgur.com/BRMGlYA.png)

### 4. Update the grub
:::info
- Apply the kernel-rt profile that we just modified to the grub
:::
```shell=
[oai@ee705-7-ip120 ~]$ sudo grub2-mkconfig -o /boot/grub2/grub.cfg
```

:::danger
- If you are using a UEFI based system
```shell
[oai@ee705-7-ip120 ~]$ sudo grub2-mkconfig -o /boot/efi/EFI/centos/grub.cfg
```
:::spoiler Ref.
[Grub2 setting in UEFI mode](https://hackmd.io/aGWqKPqNTc-Td8h1VL8hCg?view#%E2%9C%93-Grub2-setting-in-UEFI-mode)
:::

### 5. Reboot the server
```shell=
[oai@ee705-7-ip120 ~]$ sudo reboot
```

### 6. Check the kernel parameter
```shell=
[oai@ee705-7-ip120 ~]$ cat /proc/cmdline
```
:::info
- kernel parameter **example** from [Intel Flrexran](https://github.com/intel/FlexRAN#os-configuration)
- in my case, below is the example
:::
```shell=
BOOT_IMAGE=/vmlinuz-3.10.0-1062.12.1.rt56.1042.el7.x86_64 root=/dev/mapper/centos-root ro crashkernel=auto spectre_v2=retpoline rd.lvm.lv=centos/root rd.lvm.lv=centos/swap rhgb quiet processor.max_cstate=1 intel_idle.max_cstate=0 intel_pstate=disable cgroup_memory=1 cgroup_enable=memory idle=poll default_hugepagesz=1G hugepagesz=1G hugepages=32 intel_iommu=on iommu=pt usbcore.autosuspend=-1 selinux=0 enforcing=0 nmi_watchdog=0 softlockup_panic=0 audit=0 mce=off kthread_cpus=0 irqaffinity=0 skew_tick=1 isolcpus=1-19 intel_pstate=disable nosoftlockup nohz=on nohz_full=1-19 rcu_nocbs=1-19
```

<!-- ### 7. Set CPU frequency using msr-tools 
:::danger
**SKIP THIS STEP IN NTUST.**
:::
:::warning
Change n to your # of cores
:::
```shell=
[oai@ee705-7-ip120 ~]$ git clone https://github.com/intel/msr-tools/
[oai@ee705-7-ip120 ~]$ cd msr-tools/
[oai@ee705-7-ip120 ~]$ git checkout msr-tools-1.3
[oai@ee705-7-ip120 ~]$ make
[oai@ee705-7-ip120 ~]$ vi turbo-2.5G.sh

# copy this to the shell file
---
 #!/bin/sh
 for i in {0..(2n-1)}
 do
 #Set core 0-39 to 2.5GHz (0x1900). Please change according to your CPU model
./wrmsr -p ${i} 0x199 0x1900
done
#Set Uncore to Max
./wrmsr -p 0 0x620 0x1e1e
./wrmsr -p (2n-1) 0x620 0x1e1e
---

[oai@ee705-7-ip120 ~]$ chmod 755 turbo-2.5G.sh
[oai@ee705-7-ip120 ~]$ sudo sh turbo-2.5G.sh
```
:::spoiler example of turbo-2.5G.sh
```shell=
#!/bin/sh
for i in {0..35}
do
#Set core 0-35 to 2.5GHz (0x1900). Please change according to your CPU model
sudo ./wrmsr -p ${i} 0x199 0x1900
done
#Set Uncore to Max
sudo ./wrmsr -p 0 0x620 0x1e1e
sudo ./wrmsr -p 35 0x620 0x1e1e
```
::: -->
### 7. Set CPU Frequency Policy to Performance.
:::info
- Scale the CPU frequency up or down in order to improve performance
:::
```shell=
sudo cpupower frequency-set -g performance
```
### 8. RT Test and Verify the test result
:::info
- Run at least 12/24 hours for rigid performance validation. For quick performance validation, 15 minutes is recommended. Pay attention to the **Act. and Avg.** ==On a well-tuned platform, the numbers should be similar.==

- -a 0-<isolate_CPU> -t <1+isolate_CPU>
  - You can double check the setting from [ this link](#1-Edit-etctunedrealtime-virtual-host-variablesconf-to-add-isolated_cores)
:::
```shell=
sudo cyclictest -m -p95 -d0 -a 0-19 -t 20
```

<!-- - NTUST Intel (Result using Anydesk)
![](https://i.imgur.com/YGaQsCb.png) -->
- NTUST AAEON (Result using SSH)
![](https://i.imgur.com/9iIJ2ug.png)
- NTUST Intel (Result using SSH)
![](https://i.imgur.com/gs0mDUl.png)
<!-- 
- NTUST AAEON (Result using Anydesk)
![](https://i.imgur.com/ORcl8fk.png) -->

- Expectation
![](https://i.imgur.com/ISfxNC6.png)
:::warning
If you don't got the same result
- Make sure your kernel is real-time kernel
- Delete the other normal kernel
- Make sure you cpu isolated number is correct
- Make sure the kernel configuration is correct
- If after kernel setting your free memory is low, lower the number of hugepages
:::

## Appendix: Set the rt-kernel as a default kernel when booting
:::info
Basically, the OS will load the latest kernel as the default. But sometimes the rt-kernel you installed is not the latest version. And you may work from home, so you can not choose the kernel in person. That's why we need to set the **grub2** config file to change the default kernel.
:::
### 1. List the kernel you have installed
```bash=
sudo awk -F\' /^menuentry/{print\$2} /etc/grub2.cfg
```

:::success

- **Result:**
```=
[user@localhost ~]$ sudo awk -F\' /^menuentry/{print\$2} /etc/grub2.cfg
CentOS Linux (3.10.0-1160.71.1.el7.x86_64) 7 (Core)
CentOS Linux (3.10.0-1160.62.1.el7.x86_64) 7 (Core)
CentOS Linux (3.10.0-1160.53.1.el7.x86_64) 7 (Core)
CentOS Linux (3.10.0-1160.el7.x86_64) 7 (Core)
CentOS Linux (3.10.0-1062.12.1.rt56.1042.el7.x86_64) 7 (Core)
CentOS Linux (0-rescue-c045bcf3a8c448f3acd0399a12d3ec76) 7 (Core)
```
:::

### 2. Set the default kernel
:::info
According to the listed kernel above, they are numbered from up to low. The initial number is `0`. Hence, if I want to set the  `3.10.0-1062.12.1.rt56.1042.el7.x86_64` as the default kernel, you need to set `4`.
:::
```=
[user@localhost ~]$ sudo grub2-set-default 4
```

### 3. Check the setting is configured
```=
[user@localhost ~]$ sudo cat /boot/grub2/grubenv |grep saved

## Result:
saved_entry=4
```

### 4. Rebuild GRUB2

```=
[user@localhost ~]$ sudo grub2-mkconfig -o /boot/grub2/grub.cfg
```

### 5. Reboot the server
```=
[user@localhost ~]$ sudo reboot
```

### 6. Check the result
```=
[user@localhost ~]$ uname -r

## Result:
3.10.0-1062.12.1.rt56.1042.el7.x86_64
```


:::warning
**Reference:**
- https://hostmalabar.in/clients/knowledgebase/209/CentOS-or-RHEL-7--Change-default-kernel-boot-with-old-kernel.html
:::
