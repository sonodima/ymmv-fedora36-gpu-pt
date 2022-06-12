# YMMV Fedora 36 GPU Passthrough For Kids

This is as easy as VFIO GPU passthrough gets.<br>
Note that this is not a generic configuration, and comes with deal-breaking drawbacks if you don't own the same hardware config used in this guide.

## Hardware Configuration

- <b>INTEL</b> CPU _(with integrated graphics, or alternatively a secondary AMD gpu)_
- <b>NVIDIA</b> GPU _(that is going to be passed through to the VM)_

## Setup

### Configuring Kernel Args

- Blacklist <b>NVIDIA</b> _(nouveau)_ drivers
- Enable IOMMU

```sh
sudo grubby --args="rd.driver.blacklist=nouveau modprobe.blacklist=nouveau rd.driver.pre=vfio-pci iommu=pt intel_iommu=on kvm.ignore_msrs=1 allow_unsafe_interrupts=1" --update-kernel=/boot/vmlinuz-$(uname -r)
```

### Adding VFIO Kernel Drivers

```sh
sudo echo 'add_drivers+=" vfio vfio_iommu_type1 vfio_pci vfio_virqfd "' >> /etc/dracut.conf.d/local.conf
sudo dracut -f --kver $(uname -r)
```

### Installing Virtualization Packages

```sh
sudo dnf install @virtualization
sudo systemctl start libvirtd
sudo systemctl enable libvirtd
```

### Reboot!

```sh
sudo reboot
```

## Extras

### Installing Cockpit WebUI

> The Web interface is available at <b>localhost:9090</b>

```sh
sudo dnf install cockpit
sudo dnf install cockpit-machines
sudo systemctl enable --now cockpit.socket
sudo firewall-cmd --add-service=cockpit
sudo firewall-cmd --add-service=cockpit --permanent
```
