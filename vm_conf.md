# conf off vm

tutorial:
https://sysguides.com/install-kvm-on-linux

# start
1. check support

$ lscpu | grep Virtualization
-> Virtualization:                       VT-x

2. Next, ensure that your kernel includes KVM modules.

$ zgrep CONFIG_KVM /boot/config-$(uname -r)
->
CONFIG_KVM_GUEST=y
CONFIG_KVM_COMMON=y
CONFIG_KVM_MMIO=y
CONFIG_KVM_ASYNC_PF=y
CONFIG_KVM_VFIO=y
CONFIG_KVM_GENERIC_DIRTYLOG_READ_PROTECT=y
CONFIG_KVM_COMPAT=y
CONFIG_KVM_XFER_TO_GUEST_WORK=y
CONFIG_KVM_GENERIC_HARDWARE_ENABLING=y
CONFIG_KVM_GENERIC_MMU_NOTIFIER=y
CONFIG_KVM_GENERIC_MEMORY_ATTRIBUTES=y
CONFIG_KVM_PRIVATE_MEM=y
CONFIG_KVM_GENERIC_PRIVATE_MEM=y
CONFIG_KVM=m
CONFIG_KVM_WERROR=y
CONFIG_KVM_SW_PROTECTED_VM=y
CONFIG_KVM_INTEL=m
CONFIG_KVM_AMD=m
CONFIG_KVM_AMD_SEV=y
CONFIG_KVM_SMM=y
CONFIG_KVM_HYPERV=y
CONFIG_KVM_XEN=y
# CONFIG_KVM_PROVE_MMU is not set
CONFIG_KVM_EXTERNAL_WRITE_TRACKING=y
CONFIG_KVM_MAX_NR_VCPUS=4096

3. install

Ubuntu / Debian Linux:

$ sudo apt install qemu-system-x86 libvirt-daemon-system virtinst \
    virt-manager virt-viewer ovmf swtpm qemu-utils guestfs-tools \
    libosinfo-bin tuned

4. virt

not now (for windows)

5. Enable the Modular libvirt Daemon

$ sudo systemctl enable libvirtd.service

reboot

$ sudo reboot

6. Validate Host Virtualization Setup

$ sudo virt-host-validate qemu
-> 
  QEMU: Checking for hardware virtualization                                 : PASS
  QEMU: Checking if device /dev/kvm exists                                   : PASS
  QEMU: Checking if device /dev/kvm is accessible                            : PASS
  QEMU: Checking if device /dev/vhost-net exists                             : PASS
  QEMU: Checking if device /dev/net/tun exists                               : PASS
  QEMU: Checking for cgroup 'cpu' controller support                         : PASS
  QEMU: Checking for cgroup 'cpuacct' controller support                     : PASS
  QEMU: Checking for cgroup 'cpuset' controller support                      : PASS
  QEMU: Checking for cgroup 'memory' controller support                      : PASS
  QEMU: Checking for cgroup 'devices' controller support                     : PASS
  QEMU: Checking for cgroup 'blkio' controller support                       : PASS
  QEMU: Checking for device assignment IOMMU support                         : WARN (No ACPI DMAR table found, IOMMU either disabled in BIOS or not supported by this hardware platform)
  QEMU: Checking for secure guest support                                    : WARN (Unknown if this platform has Secure Guest support)

6.1. grub
change note made
/etc/default/grub

# GRUB_CMDLINE_LINUX="... intel_iommu=on iommu=pt"

6.2 status

simon@p510:~$ sudo virt-host-validate qemu
  QEMU: Checking for hardware virtualization                                 : PASS
  QEMU: Checking if device /dev/kvm exists                                   : PASS
  QEMU: Checking if device /dev/kvm is accessible                            : PASS
  QEMU: Checking if device /dev/vhost-net exists                             : PASS
  QEMU: Checking if device /dev/net/tun exists                               : PASS
  QEMU: Checking for cgroup 'cpu' controller support                         : PASS
  QEMU: Checking for cgroup 'cpuacct' controller support                     : PASS
  QEMU: Checking for cgroup 'cpuset' controller support                      : PASS
  QEMU: Checking for cgroup 'memory' controller support                      : PASS
  QEMU: Checking for cgroup 'devices' controller support                     : PASS
  QEMU: Checking for cgroup 'blkio' controller support                       : PASS
  QEMU: Checking for device assignment IOMMU support                         : WARN (No ACPI DMAR table found, IOMMU either disabled in BIOS or not supported by this hardware platform)
  QEMU: Checking for secure guest support                                    : WARN (Unknown if this platform has Secure Guest support)
