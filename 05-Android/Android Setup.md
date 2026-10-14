# Custom Rom Setup
## Phase 1: Boot Into Recovery

```bash
adb devices

adb reboot bootloader

fastboot flash recovery recovery.img

fastboot reboot recovery
```

---

## Phase 2: The "Clean Wipe" (Mandatory for Downgrade)

> You are moving backwards from Android 16 to 15.  
> If you skip this, the phone will not boot.

### On the Phone

1. Tap **Factory Reset**
    
2. Tap **Format data / Factory reset**
    
3. Tap **Format data** to confirm
    
4. Once done, tap the **Back Arrow** (top left) to return to the main menu
    

---

## Phase 3: Install the ROM (Sideload)

### Prepare the Phone

1. Tap **Apply Update**
    
2. Tap **Apply from ADB**
    
3. The screen will say:
    

```text
Now send the package...
```

### Sideload the ROM

```bash
adb sideload EvolutionX-15.0-xxxx-miatoll-Official.zip
```

---

## Phase 4: Reboot and Finish

## On the Phone

1. Tap the **Back Arrow** to go to the main menu
    
2. Tap **Reboot system now**
    

---

# Magisk Stuff

```bash
adb reboot bootloader

fastboot flash boot magisk_patched_26xxx.img

fastboot reboot
```
