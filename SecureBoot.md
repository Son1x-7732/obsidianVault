Absolutely. Here is the **complete state of the Secure Boot problem**, reconstructed from the terminal outputs and configuration information you provided so far. I’ll keep the exact commands, paths, outputs, and conclusions separate so you have a reliable record of what we’ve actually established.

# Secure Boot + Limine Problem — Complete Summary

## 1. The original problem

You are running **CachyOS / Arch Linux** on an **Acer Nitro 5**, using **Limine** as the bootloader.

The problem was:

> You want to enable Secure Boot, but Acer's firmware requires you to manually select an EFI file through **“Select an UEFI File as trusted for executing”**.

Previously, you manually trusted an EFI file in the Acer BIOS.

The problem appeared after a CachyOS/Limine update:

- the EFI bootloader file changed,
- Acer no longer considered the newly changed EFI binary trusted,
- Secure Boot consequently prevented it from executing,
- and you had to go back into the Acer BIOS and manually trust the new EFI file again.

So the underlying goal became:

> **Get Limine working with proper Secure Boot signatures so that future Limine/CachyOS updates don't require manually adding a new EFI file as trusted in the Acer BIOS every time.**

There is also a secondary goal:

> **Clean up old/stale Acer trusted-UEFI-file entries if possible.**

Importantly, we have **not yet solved the Acer BIOS trusted-file list itself**. We've mainly prepared the Linux/EFI side so far.

---

# 2. Your bootloader setup

Your system is using:

**Limine 12.9.0**

This was confirmed with:

```
sudo bootctl status
```

The relevant output was:

```
Current Boot Loader:
Product: Limine 12.9.0
...
Partition: /dev/disk/by-partuuid/c2854014-eda2-4372-9990-727283037724
Current Entry: CachyOS.linux-cachyos
...
Available Boot Loaders on ESP:
ESP: /boot
File: /boot/EFI/BOOT/BOOTX64.EFI
```

And the EFI variables showed:

```
Boot Loaders Listed in EFI Variables:
Title: Limine
ID: 0x0002
Status: active, boot-order
File: /boot/EFI/limine/limine_x64.efi

Title: Windows Boot Manager
ID: 0x0003
Status: active, boot-order
File: /boot/EFI/Microsoft/Boot/bootmgfw.efi

Title: linux
ID: 0x0000
Status: active, boot-order
File: /boot/EFI/limine/limine_x64.efi
```

So initially there were **two EFI NVRAM entries pointing to the same Limine file**:

```
Boot0002 → Limine
Boot0000 → linux
```

There was also:

```
Boot0003 → Windows Boot Manager
```

---

# 3. We removed the duplicate NVRAM boot entry

Because `Boot0000` and `Boot0002` both pointed to:

```
/boot/EFI/limine/limine_x64.efi
```

we removed the duplicate `linux` entry using:

```
sudo efibootmgr -b 0000 -B
```

That succeeded.

The resulting:

```
sudo efibootmgr
```

was:

```
BootCurrent: 0002
Timeout: 0 seconds
BootOrder: 0002,0003,2001,2002,2003

Boot0002* Limine HD(1,GPT,c2854014-eda2-4372-9990-727283037724,...)/EFI/limine/limine_x64.efi

Boot0003* Windows Boot Manager .../EFI/Microsoft/Boot/bootmgfw.efi...

Boot2001* EFI USB Device
Boot2002* EFI DVD/CDROM
Boot2003* EFI Network
```

Therefore, your custom EFI boot entries are now clean:

```
0002 → Limine
0003 → Windows Boot Manager
```

There is no longer the duplicate:

```
0000 → linux
```

### Important distinction

This cleaned the **UEFI NVRAM boot entries**.

It did **not necessarily remove the Acer BIOS "trusted UEFI file" entries**.

Those are a separate firmware mechanism, and we have deliberately not assumed that they are the same thing.

---

# 4. Your EFI files

Your EFI System Partition is mounted at:

```
/boot
```

We checked the two important Limine EFI files:

