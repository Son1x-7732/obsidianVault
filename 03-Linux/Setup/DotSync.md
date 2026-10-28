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

No, you don't need to re-stow after editing. That's the whole point of using stow.

## Why

`~/.config/niri/config.kdl` isn't a separate copy. It's a symlink (a shortcut) pointing to the real file inside `~/dotfiles/niri/.config/niri/config.kdl`. When you edit it from either path, you're editing the one real file, which is already in your repo. So your routine is just:

```bash
micro ~/.config/niri/config.kdl   # edit it normally
dotsync "tweak niri keybinds"     # commit and push
```

The same applies to kitty, fish, fuzzel and everything else you've stowed.

## When you do need to run stow again

Stow only creates the links. It doesn't sync content. You rerun it when the set of files changes:

|Situation|What to do|
|---|---|
|Edited an existing stowed file|Nothing, just `dotsync`|
|Added a **new file** to an existing package (e.g. a new `.kdl` file you `include`)|Create it inside `~/dotfiles/niri/.config/niri/`, then `stow -R niri`|
|Adding a **new program** (e.g. `btop`)|Move its config into a new package folder, then `stow btop`|
|Setting up on a fresh machine|Clone the repo, then `stow` each package|

`-R` means "restow": it removes the old links and recreates them, so new files get picked up.

## One thing that can go wrong

A few editors save by writing a temporary file and renaming it over the original. That replaces the symlink with a regular file, and from then on your edits no longer land in the repo. micro and nano handle this fine. You can check any time with:

```bash
ls -l ~/.config/niri/config.kdl
```

If it starts with `l` and shows an arrow `->`, it's still a symlink. If it's a plain file, the link broke. Fix it by moving the file back into the repo and restowing:

```bash
mv ~/.config/niri/config.kdl ~/dotfiles/niri/.config/niri/config.kdl
cd ~/dotfiles && stow niri
```

A quick sanity check is that `dotsync` should show your edit in `git status --short`. If you changed a config and git shows nothing, the link is probably broken.