# Practical 1 — Virtualization using VMware Workstation Pro and Ubuntu

> Beginner's guide. Your setup already has VMware Workstation Pro 17 and an Ubuntu VM with a 40 GB virtual disk and 6 GB RAM. Start by using that VM; do not reinstall anything unless your teacher asks for a fresh setup.

## 1. Aim
To run Ubuntu in VMware Workstation Pro and demonstrate CPU, memory, storage, and network virtualization.

## 2. What you are demonstrating
- **CPU virtualization:** Ubuntu sees the virtual CPU resources assigned to the VM.
- **Memory virtualization:** Ubuntu sees the memory allocated to the VM.
- **Storage virtualization:** VMware presents a virtual disk to Ubuntu.
- **Network virtualization:** Ubuntu uses a virtual network adapter. NAT usually lets the VM access the internet through the host.

## 3. Requirements
- Laptop with hardware virtualization support enabled.
- VMware Workstation Pro 17.
- Ubuntu installed in a VM.
- Enough free disk space and RAM; internet is optional for the ping test.
- Existing VM settings: 40 GB virtual disk, 6 GB RAM (verify actual settings).

## 4. Basic terms
- **Host machine:** Your physical laptop.
- **Host OS:** The OS running VMware on the laptop.
- **Guest OS:** Ubuntu running inside VMware.
- **VM:** Software-defined computer with virtual CPU, memory, disk, and network.
- **Hypervisor:** Software that creates/manages VMs. VMware Workstation Pro is a Type 2 (hosted) hypervisor.
- **Virtual disk:** Disk presented to the guest OS, usually backed by files on the host.
- **NAT:** Network Address Translation; the guest usually shares the host's external connection.

## 5. Part A — Start your existing VM
1. Open VMware Workstation Pro 17.
2. Select your existing Ubuntu VM in the library.
3. If the VM is powered on, continue to Step 5. If powered off, you can check settings first.
4. With the VM powered off, open **Edit virtual machine settings**. Check Memory (about 6 GB), Processors (note assigned cores), Hard Disk (about 40 GB), and Network Adapter (NAT if required). Leave working settings unchanged. Click **OK**.
5. Click **Power on this virtual machine** and wait for Ubuntu to boot.
6. Log in to Ubuntu.
7. Open Terminal with **Ctrl + Alt + T**.
8. Ensure the terminal is inside the Ubuntu VM window, not your host's terminal.

## 6. Part B — CPU and memory virtualization
Run these commands one at a time inside Ubuntu.

### CPU
```bash
lscpu
```
Observe `Architecture`, `CPU(s)`, `Core(s) per socket`, and `Model name`. This shows CPU information visible to the guest. Values depend on the VM settings and host.

### Memory
```bash
free -h
```
Observe the `Mem:` row and `total`, `used`, and `available`. The reported total may be slightly lower than the configured 6 GB.

## 7. Part C — Storage virtualization
### List virtual disks and partitions
```bash
lsblk
```
Look for the main disk, often `sda` or `nvme0n1`, and its partitions.

### Show filesystem capacity
```bash
df -h
```
Observe `Filesystem`, `Size`, `Used`, `Avail`, and `Mounted on`; `/` is commonly the root filesystem.

### Optional detailed disk listing
```bash
sudo fdisk -l
```
Enter your Ubuntu password if asked. This is an inspection command. Do not format or repartition a disk for this practical.

**Explanation:** Ubuntu sees a virtual block device provided by VMware. `df -h` reports mounted filesystem capacity, so it may not show exactly 40 GB.

## 8. Part D — Network virtualization
### Show network interfaces and IP addresses
```bash
ip addr
```
Look for a network interface with an IP address. Names vary, for example `ens33`, `ens160`, or `enp0s3`.

### Show routes
```bash
ip route
```
Look for a line starting with `default`.

### Optional internet test
```bash
ping -c 4 ubuntu.com
```
Replies mean the VM reached the destination. If it fails, check the host internet connection and VMware **Network Adapter → Connected → NAT**. DNS or ICMP can be blocked, so a failed ping alone does not prove all networking is broken.

## 9. If a fresh VM is required
Use only if the existing VM is missing or your teacher specifically requires a new installation.
1. Download an Ubuntu Desktop LTS ISO from the official Ubuntu website.
2. In VMware, choose **Create a New Virtual Machine**.
3. Select the Ubuntu ISO as the installer image.
4. Choose Linux / Ubuntu 64-bit if prompted.
5. Name the VM, e.g. `Ubuntu-Cloud-Practical`.
6. Create a virtual disk with maximum capacity 40 GB.
7. Allocate 6 GB RAM only if the host has enough memory; leave resources for the host.
8. Assign a reasonable number of CPU cores; do not allocate every core automatically.
9. Set the network adapter to NAT.
10. Power on and follow the Ubuntu installer: language, keyboard, timezone, username, password, and installation.
11. Restart when prompted. If asked to remove installation media, disconnect the ISO from the virtual CD/DVD drive and press Enter.
12. Log in and perform the commands in Sections 6–8.

