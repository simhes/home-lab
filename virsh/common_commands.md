# virh sh commands

connect:
virsh > common_commands.md

connect to remote:
virsh -c qemu+ssh://controller@192.168.0.200/system

- start:    virsh start <vm-name>
- shutdown: virsh shutdown --mode=agent <vm-name> 
- reboot:   virsh reboot --mode=agent <vm-name>
- list:     virsh list <vm-name>
- list:     virsh list --all <vm-name>
