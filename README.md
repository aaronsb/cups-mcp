# CUPS MCP Server

![CUPS MCP Server](cups.png)

A standalone MCP (Model Context Protocol) server that provides complete document creation, editing, and printing capabilities via CUPS on Linux systems.

## 🚀 Key Innovation: Collaborative Document Editing

This server introduces **collaborative editing capabilities** that prevent conflicts when multiple agents (human or AI) work on the same documents:

- **Change Detection**: Automatically detects when documents are modified externally
- **Diff Visualization**: Shows unified diffs of what changed between edits
- **Conflict Prevention**: Refuses to overwrite external changes, protecting everyone's work
- **AI-to-AI Collaboration**: Enables multiple AI agents to work together on documents without conflicts

## Core Value Proposition

This server enables ANY MCP-compatible AI client (Claude Desktop, Dive AI, or custom implementations) to have full document workflow capabilities - no specific tools required. Your AI can read, edit, create, validate, and print documents just like a desktop application.

## Features

### Printing
- List available CUPS printers with status information
- Print plain text directly
- Print Markdown documents (rendered to PDF via pandoc)
- Print files from the filesystem
- Automatic file type detection

### Document Creation & Saving
- Convert Markdown to PDF without printing
- Save Markdown content to .md files
- Save LaTeX content to .tex files
- Print LaTeX documents with full XeLaTeX compilation
- Smart path handling with Documents folder default
- Automatic file renaming to avoid overwrites

### Printer Management
- Get detailed printer information
- Set default printer
- Enable/disable printers
- Update printer descriptions and locations

### Collaborative Document Editing 🤝
- Read documents with line numbers for precise editing
- Make targeted edits with string replacement and validation
- **Track external changes** with modification time and content hashing
- **Show diffs** when documents are edited outside the AI session
- **Prevent conflicts** between multiple editors (human or AI)
- Check document status to see what changed since last read
- Enable safe concurrent editing workflows

### Smart Features
- Dependency checking at startup
- Conditional tool registration based on available dependencies
- Clear feedback when dependencies are missing
- Automatic file type detection and appropriate handling

## Prerequisites

### Required
- Linux system with CUPS installed
- Python 3.10+

### Optional (for additional features)
- `pandoc` - For markdown to PDF conversion
  - Debian/Ubuntu: `apt install pandoc`
  - Fedora: `dnf install pandoc`
  - Arch: `pacman -S pandoc`
- `weasyprint` - For HTML to PDF conversion
  - Debian/Ubuntu: `apt install weasyprint`
  - Fedora: `dnf install weasyprint`
  - Arch: `pacman -S python-weasyprint`
- `rsvg-convert` - For SVG to PDF conversion
  - Debian/Ubuntu: `apt install librsvg2-bin`
  - Fedora: `dnf install librsvg2-tools`
  - Arch: `pacman -S librsvg`
- **LaTeX/XeLaTeX** - For PDF generation from markdown and LaTeX documents
  
  **Core Requirements:**
  - XeLaTeX engine for PDF compilation
  - Latin Modern fonts for proper text rendering
  - Standard LaTeX packages for document formatting
  
  **Installation by Distribution:**
  
  - **Debian/Ubuntu**: 
    ```bash
    # Essential packages
    apt install texlive-xetex texlive-fonts-recommended texlive-latex-recommended
    
    # For TikZ diagrams and graphics (if needed)
    apt install texlive-pictures
    
    # For LaTeX validation (chktex)
    apt install chktex
    ```
  
  - **Fedora**: 
    ```bash
    # Essential packages
    dnf install texlive-xetex texlive-collection-fontsrecommended
    
    # For TikZ diagrams and graphics (if needed)
    dnf install texlive-collection-pictures
    
    # For LaTeX validation (chktex)
    dnf install texlive-chktex
    ```
  
  - **Arch**: 
    ```bash
    # Essential packages
    pacman -S texlive-xetex texlive-fontsrecommended
    
    # For TikZ diagrams and graphics (if needed)
    pacman -S texlive-pictures
    
    # For LaTeX validation (chktex)
    pacman -S texlive-binextra
    ```
  
  **What Each Package Provides:**
  - `texlive-xetex`: XeLaTeX engine and fontspec package
  - `texlive-fonts-recommended`: Latin Modern, Computer Modern, and other standard fonts
  - `texlive-latex-recommended`: Essential LaTeX packages (geometry, etc.)
  - `texlive-pictures`: TikZ package for creating diagrams and graphics
  - `chktex`/`texlive-binextra`: LaTeX validation tools for checking syntax