```
sudo ls -lh /boot/EFI/BOOT/BOOTX64.EFI /boot/EFI/limine/limine_x64.efi
```

Output:

```
-rwx------ 1 root root 340K Aug  3 16:02 /boot/EFI/BOOT/BOOTX64.EFI
-rwx------ 1 root root 371K Sep 17 09:23 /boot/EFI/limine/limine_x64.efi
```

So:

### Normal Limine loader

```
/boot/EFI/limine/limine_x64.efi
```

Size:

```
371K
```

Timestamp:

```
Sep 17 09:23
```

### EFI fallback loader

```
/boot/EFI/BOOT/BOOTX64.EFI
```

Size:

```
340K
```

Timestamp:

```
Aug 3 16:02
```

---

# 5. There is also a Limine backup file

We checked:

```
sudo ls -lah /boot/EFI/limine/
```

and got:

```
total 752K
drwx------ ...
-rwx------ 1 root root 370K Sep 17 09:09 limine_x64.bak
-rwx------ 1 root root 371K Sep 17 09:23 limine_x64.efi
```

So there is:

```
limine_x64.bak
```

and:

```
limine_x64.efi
```

The `.bak` file is **not a valid EFI binary**, so we decided to leave it alone.

---

# 6. The fallback and normal Limine binaries are different

We checked whether:

```
/boot/EFI/BOOT/BOOTX64.EFI
```

and:

```
/boot/EFI/limine/limine_x64.efi
```

were identical using:

```
sudo cmp -l /boot/EFI/BOOT/BOOTX64.EFI /boot/EFI/limine/limine_x64.efi | head
```

They differed.

That means these are currently separate binary files rather than simply two identical copies.

---

# 7. Secure Boot status before our changes

Your original:

```
sudo sbctl status
```

showed:

```
Installed: ✓ sbctl is installed
Owner GUID: 9cd63ca9-faec-4a6b-8653-1b88b36de4ef
Setup Mode: ✓ Disabled
Secure Boot: ✗ Disabled
Vendor Keys: microsoft builtin-db builtin-db builtin-db builtin-KEK builtin-PK
```

The important part is:

```
Setup Mode: ✓ Disabled
Secure Boot: ✗ Disabled
```

So:

### Setup Mode

```
Disabled
```

### Secure Boot

```
Disabled
```

We have **not enabled Secure Boot yet**.

---

# 8. Your firmware already has Secure Boot keys

We checked:

```
sudo sbctl list-enrolled-keys
```

The output was:

```
PK:
  Acer Root CA

KEK:
  Microsoft Corporation Third Party Marketplace Root
  Microsoft RSA Devices Root CA 2021
  Acer Root CA

DB:
  Microsoft Root Certificate Authority 2010
  Microsoft Root Certificate Authority 2010
  Acer Root CA
  Acer Root CA
  ABO
  linpus.com
```

This is extremely important.

Your firmware already contains:

### Platform Key

```
Acer Root CA
```

### Key Exchange Keys

```
Microsoft Corporation Third Party Marketplace Root
Microsoft RSA Devices Root CA 2021
Acer Root CA
```

### Database entries

```
Microsoft Root Certificate Authority 2010
Microsoft Root Certificate Authority 2010
Acer Root CA
Acer Root CA
ABO
linpus.com
```

Therefore, your current firmware is **not an empty Secure Boot setup**.

It already contains Acer and Microsoft trust material.

---

# 9. We explicitly decided NOT to wipe Secure Boot keys

Because Windows is installed and should remain bootable, we have deliberately avoided dangerous commands/actions such as:

```
sbctl reset
```

or BIOS options such as:

```
Clear Secure Boot keys
Restore factory keys
Delete PK/KEK/DB
```

We also have not run:

```
sbctl enroll-keys
```

or:

```
sbctl rotate-keys
```

The existing Microsoft/Acer keys are being preserved.

This is important because the goal is to add/establish Linux trust **without destroying the existing Windows Secure Boot trust chain**.

---

# 10. We checked whether Limine's EFI file is signed

We ran:

```
sudo sbctl verify
```

Among the results, the important ones were:

