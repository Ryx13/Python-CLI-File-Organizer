# 📂 CLI File Sorter

A robust Python-based Command Line Interface tool for automating file organization on Linux (Kali/Ubuntu/Debian).

## 🚀 Overview
Keeping the `~/Downloads` folder clean is a constant battle. This tool automates that process by scanning a target directory and sorting files into categories (Images, Videos, Documents, etc.) based on their file extensions.

It is built with a **"Safety First"** philosophy:
1.  **Non-Recursive:** It will not mess up software installed inside subfolders.
2.  **Dry Run Mode:** You can see exactly what will happen before moving a single file.
3.  **Conflict Resolution:** It never overwrites files; it auto-renames duplicates.

## 🛠️ Features
- **Categorization:** Sorts files into `Images`, `Videos`, `Music`, `Documents`, `Archives`, `Scripts`, and `Executables`.
- **Safe handling:** Ignores hidden files (starting with `.`) and existing folders.
- **Smart Renaming:** If `image.jpg` exists, the incoming file becomes `image_1.jpg`.
- **Simulation:** `--dry-run` flag for testing.

## 💻 Tech Stack
- **Python 3**
- **Libraries:** `os`, `shutil`, `argparse`, `pathlib`

## ⚙️ Installation & Setup

1. **Clone the repository:**
   ```bash
   git clone [https://github.com/yourusername/cli-file-sorter.git](https://github.com/yourusername/cli-file-sorter.git)
   cd cli-file-sorter
2. **Make the script executable:**
   ```bash
   chmod +x sortfiles.py
3. **(Optional) Add to System Path: To run the command from anywhere in your terminal:**
   ```bash
   sudo cp sortfiles.py /usr/local/bin/sortfiles

## 📖 Usage

- **Basic Usage: Sort a specific directory:**
   ```bash
   sortfiles ~/Downloads
- **Safety Check (Dry Run): See what would happen without actually moving files:**
   ```bash
   sortfiles ~/Downloads --dry-run
- **Sort Current Directory:**
   ```bash
   sortfiles .
  
