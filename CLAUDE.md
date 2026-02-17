# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is a Claude Code skill that analyzes markdown content files and generates distribution reports showing how content is organized by URL path prefixes. The skill is designed to be added to projects via git submodule and works independently of other skills.

## Skill Architecture

This repository contains a single Claude Code skill with the following structure:

- **SKILL.md**: Main skill definition with YAML frontmatter and instructions for Claude
  - Skill name: `url-distribution`
  - Invoked via: `/url-distribution [directory] [--detailed]`
  - Accepts optional directory argument (defaults to `./content`)
  - Supports `--detailed` flag to show file lists per category
  - Saves output to `.seo-content/url-distribution/url-distro-YYYY-MM-DD.md`

- **README.md**: User-facing documentation
  - Installation instructions (submodule or personal)
  - Usage examples
  - Output format explanation
  - Use cases and limitations

- **No configuration files**: Skill uses command-line arguments only
- **No references directory**: All instructions are self-contained in SKILL.md
- **Output directory**: `.seo-content/url-distribution/` (created automatically)

## How the Skill Works

When invoked with `/url-distribution [directory] [--detailed]`, Claude follows these steps:

1. **Determine content directory**:
   - Use provided directory argument, or default to `./content` (Hugo convention)
   - Verify directory exists, warn if not found

2. **Discover markdown files**:
   - Use Glob with pattern `**/*.md` to find all markdown files recursively
   - Filter out `transcript.md` (companion files, not content)
   - Filter out `_index.md` (Hugo section files)
   - Count total files discovered

3. **Extract URLs from front matter**:
   - Read each markdown file (first 50 lines sufficient for front matter)
   - Look for `url:` field in YAML or TOML format
   - Handle both formats: `url: /path` (YAML) and `url = "/path"` (TOML)
   - Strip quotes and whitespace, normalize paths
   - Mark files without URL field as "no-url" category

4. **Categorize by path prefix**:
   - Parse first path segment: `/podcast/episode-1` → `podcast`
   - Single-segment URLs (`/about`) → `root` category
   - Group files by path prefix
   - Count files per category

5. **Generate distribution report**:
   - Summary statistics: total files, files with/without URLs, unique prefixes
   - Distribution table sorted by count (descending), then alphabetically
   - If `--detailed` flag: list all files per category with URL and file path
   - Include timestamp in report header (e.g., "*Generated: February 16, 2026*")

6. **Save report to file**:
   - Create output directory: `.seo-content/url-distribution/` (if not exists)
   - Generate filename with date: `url-distro-YYYY-MM-DD.md`
   - Write report using Write tool
   - Handle existing files (prompt user to overwrite or cancel)
   - Display confirmation with file path and brief summary

## Key Features

- **Date-stamped output**: Saves reports to `.seo-content/url-distribution/url-distro-YYYY-MM-DD.md`
- **Timestamped reports**: Includes generation timestamp in both filename and content
- **No configuration needed**: Works out of the box with sensible defaults
- **Static site agnostic**: Works with Hugo, Jekyll, Eleventy, or any SSG that uses front matter
- **Fast execution**: Should complete in under 10 seconds for most sites (100+ files)
- **Clear reporting**: Markdown tables with percentages and counts
- **Flexible filtering**: Automatically excludes non-content files
- **Multi-format support**: Handles both YAML and TOML front matter
- **Historical tracking**: Date-stamped files allow comparison over time

## Design Principles

### Analysis and Tracking
- **Read-only on content**: Never modifies source content files
- **Quick insights**: Fast overview of content distribution
- **Persistent output**: Saves reports for historical tracking
- **Date-stamped files**: Easy to track changes over time

### User Experience
- **Zero configuration**: No config files to manage
- **Sensible defaults**: Works with Hugo convention (./content) out of the box
- **Clear output**: Easy-to-read markdown tables
- **Optional detail**: Basic or detailed view based on needs

