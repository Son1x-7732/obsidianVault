I am troubleshooting Secure Boot on an Acer Nitro 5 running CachyOS/Arch Linux with Limine 12.9.0.

GOAL:
I want Secure Boot enabled without having to repeatedly use Acer BIOS "Select an UEFI File as trusted for executing" whenever Limine updates. I also eventually want to clean stale Acer trusted-UEFI-file entries, without wiping Microsoft/Acer Secure Boot keys.

CURRENT SYSTEM:
- CachyOS / Arch
- Limine 12.9.0
- ESP mounted at /boot
- Windows is installed and must remain bootable
- Secure Boot currently DISABLED
- Setup Mode DISABLED

CURRENT sbctl status:
Installed: ✓ sbctl is installed
Owner GUID: 9cd63ca9-faec-4a6b-8653-1b88b36de4ef
Setup Mode: ✓ Disabled
Secure Boot: ✗ Disabled
Vendor Keys: microsoft builtin-db builtin-db builtin-db builtin-KEK builtin-PK

CURRENT ENROLLED KEYS:
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

IMPORTANT:
Do NOT wipe/reset Secure Boot keys.
Do NOT use sbctl reset, sbctl rotate-keys, or sbctl enroll-keys unless explicitly determined necessary.
Preserve Microsoft/Acer keys because Windows must remain bootable.

CURRENT EFI NVRAM:
BootCurrent: 0002
BootOrder: 0002,0003,2001,2002,2003

Boot0002* Limine
File: /boot/EFI/limine/limine_x64.efi

Boot0003* Windows Boot Manager
File: /boot/EFI/Microsoft/Boot/bootmgfw.efi

Previously there was:
Boot0000* linux
File: /boot/EFI/limine/limine_x64.efi

We removed the duplicate with:
sudo efibootmgr -b 0000 -B

CURRENT EFI FILES:
sudo ls -lh /boot/EFI/BOOT/BOOTX64.EFI /boot/EFI/limine/limine_x64.efi

Output:
-rwx------ 1 root root 340K Aug  3 16:02 /boot/EFI/BOOT/BOOTX64.EFI
-rwx------ 1 root root 371K Sep 17 09:23 /boot/EFI/limine/limine_x64.efi

There is also:
/boot/EFI/limine/limine_x64.bak
It is a backup and is NOT a valid EFI binary, so leave it alone.

SIGNING STATUS:
Normal Limine:
sudo sbctl verify --debug /boot/EFI/limine/limine_x64.efi

Result:
✓ /boot/EFI/limine/limine_x64.efi is signed

Fallback BEFORE signing:
sudo sbctl verify /boot/EFI/BOOT/BOOTX64.EFI

Result:
✗ /boot/EFI/BOOT/BOOTX64.EFI is not signed

Then:
sudo sbctl sign /boot/EFI/BOOT/BOOTX64.EFI

Result:
✓ Signed /boot/EFI/BOOT/BOOTX64.EFI

Then verified:
sudo sbctl verify /boot/EFI/BOOT/BOOTX64.EFI

Result:
✓ /boot/EFI/BOOT/BOOTX64.EFI is signed

Therefore CURRENTLY:
- /boot/EFI/limine/limine_x64.efi → signed ✓
- /boot/EFI/BOOT/BOOTX64.EFI → signed ✓

IMPORTANT LIMINE DISCOVERY:
The installed /usr/lib/limine/limine-common-functions contains sb_sign() which calls:
sbctl sign "$file_path"

Journal previously showed:
✓ Signed /boot/EFI/limine/limine_x64.efi

So CachyOS/Limine automatically signs the normal Limine EFI during its update/sync process.

We inspected /etc/limine-entry-tool.conf. It says:

### Default Boot Fallback
### Configure whether Limine should be installed as the default UEFI fallback loader. (yes|no|"")
#ENABLE_LIMINE_FALLBACK=""

It also documents that with Secure Boot enabled, the default fallback is not signed automatically by limine-update, and recommends disabling ENABLE_LIMINE_FALLBACK, running 'limine-install --fallback' once, and then signing the fallback manually.

CURRENT /etc/default/limine:
ESP_PATH="/boot"
KERNEL_CMDLINE[default]+="quiet nowatchdog splash rw rootflags=subvol=/@ root=UUID=db85df3f-349f-4ac3-954a-0c2653d9fe3b acpi_backlight=native"
BOOT_ORDER="*, *lts, *fallback, Snapshots"

No active ENABLE_LIMINE_FALLBACK=yes setting exists.

We inspected /usr/bin/limine-install. Relevant behavior:
- --fallback / -f sets SET_LIMINE_AS_FALLBACK=yes
- fallback is updated when:
  ENABLE_LIMINE_FALLBACK=yes
  OR SET_LIMINE_AS_FALLBACK=yes
  OR fallback file does not exist and ENABLE_LIMINE_FALLBACK is empty.
- update_limine_fallback copies /usr/share/limine/BOOTX64.EFI to /boot/EFI/BOOT/BOOTX64.EFI when needed.

Therefore we have NOT changed /etc/default/limine yet.

sbctl version:
sudo sbctl --help showed these commands:
bundle
create-keys
enroll-keys
export-enrolled-keys
generate-bundles
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

There is NO "sbctl list-keys" command in this version.

sbctl setup --print-state --json returned:
{
  "installed": true,
  "landlock": true
}

DO NOT repeat nonexistent commands like sbctl list-keys or sbctl verify --verbose.
For verbose verify, this version uses:
sudo sbctl verify --debug <file>

CURRENT POSITION:
The Linux-side EFI signing preparation is complete enough to test.
The next major step is to enable Secure Boot in Acer BIOS WITHOUT changing/clearing/resetting keys.

Before enabling Secure Boot:
- Do not use sbctl reset
- Do not use sbctl enroll-keys
- Do not use sbctl rotate-keys
- Do not clear Secure Boot keys in BIOS

If Secure Boot is enabled and CachyOS boots, run:
sudo sbctl status

Expected:
Secure Boot: ✓ Enabled

If Acer refuses to boot Limine / reports Secure Boot violation:
- do NOT start changing keys
- disable Secure Boot again
- report the exact BIOS error/message

SECONDARY UNRESOLVED ISSUE:
The Acer BIOS "Select an UEFI File as trusted for executing" entries have NOT yet been cleaned.
The efibootmgr cleanup only removed Boot0000 and does NOT prove that Acer's trusted-file records were removed.
Need investigate Acer Nitro 5 firmware behavior separately.