The server will check for these dependencies at startup and only enable features that have their requirements met. Missing dependencies will be reported with installation instructions.

## Installation

```bash
# Install system dependencies (choose your distribution)

# Debian/Ubuntu - Full installation with all features
sudo apt-get install cups pandoc texlive-xetex texlive-fonts-recommended \
                     texlive-latex-recommended texlive-pictures chktex \
                     weasyprint librsvg2-bin

# Fedora - Full installation with all features  
sudo dnf install cups pandoc texlive-xetex texlive-collection-fontsrecommended \
                 texlive-collection-pictures texlive-chktex \
                 weasyprint librsvg2-tools

# Arch - Full installation with all features
sudo pacman -S cups pandoc texlive-xetex texlive-fontsrecommended \
               texlive-pictures texlive-binextra \
               python-weasyprint librsvg

# Clone and install
git clone https://github.com/aaronsb/cups-mcp
cd cups-mcp
uv sync
```

## Quick Start

### Option 1: Run directly from GitHub (Recommended)

No installation needed! Just ensure you have `uv` installed and run:

```bash
# Install uv if you haven't already
curl -LsSf https://astral.sh/uv/install.sh | sh

# Run CUPS MCP directly from GitHub
uvx --from git+https://github.com/aaronsb/cups-mcp.git cups-mcp
```

### Option 2: Clone and run locally

```bash
# Clone the repository
git clone https://github.com/aaronsb/cups-mcp
cd cups-mcp

# Run the server
uv run cups-mcp
```

## Usage

### Quick Start - Essential Tools

For most document workflows, you'll primarily use these tools:

#### Basic Printing
- `print_text` - Print plain text directly
- `print_file` - Print any file (PDF, images, etc.)
- `list_printers` - See available printers

#### Document Creation & Publishing  
- `save_latex` - Create LaTeX documents (research papers, reports)
- `save_markdown` - Create Markdown documents (notes, documentation)
- `latex_to_pdf` - Convert LaTeX to PDF
- `print_latex` - Compile and print LaTeX documents

#### Document Editing
- `read_document` - Read files with line numbers
- `edit_document` - Make precise edits to documents

#### Font Discovery (New!)
- `list_available_fonts` - Find fonts for your LaTeX documents

See the birthday whitepaper example in `examples/` for a demonstration of advanced LaTeX capabilities including mathematical proofs, TikZ diagrams, and academic formatting.

### Available Tools

#### `list_printers`
Lists all available CUPS printers.

```json
{
  "name": "list_printers"
}
```

#### `print_text`
Prints plain text content.

```json
{
  "name": "print_text",
  "arguments": {
    "content": "Hello, World!",
    "printer": "Brother_HL_L2350DW"  // optional
  }
}
```

#### `print_markdown`
Prints Markdown content rendered as PDF via pandoc and XeLaTeX.

**Supports:**
- Standard markdown formatting (headers, lists, tables, code blocks)
- LaTeX math expressions (inline with `$`, display with `$$`)
- Latin scripts including European languages
- Greek and Cyrillic alphabets
- Basic symbols and punctuation