**Safety:** Install Ubuntu inside the VM only. Do not format the physical laptop disk.

## 10. Expected output
Your exact CPU count, IP address, disk name, and memory figures will differ. Record your actual observations.

| Command | Expected evidence |
|---|---|
| `lscpu` | CPU architecture and processor details appear. |
| `free -h` | Guest memory total and usage appear. |
| `lsblk` | Virtual disk and partition list appears. |
| `df -h` | Filesystem sizes and mount points appear. |
| `ip addr` | Network interfaces and IP addresses appear. |
| `ip route` | Routing table appears, often including a default route. |
| `ping -c 4 ubuntu.com` | Replies if DNS, internet, and ICMP are available. |

**Sample record-book observations (replace with your real results):**
- CPU: `lscpu` displayed the CPU information visible to Ubuntu.
- Memory: `free -h` displayed guest memory.
- Storage: `lsblk` and `df -h` displayed the virtual disk, partitions, and filesystems.
- Network: `ip addr` showed the virtual network interface and `ip route` showed routes.
- Connectivity: Ping succeeded / did not succeed (record honestly).

## 11. Screenshots for your record
Capture your own screenshots; do not use invented screenshots.
1. VMware window showing Ubuntu running.
2. Terminal with `lscpu` and `free -h`.
3. Terminal with `lsblk` and `df -h`.
4. Terminal with `ip addr` and `ip route`.
5. Optional terminal with `ping -c 4 ubuntu.com`.

If your teacher asks for a sample PDF/report, include: title, aim, requirements, short theory, procedure, your screenshots with captions, actual observations, result, and viva Q&A.

## 12. Common problems
- **VM will not start:** Read the error; check disk space and whether hardware virtualization is enabled in BIOS/firmware.
- **No internet in Ubuntu:** Check host internet, then VMware VM Settings → Network Adapter → Connected and NAT. Inspect `ip addr` and `ip route`.
- **`ping ubuntu.com` says name resolution failed:** Likely DNS or connectivity issue.
- **Ping fails but interface has an IP:** ICMP may be blocked; check other network details rather than assuming all networking is broken.
- **`df -h` shows less than 40 GB:** It reports filesystem capacity, not the configured virtual disk's full size; compare with `lsblk` and VMware settings.
- **`sudo` asks for a password:** Type your Ubuntu password; typed characters normally do not appear.
- **Wrong terminal:** Run commands inside the Ubuntu VM, not on the host.

## 13. Result
Ubuntu was run as a virtual machine in VMware Workstation Pro. CPU, memory, storage, and network virtualization were demonstrated using Linux commands.

## 14. Viva questions and answers
**Q1. What is virtualization?** Software-based creation of virtual versions of computing resources.

**Q2. What is a virtual machine?** A software-defined computer with virtual CPU, memory, storage, and network resources.

**Q3. What is a hypervisor?** Software that creates and manages virtual machines.

**Q4. Which type of hypervisor is VMware Workstation Pro?** Type 2, because it runs on top of a host operating system.

**Q5. What is the host machine?** The physical computer running the virtualization software.

**Q6. What is the guest OS?** The operating system running inside the VM; here, Ubuntu.

**Q7. What is CPU virtualization?** Presenting virtual CPU resources to a VM.

**Q8. What is memory virtualization?** Providing a configured amount of virtual memory to the VM.

**Q9. What is storage virtualization?** Presenting virtual disks or logical storage to the guest OS.

**Q10. What is network virtualization?** Providing virtual network interfaces and logical connectivity to VMs.

**Q11. What is NAT?** A network mode that usually lets the guest access external networks through the host using address translation.

**Q12. What does `lscpu` do?** Displays CPU architecture and processor information.

**Q13. What does `free -h` do?** Displays memory and swap usage in readable units.

**Q14. What does `lsblk` do?** Lists block devices such as disks and partitions.

**Q15. What does `df -h` do?** Displays filesystem space usage in readable units.

**Q16. What does `ip addr` do?** Displays network interfaces and IP addresses.

**Q17. What does `ip route` do?** Displays the IP routing table.

**Q18. Why might `df -h` show less than 40 GB?** Filesystem capacity differs from virtual disk capacity because of partitions, overhead, and reserved space.

**Q19. VM versus container?** A VM runs a guest OS on virtual hardware; a container shares the host kernel and isolates applications.

**Q20. Why is virtualization useful in cloud computing?** It supports resource sharing, isolation, flexible allocation, and faster provisioning.

## 15. Final checklist
- [ ] Ubuntu boots inside VMware.
- [ ] I ran commands inside the Ubuntu guest terminal.
- [ ] Captured `lscpu` and `free -h`.
- [ ] Captured `lsblk` and `df -h`.
- [ ] Captured `ip addr` and `ip route`.
- [ ] Recorded optional ping result honestly.
- [ ] Added screenshots and actual observations to the record.
- [ ] Practised the viva answers.

**Exam tip:** Explain what each command proves. Your figures may differ from other students' systems.
