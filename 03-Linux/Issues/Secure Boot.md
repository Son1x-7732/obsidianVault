```
sudo pacman -S sbctl
sudo sbctl create-keys

sudo sbctl enroll-keys -m

sudo sbctl sign -s /boot/vmlinuz-linux
sudo sbctl sign -s /boot/EFI/systemd/systemd-bootx64.efi
sudo sbctl sign -s /boot/EFI/BOOT/BOOTX64.EFI
```

_Verification:_ Run `sudo sbctl verify`. Every essential boot file should now show a green `✔ Signed` status.