**Limited support for:**
- Complex Unicode (emoji, box drawing characters)
- Right-to-left scripts (Arabic, Hebrew)
- CJK characters (Chinese, Japanese, Korean)

```json
{
  "name": "print_markdown",
  "arguments": {
    "content": "# My Document\n\nThis is **bold** text.\n\nMath: $E = mc^2$",
    "printer": "HP_LaserJet",  // optional
    "title": "My Document"     // optional
  }
}
```

#### `print_file`
Prints a file from the filesystem.

```json
{
  "name": "print_file",
  "arguments": {
    "path": "/path/to/document.pdf",
    "printer": "Canon_PIXMA"  // optional
  }
}
```

#### `get_printer_info`
Get detailed information about a specific printer including status, make/model, location, and URI.

```json
{
  "name": "get_printer_info",
  "arguments": {
    "printer_name": "My_Printer"
  }
}
```

#### `set_default_printer`
Change the default printer.

```json
{
  "name": "set_default_printer",
  "arguments": {
    "printer_name": "CanonG3260"
  }
}
```

#### `enable_printer` / `disable_printer`
Control printer availability for accepting jobs.

```json
{
  "name": "enable_printer",
  "arguments": {
    "printer_name": "My_Printer"
  }
}
```

#### `update_printer_info`
Update printer description and/or location.

```json
{
  "name": "update_printer_info",
  "arguments": {
    "printer_name": "My_Printer",
    "description": "Office Color Laser",
    "location": "Room 201"
  }
}
```

#### `markdown_to_pdf`
Convert markdown to PDF and save to a file (without printing).

**Supports:**
- Same markdown features as `print_markdown`
- Saves PDF to specified path instead of printing

```json
{
  "name": "markdown_to_pdf",
  "arguments": {
    "content": "# My Document\n\nSave this as PDF.",
    "output_path": "report.pdf",  // Saves to ~/Documents/report.pdf by default
    "title": "My Document"
  }
}
```

#### `print_latex`
Print LaTeX content (compiled to PDF via XeLaTeX).

**Supports:**
- Full LaTeX syntax and packages
- Mathematical formulas and equations
- TikZ diagrams and graphics
- Bibliography and citations
- Custom document classes

```json
{
  "name": "print_latex",
  "arguments": {
    "content": "\\documentclass{article}\n\\begin{document}\nHello LaTeX!\n\\end{document}",
    "printer": "My_Printer",  // optional
    "title": "My LaTeX Doc"  // optional
  }
}
```

#### `save_markdown`
Save markdown content to a .md file.

```json
{
  "name": "save_markdown",
  "arguments": {
    "content": "# My Document\n\nThis is my markdown content.",
    "filename": "notes.md"  // Saves to ~/Documents/notes.md by default
  }
}
```

#### `save_latex`
Save LaTeX content to a .tex file.

```json
{
  "name": "save_latex",
  "arguments": {
    "content": "\\documentclass{article}\n\\begin{document}\nHello LaTeX!\n\\end{document}",
    "filename": "document.tex"  // Saves to ~/Documents/document.tex by default
  }
}
```

#### `list_documents`
List PDF and Markdown files in the Documents folder.

```json
{
  "name": "list_documents",
  "arguments": {
    "folder": "reports"  // Optional: list files in ~/Documents/reports
  }
}
```

#### `print_from_documents`
Print a PDF or Markdown file from the Documents folder.

```json
{
  "name": "print_from_documents",
  "arguments": {
    "filename": "report.pdf",     // or just "report" - will find .pdf or .md
    "printer": "My_Printer",  // optional
    "folder": "reports"           // optional subfolder
  }
}
```

**Features:**
- Automatically finds .pdf or .md extension if not specified
- Converts Markdown files to PDF before printing
- Works with subfolders in Documents

#### `list_available_fonts`
List fonts available for use with XeLaTeX documents.

```json
{
  "name": "list_available_fonts",
  "arguments": {
    "style": "serif"  // Optional: filter by 'serif', 'sans', 'mono', or None for all
  }
}
```