```
/boot/EFI/BOOT/BOOTX64.EFI
```

→ **NOT signed**

while:

```
/boot/EFI/limine/limine_x64.efi
```

→ **signed**

Your CachyOS kernels were also reported signed, including:

```
/boot/.../linux-cachyos/vmlinuz
```

and:

```
/boot/.../linux-cachyos-lts/vmlinuz
```

There were also numerous Microsoft EFI/DLL/MUI files reported as unsigned by `sbctl`.

We explicitly did **not** start manually signing Microsoft files.

That is not the objective here.

---

# 11. We discovered why Limine itself was already getting signed

This was one of the most important findings.

We looked at the installed Limine scripts and found that:

```
/usr/lib/limine/limine-common-functions
```

contains logic that checks for `sbctl` and defines a signing function.

The relevant source is essentially:

```
sb_sign()
```

which calls:

```
sbctl sign "$file_path"
```

The file therefore establishes that the Limine integration can automatically sign EFI files using `sbctl`.

This matches what we saw in your system journal.

---

# 12. Your system journal proved automatic Limine signing

We looked at the journal and found:

```
Sep 17 09:13:44 cachyos kernel: Secure boot disabled
```

Then later:

```
Sep 17 09:13:48 ... Started [limine-snapper-sync.service] ...
```

And at approximately 09:23:

```
Sep 17 09:23:48 ... limine-snapper-sync ...
Sep 17 09:23:49 ... Updated: /boot/limine.conf
Sep 17 09:23:49 ... ✓ Signed /boot/EFI/limine/limine_x64.efi
Sep 17 09:23:50 ... ✓ Signed /boot/EFI/limine/limine_x64.efi
```

This is strong evidence that your CachyOS/Limine integration is already automatically signing:

```
/boot/EFI/limine/limine_x64.efi
```

during its update/synchronization process.

This is why the normal Limine loader is already signed even though Secure Boot is currently disabled.

---

# 13. We checked whether pacman owns the EFI file

We ran:

```
sudo pacman -Qo /boot/EFI/limine/limine_x64.efi
```

and got:

```
error: No package owns /boot/EFI/limine/limine_x64.efi
```

This isn't evidence that Limine is broken.

The EFI file is installed/managed on the ESP through the Limine installation/update mechanism rather than being a directly package-owned file in the normal pacman file database.

We also ran:

```
sudo pacman -Ql limine | grep -E '.efi$'
```

and it returned nothing.

---

# 14. We investigated the signing utilities

We checked for other EFI signing utilities:

```
command -v pesign
command -v sbverify
command -v sbsign
```

and:

```
command -v systemd-sbsign
```

None were available.

The only relevant command we found was:

```
/usr/sbin/openssl
```

We also tried treating the EFI file directly as a PKCS#7 object with OpenSSL, but that failed—which is expected because an entire PE/COFF EFI binary isn't itself simply a standalone PKCS#7 object.

So we stopped going down that path.

---

# 15. We checked the EFI binary's strings

You ran:

```
sudo strings /boot/EFI/limine/limine_x64.efi | grep -i -E 'CachyOS|Acer|Microsoft|limine'
```

The output contained Limine-related strings and:

```
Limine 12.9.0
```

but there was no useful signer identity exposed through `strings`.

So we didn't use `strings` as a way to determine the signing certificate.

---

# 16. We discovered the crucial fallback problem

The big discovery came from examining:

```
/etc/limine-entry-tool.conf
```

and:

```
/usr/lib/limine/limine-common-functions
```

The Limine configuration documentation explicitly states:

```
### Default Boot Fallback
### Configure whether Limine should be installed as the default UEFI fallback loader. (yes|no|"")
...
#ENABLE_LIMINE_FALLBACK=""
```

And, critically, the documentation says that with Secure Boot enabled, the default fallback isn't signed automatically by `limine-update` and recommends disabling automatic fallback installation, running:

```
limine-install --fallback
```

once, and then signing the fallback manually.

This explained the discrepancy:

### Normal Limine

```
/boot/EFI/limine/limine_x64.efi
```

