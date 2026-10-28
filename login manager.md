Since you're running Arch with Niri and a minimal Noctelia-style setup, I'd focus on login managers that stay out of the way, look clean, and don't introduce unnecessary desktop dependencies.

Your two complaints are useful clues:

- tuigreet: the initial left-to-center movement sounds like a layout or terminal redraw issue. Changing graphics drivers probably won't help if it's happening inside a TTY-based greeter.
    
- ly: its background-color setting is not as straightforward as its dialog colors suggest. The terminal's background palette and Ly's own interface colors are separate things. There is a workaround, so you may not need to abandon it.
    
    ![](https://www.google.com/s2/favicons?domain=https://github.com&sz=32)
    
    GitHub
    
    +1
    

## The best minimal Linux login managers to consider

![GitHub - rharish101/ReGreet: Clean and customizable greeter for greetd · GitHub](https://images.openai.com/static-rsc-4/Iwtl_QaoTbFQJXXTjKlAOJ8AW_RKIYCdBB8c3M8xd4GmodmspxHmb2CUJFU0kmu7UCxPmLW0KYn6xbD7Y9s58BTBD549yMt1r-Iu5Nu_b_4ioMJgorOY_lcHVM05nPET7d2HZTtv4QJgNUulfA4cFku3iG4L-saQTxrfAYOTWpM?purpose=inline)

## 1. ReGreet

My top pick for customization

A clean graphical login screen with a user/session selector, optional background image, clock, GTK theming, and custom CSS. It works with Wayland compositors, including Niri.

![](https://www.google.com/s2/favicons?domain=https://github.com&sz=32)

GitHub

+1

- Look: modern, minimal, customizable.
    
- Trade-off: needs a small compositor such as Cage to run the greeter, so it's not as lightweight as a TUI.
    
- Arch install: `paru -S greetd-regreet`
    

![Linux Display Managers (LightDM, SLiM, XDM, GDM, SDDM, KDM, Ly): Install and Uninstall Guide | Baeldung on Linux](https://images.openai.com/static-rsc-4/pgLTdpZD3LVOvwDwd886O4iPEOwMbVRQGSZ8gGW4MqCkY5E4vI25oZT3yJTsmF69WR3P9OHPtiutagS4o8Eb6U4HCqBoEkNxi9Pj0Nm-jBP2uCvr9dolgsdgR_PM7GWDv6bB1RmCNN_6aYH11aTyHZCA20GMsXHBc-hdZvP53w0?purpose=inline)

## 2. Ly

Best if you prefer TUI

You've already liked its interface, and I wouldn't give up on it just yet.

- Look: keyboard-driven, compact, no graphical desktop required for the greeter.
    
- Trade-off: the full-screen background color isn't controlled by the same `bg` option that styles its dialog.
    
- Fix: set the TTY's ANSI background palette before Ly starts and restore it when the terminal resets. A documented workaround uses `ExecStartPre` and `term_reset_cmd`.
    
    ![](https://www.google.com/s2/favicons?domain=https://github.com&sz=32)
    
    GitHub
    
    +1
    

![lemurs - A customizable TUI display/login manager written in Rust. - Terminal Trove](https://images.openai.com/static-rsc-4/yejj4Rd_b5IjWAUH_RtJ9FL0w86hwPb2DkrOrnmTFAGvyPm7t86EPHmeznIAXIpPdaKnf8cAaSvrri3PGDktWdD4T63I1ruVN6zQc5h5B_I-FpJrTpROvoxS9f3KoC_RNasCH7fqIYEsZttWTf2M12DgFf3Bu6PcxoUl0cQtnhE?purpose=inline)

## 3. Lemurs

A customizable Rust-based terminal display manager with configurable layouts, colors, and session scripts.

![](https://www.google.com/s2/favicons?domain=https://github.com&sz=32)

GitHub

- Look: a good alternative if you like Ly but want a different interface.
    
- Trade-off: its configuration and session setup take more work than a plug-and-play graphical greeter.
    
- Arch: [Upstream GitHub project](https://github.com/coastalwhite/lemurs?utm_source=chatgpt.com)
    

![nwg-hello Alternatives - Explore Similar Software | AlternativeTo](https://images.openai.com/static-rsc-4/JTJXBiqERFFmetbDsZeKVQiMDVsMtADDCRtbTaZunucB0KiJtvIyY2escJzQYR_j1rxisyeJ-9wQyswol6jHFWCAPOyej-WvXVYWdebKCuAtBkEk6w4n3pFVbD4O9aAOmSw9oEeF4uvH0Q0z3A6jmol54RicJm1lEpZzEqiewSQ?purpose=inline)

## 4. nwg-hello

A graphical greeter designed with Wayland-oriented setups in mind. It's worth considering if you want a small, conventional login window instead of a terminal UI.

![](https://www.google.com/s2/favicons?domain=https://wiki.archlinux.org&sz=32)

ArchWiki

- Look: straightforward GTK interface.
    
- Trade-off: less compelling than ReGreet if extensive styling is your main goal.
    
- Project: [nwg-hello on GitHub](https://github.com/nwg-piotr/nwg-hello?utm_source=chatgpt.com)
    

![Display Managers | Athena OS](https://images.openai.com/static-rsc-4/hbgCpZ2HRNyxiV6zklgLkMc61SSan3FQBTV29vUwO-ab4_CJOiV31mcmo8pQKtEi9GUJKW4lm0USCpADYSUp5X6rJzV45PI5YFUD-_-yIXr9lg9UxdTpzV3eGWnmKTHrtfoLyxiI-kUgqcRd_FwybGAOYpHR2aPqIXmfZWFcZfo?purpose=inline)

## 5. emptty

A simple CLI display manager that suits users who want a straightforward login flow without a graphical greeter.

![](https://www.google.com/s2/favicons?domain=https://wiki.archlinux.org&sz=32)

ArchWiki

- Look: functional and terminal-oriented.
    
- Trade-off: less of a visual-customization choice than ReGreet or Ly.
    
- Project: [emptty on GitHub](https://github.com/tvrzna/emptty?utm_source=chatgpt.com)
    

## Which one should you actually choose?

|Your priority|My recommendation|
|---|---|
|Minimal TUI, keyboard-driven|Ly, with the background fix|
|Graphical login with full theme control|ReGreet + greetd + Cage|
|A different terminal-style interface|Lemurs|
|The simplest possible login|emptty|

For your Niri setup, I'd choose between Ly and ReGreet. If your ideal login is a plain, beautifully colored terminal with no graphical elements, stick with Ly. If you want a polished login screen with a background matching your Noctelia/Nocturne aesthetic, choose ReGreet.

## Fixing Ly's background color

The important detail is that Ly's `bg` setting affects its UI elements, not necessarily the entire TTY background. The workaround is to change the Linux console palette itself.

![](https://www.google.com/s2/favicons?domain=https://gist.github.com&sz=32)

GitHub

+1

For example, the underlying palette can be set using this escape sequence:

```
printf '\e]P0FFC5C4\e]P7FFFFFF\ec'
```

Here, `FFC5C4` is a pale pink background color and `FFFFFF` is white. You can replace those hex values with your own colors.

Don't run this as a permanent configuration change yet. It is a diagnostic example; making it persist through Ly startup, logout, and terminal resets requires configuring the Ly systemd service and its terminal reset command appropriately. Prefer a systemd drop-in over editing the packaged service directly.

## Fixing the tuigreet alignment snap

Before abandoning tuigreet, I'd test a fixed layout width and remove any unnecessary layout padding. Its layout is configurable, and you can use a configuration file at `/etc/tuigreet/config.toml`.

![](https://www.google.com/s2/favicons?domain=https://github.com&sz=32)

GitHub

+1

If the prompt still jumps, I would investigate the TTY's initial font and display geometry rather than changing graphics drivers again.

One question before I recommend your final setup: which login style do you want?

1. Login interface

Pure TUI — Ly/Lemurs, no graphical login window

Minimal graphical login — ReGreet or similar

Either, as long as it looks clean

2. Background

A solid custom color

A wallpaper matching my desktop

Pure black, with simple text

3. Most important

As lightweight as possible

As customizable as possible

Reliable startup and no visual glitches

Find my best setup


[Web search](chatgpt-system-hint://search) find me the best login managers.  
tried tuigreet but the initial dialog box starts from the left and then aligns to center in a snap but that bugs me off and many driver changes didn't seem to solve the problem  
tried ly loved it but couldn't change the color of its background. any way to solve that as i was unable to achieve so after many trials of color tweaking and settings.  
what are other possible option minimal but best also


ask for how to fix this 12 o clock temporary night light disable thing....