**Features:**
- Lists all system fonts compatible with XeLaTeX
- Filter by font style (serif, sans-serif, monospace)
- Groups fonts alphabetically for easy browsing
- Provides usage examples for LaTeX documents
- Shows popular font recommendations

#### `validate_latex`
Validate LaTeX content for syntax errors before compilation.

```json
{
  "name": "validate_latex",
  "arguments": {
    "content": "\\documentclass{article}\n\\begin{document}\nHello!\n\\end{document}"
  }
}
```

**Features:**
- Uses `lacheck` and `chktex` for syntax checking (if available)
- Performs test compilation with XeLaTeX
- Returns detailed error reports and warnings
- Helps catch errors before printing

#### `read_document`
Read a document file with line numbers for editing.

```json
{
  "name": "read_document",
  "arguments": {
    "file_path": "proposal.tex",  // or full path
    "offset": 1,                  // starting line (optional, default: 1)
    "limit": 50                   // number of lines (optional, default: 50)
  }
}
```

**Features:**
- Returns content with line numbers in `cat -n` format
- Works with any text file in Documents folder
- Smart path handling (defaults to ~/Documents/)
- Supports reading portions of large files

#### `edit_document`
Edit a document file by replacing exact string matches.

```json
{
  "name": "edit_document",
  "arguments": {
    "file_path": "proposal.tex",
    "old_string": "Hello World",
    "new_string": "Hello CUPS MCP",
    "expected_replacements": 1    // optional, default: 1
  }
}
```

**Features:**
- Exact string replacement with occurrence validation
- Returns context snippet showing changes
- Prevents accidental replacements with count validation
- Same smart path handling as read_document
- **Collaborative editing support:** Detects external file changes and shows diffs
- Prevents overwrites when user has edited file externally
- Automatically tracks file modifications for safe concurrent editing

#### `check_document_status`
Check if a document has been modified externally and show changes.

```json
{
  "name": "check_document_status",
  "arguments": {
    "file_path": "proposal.tex"  // or full path
  }
}
```

**Features:**
- Tracks document modification times and content hashes
- Detects external changes made by users or other programs
- Shows unified diff of what changed since last read
- Helps coordinate collaborative editing between AI and users
- Essential for preventing conflicting edits in shared documents

**Path handling for save tools:**
- Simple filename (e.g., `report.pdf`) → Saves to `~/Documents/`
- Full path (e.g., `/home/user/Documents/report.pdf`) → Uses exact path
- Path with `~` (e.g., `~/Downloads/report.pdf`) → Expands to home directory

**Automatic features:**
- Creates Documents directory if it doesn't exist
- Generates unique filename if file already exists (adds _1, _2, etc.)
- Returns clear error messages for permission issues or other failures

### AI Agent Guidelines

#### Printer Selection Logic

When using the printing tools, AI agents should follow this logic:

1. **First print request:**
   - If user doesn't specify, ask: "Would you like to print or save as PDF?"
   - If printing and no default printer exists, ask which printer to use
   - Remember the chosen printer for the session

2. **Subsequent requests:**
   - Use the remembered printer from the first request
   - Only change if user explicitly specifies a different printer

#### File Paths

When saving PDFs with `markdown_to_pdf`:
- Use simple filenames (e.g., `report.pdf`) which save to `~/Documents/`
- Don't assume the user's home directory path
- Let the tool handle path expansion

This ensures users aren't repeatedly asked about printer selection and files are saved to predictable locations.

## Claude Desktop Configuration

### Method 1: Run from GitHub (Recommended)

Add to your Claude Desktop config:

```json
{
  "mcpServers": {
    "cups-mcp": {
      "command": "uvx",
      "args": ["--from", "git+https://github.com/aaronsb/cups-mcp.git", "cups-mcp"]
    }
  }
}
```

