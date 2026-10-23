You need **all three, but only once**, and in this order. They're one-time setup for linking your local repo to GitHub:

1. `git remote add origin ...` tells git where the GitHub repo lives.
2. `git branch -M main` renames your branch to `main`.
3. `git push -u origin main` uploads everything for the first time. The `-u` remembers the link, so later you only need `git push`.

Before you run them:

- **Create the empty repo on GitHub first** (no README, no .gitignore, since those would conflict with your local commits).
- **Replace `username`** with your actual GitHub username.
- **Make sure you've committed.** You already did with `git commit`, so you're fine.

**Authentication:** with an `https://` URL, GitHub won't accept your password. It asks for a personal access token, which is annoying. The easier route is the GitHub CLI:

```bash
sudo pacman -S github-cli
gh auth login
```

Follow the prompts, and `git push` will then work over HTTPS without extra steps.

## Making it quick afterwards

Because stow uses symlinks, any edit you make to `~/.config/niri/config.kdl` is already an edit inside your repo. So the routine is just:

```bash
cd ~/dotfiles
git add . && git commit -m "update configs" && git push
```

To avoid typing that, add this to your `~/.zshrc`:

```bash
dotsync() {
  cd ~/dotfiles || return
  git status --short
  git add .
  git commit -m "${1:-update configs}" && git push
}
```

Then run `source ~/.zshrc` once. After that:

```bash
dotsync                      # uses the default message
dotsync "tweak niri keybinds"  # custom message
```

The function prints `git status --short` before committing, so you can see what's about to go up. Use that glance to make sure no secrets or junk files slip in, since `git add .` takes everything.

Since your `.zshrc` is a config file too, it's a good candidate for stow next, with a `zsh/` package holding `.zshrc`.


You need **all three, but only once**, and in this order. They're one-time setup for linking your local repo to GitHub:

1. `git remote add origin ...` tells git where the GitHub repo lives.
2. `git branch -M main` renames your branch to `main`.
3. `git push -u origin main` uploads everything for the first time. The `-u` remembers the link, so later you only need `git push`.

Before you run them:

- **Create the empty repo on GitHub first** (no README, no .gitignore, since those would conflict with your local commits).
- **Replace `username`** with your actual GitHub username.
- **Make sure you've committed.** You already did with `git commit`, so you're fine.

**Authentication:** with an `https://` URL, GitHub won't accept your password. It asks for a personal access token, which is annoying. The easier route is the GitHub CLI:

```bash
sudo pacman -S github-cli
gh auth login
```

Follow the prompts, and `git push` will then work over HTTPS without extra steps.

## Making it quick afterwards

Because stow uses symlinks, any edit you make to `~/.config/niri/config.kdl` is already an edit inside your repo. So the routine is just:

```bash
cd ~/dotfiles
git add . && git commit -m "update configs" && git push
```

To avoid typing that, add this to your `~/.zshrc`:

```bash
dotsync() {
  cd ~/dotfiles || return
  git status --short
  git add .
  git commit -m "${1:-update configs}" && git push
}
```

Then run `source ~/.zshrc` once. After that:

```bash
dotsync                      # uses the default message
dotsync "tweak niri keybinds"  # custom message
```

The function prints `git status --short` before committing, so you can see what's about to go up. Use that glance to make sure no secrets or junk files slip in, since `git add .` takes everything.

Since your `.zshrc` is a config file too, it's a good candidate for stow next, with a `zsh/` package holding `.zshrc`.