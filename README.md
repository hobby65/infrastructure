#Buildingblock Infrastructure

This repo contains the role to prepare the Infrastructure for a VM
It will create the VM, add LVM disks and Register the Linux host
to Satellite and the CMDB (Marval).

##Requirements
There are multiple usecases:
### VMware:
* A vCenter is required to create the vm.
* A RHEL template image is made available on the vCenter environment.
* The template image needs to have an ansible user which is accessible to complete vm configuration.


#### Example group_vars
```yml
vcenter_server: <vcenter_fqdn>
vmware: 
  networks:
    - frontend1:
        - gateway: 10.95.6.1
          netmask: 255.255.255.0
          network: 10.95.6.0
          vmware_name: PROD_GR (vlan767)
    - backend1:
        - netmask: 255.255.255.0
        - network: 10.95.7.0
        - vmware_name: CIC_Int_GR (vlan430)
  source_template: rhel8_template_development_v3
    cluster: CLU_UA_MER
    datacenter: DC_UA_MER
    datastore_filter: UA_MER_PROD
    vm_folder: Linux/development
```
### VirtualBox:

* A vbox host is required
* A vagrant 'box' file must be present
* the networks must be defined in virtualbox

````
vbox:
  server: virtserver.localdomain
  networks:
    backend1:
      netmask: 255.255.255.0
      network: 10.0.10.0
      net_name: NatNetwork
    frontend1:
      gateway: 192.168.15.1
      netmask: 255.255.255.0
      network: 192.168.15.0
      net_name: natnet1
  vm_folder: 'F:'
  vm_group: datacenter
  template_box: redhat/rhel-8
satellite_activation_key: rhel8_os_dev
````

### Example host_vars
```yml
size: m
type: vm
hostname: ora_hb_1.localdomain
os:
  name: rhel
  version: master
root_disk_size: 100
primary_interface:
  ip: 192.168.15.50
  network: frontend1
additional_interfaces:
- ip: 10.0.10.50
  network: backend1
disks:
- size: 110
  vg: oracle
lvm:
  - lv_name: u01
    mountpoint: /u01
    size: '40'
    vg_name: oracle
  - lv_name: oradata
    mountpoint: /u01/app/oracle/oradata
    size: '20'
    vg_name: oracle
  - lv_name: orafra
    mountpoint: /u01/app/oracle/fast_recovery_area
    size: '30'
    vg_name: oracle
  - lv_name: oraredo
    mountpoint: /u01/app/oracle/oraredo
    size: '20'
    vg_name: oracle
buildingblock:
  multinode_group: ora_hb
  name: oracle_hb
  version: master
```

Dependencies
------------

N/A

Author Information
------------------

'--'
