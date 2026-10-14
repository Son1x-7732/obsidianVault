
A clean, reproducible checklist for setting up **KDE Plasma** with audio, development tools, theming, and system tweaks.

---

## 📦 Package Installation

Install all essential applications and fonts using **paru**:

```bash
paru -S \
  kitty \
  reaper \
  papirus-theme-icon-git \
  sfizz-vst3 \
  ttf-jetbrains-mono-nerd \
  ttf-martian-mono-nerd \
  zen-browser-bin \
  github-cli \
  zed \
  qbittorrent-enhanced \
  pipewire-jack \
  helium-browser
```

---

## 🔄 System Update & Repair

Always keep the system clean and synced:

```bash
paru -Syu
```

> This also helps resolve **broken packages** or partial upgrades.

---

## ⌨️ Shortcut Configuration

> *(Configured manually via KDE System Settings)*

* Global shortcuts
* Application shortcuts
* Window management shortcuts

---

## 🌱 Git Configuration

Set global Git identity and editor:

```bash
git config --global user.name "son1x"
git config --global user.email "162944716+Son1x-7732@users.noreply.github.com"
git config --global core.editor "micro"
```

Verify configuration:

```bash
git config --list
```

---

## 🔐 GitHub Authentication

Authenticate GitHub CLI:

```bash
gh auth login
```

Recommended options:

* Protocol: **HTTPS**
* Authentication: **Browser login**

---

## 🔆 Brightness Slider Fix (Limine)

Edit Limine configuration:

```bash
sudo micro /etc/default/limine
```

### Recommended Configuration

```ini
# Timeout in seconds before the default entry is booted
TIMEOUT=5

# Path to EFI System Partition
ESP_PATH="/boot"

# Default Kernel Parameters
KERNEL_CMDLINE[default]="quiet nowatchdog splash rw rootflags=subvol=/@ root=UUID=7543056f-2f3f-4ee8-96cb-5911bbb6ef60 acpi_backlight=native"
```

Apply changes:

```bash
sudo limine-mkinitcpio
```

---

## 🎨 SDDM Theme Setup (Astronaut)

Install the theme:

```bash
paru -S sddm-astronaut-theme
```

Edit SDDM config:

```bash
sudo micro /etc/sddm.conf
```

```ini
[Theme]
Current=sddm-astronaut-theme
```

---

## 🧩 Astronaut Theme Variants

Edit metadata:

```bash
sudo micro /usr/share/sddm/themes/sddm-astronaut-theme/metadata.desktop
```

### Available Presets

```ini
# Default
ConfigFile=Themes/astronaut.conf

# Cyberpunk
ConfigFile=Themes/cyberpunk.conf

# Jungle / Green
ConfigFile=Themes/jungle.conf
```

#Required
ConfigFile=Themes/jake_the_dog.conf

List all available themes:

```bash
ls /usr/share/sddm/themes/sddm-astronaut-theme/Themes
```

---

## 🧪 Test SDDM Theme (No Reboot Needed)

```bash
sddm-greeter-qt6 --test-mode --theme /usr/share/sddm/themes/sddm-astronaut-theme/
```

---

## 🎧 Audio: PipeWire + JACK (Reaper)

Ensure JACK support is installed:

```bash
paru -S pipewire-jack
```

Reboot recommended after installation.