### Independence
- **No skill dependencies**: Works standalone, doesn't require other skills
- **No external data**: Only reads from markdown files
- **Consistent output location**: Uses `.seo-content/` convention like other K3ND477 skills

## Testing the Skill

### Setup Test Environment

1. Install the skill:
   ```bash
   # Project scope
   mkdir -p .claude/skills
   ln -s /path/to/this/repo .claude/skills/url-distribution

   # OR personal scope
   ln -s /path/to/this/repo ~/.claude/skills/url-distribution
   ```

2. Create test content (if needed):
   ```bash
   mkdir -p test-content
   # Add markdown files with url: fields in front matter
   ```

### Test Basic Analysis

1. Invoke in Claude Code:
   ```
   /url-distribution
   ```

2. Verify:
   - Uses default directory (./content)
   - Discovers all markdown files
   - Excludes transcript.md and _index.md files
   - Extracts URLs from front matter
   - Groups by path prefix correctly
   - Creates `.seo-content/url-distribution/` directory
   - Saves report to `url-distro-YYYY-MM-DD.md`
   - Includes timestamp in filename and content
   - Displays confirmation with file path
   - Calculates percentages correctly

### Test with Arguments

1. Test custom directory:
   ```
   /url-distribution ./content/articles
   ```

2. Test detailed output:
   ```
   /url-distribution --detailed
   ```

3. Verify:
   - Custom directory is used correctly
   - Detailed mode lists all files per category
   - File paths and URLs are displayed in report
   - Report is saved to correct location
   - Format is readable and clear

### Edge Cases to Test

- **Empty directory**: No markdown files found
- **No URLs**: Files without url: field in front matter
- **Mixed formats**: YAML and TOML front matter in same directory
- **Single-segment URLs**: URLs like /about or /contact
- **Trailing slashes**: URL normalization (/path/ vs /path)
- **Invalid paths**: Directory doesn't exist
- **Large sites**: 100+ files (performance test)
- **No front matter**: Files without front matter at all
- **Existing file**: Running twice on same day (overwrite prompt)
- **Output directory creation**: Running when `.seo-content/` doesn't exist

## Modifying the Skill

### Changing Discovery Logic

Edit SKILL.md "### 2. Discover Content Files":
- Add/remove file exclusion patterns
- Change glob pattern
- Adjust filtering rules

### Changing Categorization Logic

Edit SKILL.md "### 4. Categorize by Path Prefix":
- Modify path parsing rules
- Change single-segment handling
- Adjust grouping logic

### Changing Output Format

Edit SKILL.md "### 5. Generate Distribution Report":
- Modify table structure
- Change sorting rules
- Adjust percentage calculations
- Customize summary statistics

## Use Cases

### Content Strategy
- Understand current content organization
- Identify over/under-invested content areas
- Compare content distribution to SEO strategy pillars
- Balance content across topic areas

### Site Migration
- Audit URL structure before migration
- Identify inconsistencies in URL patterns
- Plan redirects and URL consolidation
- Verify URL changes after migration

### Content Planning
- See which topic areas need more content
- Identify orphaned or miscategorized content
- Validate content distribution matches strategy
- Track content growth over time

### SEO Analysis
- Compare URL structure to pillar-cluster strategy
- Identify content gaps in target topics
- Validate URL naming conventions
- Assess content depth per topic area

## Integration Points

### Standalone Usage
This skill works independently and requires no other skills.

### Optional Workflow Integration

**With seo-strategy skill**:
- Run `/url-distribution` before `/seo-strategy` to understand current content
- Compare distribution report to strategy recommendations
- Validate strategy pillar distribution matches content reality
- Identify gaps between strategy and actual content

**With seo-content-drafts skill**:
- Run `/url-distribution` periodically to track content growth
- Verify new published articles appear in correct categories
- Monitor content balance as articles are published

**General workflow**:
1. Run `/url-distribution` to audit current state
2. Run `/seo-strategy` to generate recommendations
3. Use distribution data to inform pillar decisions
4. Run `/url-distribution` again after publishing to track progress

