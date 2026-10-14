# Virtual Setup for OS

## KVM/QEMU via Virt-Manager

### Step 1: Install the Required Packages

CachyOS repositories contain everything needed for virtualization. Run:

```
sudo pacman -S virt-manager qemu-desktop libvirt edk2-ovmf dnsmasq iptables-nft
```

### Step 2: Enable and Start the Virtualization Daemon

Interact with `systemd` to ensure the virtualization service runs in the background and starts on boot:

```
sudo systemctl enable --now libvirtd
```

### Step 3: Configure User Permissions

By default, `libvirt` requires root privileges. Add your user account to the `libvirt` group so you can manage VMs without constantly entering your password:

```
sudo usermod -aG libvirt $USER
```

> **Note:** You must log out of your desktop session and log back in (or restart) for this group change to take effect.

# Step 1: Launch Virt-Manager

Open your application launcher and search for **Virtual Machine Manager**.

Alternatively, launch it from the terminal:

```
virt-manager
```

---

# Step 2: Create the Virtual Machine

1. Click the **"+"** icon (**Create a new virtual machine**) in the top-left corner.
2. Select **Local install media (ISO image or CDROM)** and click **Forward**.
3. Click **Browse...** → **Browse Local** and select the downloaded Arch Linux ISO.
4. If Virt-Manager shows the OS type as **generic**:
    - Uncheck **Automatically detect**
    - Manually select **Arch Linux**
5. Click **Forward**.

---

# Step 3: Allocate Resources

Recommended resource allocation for a smooth experience:

- **Memory:** 4096 MB (4 GB)
- **CPUs:** 2–4 cores

Click **Forward** after configuring resources.

---

# Step 4: Create the Virtual Drive

This virtual disk will later be partitioned during installation.

1. Keep **Create a disk image for the virtual machine** enabled.
2. Set disk size to:
    - **20 GB** or
    - **30 GB**
3. Click **Forward**.

---

# Step 5: Enable UEFI Firmware (Important)

> Do not skip this step.

1. On the final configuration screen:
    - Enable **Customize configuration before install**
2. Click **Finish**.
3. In the hardware configuration window:
    - Open the **Overview** tab from the left sidebar.
4. Locate the **Firmware** dropdown.
5. Change firmware from **BIOS** to **UEFI**  
    Example:

```
UEFI x86_64: /usr/share/edk2/x64/OVMF_CODE.fd
```

6. Click **Apply**.

---

# Step 6: Boot the Live Environment

1. Click **Begin Installation** in the top-left corner.
2. The VM will boot into the Arch Linux boot menu.
3. Select:

```
Arch Linux install medium
```

4. After booting completes, you should see the Arch Linux root prompt:

```
root@archiso ~ #
```



# Phase 1: Pre-Flight Checks

## 1. Verify Network Connectivity

Ensure the live ISO has internet access:

```
ping -c 3 archlinux.org
```

If replies are received, networking is working.

---

## 2. Sync the System Clock

Enable automatic time synchronization to avoid HTTPS/package download issues:

```bash
timedatectl set-ntp true
```

This runs silently in the background.

---

# Phase 2: Partition the Virtual Drive

## 1. Identify the Target Drive

List all storage devices:

```bash
lsblk
```

Look for the virtual disk:

- Usually named `vda`
    
- Size should match the allocated VM storage (20–30 GB)
    

---

## 2. Open the Partitioner

Launch `cfdisk` for the target drive:

```bash
cfdisk /dev/vda
```

When prompted, select:

```text
gpt
```

---

## 3. Create the Partition Layout

### Partition 1 — EFI System Partition

- Select **Free space**
    
- Choose **New**
    
- Size: `1G`
    
- Change **Type** to:
    

```text
EFI System
```

Purpose: Stores the bootloader.

---

### Partition 2 — Root Partition

- Use remaining free space
    
- Keep type as:
    

```text
Linux filesystem
```

Purpose: Main Arch Linux system (later formatted as Btrfs).

