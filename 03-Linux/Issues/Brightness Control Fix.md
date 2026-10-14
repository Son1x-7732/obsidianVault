## Problem  
  
Brightness slider did not work on Linux.  
Changing brightness had no visible effect.

## Symptoms  
  
- Brightness keys worked visually  
- Slider moved  
- Actual brightness did not change

## GRUB Method  
  
Edit:  
  
```bash  
/etc/default/grub
```  
  
Add:  
  
```bash  
GRUB_CMDLINE_LINUX_DEFAULT="acpi_backlight=vendor"
```

Then regenerate grub config:  
  
```bash  
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

## Limine Method  
  
Edit:  
  
```bash  
sudo micro /etc/default/limine
```  
  
Add:  
  
```bash  
GRUB_CMDLINE_LINUX_DEFAULT="acpi_backlight=vendor"
```

Then regenerate grub config:  
  
```
sudo limine-update
```
