## Prequisite

```
sudo pacman -S --needed zsh micro bat eza fzf pkgfile git github-cli expac trash-cli zoxide fd zsh-autosuggestions zsh-syntax-highlighting zsh-history-substring-search
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ~/powerlevel10k
sudo pkgfile --update
chsh -s /usr/bin/zsh
```

## ZSHRC config file

```
# ==========================================
#  ZSH CONFIG
# ==========================================

# --- 1. INSTANT PROMPT (must be first, no other output before this) ---
# Enables the prompt to appear instantly while plugins load in the background
if [[ -r "${XDG_CACHE_HOME:-$HOME/.cache}/p10k-instant-prompt-${(%):-%n}.zsh" ]]; then
  source "${XDG_CACHE_HOME:-$HOME/.cache}/p10k-instant-prompt-${(%):-%n}.zsh"
fi

# --- 2. ENVIRONMENT & SETTINGS ---
DISABLE_AUTO_TITLE="true"
export EDITOR='micro'
export VISUAL='micro'
export TERMINAL='kitty'
export MANPAGER="sh -c 'col -bx | bat -l man -p'"
export PATH="$HOME/.local/bin:$PATH"

# History Settings
HISTFILE=~/.zsh_history
HISTSIZE=10000
SAVEHIST=10000
setopt APPEND_HISTORY
setopt INC_APPEND_HISTORY
setopt HIST_IGNORE_DUPS
setopt HIST_FIND_NO_DUPS
setopt SHARE_HISTORY
setopt BANG_HIST

# --- AUTOCOMPLETION ENGINE (cached for faster startup) ---
autoload -Uz compinit
if [[ -n ${ZDOTDIR:-$HOME}/.zcompdump(#qN.mh+24) ]]; then
  compinit
else
  compinit -C
fi
zstyle ':completion:*' menu select

# --- 3. PLUGINS ---
# A. Theme (local clone)
source ~/powerlevel10k/powerlevel10k.zsh-theme

# B. Syntax Highlighting (fish-like)
[[ -f /usr/share/zsh/plugins/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh ]] && source /usr/share/zsh/plugins/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh

# C. Autosuggestions (ghost history)
[[ -f /usr/share/zsh/plugins/zsh-autosuggestions/zsh-autosuggestions.zsh ]] && source /usr/share/zsh/plugins/zsh-autosuggestions/zsh-autosuggestions.zsh
ZSH_AUTOSUGGEST_HIGHLIGHT_STYLE='fg=242'

# D. History Substring Search
[[ -f /usr/share/zsh/plugins/zsh-history-substring-search/zsh-history-substring-search.zsh ]] && source /usr/share/zsh/plugins/zsh-history-substring-search/zsh-history-substring-search.zsh

# E. "Command Not Found" handler
source /usr/share/doc/pkgfile/command-not-found.zsh

# F. FZF Integration (fuzzy finder for history/files)
[ -f /usr/share/fzf/key-bindings.zsh ] && source /usr/share/fzf/key-bindings.zsh
[ -f /usr/share/fzf/completion.zsh ] && source /usr/share/fzf/completion.zsh

# G. Zoxide (smarter cd, learns frequent dirs)
eval "$(zoxide init zsh)"

# --- 4. KEYBINDINGS ---
bindkey -e
bindkey '^[[A' history-substring-search-up
bindkey '^[[B' history-substring-search-down

# --- 5. HELPER FUNCTIONS ---
function backup() {
    cp -iv "$1" "$1.bak"
}

# --- 6. ALIASES ---
# > Navigation
alias ..='cd ..'
alias ...='cd ../..'
alias cd='z'
alias ll='eza --icons --git --long --header --group-directories-first'
alias ls='eza --icons --git'
alias lt='eza --icons --tree'

# > Package Management (Pacman)
alias update='sudo pacman -Syu'
alias install='sudo pacman -S'
alias search='pacman -Ss'
alias delete='sudo pacman -Rns'
alias cleanup='sudo pacman -Rns $(pacman -Qtdq)'
alias cleancache='sudo pacman -Sc'
alias fixpacman="sudo rm /var/lib/pacman/db.lck"
alias mirror="sudo cachyos-rate-mirrors"
alias findpkg="pacman -Qs"
alias rip="expac --timefmt='%Y-%m-%d %T' '%l\t%n %v' | sort | tail -200 | nl"

# > Config Shortcuts
alias zshconf="micro ~/.zshrc"
alias kittyconf="micro ~/.config/kitty/kitty.conf"
alias niriconf="micro ~/.config/niri/config.kdl"
alias reload="source ~/.zshrc"

# > Utils
alias tb='nc termbin.com 9999'
alias gstatus='gh status'
alias gclone='gh repo clone'
alias gcreate='gh repo create'
alias fixfonts="fc-cache -fv"

# > Safety
alias cp="cp -iv"
alias mv="mv -iv"
alias rm="trash-put -v"
alias rmrf="/usr/bin/rm -rf"

# --- 7. LOAD THEME LAYOUT ---
[[ ! -f ~/.p10k.zsh ]] || source ~/.p10k.zsh

# --- 8. BARE REPO DOTFILES ---
alias config='/usr/bin/git --git-dir=$HOME/.dotfiles/ --work-tree=$HOME'
# Manual dotsync preferred over automated push, to avoid pushing broken
# configs or sensitive data. Uncomment to automate:
# dotsync() {
#    /usr/bin/git --git-dir=$HOME/.dotfiles/ --work-tree=$HOME add -u
#    /usr/bin/git --git-dir=$HOME/.dotfiles/ --work-tree=$HOME commit -m "Update: $(date)"
#    /usr/bin/git --git-dir=$HOME/.dotfiles/ --work-tree=$HOME push
# }

```