---

## 4. Write Changes

1. Select **Write**
    
2. Type:
    

```text
yes
```

3. Select **Quit**
    

---

## 5. Verify Partitions

Run:

```bash
lsblk
```

You should now see:

- `vda1` → EFI partition
    
- `vda2` → Root partition
# 1. Boot Issue — “No Boot Device Found”

## Cause

After forcing the VM off and restarting it, the VM attempted to boot from the empty virtual hard drive instead of the Arch Linux ISO.

This caused the UEFI firmware (TianoCore) to display a boot error.

---

## Fix the Boot Order

### Steps

1. Open the VM window in Virt-Manager.
2. Click the **Lightbulb icon** (**Hardware Details**).
3. In the left sidebar, select:

```
Boot Options
```

4. Under **Boot device order**:
    - Locate:

```
SATA CDROM 1
```

- Enable its checkbox.

5. Use the **Up Arrow** button to move the CDROM device to the top of the list.
6. Click **Apply**.

The VM should now boot from the Arch Linux ISO correctly.

---

# 2. Network Issue — VM Has No IP Address

## Cause

CachyOS enables **UFW (Uncomplicated Firewall)** by default.

This blocks libvirt’s virtual DHCP service (`dnsmasq`) from assigning IP addresses to virtual machines.

---

## Temporary Fix (Recommended for Practice)

Run the following commands on the **host CachyOS system**:

```
sudo ufw disable
sudo systemctl restart libvirtd
sudo virsh net-destroy default
sudo virsh net-start default
```

This disables the firewall temporarily and restarts the libvirt networking stack.

---

## Permanent Alternative

Instead of disabling the firewall:

1. Edit:

```
/etc/libvirt/network.conf
```

2. Add:

```
firewall_backend = "iptables"
```

3. Restart libvirt:

```
sudo systemctl restart libvirtd
```

### Check the Virtual CD Drive

1. Click the **Lightbulb icon** (Hardware Details) at the top of the window.
    
2. Click on **SATA CDROM 1** in the left sidebar.
    
3. Look at the right side of the screen. Under **Source path**, does it show the path to your downloaded Arch Linux ISO, or is it blank/empty?
    

### Step 2: "Insert" the Arch ISO

If the source path is empty:

1. Click the **Browse** button right next to it.
    
2. Select **Browse Local** and go find your Arch Linux ISO file again.
    
3. Select it, and make sure you click **Apply** in the bottom right corner.
    

### Step 3: Reboot

1. Click the **Monitor icon** at the top to go back to the blue screen.
    
2. Click the **Power icon** and select **Force Off**.
    
3. Click the **Play** button to turn it back on.
    

Because you already fixed the boot order in the previous step, as soon as the VM turns on and sees that the ISO is physically inside the CD drive, it will immediately bypass this blue menu and load the black Arch Linux bootloader screen.

Once you get back to the `root@archiso ~ #` prompt, hit it with the `ping -c 3 archlinux.org` to verify your firewall fix worked!


---

# Phase 3: Format the Partitions

## 1. Format the EFI Partition

UEFI systems require the EFI partition to use FAT32.

```bash
mkfs.fat -F 32 /dev/vda1
```

---

## 2. Format the Root Partition

Format the main Linux partition as Btrfs:

```bash
mkfs.btrfs /dev/vda2
```

---

# Phase 4: Create Btrfs Subvolumes

## 1. Temporarily Mount the Root Partition

```bash
mount /dev/vda2 /mnt
```

---

## 2. Create Subvolumes

Create separate Btrfs subvolumes for system organization and snapshots:

```bash
btrfs subvolume create /mnt/@
btrfs subvolume create /mnt/@home
btrfs subvolume create /mnt/@log
btrfs subvolume create /mnt/@pkg
btrfs subvolume create /mnt/@snapshots
```

### Subvolume Purpose

