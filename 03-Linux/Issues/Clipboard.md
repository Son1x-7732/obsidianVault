### Fix : Install `wl-clip-persist`

Another major quirk with Wayland is that if you copy text from an app, and then that app loses focus or is closed, Wayland immediately deletes the clipboard data. This often causes the "paste previous" behavior because the new copy was instantly wiped out by Wayland's security rules.

You can fix this by installing a tiny background daemon that tricks Wayland into keeping your clipboard active no matter what.

1. Open your terminal and install the persist tool from the Arch repositories:
    
    Bash
    
    ```
    sudo pacman -S wl-clip-persist
    ```
    
2. Add it to your Niri autostart config (`~/.config/niri/config.kdl`):
    
    Code snippet
    
    ```
    spawn-at-startup "wl-clip-persist" "--clipboard" "regular"
    ```
    
3. Save the config.
    

Between disabling the primary selection watcher and enabling clipboard persistence, `Ctrl+C` will instantly work on the very first try, every single time.