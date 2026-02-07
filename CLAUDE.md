# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a Japanese technical book project based on the TechBooster Re:VIEW Template (version 5.9). Re:VIEW is a documentation authoring toolchain that converts `.re` (Re:VIEW format) files into various output formats including PDF, EPUB, HTML, and plain text.

## Build Commands

### Prerequisites Setup

```bash
# Install Ruby dependencies
gem install bundler
bundle install

# Install Node.js dependencies
npm install
```

### Build Outputs

All build commands should be run from the project root:

```bash
# Generate PDF (most common)
npm run pdf

# Generate EPUB
npm run epub

# Generate HTML (for web)
npm run web

# Generate plain text
npm run text

# Generate Markdown
npm run md
```

### Alternative: Using Rake Directly

Navigate to `articles/` directory and use Rake commands:

```bash
cd articles/
rake pdf    # or epub, web, text
```

### Docker Build

If you don't have TeX environment locally:

```bash
# Pull Docker image
docker pull ghcr.io/vvakame/review:5.9

# Build using Docker
bash ./build-in-docker.sh

# Build with alternative config
REVIEW_CONFIG_FILE=config-ebook.yml bash ./build-in-docker.sh
```

### Build with Alternative Configuration

To use different config files (e.g., for ebook vs print versions):

```bash
REVIEW_CONFIG_FILE=config-ebook.yml npm run pdf
```

## Project Structure

### Content Files

- **`articles/*.re`**: Re:VIEW format chapter files (main content)
  - `00-hajimeni.re`: Introduction/preface
  - `contributors.re`: Contributor profiles
- **`articles/catalog.yml`**: Defines book structure (PREDEF, CHAPS, APPENDIX, POSTDEF sections)
- **`articles/config.yml`**: Main build configuration (paper size, fonts, metadata, etc.)
- **`articles/config-ebook.yml`**: Alternative config for electronic editions

### Build System

- **`Gruntfile.js`**: Node.js build orchestration (wraps Re:VIEW commands)
- **`articles/Rakefile`**: Ruby build tasks (loads tasks from `lib/tasks/`)
- **`articles/lib/tasks/*.rake`**: Individual Rake task definitions

### Styling

- **`articles/sty/`**: LaTeX style files for PDF output
  - `reviewmacro.sty`, `techbooster-doujin-base.sty`: Custom TechBooster styles
  - `review-jsbook.cls`, `review-base.sty`: Re:VIEW base styles
- **`articles/*.scss`**: SCSS source files for EPUB/Web styling
  - Run `./rebuild-css.sh` to compile SCSS to CSS

### Supporting Files

- **`articles/images/`**: Image assets referenced in content
- **`articles/layouts/`**: Custom layout templates
- **`prh-rules/`**: Proofreading helper rules
- **`redpen-conf-ja.xml`**: RedPen configuration for Japanese text validation

## Configuration

### Paper Size and Format Switching

Edit `articles/config.yml` to change paper size or print/ebook format. Uncomment the desired `texdocumentclass` line:

- **B5 print**: `media=print,paper=b5,...` (default)
- **B5 ebook**: `media=ebook,paper=b5,...`
- **A5 print**: `media=print,paper=a5,...`
- **A5 ebook**: `media=ebook,paper=a5,...`

Print editions include crop marks, bleed, hidden folios, and disabled hyperlinks. Ebook editions are trimmed to final size with active hyperlinks and cover image placement.

### Adding New Chapters

1. Create a new `.re` file in `articles/` directory (e.g., `01-chapter.re`)
2. Add the filename to `articles/catalog.yml` under the appropriate section:
   - `PREDEF`: Front matter (preface, introduction)
   - `CHAPS`: Main chapters
   - `APPENDIX`: Appendices
   - `POSTDEF`: Back matter (contributors, colophon)

Example:
```yaml
CHAPS:
  - 01-introduction.re
  - 02-implementation.re
```

## Re:VIEW Syntax Basics

Re:VIEW files use a lightweight markup syntax. Common patterns:

- `= Heading 1` (chapter title)
- `== Heading 2` (section)
- `=== Heading 3` (subsection)
- `//list[id][caption]{...}`: Code listing
- `//image[id][caption]{...}`: Image reference
- `//table[id][caption]{...}`: Table
- `@<code>{...}`: Inline code
- `@<chap>{chapter-id}`: Cross-reference to chapter
- `//footnote[id][text]`: Footnote

Images should be placed in `articles/images/` and referenced by filename (without directory path).

## Common Workflows

### Updating Styles for EPUB/Web

1. Edit `articles/*.scss` files
2. Run `./rebuild-css.sh` to compile
3. Rebuild with `npm run epub` or `npm run web`

### Creating Both Print and Ebook PDFs

```bash
# Print version (with crop marks)
npm run pdf

# Ebook version (trimmed, with hyperlinks)
REVIEW_CONFIG_FILE=config-ebook.yml npm run pdf
```

### Migration from Older Re:VIEW Versions

If updating from Re:VIEW 3/4/5 projects, use:
```bash
cd articles/
review-update
```

Then update `reviewmacro.sty` and `techbooster-doujin-base.sty` from the TechBooster template repository.

## Environment Information

- **Re:VIEW Version**: 5.9.0
- **Node.js**: >=20.0.0
- **npm**: >=10.8.0
- **Ruby**: Uses Bundler for dependency management
- **Main Branch**: `master` (use for PRs)
- **Current Branch**: `book`

## Additional Tools

- **RedPen**: Text validation (install with `brew install redpen`)
- **prh**: Proofreading helper for consistent terminology
- **Playwright**: Used for image cropping in PDF generation
- **graphviz**: May be required for diagram generation (`brew install graphviz`)

## Output Files

Build artifacts are generated in `articles/`:
- PDF: `articles/ReVIEW-Template.pdf`
- EPUB: `articles/*.epub`
- Web: `articles/webroot/`
- Clean build artifacts: Handled automatically by Grunt tasks
