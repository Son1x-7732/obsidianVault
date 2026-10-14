### 01

### Install Virtualization Packages

Ensure your host system has all required virtualization tools.

Run in terminal:

- `sudo pacman -S virt-manager qemu-desktop libvirt edk2-ovmf dnsmasq iptables-nft`
    
- Wait for installation to complete without errors
    

### 02

### Enable Libvirt Service

Start the virtualization daemon so Virt-Manager can manage VMs.

Run in terminal:

- `sudo systemctl enable --now libvirtd`
    
- Confirm with `systemctl status libvirtd` that it shows **active (running)**
    

### 03

### Add User to Libvirt Group

This allows you to manage VMs without root password prompts.

Run in terminal:

- `sudo usermod -aG libvirt $USER`
    
- Log out and back in (or reboot) to apply group change




![[etc_default_limine.png]]

![[boot_limine.conf.png]]





![[Pasted image 20260604104610.png]]



![[Pasted image 20260604104743.png]]


![[limine.conf.png]]


sudo pacman -S rustup
rustup default stable

git clone https://aur.archlinux.org/paru.git
cd paru
makepkg -si