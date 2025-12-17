# GPU-passtrue
date:20251217
name:simon

# setup-proxmox
*https://pve.proxmox.com/wiki/PCI_Passthrough*
dmesg | grep -e DMAR -e IOMMU # drivers
dmesg | grep 'remapping'

pvesh get /nodes/{nodename}/hardware/pci --pci-class-blacklist ""
~~~ {nodenname} == pve ~~~
# blacklist - NVIDIA GPUs
echo "blacklist nouveau" >> /etc/modprobe.d/blacklist.conf 
echo "blacklist nvidia*" >> /etc/modprobe.d/blacklist.conf 

# host
*https://medium.com/@cactusmccoy/gpu-access-from-a-virtual-machine-on-proxmox-server-ubuntu-vm-903bb9783cb3*
##update grub
nano /etc/default/grub

change: GRUB_CMDLINE_LINUX_DEFAULT
- Intel: GRUB_CMDLINE_LINUX_DEFAULT="quiet intel_iommu=on iommu=pt pcie_acs_override=downstream,multifunction nofb nomodeset video=vesafb:off,efifb:off"
- AMD: GRUB_CMDLINE_LINUX_DEFAULT="quiet amd_iommu=on"

update-grub # update grub
   
dmesg | grep -e DMAR -e IOMMU
## vfio
nano /etc/modules-load.d/modules.conf
vfio
vfio_iommu_type1
vfio_pci
vfio_virqfd
## immou commands
echo "options vfio_iommu_type1 allow_unsafe_interrupts=1" > /etc/modprobe.d/iommu_unsafe_interrupts.conf
echo "options kvm ignore_msrs=1" > /etc/modprobe.d/kvm.conf
## blak list
echo "blacklist nouveau" >> /etc/modprobe.d/blacklist.conf 
echo "blacklist nvidia*" >> /etc/modprobe.d/blacklist.conf

## gpu info
root@pve:~# lspci -v | grep -i nvidia
03:00.0 VGA compatible controller: NVIDIA Corporation GA104 [GeForce RTX 3060] (rev a1) (prog-if 00 [VGA controller])
        Kernel modules: nvidiafb, nouveau
03:00.1 Audio device: NVIDIA Corporation GA104 High Definition Audio Controller (rev a1)
root@pve:~# lspci -n -s 03:00
03:00.0 0300: 10de:2487 (rev a1)
03:00.1 0403: 10de:228b (rev a1)
root@pve:~#

echo "options vfio-pci ids=10de:2487, 10de:228b" > /etc/modprobe.d/vfio.conf