## Technical Details

### URL Extraction Logic

**YAML Front Matter:**
```yaml
---
url: /podcast/best-interview-podcasts
---
```
Pattern: `url:\s*(.+)`

**TOML Front Matter:**
```toml
+++
url = "/questions/what-does-long-in-the-tooth-mean"
+++
```
Pattern: `url\s*=\s*["'](.+)["']`

**Normalization:**
- Strip leading/trailing quotes
- Strip leading/trailing whitespace
- Remove trailing slashes (for consistent categorization)

### Path Categorization Logic

**Examples:**
- `/podcast/episode-1` → Category: `podcast`
- `/questions/what-is-x` → Category: `questions`
- `/about` → Category: `root`
- `/` → Category: `root`

**Algorithm:**
1. Remove leading `/`
2. Split by `/`
3. Take first segment
4. If empty or no segments → `root`
5. Otherwise → first segment

### Performance Considerations

**For large sites (100+ files):**
- Read only first 50 lines per file (front matter is at top)
- Keep all data in memory (no intermediate files)
- Use Glob tool for efficient file discovery
- Process files sequentially (no parallelization needed for this size)

**Expected performance:**
- <10 seconds for 100 files
- <30 seconds for 500 files
- <60 seconds for 1000+ files

## Future Considerations

Potential enhancements to consider:

### Export and Tracking
- **CSV export**: Output to CSV for spreadsheet analysis
- **Diff reports**: Show changes since last analysis (compare two date-stamped reports)

### Advanced Analysis
- **URL conflict detection**: Identify duplicate URLs
- **Depth analysis**: Show distribution by path depth (level 1, 2, 3+)
- **Orphan detection**: Find files with no internal links
- **URL validation**: Check for invalid URL patterns

### Integration Features
- **Strategy comparison**: Diff against seo-strategy pillar list
- **Recommendation mode**: Suggest URL reorganization
- **Gap analysis**: Identify missing categories from strategy
- **Auto-categorization**: Suggest categories for no-url files

### Output Formats
- **JSON export**: Structured data output
- **Chart generation**: ASCII charts or HTML visualizations
- **Summary mode**: Single-line summary (for automation)
- **Watch mode**: Monitor directory and update report

## Related Skills

This skill is independent but can complement:

- **seo-strategy**: Compare content distribution to strategy pillars
- **seo-content-drafts**: Track content growth after publishing
- **transcript-seo**: Validate content coverage of extracted keywords

## Maintenance Notes

### Breaking Changes

Consider a breaking change if:
- Output format structure changes significantly
- Command-line argument syntax changes
- Default directory changes from ./content
- Categorization logic changes (affects report comparison)

### Backwards Compatibility

Maintain compatibility for:
- **Output format**: Add new columns, don't remove existing
- **Arguments**: Add new flags, keep existing (directory, --detailed)
- **Categorization**: Extend logic, don't change existing behavior
- **Front matter parsing**: Support new formats, keep existing

### Version Control

- Skill maintains its own git repository
- Distributed as git submodule or standalone clone
- Use semantic versioning for releases
- Document changes in commit messages
- Keep SKILL.md and README.md synchronized

### When Updating SKILL.md

- Keep instructions explicit and unambiguous
- Test with real content directories (Hugo, Jekyll, etc.)
- Verify both YAML and TOML parsing works
- Check edge cases (no URLs, empty directories, large sites)
- Update CLAUDE.md if architecture changes
- Update README.md if usage or output format changes

## Distribution

This skill is meant to be distributed as a git repository that users can:
- Add as a submodule to their projects (`.claude/skills/url-distribution`)
- Clone to their personal skills directory (`~/.claude/skills/url-distribution`)
- Include as part of the K3ND477 Skills collection

Users should:
- Use skill as-is (no customization needed)
- Report issues if URL extraction fails for their SSG
- Suggest additional file exclusion patterns if needed
