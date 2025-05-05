# stow commands (basics) 

GNU Stow helps manage dotfiles and other configs by creating **symlinks** from your `~/dotfiles` to your actual system folders.

---

##  1. Basic Usage

```bash
cd ~/dotfiles
stow zsh
```

This symlinks everything from `dotfiles/zsh/` into `~`.

For example:

```
~/dotfiles/zsh/.zshrc → ~/.zshrc
```

---

## 🔁 2. Restow (update links after changes)

If you make changes inside your dotfiles, re-run:

```bash
stow zsh
```

Stow will skip existing correct symlinks and only update what's needed.

---

## ❌ 3. Unstow (remove symlinks)

```bash
stow -D zsh
```

This removes all symlinks that `stow zsh` previously created.

---

## 🚫 4. Handle Conflicts (e.g., file already exists)

If you see:

```
WARNING! ... since neither a link nor a directory and --adopt not specified
```

Use:

```bash
stow --adopt zsh
```

This moves existing files into the stow folder and creates symlinks cleanly.

---

## 📁 5. Stow into a Custom Directory (e.g., `~/walls`)

If you have this structure:

```
~/dotfiles/walls/
├── wall1.jpg
├── folder1/
```

And you want everything to appear inside `~/walls/` (not `~/`), use:

```bash
mkdir -p ~/walls
cd ~/dotfiles
stow --target=~/walls walls
```

This creates symlinks like:

```
~/walls/wall1.jpg     → ~/dotfiles/walls/wall1.jpg
~/walls/folder1/      → ~/dotfiles/walls/folder1/
```

To undo:

```bash
stow -D --target=~/walls walls
```

---

for more - [stow docs](https://www.gnu.org/software/stow/manual/stow.html)
