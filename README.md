# LiteratureShelf

LiteratureShelf is a lightweight, local-first PDF literature index for Windows. It helps organize and retrieve papers stored across multiple folders without moving, renaming, uploading, or modifying the original PDF files.

The application runs locally with Python and uses a browser as its interface. All literature paths, tags, reading statuses, and notes remain on your computer.

## Features

- Register and manage multiple literature libraries from different local folders.
- Recursively scan PDF files while preserving the existing folder structure.
- Parse literature types from filename prefixes such as `[A]`, `[R]`, `[B]`, and `[P]`.
- Recognize `★` at the beginning of a title as a starred-paper marker.
- Search across titles, folders, tags, notes, and suggested tags.
- Filter results by library, literature type, reading status, and tag.
- Assign multiple tags to the same paper, allowing one paper to belong to several topics without creating duplicate files.
- Track reading progress and save personal notes for later retrieval.
- Open the original PDF directly from the literature detail panel.
- Detect duplicate candidates by normalized titles and SHA-256 file hashes.
- Suggest tags using configurable title-keyword rules and existing folder names.
- Detect newly added, renamed, moved, changed, or missing PDF files during rescanning.
- Automatically rescan registered libraries every 120 seconds by default.

> [!IMPORTANT]
> Tag suggestions are rule-based. The current version matches keywords against filenames or titles and does not use AI, online databases, PDF full text, abstracts, or DOI metadata.

## Requirements

- Windows 10 or Windows 11
- Python 3.9 or later
- A modern web browser

LiteratureShelf uses only the Python standard library. No third-party Python packages are required.

## Installation

1. Download or clone this repository.
2. Keep `LiteratureShelf.py` in a permanent local folder.
3. Open PowerShell in that folder.
4. Run:

```powershell
py LiteratureShelf.py
```

If the `py` command is unavailable, try:

```powershell
python LiteratureShelf.py
```

The application will start a local server and open the interface in your default browser. Close the terminal window or press `Ctrl+C` to stop the application.

## One-click startup on Windows

Create a file named `Start_LiteratureShelf.bat` in the same folder as `LiteratureShelf.py`:

```bat
@echo off
cd /d "%~dp0"
py LiteratureShelf.py
if errorlevel 1 pause
```

You can then create a desktop shortcut for the batch file and assign `LiteratureShelf_Windows.ico` as its icon. Setting the shortcut to run minimized keeps the terminal window out of the way while the application is running.

## Getting started

1. Open **Manage Libraries**.
2. Enter a library name and its full local path, for example:

```text
Name: NB
Path: D:\project\NB\ARTICLE
```

3. Add the library and start the first scan.
4. Search the indexed papers or open a paper to edit its title, type, reading status, tags, and notes.
5. Add classification rules to generate suggested tags from title keywords.

The first scan may take some time because LiteratureShelf calculates file hashes for duplicate detection. Subsequent scans reuse existing information when the file has not changed.

## Filename convention

LiteratureShelf can infer the literature type from a prefix at the beginning of the PDF filename:

| Prefix | Suggested meaning |
| --- | --- |
| `[A]` | Research article |
| `[R]` | Review |
| `[B]` | Book or book chapter |
| `[P]` | User-defined type |

Example:

```text
[A]Reversible transitions between noradrenergic and mesenchymal tumor identities.pdf
```

Files without a recognized prefix are still indexed. Their type can be edited manually in the literature detail panel.

## Tags and classification suggestions

Folders provide only one physical location, while a paper may be relevant to several topics. LiteratureShelf therefore separates storage from classification:

- The PDF remains in one folder.
- The paper can receive multiple tags.
- Existing folder names are retained as searchable context.
- Suggested tags must be reviewed and confirmed manually.

For example, a paper may simultaneously carry the tags `ADRN/MES`, `Epigenetic regulation`, and `Single-cell omics` without being copied into three folders.

A classification rule consists of a tag and one or more title keywords:

```text
Tag: ADRN/MES
Keywords: adrenergic, mesenchymal, noradrenergic
```

If a keyword appears in a paper title, the corresponding tag is shown as a suggestion. This process is mechanical keyword matching rather than semantic interpretation.

## Search

The search box covers:

- Paper title
- Original folder and relative path
- Confirmed tags
- Personal notes
- Suggested tags

Multiple search terms must all be present somewhere in the indexed fields. Use quotation marks to search for an exact phrase.

Examples:

```text
SP100 PML
MES ALK resistance
"single-cell" plasticity
```

## Reading status and notes

Reading status is updated manually. The current interface provides the following states:

- Unread
- Skimmed
- Read closely
- Cited

Notes are intended for short, personal retrieval cues rather than full PDF annotation. A useful note records why the paper matters, its main conclusion, or where it may be used. Notes are included in search, making it possible to find a paper even when its exact title has been forgotten.

## Duplicate detection

LiteratureShelf reports duplicate candidates when:

- Two records have the same normalized title; or
- Two PDF files have the same SHA-256 hash.

Duplicate results are suggestions only. The application never deletes or merges PDF files automatically because papers with the same title may represent different versions.

## Local data and backup

The SQLite index is stored at:

```text
%LOCALAPPDATA%\LiteratureShelf\literature.sqlite3
```

This database contains registered library paths, tags, notes, reading statuses, rules, and scan metadata. It does not contain copies of the PDF files.

For backup, stop LiteratureShelf and copy `literature.sqlite3` to a safe location. When migrating to another computer, restore the database and ensure that the literature folders remain available at the same paths.

## Privacy

- PDF files remain in their original local folders.
- No PDF content is uploaded to an external service.
- The interface is served only on `127.0.0.1`, so it is not exposed as a public website.
- The current version does not contact online literature APIs.

## Command-line options

Change the automatic scan interval to 30 seconds:

```powershell
py LiteratureShelf.py --interval 30
```

Disable automatic scanning:

```powershell
py LiteratureShelf.py --interval 0
```

Start without automatically opening a browser and use a fixed port:

```powershell
py LiteratureShelf.py --no-browser --port 8765
```

Use a custom index directory:

```powershell
py LiteratureShelf.py --data-dir "D:\LiteratureShelfData"
```

## Current limitations

- Windows is the primary supported platform.
- PDF full text, abstracts, authors, journals, publication years, and DOI metadata are not extracted.
- Tag suggestions depend on manually configured keywords and title text.
- Reading status and notes must be entered manually.
- Literature libraries must be added by pasting their full local paths.
- The application does not automatically move, rename, delete, or merge PDF files.

## Roadmap

Possible future improvements include:

- PDF metadata, DOI, and abstract extraction
- Optional online metadata lookup
- Full-text indexing
- Tree-based browsing of libraries and folders
- A review queue for newly detected papers
- Batch tagging and batch renaming
- Guided import from the Downloads folder
- Export and restore tools for the index database
- Packaging as a standalone Windows application

## Project structure

```text
LiteratureShelf.py              Main application
test_literature_shelf.py       Automated tests
LiteratureShelf_Windows.ico    Windows shortcut icon
README.md                       Project documentation
```

## Testing

Run the included tests with:

```powershell
py -m unittest -v test_literature_shelf.py
```

The tests use temporary directories and do not modify your literature libraries.

## Safety notes

LiteratureShelf is designed as a non-destructive index. Nevertheless, keep independent backups of important PDF libraries and the SQLite index before making major changes to your folder structure.
