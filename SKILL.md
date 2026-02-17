---
name: url-distribution
description: Analyze content distribution by URL path from front matter
version: 1.0.0
---

# URL Distribution Analyzer

Analyze markdown content files and generate a distribution report showing how content is organized by URL path prefixes.

## Usage

```
/url-distribution [directory] [--detailed]
```

**Basic Analysis:**
- `/url-distribution` - Analyze default content directory
- `/url-distribution ./content/articles` - Analyze specific directory

**Detailed Output:**
- `/url-distribution --detailed` - Include list of all files per path category

## What This Skill Does

This skill helps you understand your content structure by:

1. Finding all markdown files in the specified directory
2. Extracting the `url` field from each file's front matter
3. Grouping content by the first URL path segment (e.g., `/podcast`, `/questions`, `/articles`)
4. Generating a markdown table with counts and percentages
5. Optionally listing all files in each category
6. Saving the report to `.seo-content/url-distribution/url-distro-YYYY-MM-DD.md`

## Analysis Process

### 1. Determine Content Directory

- If directory argument provided: Use that directory
- If no argument: Use `./content` (Hugo default)
- Check if directory exists, warn if not found

### 2. Discover Content Files

- Use Glob to find all `*.md` files in the directory (recursive)
- Exclude files named `transcript.md` (these are companion files, not content)
- Exclude `_index.md` files (these are Hugo section files)
- Count total files found

### 3. Extract URLs from Front Matter

For each markdown file:

1. **Read the file** (first 50 lines is sufficient for front matter)
2. **Look for `url:` field** in the front matter (YAML or TOML format)
   - YAML format: `url: /path/to/page`
   - TOML format: `url = "/path/to/page"`
3. **Extract the URL value** (strip quotes, whitespace)
4. **Handle missing URLs:**
   - If no `url` field found, mark as "no-url" category
   - Note: Some files may use Hugo's default URL structure from filename

### 4. Categorize by Path Prefix

For each URL found:

1. **Parse the first path segment** (the part after the first `/` and before the second `/`)
   - Example: `/podcast/best-interview-podcasts` → `podcast`
   - Example: `/questions/what-does-long-in-the-tooth-mean` → `questions`
   - Example: `/about` → `root` (single-segment URLs)
2. **Group files by path prefix**
3. **Count files in each category**

### 5. Generate Distribution Report

Create a markdown report with:

**Summary Statistics:**
```markdown
# Content Distribution Report
*Generated: [Date]*

**Total Files Analyzed:** [N]
**Total with URL field:** [N]
**Total without URL field:** [N]
**Unique Path Prefixes:** [N]
```

**Distribution Table:**
```markdown
## Content Distribution by URL Path

| Path | Count | Percentage | Files |
|------|-------|------------|-------|
| `/podcast` | 12 | 28.6% | [links if --detailed] |
| `/questions` | 5 | 11.9% | [links if --detailed] |
| ... | ... | ... | ... |
```

Sort table by count (descending), then alphabetically by path.

**If `--detailed` flag:**

For each path category, list all files:
```markdown
### `/podcast` (12 files)

- /podcast/best-interview-podcasts - `content/articles/best-interview-podcasts.md`
- /podcast/conversational-podcasts-guide - `content/articles/conversational-podcasts-guide.md`
- ...
```

### 6. Save Report to File

**Create output directory:**
- Output path: `.seo-content/url-distribution/`
- Check if directory exists
- If not, create it using `mkdir -p .seo-content/url-distribution`

**Generate filename with timestamp:**
- Format: `url-distro-YYYY-MM-DD.md`
- Use today's date from currentDate context
- Example: `url-distro-2026-02-16.md`

**Write report file:**
- Use Write tool to save the report
- Include timestamp in the report header: `*Generated: February 16, 2026*`
- Full path example: `.seo-content/url-distribution/url-distro-2026-02-16.md`

**Handle existing files:**
- If file already exists for today's date, inform user
- Ask if they want to overwrite or cancel
- If overwrite confirmed, proceed with Write

**Display confirmation:**
- Show success message with full file path
- Display a brief summary of the report (total files, unique prefixes)
- Optionally show the full report content to user as well

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
| **`/podcast`** | **12** | **28.6%** |
| **`/episodes`** | **7** | **16.7%** |
| **`/questions`** | **5** | **11.9%** |
| `/creativity` | 5 | 11.9% |
| `/connection` | 5 | 11.9% |
| `/career` | 2 | 4.8% |
| `/web-design` | 1 | 2.4% |
| `/tech` | 1 | 2.4% |
| `/history` | 1 | 2.4% |
| `/communication` | 1 | 2.4% |
| `/subscribe` | 1 | 2.4% |
| `/about` | 1 | 2.4% |

**TOTAL:** 42 files across 12 path prefixes
```

## Implementation Guidelines

### File Discovery
- Use Glob tool with pattern `**/*.md` to find all markdown files recursively
- Filter out `transcript.md` and `_index.md` files after discovery
- Handle empty directories gracefully

### Front Matter Parsing
- Read first 50 lines of each file (front matter is typically at the top)
- Support both YAML (`url:`) and TOML (`url =`) formats
- Handle URLs with or without quotes
- Trim whitespace and normalize paths (remove trailing slashes)

### Error Handling
- If directory doesn't exist: Display clear error and suggest correct path
- If no markdown files found: Inform user and suggest checking directory
- If no URLs found: Report that no `url` fields were found in front matter
- Skip files that can't be read (permissions, etc.) and note them

### Performance
- For large sites (100+ files), show progress indicator
- Keep all data in memory (no intermediate files needed)
- This should complete in under 10 seconds for most sites

## Use Cases

**Content Strategy:**
- Understand current content organization
- Identify over/under-invested content areas
- Compare content distribution to SEO strategy pillars

**Site Migration:**
- Audit URL structure before migration
- Identify inconsistencies in URL patterns
- Plan redirects and URL consolidation

**Content Planning:**
- See which topic areas need more content
- Balance content across pillars
- Identify orphaned or miscategorized content

## Notes

- This skill analyzes URL structure, not actual site structure
- URLs in front matter may differ from Hugo's default permalink structure
- For Hugo sites, this shows intentional URL overrides via front matter
- Works with any static site that uses front matter (Jekyll, Eleventy, etc.)

## Future Enhancements

Possible additions for v2.0:
- Export to CSV for spreadsheet analysis
- Compare distribution reports (diff two date-stamped reports)
- Identify URL conflicts or duplicates
- Suggest URL structure improvements
- Integration with SEO strategy reports
- Chart visualization of distribution
- Trend analysis (track changes over multiple reports)
