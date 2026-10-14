curl -O https://raw.githubusercontent.com/Son1x-7732/arch-deploy/refs/heads/main/install.sh
curl -O https://raw.githubusercontent.com/Son1x-7732/arch-deploy/refs/heads/main/chroot_setup.sh

2. Make them executable: 
	chmod +x install.sh chroot_setup.sh

3. Run the master script:
	./install.sh

# Building the Perfect Minimal Status Bar (Hyprland + Waybar)

This guide breaks down the process used to create a custom, highly functional Waybar configuration for Hyprland.

## Prerequisites

1.  **Terminal Emulator:** The creator uses Ghostty.
2.  **Code Editor:** Neovim is used, but VS Code is suggested as an alternative (`yay -S code`).

## Step 1: Install and Set Up Waybar

1.  **Install Waybar:** Run `yay -S waybar`.
2.  **Create Configuration Directory:** Run `mkdir -p ~/.config/waybar`.
3.  **Navigate to Directory:** `cd ~/.config/waybar`.
4.  **Create Configuration Files:** Open your editor and create `config.jsonc`.
5.  **Get Base Configuration:** Grab a default configuration. The creator copies the minimal example from the Waybar configuration wiki.
6.  **Adapt for Hyprland:** Replace all instances of `sway` in the base configuration with `hyprland` (e.g., `sway/workspaces` becomes `hyprland/workspaces`).
7.  **Auto-Reload Script:**
    * Install `inotify-tools`: `yay -S inotify-tools`.
    * Create a script `auto_reload.sh` in the waybar directory to restart Waybar on configuration changes:
        ```bash
        while inotifywait -e close_write ~/.config/waybar; do
            killall -SIGUSR2 waybar
        done
        ```
    * Make it executable: `chmod +x auto_reload.sh`.
    * Run it: `./auto_reload.sh`.

## Step 2: Basic Styling (CSS)

1.  **Create Style File:** Create `style.css` in the `~/.config/waybar` directory.
2.  **Get Base CSS:** Copy the default style from the Waybar styling wiki into `style.css`.
3.  **Install Font:** Install a Nerd Font for icons (e.g., `yay -S ttf-jetbrains-mono-nerd`). Update `style.css` to use the font family `JetBrainsMono Nerd Font Propo`.
4.  **Define Color Variables:** In `style.css`, define `@define-color` variables at the top to maintain a consistent color scheme.
5.  **Style the Bar Container (`window#waybar`):** Set the background color (using the defined variables) and adjust opacity if desired. Remove borders or shadows as needed.
6.  **Style Workspaces (`#workspaces button`):**
    * Set padding, background to transparent, border-radius, and text color.
    * Style active workspaces (`#workspaces button.active`) with a highlight background and bolder text.
    * Style urgent workspaces (`#workspaces button.urgent`) with a distinct warning color.

## Step 3: Adding and Styling Modules

### Right Side Modules

1.  **Battery:** Copy the battery module configuration from the wiki. Configure the format (e.g., `{capacity}% {icon}`) and style it in `style.css` with a background color, padding, and border-radius.
2.  **Bluetooth:** Copy the Bluetooth configuration. Set the format to show an icon, hide when disabled, and show connection count. Add styling to `style.css`.
3.  **System Info (CPU, Memory, Network):**
    * **CPU:** Configure to show usage percentage and update interval.
    * **Memory:** Configure to show percentage and tooltips.
    * **Network:** Configure format based on connection type (wifi, ethernet, disconnected).
    * **Styling:** Add these modules to the existing selector block in `style.css` that styles the right-side modules to give them a uniform look.

### Center Module (Clock)

1.  **Clock Format:** Use the format string syntax (e.g., `%a %d %b %I:%M %p`) to display abbreviated day, date, month, and time.
2.  **Calendar Tooltip:** Add a `tooltip-format` to show a small calendar on hover, highlighting the current day.

### Grouping and Sliders (Audio)

1.  **PulseAudio Module:** Add the standard `pulseaudio` module showing volume percentage and icons based on mute status/volume level.
2.  **PulseAudio Slider:** Add the `pulseaudio/slider` module. Style the slider trough and highlight color in `style.css`, and hide its default icon.
3.  **Grouping:** Group the audio icon and slider horizontally by defining a `"group/volume"` in `config.jsonc` that contains both modules as an array. Update the `modules-right` list to use the group instead of the individual modules. Style the group container in `style.css`.

## Step 4: Custom Media Module (Scrolling Text)

1.  **Prerequisites:** Install `playerctl` for media info. Create subdirectories `custom_modules/media` inside the waybar config folder.
2.  **Base Script:** Create `media-now-playing.sh` to fetch title and artist using `playerctl`.
3.  **Scrolling (zscroll):**
    * Install python dependencies: `yay -S python-setuptools zscroll`.
    * Update `media-now-playing.sh` to use `zscroll` to wrap and scroll the `playerctl` output with a defined length and delay. Ensure the script loops continually.
4.  **Add Custom Module:** Define a `custom/media_now_playing` module in `config.jsonc` that executes the script (`exec`). Set `escape` to `true` and add it to `modules-left`.

## Step 5: Media Animation & Grouping

1.  **Animation Script:** Create `media-animation.sh`. This script uses a while loop to cycle through text-based animation frames (e.g., lines resembling a visualizer) while media is playing.
2.  **Time Script:** Create `media-time.sh` to output the current playback position and total length using `playerctl`.
3.  **Configure Custom Modules:** Add entries for `custom/media_animation` and `custom/media_time` in `config.jsonc`. Set an update `interval` of `1` for the time script.
4.  **Group the Media Modules:** Create a `"group/media"` module that horizontally groups the animation, now playing text, and time output. Update `modules-left` to use the group.
5.  **Styling the Group:** Add CSS rules in `style.css` targeting the media group to adjust margins, font sizes, and specific colors for the animation and time elements.

## Step 6: Adding Interactivity (Click Handlers)

Add `on-click` commands to modules in `config.jsonc` to execute applications:

* **Clock:** `exec open chromium --app="google calendar URL"`
* **Bluetooth:** `exec ghostty --title="bluetui" -e bluetui`
* **CPU:** `exec ghostty --title="btop" -e btop`
* **Memory:** `exec ghostty --title="htop" -e htop`
* **Network:** `exec nm-connection-editor`
* **PulseAudio:** `exec pavucontrol`
* **Media Now Playing:**
    * `on-click`: `playerctl play-pause`
    * `on-click-right`: `playerctl next`
    * `on-click-middle`: `playerctl previous`

## Step 7: Finalize Startup

Ensure Waybar and necessary daemons start automatically. Open `~/.config/hypr/hyprland.conf` and add `exec-once` commands to start `waybar` and `playerctld`.