is automatically signed.

### Fallback Limine

```
/boot/EFI/BOOT/BOOTX64.EFI
```

was not automatically signed.

That is exactly why we manually signed the fallback.

---

# 17. Your `/etc/default/limine`

You showed:

```
ESP_PATH="/boot"
KERNEL_CMDLINE[default]+="quiet nowatchdog splash rw rootflags=subvol=/@ root=UUID=db85df3f-349f-4ac3-954a-0c2653d9fe3b acpi_backlight=native"
BOOT_ORDER="*, *lts, *fallback, Snapshots"
```

There is no active:

```
ENABLE_LIMINE_FALLBACK=yes
```

setting.

The configuration therefore does not explicitly request automatic fallback installation.

---

# 18. We then inspected the actual `limine-install` script

You ran:

```
grep -n -A45 -B10 'fallback' /usr/bin/limine-install
```

The output showed the command-line option:

```
--fallback | -f)
    SET_LIMINE_AS_FALLBACK=yes
```

and the help text:

```
--fallback        | -f    Make Limine the default fallback bootloader.
```

More importantly, we found the actual fallback update function:

```
# Function to update the Limine fallback EFI binary if needed
update_limine_fallback() {
    local source_file="${BINARY_SOURCE_PATH}"
    local target_file="${BINARY_FALLBACK_PATH}"
```

The script checks the source and target hashes:

```
if command -v b2sum &>/dev/null && [[ -f "$target_file" ]]; then
    local source_hash target_hash
    source_hash=$(b2sum "$source_file" | awk '{print $1}')
    target_hash=$(b2sum "$target_file" | awk '{print $1}')

    if [[ "$source_hash" == "$target_hash" ]]; then
        # No update needed
        return 0
    fi
fi
```

Then it can copy the source binary to the fallback:

```
info_msg "Updating Limine fallback EFI..."
cp -f "$source_file" "$target_file"
```

So the fallback **can** be overwritten when the fallback installation mechanism is invoked.

---

# 19. We confirmed when the fallback gets updated

The script contains:

```
# Optionally set Limine as fallback bootloader
if [[ "${ENABLE_LIMINE_FALLBACK:-}" == "yes" ]] ||
    [[ "${SET_LIMINE_AS_FALLBACK:-}" == "yes" ]] ||
    [[ ! -f "${BINARY_FALLBACK_PATH}" && -z "${ENABLE_LIMINE_FALLBACK:-}" ]]; then
```

Then:

```
update_limine_fallback
```

is called. Pasted text.txtTXT

Therefore, with your current setup:

```
ENABLE_LIMINE_FALLBACK
```

is not set to `yes`.

And:

```
/boot/EFI/BOOT/BOOTX64.EFI
```

already exists.

So **ordinary operation does not automatically replace the fallback simply because Limine updates**.

That is an important part of why we decided not to change `/etc/default/limine` unnecessarily.

---

# 20. We manually signed the fallback EFI

Before this step:

```
sudo sbctl verify /boot/EFI/BOOT/BOOTX64.EFI
```

gave:

```
✗ /boot/EFI/BOOT/BOOTX64.EFI is not signed
```

Then you ran:

```
sudo sbctl sign /boot/EFI/BOOT/BOOTX64.EFI
```

and got:

```
✓ Signed /boot/EFI/BOOT/BOOTX64.EFI
```

So the fallback was successfully signed.

---

# 21. We verified the fallback again

You then ran:

```
sudo sbctl verify /boot/EFI/BOOT/BOOTX64.EFI
```

and got:

```
✓ /boot/EFI/BOOT/BOOTX64.EFI is signed
```

So now we have:

```
/boot/EFI/limine/limine_x64.efi
    ✓ signed

/boot/EFI/BOOT/BOOTX64.EFI
    ✓ signed
```

This is a major improvement over the original state.

---

# 22. We verified Limine with debug output

We attempted:

```
sudo sbctl verify --verbose /boot/EFI/limine/limine_x64.efi
```

but your installed version of `sbctl` responded:

```
unknown flag: --verbose
Usage:
  sbctl verify [flags]

Flags:
  -h, --help   help for verify

Global Flags:
      --config string      Path to configuration file
      --debug              Enable verbose debug logging
      --disable-landlock   Disable landlock sandboxing
      --json               Output as json
      --quiet              Mute info from logging
```

So we corrected that to:

```
sudo sbctl verify --debug /boot/EFI/limine/limine_x64.efi
```

Your output contained:

```
time=2026-09-17T09:43:26.230+05:30 level=DEBUG ...
```

including access to:

```
/var/lib/sbctl
```

and:

```
/sys/firmware/efi/efivars/
```

and then:

```
✓ /boot/EFI/limine/limine_x64.efi is signed
```

So the normal Limine EFI remains properly signed.

The debug mode didn't expose the certificate identity, so we don't actually need to continue trying to extract it that way.

---

# 23. We checked your installed `sbctl` commands

You tried:

```
sudo sbctl list-keys
```

but your version returned:

```
unknown command "list-keys" for "sbctl"
```

Then you ran:

```
sudo sbctl --help
```

and got the actual command set:

```
Available Commands:
  bundle
  create-keys
  enroll-keys
  export-enrolled-keys
  generate-bundles
  help
  import-keys
  list-bundles
  list-enrolled-keys
  list-files
  remove-bundle
  remove-file
  reset
  rotate-keys
  setup
  sign
  sign-all
  status
  verify
```

So we now know your `sbctl` version simply doesn't provide:

```
sbctl list-keys
```

We won't keep trying nonexistent commands.

---

# 24. We inspected `sbctl setup`

We ran:

```
sudo sbctl setup --help
```

and got:

```
Setup sbctl

Usage:
  sbctl setup [flags]

Flags:
  -h, --help           Show help
      --migrate        migrate the sbctl installation
      --print-config   print config file
      --print-state    print the state of sbctl
      --setup          setup the sbctl installation
```

We deliberately did **not** run:

```
--setup
```

because we don't want to alter your existing Secure Boot setup unnecessarily.

---

# 25. We checked sbctl state

We first tried:

```
sudo sbctl setup --print-state
```

and your version responded:

```
can only use --print-state with --json
```

So we correctly changed it to:

```
sudo sbctl setup --print-state --json
```

which produced:

```
{
  "installed": true,
  "landlock": true
}
```

This tells us:

```
installed = true
```

and:

```
landlock = true
```

It does **not** expose the signing certificate/key identity.

Therefore, this wasn't useful for identifying the signer, and we stopped probing this particular path.

---

# 26. Where the system stands right now

At this exact point:

## Bootloader

```
Limine 12.9.0
```

## Linux EFI partition

```
/boot
```

## Normal Limine EFI

```
/boot/EFI/limine/limine_x64.efi
```

Status:

```
✓ signed
```

## Fallback EFI

```
/boot/EFI/BOOT/BOOTX64.EFI
```

Status:

```
✓ signed
```

## Windows

Still has its normal EFI loader:

```
/boot/EFI/Microsoft/Boot/bootmgfw.efi
```

and NVRAM entry:

```
Boot0003* Windows Boot Manager
```

## Limine NVRAM entry

```
Boot0002* Limine
```

## Duplicate NVRAM entry

Removed:

```
Boot0000 linux
```

## Secure Boot

Still:

```
Disabled
```

We have **not enabled it yet**.

## Setup Mode

Still:

```
Disabled
```

## Firmware keys

Still contain:

```
Acer Root CA
Microsoft...
```

and we have not wiped or reset them.

---

# 27. What we have actually solved

We've addressed the Linux-side signing problem substantially.

Originally:

```
Normal Limine EFI       → signed
Fallback EFI            → unsigned
```

Now:

```
Normal Limine EFI       → signed ✓
Fallback EFI            → signed ✓
```

We've also established that CachyOS's Limine integration automatically signs:

```
/boot/EFI/limine/limine_x64.efi
```

during its synchronization/update process.

The fallback is different: the Limine configuration documentation explicitly warns that it isn't automatically signed in the same way.