|Subvolume|Purpose|
|---|---|
|`@`|Root filesystem|
|`@home`|User home directories|
|`@log`|System logs|
|`@pkg`|Pacman package cache|
|`@snapshots`|Filesystem snapshots|

---

## 3. Unmount Temporary Mount

```bash
umount /mnt
```

---

# Phase 5: Final Mount Layout

## 1. Mount Root Subvolume

Mount the main root subvolume with Btrfs optimizations:

```bash
mount -o noatime,compress=zstd,space_cache=v2,subvol=@ /dev/vda2 /mnt
```

---

## 2. Create Mount Directories

```bash
mkdir -p /mnt/{boot,home,var/log,var/cache/pacman/pkg,.snapshots}
```

---

## 3. Mount Remaining Subvolumes

```

mount -o noatime,compress=zstd,space_cache=v2,subvol=@home /dev/vda2 /mnt/home

mount -o noatime,compress=zstd,space_cache=v2,subvol=@log /dev/vda2 /mnt/var/log

mount -o noatime,compress=zstd,space_cache=v2,subvol=@pkg /dev/vda2 /mnt/var/cache/pacman/pkg

mount -o noatime,compress=zstd,space_cache=v2,subvol=@snapshots /dev/vda2 /mnt/.snapshots

```

---

## 4. Mount the EFI Boot Partition

```
mount /dev/vda1 /mnt/boot
```

---

# Verification

Run:

```bash
lsblk
```

You should see:

- `vda1` mounted at `/mnt/boot`
    
- `vda2` mounted multiple times via Btrfs subvolumes


---

# Phase 6: Install the Base System

Use `pacstrap` to install Arch Linux into the mounted `/mnt` directory.

## Install Required Packages

```

pacstrap -K /mnt base base-devel linux linux-firmware linux-headers btrfs-progs networkmanager micro

```

### Included Packages

|Package|Purpose|
|---|---|
|`base`|Core Arch Linux system|
|`base-devel`|Development tools|
|`linux`|Linux kernel|
|`linux-firmware`|Hardware firmware|
|`linux-headers`|Kernel headers|
|`btrfs-progs`|Btrfs filesystem tools|
|`networkmanager`|Network management after reboot|
|`micro`|Terminal text editor|

The installation may take several minutes depending on internet speed.

---

# Phase 7: Generate the Filesystem Table (fstab)

The new system needs an `fstab` file to know how partitions and subvolumes should mount during boot.

## Generate fstab

```bash
genfstab -U /mnt >> /mnt/etc/fstab
```

This uses UUIDs for reliable mounting.

---

## Verify the Generated fstab

Always inspect the generated file:

```bash
cat /mnt/etc/fstab
```

You should see:

- Btrfs subvolumes (`@`, `@home`, etc.)
    
- EFI partition entries
    
- Mount options like:
    

```text
compress=zstd
```

---

# Phase 8: Enter the Installed System (Chroot)

Switch from the live ISO environment into the installed Arch Linux system:

```bash
arch-chroot /mnt
```

After this command, you are operating directly inside the newly installed system as the root administrator.

---

# Phase 9: Core System Configuration

Run the following commands inside the `arch-chroot` environment.

---

# 1. Configure Timezone

Set the system timezone to Indian Standard Time (IST) and sync the hardware clock:

```

ln -sf /usr/share/zoneinfo/Asia/Kolkata /etc/localtime
hwclock --systohc

```



---

# 2. Configure Locale

## Edit Locale Configuration

Open the locale configuration file:

```bash
micro /etc/locale.gen
```

Find:

```text
#en_US.UTF-8 UTF-8
```

Remove the `#` to uncomment it.

Save and exit:

- `Ctrl + S` → Save
    
- `Ctrl + Q` → Quit
    

---

## Generate and Apply Locale

```bash
locale-gen
echo "LANG=en_US.UTF-8" > /etc/locale.conf
```

---

# 3. Set Hostname

Assign a hostname to the system:

```bash
echo "arch-vm" > /etc/hostname
```

---

# 4. Enable NetworkManager

Enable automatic network management on boot:

