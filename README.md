# Setting Static MAC Address for DPDK Interface in OpenShift

This README guide provides step-by-step instructions on how to set a static MAC address for a DPDK (Data Plane Development Kit) interface in an OpenShift environment. By following these instructions, you will ensure consistent networking behavior and enhanced control over your DPDK-enabled interface.
## Prerequisites
* OpenShift cluster is up and running.
* DPDK-enabled interface is available and identified in the OpenShift cluster.
* ## Steps
**Identify the DPDK Interface**

Log in to your OpenShift cluster and identify the DPDK-enabled interface for which you want to set a static MAC address.

**Determine the Desired MAC Address**

Decide on the MAC address you want to assign to the DPDK interface. Ensure that the MAC address is unique within your network to prevent conflicts.

**Backup Existing Configuration (Optional but Recommended)**

Before making any changes, create a backup of the current configuration to ensure you can revert if necessary.

**Create VF's for DPDK**

$ oc create -f https://github.com/shashank-6777/Setting-Static-MAC-Address-for-DPDK-Interface-in-OpenShift/blob/main/intel-dpdk-node-policy.yaml

**Create/attach DPDK network**

oc create -f https://github.com/shashank-6777/Setting-Static-MAC-Address-for-DPDK-Interface-in-OpenShift/blob/main/dpdk-network.yaml

**Onboard DPDK test app to test**

oc create -f https://github.com/shashank-6777/Setting-Static-MAC-Address-for-DPDK-Interface-in-OpenShift/blob/main/application-dpdk.yaml
## Validation with Screenshots

```bash
[root@vm_caasmng001 final]# oc exec -it sriov -- bin/bash
[root@sriov /]# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: eth0@if94: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1400 qdisc noqueue state UP group default
    link/ether 0a:58:0a:83:00:cc brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 10.131.0.204/23 brd 10.131.1.255 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::858:aff:fe83:cc/64 scope link
       valid_lft forever preferred_lft forever
[root@sriov /]#
[root@sriov /]#


[root@sriov /]# dpdk-app
ENTER dpdk-app:
argc=1
dpdk-app
  cpuRsp.CPUSet = 0-111
  Interface[0]:
    IfName="eth0"  Name="ovn-kubernetes"  Type=SR-IOV
    MAC="0a:58:0a:83:00:cc"  IP="10.131.0.204"
    PCIAddress=0000:ca:02.3
  Interface[1]:
IfName="net1"  Name="gnb00000001--cu-u000000001/sriov-network-cu-u01-01-if00"  Type=SR-IOV
    MAC=""

 

myArgc=14
dpdk-app -n 4 -l 1 --master-lcore 1 -w 0000:ca:02.3 -- -p 0x1 -P --config="(0,0,1)" --parse-ptype
EAL: Detected 112 lcore(s)
EAL: Detected 2 NUMA nodes
EAL: Multi-process socket /var/run/dpdk/rte/mp_socket
EAL: Selected IOVA mode 'VA'
EAL: No available hugepages reported in hugepages-2048kB
EAL: Probing VFIO support...
EAL: VFIO support initialized
EAL: PCI device 0000:ca:02.3 on NUMA socket 1
EAL:   probe driver: 8086:1889 net_iavf
EAL:   using IOMMU type 1 (Type 1)
soft parse-ptype is enabled
LPM or EM none selected, default LPM on
Initializing port 0 ... Creating queues: nb_rxq=1 nb_txq=1... Port 0 modified RSS hash function based on hardware support,requested:0xa38c configured:0x88
Address:** 20:04:0F:F1:88:01 **, Destination:02:00:00:00:00:00, Allocated mbuf pool on socket 1
LPM: Adding route 192.18.0.0 / 24 (0)
LPM: Adding route 2001:200:: / 48 (0)
txq=1,0,1

 

Initializing rx queues on lcore 1 ... rxq=0,0,1

 

Port 0: softly parse packet type info

 

Checking link statusdone
Port0 Link Up. Speed 25000 Mbps -full-duplex
L3FWD: entering main loop on lcore 1
L3FWD:  -- lcoreid=1 portid=0 rxqueueid=0 ""

  ```
