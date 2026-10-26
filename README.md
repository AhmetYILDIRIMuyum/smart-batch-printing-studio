<div align="center">

# Smart Batch Printing Studio (SBPS)

**Multi-Format Batch Printing for Windows — Organize, Filter, and Print Different File Types in One Queue**

A C# / .NET 9 Windows desktop application for organizing documents and automating the submission of supported files to an installed printer, without opening each file manually.

[**Türkçe**](README.tr.md) · [**Installation**](docs/INSTALLATION.md) · [**User Guide**](docs/USER_GUIDE.md) · [**Troubleshooting**](docs/TROUBLESHOOTING.md)

![Smart Batch Printing Studio main window](docs/images/Main-Window.png)

</div>

## Overview

**Smart Batch Printing Studio (SBPS)** is a Windows batch printing application built with **C#, .NET 9, Windows Forms, and Microsoft Edge WebView2**. It helps users prepare and process a multi-file print queue from a local folder, including mixed PDF, image, text, and web document formats.

Instead of opening documents one by one, users can select a folder, filter the file types found there, include or exclude individual files, choose an installed Windows printer, inspect page-count information when it can be safely determined, and submit files in **file-name order**.

SBPS keeps each source file as a **separate print job**. It also supports optional blank separator pages between consecutive files, printer settings, duplex printing where supported, cancellation, and per-file diagnostic status.

This project is intended for Windows operators seeking **multi-format batch printing**, **folder-based printing**, **multiple-file printing**, and **print queue management**; PDF is one of several supported formats, not the sole focus. Actual printing capabilities depend on Windows, the selected printer, and its driver.

## Key Features

- Enter a folder path manually, select a Windows folder, or drag and drop a folder into the window; optionally scan subfolders
- View how many supported files were listed and how many unsupported files were excluded
- Filter the queue using file-type filters generated dynamically from the selected folder
- Include or exclude individual files through per-file selection
- See real-time total, selected, and printable file counts
- Inspect per-file page-count information when available, without starting a print job
- Review known content-page totals and planned separator-page totals
- Select an installed Windows printer and refresh the printer list
- Enable two-sided (duplex) printing when supported by the selected printer; enabled by default
- Optionally open the Windows printer settings dialog before submission
- Insert optional blank separator pages between consecutive files; enabled by default
- Submit print jobs in file-name order with cancellation, per-file status, and diagnostic logging
- Continue with remaining files after a failed item when **Skip failed files** is enabled (the default); disable it to leave the remaining files unsent
- Turn on optional Turkish and English tips; tips are hidden until **Tips** is enabled
- Use a Turkish or English interface, initially selected from Windows regional settings and changeable at runtime
- Work with a high-DPI-aware Windows Forms interface and sectioned in-app help
- Use the on-screen **Exit** button; closing during an active print prompts for confirmation

## Supported File Formats

SBPS supports the following file extensions in its current printing workflow:

| Category | Extensions | Page-count display |
|---|---|---|
| PDF documents | `.pdf` | Read from the document when safely detectable; otherwise unavailable |
| Images | `.jpg`, `.jpeg`, `.jfif`, `.png`, `.bmp`, `.gif`, `.webp`, `.svg`, `.avif`, `.ico`, `.jxl` | 1 page, matching the current print flow |
| Text files | `.txt`, `.csv`, `.xml` | Determined at print time |
| Web documents | `.html`, `.htm`, `.xhtml`, `.mht`, `.mhtml` | Determined at print time |

**Not supported natively:** Microsoft Word `.docx` and Microsoft Excel `.xlsx` files. Do not assume that every extension can be printed successfully in every Windows/printer configuration.

## Getting Started

### System Requirements