---

# 28. What is NOT solved yet

There are still two important questions.

### A. Will Acer actually accept the signatures?

`sbctl` saying:

```
✓ ... is signed
```

means the EFI binary has a valid signature according to `sbctl`.

It does **not by itself prove that your Acer firmware will accept that particular certificate/signing chain when Secure Boot is enabled**.

That is why we haven't declared victory yet.

The real test is enabling Secure Boot and seeing whether the Acer firmware accepts Limine.

---

### B. The old Acer "Trusted UEFI File" entries

We have **not yet cleaned these up**.

We only cleaned:

```
efibootmgr
```

NVRAM entries.

That removed:

```
Boot0000 linux
```

but it doesn't establish that the Acer BIOS's:

```
Select an UEFI File as trusted for executing
```

list has been modified.

We still need to determine how your particular Acer firmware stores/removes those trusted-file entries.

And importantly, **we should not clear the entire Secure Boot key database just to get rid of them**, because your firmware currently contains the Microsoft and Acer trust keys that we want to preserve.

---

# 29. The current logical plan

We are now at this point:

```
                     ┌──────────────────────────┐
                     │ Acer Secure Boot disabled │
                     └────────────┬─────────────┘
                                  │
                                  ▼
                  ┌──────────────────────────────┐
                  │ Normal Limine EFI             │
                  │ limine_x64.efi                │
                  │ ✓ signed                      │
                  └──────────────────────────────┘
                                  │
                                  ▼
                  ┌──────────────────────────────┐
                  │ Fallback EFI                  │
                  │ BOOTX64.EFI                   │
                  │ ✓ signed                      │
                  └──────────────────────────────┘
                                  │
                                  ▼
                  ┌──────────────────────────────┐
                  │ Acer + Microsoft keys intact │
                  └──────────────────────────────┘
                                  │
                                  ▼
                    NEXT: Enable Secure Boot
                                  │
                                  ▼
                     Does Acer accept Limine?
                           /             \
                         YES              NO
                         │                 │
                         ▼                 ▼
                  Verify Linux       Keep Secure Boot
                  + Windows          disabled and diagnose
```

---

# 30. One particularly important detail

Your Limine configuration currently contains:

```
BOOT_ORDER="*, *lts, *fallback, Snapshots"
```

So your Limine menu has a fallback-related entry, but that's not the same thing as saying the physical EFI fallback file is automatically updated every time.

The actual `limine-install` condition we inspected is what matters for:

```
/boot/EFI/BOOT/BOOTX64.EFI
```

and with your current configuration, the existence of that file means normal Limine operation won't continually replace it unless fallback installation is explicitly requested. Pasted text.txtTXT

---

# Bottom line

**The original problem was that Acer Secure Boot was tied to manually trusting changing EFI files.**

We've now:

- identified that you're using **Limine 12.9.0**;
- confirmed `/boot` is your ESP;
- identified the two relevant EFI binaries;
- removed the duplicate `Boot0000 linux` NVRAM entry;
- preserved your **Acer + Microsoft Secure Boot keys**;
- confirmed the normal Limine EFI is signed;
- discovered CachyOS/Limine automatically signs the normal Limine EFI;
- discovered that the fallback EFI isn't automatically signed;
- manually signed the fallback EFI;
- verified the fallback signature;
- verified the normal Limine signature again;
- confirmed `sbctl` is installed and functioning;
- avoided `sbctl reset`, `enroll-keys`, `rotate-keys`, and BIOS key wiping;
- confirmed Windows Boot Manager remains registered.

### Current status:

```
Limine 12.9.0                         ✓
Normal Limine EFI signed              ✓
Fallback BOOTX64.EFI signed          ✓
Windows Boot Manager preserved       ✓
Duplicate NVRAM entry removed        ✓
Acer/Microsoft Secure Boot keys      ✓ preserved
Secure Boot                           ✗ currently disabled
Acer trusted-file list                ? not yet cleaned/tested
```

So **we are now at the point where the next meaningful test is enabling Secure Boot**, rather than doing more random `sbctl` commands.