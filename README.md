# Summary
Build cloud-initi templates using ansible plays. Use the sample hosts and variables file to generate your own, simply remove the sample in the file name.

From the root of this project directory call: `ansible-playbook ./plays/build.yaml --ask-pass` to begin downloading vm images and creating templates. 

## Requirements
### Binaries
- ansible

### Variables
__hosts:__  
It is required that each proxmox host have some variables attached to it.  
- The `host_vm_id` variable will be combined with the `id` of the image (see below) to create the full VM ID. For example using both samples below a VM ID of 9011 will be produced for the CentOS-Stream-8 image.
- The `storage_type` variable is used to determine what storage this node will use fro creating the VM, typically this will either be local-zfs or local-lvm.

Example:
``` yaml
nodes:
  hosts:
    192.168.10.4:
      host_vm_id: 901
      storage_type: "local-lvm"
```

__images:__  
The variables below are used to download the images desired.
``` yaml
images:
  - name: CentOS-Stream-8
    id: 1
    url: https://cloud.centos.org/centos/8-stream/x86_64/images/CentOS-Stream-GenericCloud-8-20210603.0.x86_64.qcow2
```

## Notes:
__Idempotence:__  
The `create_vm` tasks list lacks idempotence to a degree. The proxmox_kvm module seems to work erratically at best when creating VMs, and lacks many functions that would help in producing the cloud-init templates. As such the shell module was used extensively. The playbook is safe to run multiple times and does have checks to determine if an image should be imported or not.
