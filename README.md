# URL Distribution Analyzer

A Claude Code skill that analyzes your content structure by URL path and generates distribution reports.

## Installation

### Project-Specific Installation (Recommended)

```bash
cd your-project-directory
mkdir -p .claude/skills
cd .claude/skills
git clone <this-repo> url-distribution
```

### Personal Installation (Global)

```bash
mkdir -p ~/.claude/skills
cd ~/.claude/skills
git clone <this-repo> url-distribution
```

## Usage

Once installed, invoke the skill in Claude Code:

### Basic Analysis

```
/url-distribution
```

Analyzes the default `./content` directory and saves a distribution report to `.seo-content/url-distribution/url-distro-YYYY-MM-DD.md`.

### Analyze Specific Directory

```
/url-distribution ./content/articles
```

### Detailed Output

```
/url-distribution --detailed
```

Shows all files grouped under each path prefix.

## What You'll Get

The skill generates a markdown report saved to `.seo-content/url-distribution/url-distro-YYYY-MM-DD.md` with:

- **Generation timestamp** (in both filename and content)
- **Summary statistics** (total files, files with/without URLs, unique prefixes)
- **Distribution table** showing:
  - Path prefix (e.g., `/podcast`, `/questions`)
  - Count of files in each path
  - Percentage of total content
- **Optional:** List of all files per category (with `--detailed`)

## Output Location

Reports are saved to:
```
.seo-content/url-distribution/url-distro-YYYY-MM-DD.md
```

Example:
```
.seo-content/url-distribution/url-distro-2026-02-16.md
```

The directory is created automatically if it doesn't exist. Running the skill multiple times on the same day will prompt you to overwrite the existing file.

## Example Output

```markdown
# Content Distribution Report
*Generated: February 16, 2026*

**Total Files Analyzed:** 42
**Total with URL field:** 42
**Total without URL field:** 0
**Unique Path Prefixes:** 12

---

## Content Distribution by URL Path

| Path | Count | Percentage |
|------|-------|------------|
| `/podcast` | 12 | 28.6% |
| `/questions` | 5 | 11.9% |
| `/articles` | 8 | 19.0% |
...

**TOTAL:** 42 files across 12 path prefixes
```

## Use Cases

- **Content Strategy:** Understand where your content is concentrated
- **SEO Planning:** Compare content distribution to your pillar strategy
- **Content Gaps:** Identify underserved topic areas
- **URL Auditing:** See your actual URL structure at a glance
- **Historical Tracking:** Compare distribution reports over time to track content growth

## Requirements

- Claude Code CLI
- Markdown files with `url` field in front matter (YAML or TOML)
- Works with Hugo, Jekyll, Eleventy, or any static site generator

## How It Works

1. Finds all `.md` files in the specified directory
2. Extracts the `url:` field from each file's front matter
3. Groups by the first URL path segment
4. Generates a distribution table sorted by count
5. Saves the report to `.seo-content/url-distribution/` with date-stamped filename

## Limitations

- Only analyzes files with explicit `url` field in front matter
- Excludes `transcript.md` and `_index.md` files
- Does not analyze actual site build output (only source files)

## Contributing

Suggestions and improvements welcome! This is a simple, focused tool designed to do one thing well.

## License

MIT

## Version

1.0.0 - Initial release
