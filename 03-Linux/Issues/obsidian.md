### Step 1: The Internal Obsidian Setting

Obsidian is explicitly coded to hide the OS-level borders so it can draw its own seamless interface. We need to disable this.

1. Open **Obsidian**.
    
2. Go to **Settings** (the gear icon).
    
3. Navigate to the **Appearance** tab.
    
4. Scroll down to the **Advanced** section.
    
5. Find **Window frame style** and change it from `Hidden` to **`Native`**.
    
6. Restart Obsidian.
    

_(Just doing this might instantly fix the border and allow Niri's focus ring to wrap around it perfectly!)_

### Step 2: Force Native Wayland (The Electron Fix)

If Obsidian is still acting weird or looks slightly blurry, it is likely falling back to XWayland. XWayland windows often fail to communicate their exact geometric boundaries to Niri, causing focus-ring glitches and panel sticking.

You can force Obsidian to run as a 100% native Wayland application by giving it an Ozone flag configuration file.

Run this in your terminal:

Bash

```
micro ~/.config/obsidian-flags.conf
```

Paste these exact lines into the file:

Plaintext

```
--enable-features=UseOzonePlatform
--ozone-platform=wayland
--enable-wayland-ime
```

Save and exit. The next time you launch Obsidian, it will boot natively in Wayland, communicating flawlessly with Niri's window geometry rules.

### Step 3: The Niri Fallback Rule

If you ever encounter an app that stubbornly refuses to drop its CSDs even after changing its internal settings, you can use Niri's window rules to physically draw the border _inside_ the app's claimed geometry, ensuring the highlight ring always shows up.

If you ever need it, add this to your Niri config's `window-rule` section:

Code snippet

```
window-rule {
    match app-id="obsidian"
    // Forces the border to draw even if the app tries to hide it
    draw-border-with-background false
}
```

ll