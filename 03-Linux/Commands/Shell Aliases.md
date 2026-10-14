
## Navigation

```bash
alias ..='cd ..'
alias ...='cd ../..'

alias ll='eza -al --color=always --group-directories-first --icons'
alias ls='eza -al --color=always --group-directories-first --icons'
alias lt='eza -aT --color=always --group-directories-first --icons'
```

---

## Package Management

```bash
alias update='paru -Syu'
alias install='paru -S'
alias search='paru -Ss'
alias delete='paru -Rns'

alias cleanup='paru -Rns $(pacman -Qtdq)'
alias cleancache='paru -Sc'

alias fixpacman="sudo rm /var/lib/pacman/db.lck"
alias mirror="sudo cachyos-rate-mirrors"

alias findpkg="paru -Qs"

alias rip="expac --timefmt='%Y-%m-%d %T' '%l\t%n %v' | sort | tail -200 | nl"
```

---

## Config Shortcuts

```bash
alias zshconf="micro ~/.zshrc"
alias kittyconf="micro ~/.config/kitty/kitty.conf"

alias hyprconf="micro ~/.config/hypr/hyprland.conf"
alias niriconf="micro ~/.config/niri/config.kdl"

alias waybarjson="micro ~/.config/waybar/config.jsonc"
alias waybarcss="micro ~/.config/waybar/style.css"

alias reload="source ~/.zshrc"
```

---

## GitHub

```bash
alias gstatus='gh status'
alias gclone='gh repo clone'
alias gcreate='gh repo create'
```

---

## Utilities

```bash
alias tb='nc termbin.com 9999'
alias fixfonts="fc-cache -fv"
```

---

## Safety

```bash
alias cp="cp -iv"
alias mv="mv -iv"
alias rm="rm -v"
```

---

## Dotfiles

```bash
alias config='/usr/bin/git --git-dir=/home/zen0/.dotfiles/ --work-tree=/home/zen0'
```

```bash
dotsync() {
    /usr/bin/git --git-dir=$HOME/.dotfiles/ --work-tree=$HOME add -u
    /usr/bin/git --git-dir=$HOME/.dotfiles/ --work-tree=$HOME commit -m "Update: $(date)"
    /usr/bin/git --git-dir=$HOME/.dotfiles/ --work-tree=$HOME push
}
```

---

## Helpers

```bash
backup() {
    cp -iv "$1" "$1.bak"
}
```
