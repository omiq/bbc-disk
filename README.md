# BBC Micro Disk Image Creator

A Python 3 tool for creating working BBC Micro `.ssd` (Single Sided Disk) image files. This utility allows you to package files into a disk image that can be used with BBC Micro emulators or transferred to real BBC Micro hardware.

## What is this?

The BBC Micro was a popular 8-bit computer from the 1980s. This tool creates disk images in the `.ssd` format, which is the standard format for single-sided floppy disks on the BBC Micro. The tool handles the complex disk catalog structure and file allocation that the BBC Micro operating system expects.

## Quick Start

### Basic Usage

Add a single file to a disk image:
```bash
python3 makedisk.py myfile.txt
```

This creates `out.ssd` with your file on it.

### Multiple Files

Add several files at once:
```bash
python3 makedisk.py file1.txt file2.txt file3.txt
```

### Custom Output

Specify a different output filename:
```bash
python3 makedisk.py -d mydisk.ssd myfile.txt
```

## Comprehensive Usage

### Input File Format

Files are specified using this format: `[DIR.]FILENAME[:LOADADDR[:EXECADDR]]`

- `DIR.` (optional): Directory name (defaults to `$`, the default BBC Micro directory)
- `FILENAME`: The file to add
- `LOADADDR` (optional): Memory address to load the file (hexadecimal)
- `EXECADDR` (optional): Memory address to start execution (hexadecimal)

### Examples

```bash
# Basic file in default directory
python3 makedisk.py program.bas

# File in specific directory
python3 makedisk.py LIB.program.bas

# File with load address
python3 makedisk.py program.bas:2000

# File with load and exec addresses
python3 makedisk.py program.bas:2000:3000

# Multiple files with different specifications
python3 makedisk.py LIB.data.txt:4000 main.bas:2000:3000
```

### Command Line Options

```bash
python3 makedisk.py [OPTIONS] FILES...

Options:
  -d, --dest FILE       Output .ssd file (default: out.ssd)
  -t, --title TITLE     Disk title (default: XX)
  -o, --opt {load,run,exec}  Boot option
  -h, --help            Show help message
```

### Boot Options

The `-o` option sets what happens when the disk is booted:

- `load`: Load the first file into memory
- `run`: Load and run the first file
- `exec`: Execute the first file at its exec address

### Complete Example

```bash
python3 makedisk.py \
  -d mygame.ssd \
  -t "MYGAME" \
  -o run \
  LIB.data.txt:4000 \
  main.bas:2000:3000 \
  sprites.bin:5000
```

This creates a disk image called `mygame.ssd` with:
- Title: "MYGAME"
- Boot option: run (load and execute first file)
- Three files with specific memory addresses

## How It Works

### Disk Structure

BBC Micro .ssd files have a specific structure:

1. **Sector 0**: Disk catalog (first 8 bytes of title + file entries)
2. **Sector 1**: Extended catalog (remaining title + file metadata)
3. **Sector 2+**: File data

### File Classes

- **`File`**: Represents a file to be added, handles reading file content and calculating sectors needed
- **`Sector`**: Manages writing data to individual disk sectors (256 bytes each)
- **`Surface`**: Handles the overall disk structure and catalog creation
- **`FileSpec`**: Parses the input file format into structured data

### Key Constants

- **256 bytes per sector**: Standard BBC Micro sector size
- **10 sectors per track**: BBC Micro disk geometry
- **80 tracks**: Total disk capacity
- **Sector 2**: Where file data starts (sectors 0-1 are catalog)

### Catalog Format

The disk catalog contains:
- 13-byte disk title
- File entries with names, directories, load/exec addresses, and sector locations
- Boot options and disk metadata

### Memory Addresses

BBC Micro programs often need specific memory addresses:
- **Load address**: Where the file is loaded into memory
- **Exec address**: Where execution begins (for machine code)
- Addresses are specified in hexadecimal (e.g., `2000` = 8192 decimal)

## Requirements

- Python 3.x
- No external dependencies (uses only standard library)

## Use Cases

- Creating disk images for BBC Micro emulators
- Packaging BBC Micro software for distribution
- Converting modern files to BBC Micro format
- Educational projects involving retro computing
- Archiving BBC Micro software

## Notes

- The tool automatically calculates sector allocation
- File names are limited to 7 characters (BBC Micro limitation)
- Directory names are single characters (e.g., A, B, C, with $ being the default)
- Memory addresses should be valid for BBC Micro (typically 0x0000-0xFFFF)
- The tool preserves the exact BBC Micro disk format for maximum compatibility