Or use the Claude CLI:

```bash
claude mcp add cups-mcp uvx -- --from git+https://github.com/aaronsb/cups-mcp.git cups-mcp
```

### Method 2: Run from local directory

If you've cloned the repository:

```json
{
  "mcpServers": {
    "cups-mcp": {
      "command": "uv",
      "args": ["--directory", "/path/to/cups-mcp", "run", "cups-mcp"]
    }
  }
}
```

## AI-to-AI Collaboration 🤖🤝🤖

The collaborative editing features enable fascinating multi-agent workflows:

### Use Cases
- **Parallel Document Development**: Multiple AI agents can work on different sections simultaneously
- **Review Workflows**: One AI drafts, another reviews and edits
- **Specialized Collaboration**: Domain-specific AIs (e.g., technical writer + code reviewer) working together
- **Iterative Refinement**: AIs can build upon each other's contributions with full visibility

### How It Works
1. **Agent A** reads and edits a document, establishing a baseline
2. **Agent B** detects Agent A's changes through the diff system
3. **Agent B** reviews the changes before making its own contributions
4. Each agent maintains awareness of others' modifications through the tracking system

### Example Workflow
```bash
# Agent 1 (Technical Writer AI)
- Creates initial documentation structure
- Writes API reference sections

# Agent 2 (Code Examples AI)  
- Detects Agent 1's additions
- Adds code examples to each API section
- Preserves Agent 1's documentation

# Agent 3 (Review AI)
- Sees combined work from both agents
- Fixes inconsistencies
- Adds cross-references
```

This opens up entirely new possibilities for AI collaboration on complex documentation and content creation tasks.

## Examples

### LaTeX Capabilities Showcase

The `examples/` directory contains a demonstration of the server's advanced LaTeX capabilities:

- **`birthday_whitepaper.tex`** - A humorous academic paper showcasing:
  - Complex mathematical equations and proofs
  - TikZ diagrams with electrical circuits
  - Multi-column layouts
  - Custom theorem environments
  - Bibliography and citations
  - Professional academic formatting

- **`birthday_whitepaper_unified_theory.pdf`** - The compiled PDF showing the final output

These examples demonstrate the server's ability to handle sophisticated LaTeX documents with:
- Mathematical typesetting
- Technical diagrams
- Academic paper formatting
- Cross-references and citations
- Custom LaTeX packages

### Enhanced Workflow Features

Recent improvements prevent common AI workflow issues:

1. **Smart Content Detection**: The server now detects when LaTeX content has already been saved and warns against regenerating it
2. **Clear Tool Guidance**: Tool descriptions explicitly guide the preferred workflow (save → use file path)
3. **Better Error Handling**: Enhanced LaTeX error parser provides specific package installation instructions

Example of the improved workflow:
```python
# Step 1: Save LaTeX content
save_latex(content="...", filename="paper.tex")
# Returns: "LaTeX saved successfully to: /home/user/Documents/paper.tex"

# Step 2: Convert to PDF using file path (not content!)
latex_to_pdf(file_path="/home/user/Documents/paper.tex", output_path="paper.pdf")
# Efficient: Uses saved file instead of regenerating content
```

## Documentation

- 📖 [Tool Reference](docs/TOOL_REFERENCE.md) - Complete guide to all 20 tools
- 🤝 [Collaborative Editing Guide](docs/COLLABORATIVE_EDITING.md) - Deep dive into collaboration features
- 🏗️ [Architecture Overview](docs/ARCHITECTURE.md) - Technical design and implementation details

## Future Enhancements

- [ ] HTML to PDF printing (via weasyprint)
- [ ] SVG to PDF printing (via rsvg-convert)
- [ ] Image format handling and scaling
- [ ] Print job status tracking
- [ ] Print job cancellation
- [ ] Printer options (paper size, orientation, etc.)
- [ ] Base64 encoded content support

## License

MIT