```bash
systemctl enable NetworkManager
```

Without this step, networking will not work after reboot.

---

# 5. Set Root Password

Set the password for the root administrator account:

```bash
passwd
```

Enter and confirm the password when prompted.

> Password input remains invisible while typing for security.

## Limine Installation (UEFI)

1. **Install Limine package**
    

bash

```
pacman -S limine efibootmgr
```

2. **Deploy Limine EFI binary**
    

bash

```
mkdir -p /boot/EFI/arch-limine
cp /usr/share/limine/BOOTX64.EFI /boot/EFI/arch-limine/
```

3. **Register boot entry** Replace `/dev/vda` and partition index with your disk + ESP partition:
    

bash

```
efibootmgr --create --disk /dev/vda --part 1 \
  --label "Arch Linux Limine" \
  --loader '\EFI\arch-limine\BOOTX64.EFI'
```

## ✅ Limine Configuration (`/boot/limine.conf`)

Minimal example for Arch:

Code

```
timeout: 5

:Arch Linux
    protocol: linux
    path: boot():/vmlinuz-linux
    cmdline: root=UUID=<your-root-UUID> rw rootflags=subvol=/@
    module_path: boot():/initramfs-linux.img
```

- `boot():/` → refers to the ESP where `limine.conf` resides.
    
- Replace `<your-root-UUID>` with the UUID of `/dev/vda2` (check with `blkid`).
    
- `rootflags=subvol=/@` ensures the correct Btrfs subvolume is mounted.
    

## ✅ Snapper + Limine Integration

1. **Install Snapper + snap-pac**
    

bash

```
pacman -S snapper snap-pac
```

2. **Install limine-snapper-sync (AUR)**
    

bash

```
yay -S limine-snapper-sync
```

3. **Configure Snapper for root subvolume**
    

bash

```
snapper -c root create-config /
```

4. **Enable limine-snapper-sync** Edit `/etc/default/limine` to set paths:
    

Code

```
ESP_PATH=/boot
ROOT_SUBVOLUME_PATH=/@
ROOT_SNAPSHOTS_PATH=/@/.snapshots
```

5. **Run initial sync**
    

bash

```
limine-snapper-sync
systemctl enable --now limine-snapper-sync.service
```

This will automatically add snapshot boot entries to Limine. After every package upgrade, `snap-pac` creates snapshots, and `limine-snapper-sync` ensures they appear in the boot menu.

## ⚠️ Important Notes

- **ESP size**: Ensure your EFI partition is at least 1–2 GB; ArchWiki recommends >4 GB if you plan to keep many snapshots.
    
- **Kernel modules**: Snapshots require matching `/usr/lib/modules` inside the same subvolume.
    
- **OverlayFS**: For testing snapshots read-only, you may need `sd-btrfs-overlayfs` hook with mkinitcpio.

### ✅ Step 1: Create User

bash

```
useradd -m -G wheel -s /bin/bash shubham
passwd shubham
```

- `-m` → creates home directory `/home/shubham`
    
- `-G wheel` → adds to the `wheel` group (needed for sudo)
    
- `-s /bin/bash` → sets Bash as default shell
    

### ✅ Step 2: Install and Configure Sudo

bash

```
pacman -S sudo
EDITOR=nano visudo
```

Inside `visudo`, **uncomment** this line:

Code

```
%wheel ALL=(ALL:ALL) ALL
```

Save and exit.

### ✅ Step 3: Switch to User

bash

```
su - shubham
```

Now you’re logged in as your normal user.

### ✅ Step 4: Test Sudo

bash

```
sudo pacman -Syu
```

If it asks for your password and runs, sudo is working.

## 🛠️ Recommended Extras

For a full DE‑like experience with Niri, you’ll want to add:

- **Waybar** → status bar
    
- **Fuzzel** → app launcher
    
- **Mako** → notifications
    
- **Swaybg** → wallpaper
    
- **Swaylock/Swayidle** → screen lock/idle management

