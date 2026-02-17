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

---

## Content Distribution by URL Path

| Path | Count | Percentage |
|------|-------|------------|
| `/podcast` | 12 | 27.3% |
| `/questions` | 5 | 11.4% |
| ... | ... | ... |

**TOTAL:** [N] files across [N] path prefixes
```

**Important formatting rules:**
- Bold the top 3 categories with highest counts using `**`
- Sort table by count (descending), then alphabetically by path
- Use `(root)` for single-segment URLs
- Use `(no-url)` for files without URL fields
- Add horizontal rule (`---`) after the table

**After the distribution table, add:**

```markdown
---

## Content Insights

### Strengths
[Brief bullet points about what the distribution reveals]

### Opportunities
[Brief bullet points about gaps or areas to expand]

### Content Distribution Balance
[High-level breakdown of content themes]
```

**If `--detailed` flag:**

Add a section listing all files per category:
```markdown
---

## Files by Category

### `/podcast` (12 files)
1. /podcast/best-interview-podcasts - `content/articles/best-interview-podcasts.md`
2. /podcast/conversational-podcasts-guide - `content/articles/conversational-podcasts-guide.md`
...
```

**DO NOT include:**
- ❌ "## Analysis Summary" section
- ❌ "### Top Content Categories" detailed commentary
- ❌ "### Secondary Themes" section
- ❌ "### Emerging Categories" section
- ❌ "## Notes" section at the end

**DO include:**
- ✅ Title and timestamp
- ✅ Summary statistics (counts)
- ✅ Distribution table with percentages
- ✅ Total count summary
- ✅ "## Content Insights" section (Strengths, Opportunities, Balance)
- ✅ Files by category (if --detailed flag used)

Keep insights concise and actionable.

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

### Basic Output (without --detailed flag)

```markdown
# Content Distribution Report
*Generated: February 16, 2026*

**Total Files Analyzed:** 44
**Total with URL field:** 42
**Total without URL field:** 2
**Unique Path Prefixes:** 11

---

## Content Distribution by URL Path

| Path | Count | Percentage |
|------|-------|------------|
| **`/podcast`** | **12** | **27.3%** |
| **`/episodes`** | **7** | **15.9%** |
| **`/questions`** | **5** | **11.4%** |
| **`/creativity`** | **5** | **11.4%** |
| **`/connection`** | **5** | **11.4%** |
| `/career` | 2 | 4.5% |
| `(root)` | 2 | 4.5% |
| `(no-url)` | 2 | 4.5% |
| `/history` | 1 | 2.3% |
| `/tech` | 1 | 2.3% |
| `/communication` | 1 | 2.3% |
| `/web-design` | 1 | 2.3% |

**TOTAL:** 44 files across 11 path prefixes

---

## Content Insights

### Strengths
1. **Clear primary focus**: Over 43% of content directly relates to podcasting
2. **Balanced supporting content**: Multiple themes at ~11% each provide diversity
3. **Diverse topic coverage**: 11 distinct pillars for organic search

### Opportunities
1. **Single-article categories**: History, tech, communication, web-design could be expanded
2. **Missing URL fields**: Two pillar pages need URLs added to front matter
3. **Career content**: Only 4.5% despite strong connection to podcast themes

### Content Distribution Balance
- **Podcast/Episodes combined**: 43.2% (19 files)
- **Supporting articles**: 52.3% (23 files)
- **Utility/Missing**: 4.5% (2 files)
```

### Detailed Output (with --detailed flag)

Adds "## Files by Category" section after Content Insights with numbered file listings per category.

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
