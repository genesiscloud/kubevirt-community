# Overview

Qemu supports adding virtual hardware to VMs by using vhost-user sockets.
The "hardware" is handled by a userspace tool.
Examples include SPDK for storage and DPDK/VPP for networking.

This proposal defines a way of adding references to such sockets to a `VirtualMachine`.

## Motivation

Users of kubevirt who wish to use specialized virtual hardware have to integrate it by using the hooking system of kubernetes and kubevirt.

## Goals

Have a clear, stable and backwards compatible way of specifiying vhost-user resources specifically for vhost virtio-blk disks.

## Non Goals

Specifying for other device types than virtio-blk.

## Definition of Users
(who is this feature set intended for)

## User Stories

* Fast local storage
* Non-PVC hotlugging

## Repos

* kubevirt/kubevirt

# Design

* Hotplug API in sidecar or virt-launcher

## API Examples

New parts of the VM spec:

```yaml
spec:
  vhostuser-disks:
    path: /srv/vhost-volumes
    disks:
      - socket: volume-a.sock
        dev: vdb
        ...
```

this will

* change the virt-launcher Pod to mount the path `/srv/vhost-volumes` into a well-known place (e.g. `/run/vhost-volumes`)
* change the qemu domain XML to include this snippet:

```xml
<devices>
  <disk type="vhostuser" device="disk" model="virtio-non-transitional" snapshot="no">
    <driver name="qemu" type="raw"/>
    <source type="unix" path="/run/vhost-volumes/volume-a.sock">
      <reconnect enabled="yes" timeout="1"/>
    </source>
    <target dev="vdb" bus="virtio"/>
  </disk>
</devices>
```

## Scalability
(overview of how the design scales)

## Update/Rollback Compatibility
(does this impact update compatibility and how)

## Functional Testing Approach
(an overview on the approaches used to functional test this design)

# Implementation Phases
(How/if this design will get broken up into multiple phases)
