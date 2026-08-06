# CLI File Sorter

A small, dependency-free Python CLI that sorts a messy directory (`~/Downloads`, most
often) into category folders by file extension — `Images/`, `Documents/`, `Archives/`,
and so on. Built out of genuinely wanting my own Downloads folder to stop being a junk
drawer, and used since as a quick tool on every fresh Kali/Ubuntu box.

```
$ sortfiles ~/Downloads --dry-run
Scanning directory: /home/kali/Downloads
--- DRY RUN MODE: No files will be moved ---
[Would Move] nmap_scan.txt -> Documents/nmap_scan.txt
[Would Move] payload.py -> Scripts/payload.py
[Would Move] screenshot.png -> Images/screenshot.png
[Would Move] tools.zip -> Archives/tools.zip

Dry run complete. No changes made.
```

## Design philosophy

The whole script is built around not trusting itself with your files until you've
seen what it plans to do:

1. **Non-recursive.** Only files directly inside the target directory are touched.
   Subfolders (installed software, project directories, `node_modules`, etc.) are left
   completely alone — there's no risk of it reaching into something it shouldn't.
2. **Dry-run first.** `--dry-run` prints exactly what would move, and where, without
   moving anything. I run this by default before ever running the real thing on a
   directory I care about.
3. **Never overwrites.** If `image.jpg` already exists in the destination, the incoming
   file becomes `image_1.jpg`, then `image_2.jpg`, and so on — collisions are resolved
   by renaming, never by silently clobbering an existing file.
4. **Hidden files are left alone.** Anything starting with `.` (dotfiles, `.gitignore`,
   config files that happen to live in the target dir) is skipped entirely.

## How it works

The script is a single file (`sortfiles`) with no external dependencies — just `os`,
`shutil`, `argparse`, and `pathlib` from the standard library.

```python
CATEGORIES = {
    "Images":       ['.jpg', '.jpeg', '.png', '.gif', '.bmp', '.svg', '.webp', '.tiff', '.ico'],
    "Videos":       ['.mp4', '.mkv', '.mov', '.avi', '.flv', '.wmv', '.webm', '.m4v'],
    "Music":        ['.mp3', '.wav', '.aac', '.flac', '.ogg', '.wma', '.m4a'],
    "Documents":    ['.pdf', '.docx', '.doc', '.txt', '.xlsx', '.xls', '.pptx', '.ppt', '.csv', '.md', '.odt'],
    "Archives":     ['.zip', '.rar', '.tar', '.gz', '.7z', '.iso', '.bz2', '.xz'],
    "Scripts":      ['.py', '.sh', '.js', '.php', '.html', '.css', '.cpp', '.c', '.java', '.rb', '.pl'],
    "Executables":  ['.exe', '.msi', '.deb', '.rpm', '.bin', '.appimage'],
}
```

Anything with an extension not in this map falls into an `Others` folder rather than
being skipped or erroring out.

The core loop (`sort_files`) does exactly four things per file in the target directory:

1. Skip it if it's hidden (`.`-prefixed) or a directory.
2. Look up its category from the extension map (`get_category`).
3. Work out a collision-safe destination filename (`get_unique_filename`) — this walks
   `name.ext`, `name_1.ext`, `name_2.ext`... until it finds one that doesn't already
   exist in the destination folder.
4. In dry-run mode, print what *would* happen. Otherwise, create the category folder if
   it doesn't exist yet and `shutil.move()` the file into it.

Errors on an individual file (permissions, a file disappearing mid-run, etc.) are
caught and printed per-file rather than crashing the whole run — a failed move for one
file doesn't stop the rest of the directory from being sorted.

## Installation

```bash
git clone https://github.com/Ryx13/Python-CLI-File-Organizer.git
cd Python-CLI-File-Organizer
chmod +x sortfiles
```

Optionally, put it on your `PATH` so `sortfiles` works from anywhere:

```bash
sudo cp sortfiles /usr/local/bin/sortfiles
```

No `pip install` needed — it only uses the Python standard library, and targets Python
3 on Linux (built and tested on Kali/Ubuntu/Debian).

## Usage

```bash
# Sort a specific directory
sortfiles ~/Downloads

# See what would happen without touching anything (recommended first run)
sortfiles ~/Downloads --dry-run

# Sort the current directory
sortfiles .
```

```
usage: sortfiles [-h] [--dry-run] path

Sort files in a directory by extension.

positional arguments:
  path        The directory path to sort (e.g., /home/kali/Downloads)

options:
  -h, --help  show this help message and exit
  --dry-run   Simulate the sorting without moving files
```

## Possible extensions

Things I've considered but haven't needed yet: a config file for custom category
mappings instead of editing the `CATEGORIES` dict directly, an `--undo` mode that
replays the last run's moves in reverse, and a scheduled/cron mode for automatic
periodic sorting. Kept out for now in the interest of the script staying a single file
you can read top to bottom in under a minute.

## Author

Ryan Dube — [ryxvoid.xyz](https://ryxvoid.xyz) · [linkedin.com/in/ryxvoid](https://linkedin.com/in/ryxvoid)
