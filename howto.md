# GNU Stow — walkthrough with kitty

Stow keeps your configs in this git repo and puts **symlinks** where apps expect them under `$HOME`.

You edit once, in the repo. The symlink makes `~/.config/kitty/...` point at that same file.

---

## The example: kitty

Your kitty package looks like this:

```
~/Repos/dotfiles/
└── kitty/                          ← the package name
    └── .config/
        └── kitty/
            └── kitty.conf          ← the real file lives here
```

That tree under `kitty/` is meant to **mirror** what you want under home. Stow does not invent paths — it copies the layout.

So this package path:

```
kitty/.config/kitty/kitty.conf
```

is meant to become:

```
~/.config/kitty/kitty.conf
```

---

## Stow it

Always run Stow from the repo root (the folder that *contains* the packages):

```bash
cd ~/Repos/dotfiles
stow kitty
```

What that does:

1. Stow looks at the `kitty/` package.
2. It walks the tree inside it (`.config/kitty/kitty.conf`).
3. For each file, it creates a symlink from `$HOME` into the repo.

After that:

```
~/.config/kitty/kitty.conf
        │
        └──► ~/Repos/dotfiles/kitty/.config/kitty/kitty.conf
```

Check it:

```bash
ls -l ~/.config/kitty/kitty.conf
# should show something like:
# ... -> /home/sahilkr/Repos/dotfiles/kitty/.config/kitty/kitty.conf
```

Kitty now reads the file in your repo through that link. There is only one real file.

---

## Editing

Either path is fine — they are the same inode:

```bash
nano ~/.config/kitty/kitty.conf
# or
nano ~/Repos/dotfiles/kitty/.config/kitty/kitty.conf
```

Then commit when you care about history:

```bash
cd ~/Repos/dotfiles
git add kitty
git commit -m "Tweak kitty config"
git push
```

---

## Preview before changing anything

Dry-run (no writes). `-v` prints what it would do:

```bash
cd ~/Repos/dotfiles
stow -nv kitty
```

Use this when you're unsure, or before restowing after you moved files around.

---

## Unstow (remove the links)

```bash
stow -D kitty
```

That deletes the symlinks under `$HOME`. The files in `~/Repos/dotfiles/kitty/` stay. Kitty just no longer sees your managed config via those links.

---

## Restow

If you renamed or moved things inside the package and the old links are stale:

```bash
stow -R kitty
```

Same as unstow + stow for that package.

---

## Mental model (still kitty)


| Thing        | Meaning                       |
| ------------ | ----------------------------- |
| Package      | Top-level folder: `kitty/`    |
| Contents     | Paths relative to `$HOME`     |
| `stow kitty` | Create links for that package |
| Repo         | Source of truth               |


Stow never “installs” kitty the app. It only manages where `kitty.conf` points.

---

## Same idea for other packages

`niri` and `zsh` work the same way — different trees, same rule:

```
niri/.config/niri/config.kdl  →  ~/.config/niri/config.kdl
zsh/.zshrc                    →  ~/.zshrc
zsh/.p10k.zsh                 →  ~/.p10k.zsh
```

```bash
stow niri kitty zsh    # link several
stow -D niri           # unlink one
```

---

## Adding a new package (same pattern as kitty)

Say you want to manage fastfetch the way you manage kitty.

1. Build the home-shaped tree inside a new package folder:

```bash
cd ~/Repos/dotfiles
mkdir -p fastfetch/.config/fastfetch
cp ~/.config/fastfetch/config.jsonc fastfetch/.config/fastfetch/
```

2. Remove or rename the **original** file/dir under `~/.config/fastfetch/` so Stow can place a symlink (Stow won't overwrite a real file).

3. Link it:

```bash
stow -nv fastfetch   # preview
stow fastfetch
```

You now have the same setup as kitty: one real file in the repo, one symlink in `$HOME`.

---

## Custom target (not `$HOME`)

`stow kitty` defaults to target `~`. Package paths are always **relative to the target**.

To put links under somewhere else (e.g. `~/filesharsh`), set `--target`:

```
~/filesharsh/music/ad.mp3
        │
        └──► ~/Repos/dotfiles/musicdot/music/ad.mp3
```

### 1. Layout in the repo

Paths under the package match paths under the target (not under `$HOME`):

```
~/Repos/dotfiles/
└── musicdot/
    └── music/
        └── ad.mp3
```

### 2. Stow once

```bash
cd ~/Repos/dotfiles
stow --target=$HOME/filesharsh musicdot
```

### 3. After that

Edit or replace the file in the repo — no restow unless the package layout changes. Git tracks the repo copy.

| Command | Target | Links into |
| --- | --- | --- |
| `stow kitty` | `~` | `~/.config/...` |
| `stow --target=$HOME/filesharsh musicdot` | `~/filesharsh` | `~/filesharsh/music/...` |

Rule of thumb: `stow --target=<where the symlink should live> <package>`