- Windows 10 or Windows 11
- [.NET 9 SDK](https://dotnet.microsoft.com/download/dotnet/9.0) for development
- Visual Studio 2022 version 17.12 or later with the **.NET desktop development** workload, or the .NET CLI
- Microsoft Edge WebView2 Runtime
- At least one installed Windows printer for printing

### Clone, Build, and Run

```powershell
git clone https://github.com/AhmetYILDIRIMuyum/smart-batch-printing-studio.git
cd smart-batch-printing-studio
dotnet restore .\SBPS.sln
dotnet build .\SBPS.sln -c Debug
dotnet run --project .\src\SBPS\SBPS.csproj
```

For a Release build:

```powershell
dotnet build .\SBPS.sln -c Release
```

See the [Installation Guide](docs/INSTALLATION.md) for development and runtime details.

## How to Batch Print Files

1. Enter a folder path, choose a folder, or drop a folder into the SBPS window.
2. Optionally include subfolders, then refresh the file list.
3. Choose the file types to include. PDF is selected initially when available.
4. Refine the print queue using individual file checkboxes.
5. Review per-file page-count information and queue statistics.
6. Select an installed printer and confirm the duplex, separator-page, and advanced-settings options.
7. Select **Send to Printer** and approve the confirmation dialog.
8. Select **Help** for the in-app guide, or **Exit** to close the application.

**Separator-page rule:** No blank separator page is added before the first file or after the last file. A queue of *N* files creates at most *N − 1* separator pages.

For detailed steps, see the [User Guide](docs/USER_GUIDE.md).

## Technology Stack

| Technology | Purpose |
|---|---|
| **C# / .NET 9** | Windows desktop application |
| **Windows Forms (WinForms)** | User interface |
| **Microsoft Edge WebView2** | Document rendering and printing |
| **`System.Drawing.Printing`** | Access to Windows printing APIs |
| **Windows Property System** | Best-effort PDF page-count fallback |

The project's only NuGet dependency is **`Microsoft.Web.WebView2`**.

## Project Structure

```text
SBPS.sln
src/
  SBPS/
    SBPS.csproj
    MainForm.cs
    FilePageCountService.cs
    PdfFileItem.cs
    Localization.cs
    HelpDialog.cs
    PremiumControls.cs
    Program.cs
    app.manifest
docs/
  ARCHITECTURE.md
  INSTALLATION.md
  USER_GUIDE.md
  TROUBLESHOOTING.md
```

For implementation details, see [Architecture](docs/ARCHITECTURE.md).

## Configuration and Application Data

SBPS reads installed printers from Windows. Printer capabilities and final duplex behavior are controlled by the selected printer and its driver.

Print choices are **not persisted** between sessions. By default:

| Setting | Initial state |
|---|---|
| Blank separator pages | Enabled |
| Two-sided printing | Enabled, subject to printer support |
| Skip failed files | Enabled |
| Show settings first | Disabled |
| Tips | Disabled |

The last loaded folder is stored at `%LocalAppData%\FilePrintStudio\last-folder.txt`. Diagnostic logs and the WebView2 cache continue to use `%LocalAppData%\FilePrintStudio` for compatibility with earlier installations.

## Build and Development Notes

The solution targets `net9.0-windows`. Build artifacts in `bin/`, `obj/`, `.vs/`, and `publish/` are excluded from source control.

There is currently **no automated test project**. Changes should be validated through Debug and Release builds, along with non-printing startup and UI checks. **Do not send physical test pages without an operator's explicit approval.**

## Troubleshooting

- **WebView2 does not initialize:** Install or repair the Microsoft Edge WebView2 Evergreen Runtime.
- **Printer is missing:** Check Windows printer settings and use **Refresh Printers**.
- **PDF page count is unavailable:** The PDF may be encrypted, damaged, unusually encoded, or beyond the safe inspection limit. Page-count inspection and actual printing are separate operations.
- **A file fails during printing:** With **Skip failed files** enabled, SBPS skips the failed file and continues. Turn the option off to leave the remaining files unsent. A diagnostic log is written in either case.

See [Troubleshooting](docs/TROUBLESHOOTING.md) for more information.

## Limitations

- Reported page count is **not** a guarantee of physical sheet count. Copies, duplex mode, printer-driver behavior, and separator jobs may change the result.
- Text and HTML pagination depends on the print engine, paper settings, margins, and printer; these counts are determined at print time.
- Some compressed, encrypted, malformed, or very large PDFs may have an unavailable page count.
- Separate print jobs cannot guarantee how every printer driver handles duplex boundaries or blank separator sheets.
- Windows and Microsoft Edge WebView2 Runtime are required.
- SBPS does **not** provide native `.docx` or `.xlsx` printing.

## License and Usage Rights

**Source available does not mean open source.** The source code is publicly visible, but **no permission to use, copy, modify, redistribute, sublicense, or sell** the software is granted without prior written authorization.

Read the full [LICENSE](LICENSE) before using the software or its source code.

## Author

**Ahmet YILDIRIM [dede]**  
[GitHub Profile](https://github.com/AhmetYILDIRIMuyum) · [Email](mailto:ahmetyildirimuyum@gmail.com)

## Contributing

No public contribution policy has been established. Open an issue or contact the author before preparing a contribution. Repository visibility alone does not grant reuse or contribution rights.

## Project Status

**Active development.** Debug and Release build results are documented when changes are published. No production-readiness or release-support guarantee is implied.

---

**Find this project:** [Smart Batch Printing Studio (SBPS) on GitHub](https://github.com/AhmetYILDIRIMuyum/smart-batch-printing-studio) — a Windows application for batch-printing different supported file types, including PDFs, images, text files, and web documents.