simon@p510:~$ sudo dmesg | grep -i -e DMAR -e IOMMU
[   34.294072] iommu: Default domain type: Translated
[   34.294072] iommu: DMA domain TLB invalidation policy: lazy mode
simon@p510:~$ 

7. optimisce for vm

simon@p510:~$ sudo systemctl enable --now tuned
simon@p510:~$ tuned-adm active
Current active profile: balanced
simon@p510:~$ tuned-adm list
Available profiles:
- accelerator-performance     - Throughput performance based tuning with disabled higher latency STOP states
- atomic-guest                - Optimize virtual guests based on the Atomic variant
- atomic-host                 - Optimize bare metal systems running the Atomic variant
- aws                         - Optimize for aws ec2 instances
- balanced                    - General non-specialized tuned profile
- cpu-partitioning            - Optimize for CPU partitioning
- cpu-partitioning-powersave  - Optimize for CPU partitioning with additional powersave
- default                     - Legacy default tuned profile
- desktop                     - Optimize for the desktop use-case
- desktop-powersave           - Optmize for the desktop use-case with power saving
- enterprise-storage          - Legacy profile for RHEL6, for RHEL7, please use throughput-performance profile
- hpc-compute                 - Optimize for HPC compute workloads
- intel-sst                   - Configure for Intel Speed Select Base Frequency
- laptop-ac-powersave         - Optimize for laptop with power savings
- laptop-battery-powersave    - Optimize laptop profile with more aggressive power saving
- latency-performance         - Optimize for deterministic performance at the cost of increased power consumption
- mssql                       - Optimize for Microsoft SQL Server
- network-latency             - Optimize for deterministic performance at the cost of increased power consumption, focused on low latency network performance
- network-throughput          - Optimize for streaming network throughput, generally only necessary on older CPUs or 40G+ networks
- openshift                   - Optimize systems running OpenShift (parent profile)
- openshift-control-plane     - Optimize systems running OpenShift control plane
- openshift-node              - Optimize systems running OpenShift nodes
- optimize-serial-console     - Optimize for serial console use.
- oracle                      - Optimize for Oracle RDBMS
- postgresql                  - Optimize for PostgreSQL server
- powersave                   - Optimize for low power consumption
- realtime                    - Optimize for realtime workloads
- realtime-virtual-guest      - Optimize for realtime workloads running within a KVM guest
- realtime-virtual-host       - Optimize for KVM guests running realtime workloads
- sap-hana                    - Optimize for SAP HANA
- sap-hana-kvm-guest          - Optimize for running SAP HANA on KVM inside a virtual guest
- sap-netweaver               - Optimize for SAP NetWeaver
- server-powersave            - Optimize for server power savings
- spectrumscale-ece           - Optimized for Spectrum Scale Erasure Code Edition Servers
- spindown-disk               - Optimize for power saving by spinning-down rotational disks
- throughput-performance      - Broadly applicable tuning that provides excellent performance across a variety of common server workloads
- virtual-guest               - Optimize for running inside a virtual guest
- virtual-host                - Optimize for running KVM guests
Current active profile: balanced
simon@p510:~$ sudo tuned-adm profile virtual-host
simon@p510:~$ tuned-adm active
Current active profile: virtual-host
simon@p510:~$ sudo tuned-adm verify
Verification succeeded, current system settings match the preset profile.
See TuneD log file ('/var/log/tuned/tuned.log') for details.
simon@p510:~$ 

8. ethernet 

9. user promissions

$ virsh uri
-> qemu:///system

$ sudo virsh uri
$ sudo usermod -aG libvirt $USER
$ echo "export LIBVIRT_DEFAULT_URI='qemu:///system'" >> ~/.bashrc
$ source ~/.bashrc
$ virsh uri

10. USER
$ ls /var/lib/libvirt/images/
$ sudo setfacl -R -b /var/lib/libvirt/images
$ sudo setfacl -R -m u:$USER:rwX /var/lib/libvirt/images
$ sudo setfacl -m d:u:$USER:rwx /var/lib/libvirt/images

$ getfacl /var/lib/libvirt/images
->

getfacl: Removing leading '/' from absolute path names
# file: var/lib/libvirt/images
# owner: root
# group: root
user::rwx
user:simon:rwx
group::--x
mask::rwx
other::--x
default:user::rwx
default:user:simon:rwx
default:group::--x
default:mask::rwx
default:other::--x

10.1 TEST

touch /var/lib/libvirt/images/test_file
simon@p510:~$ ls -l /var/lib/libvirt/images/
total 0
-rw-rw----+ 1 simon simon 0 Oct 14 18:42 test_file

