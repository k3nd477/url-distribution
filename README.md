# URL Distribution Analyzer

A Claude Code skill that analyzes your content structure by URL path and generates distribution reports.

## Installation

### Option 1: Project Submodule (Recommended)

Add this skill as a git submodule to your project. This keeps the skill updated across projects and makes it easy to pull updates.

```bash
# Navigate to your project
cd /path/to/your/project

# Add the skill as a submodule
git submodule add git@github.com:k3nd477/url-distribution.git .claude/skills/url-distribution

# Commit the submodule addition
git add .gitmodules .claude/skills/url-distribution
git commit -m "Add url-distribution skill as submodule"
```

**Updating the skill:**
```bash
# Update to latest version
git submodule update --remote .claude/skills/url-distribution

# Commit the update
git add .claude/skills/url-distribution
git commit -m "Update url-distribution skill to latest version"
```

**Cloning a project with this submodule:**
```bash
# Clone with submodules
git clone --recurse-submodules <your-project-repo>

# Or if already cloned
git submodule init
git submodule update
```

### Option 2: Personal Installation (Global)

Install globally for use across all your projects without submodules:

```bash
# Clone to personal skills directory
cd ~/.claude/skills
git clone git@github.com:k3nd477/url-distribution.git url-distribution

# Update when needed
cd ~/.claude/skills/url-distribution
git pull origin main
```

This makes the skill available globally, but you'll need to update it manually on each machine.

## File Structure

**After installation and usage, your project will look like:**

```
your-project/
├── .claude/
│   └── skills/
│       └── url-distribution/    # This skill as submodule
├── .seo-content/                 # Created by the skill
│   └── url-distribution/
│       ├── url-distro-2026-02-15.md
│       ├── url-distro-2026-02-16.md
│       └── url-distro-2026-02-17.md
├── content/                      # Your Hugo content directory
│   ├── articles/
│   │   └── my-post.md
│   └── posts/
│       └── another-post.md
└── [your other project files]
```

**Note:** The `.seo-content/` output directory is created in YOUR project root, not in the skill repository.

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

**Basic report (without --detailed):**

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
| `/creativity` | 5 | 11.4% |
| `/connection` | 5 | 11.4% |
| `/career` | 2 | 4.5% |
| `(root)` | 2 | 4.5% |
| `(no-url)` | 2 | 4.5% |
| `/history` | 1 | 2.3% |
...

**TOTAL:** 44 files across 11 path prefixes

---

## Content Insights

### Strengths
- Clear primary focus on podcasting (43% of content)
- Balanced supporting content across multiple themes
- Diverse topic coverage for organic search

### Opportunities
- Single-article categories could be expanded
- Missing URL fields on some pillar pages
- Underserved themes with growth potential

### Content Distribution Balance
- Podcast/Episodes: 43.2%
- Supporting articles: 52.3%
- Utility pages: 4.5%
```

**With --detailed flag:**

Adds a "## Files by Category" section listing all files grouped by path prefix.

## Use Cases

- **Content Strategy:** Understand where your content is concentrated
- **SEO Planning:** Compare content distribution to your pillar strategy
- **Content Gaps:** Identify underserved topic areas
- **URL Auditing:** See your actual URL structure at a glance
- **Historical Tracking:** Compare distribution reports over time to track content growth

## Workflow Integration

### Working with seo-strategy skill

This skill complements the `seo-strategy` skill by providing data-driven insights into your current content distribution.

#### Recommended Workflow

**Step 1: Audit Current Distribution** (this skill)
```bash
/url-distribution
```
Generates: `.seo-content/url-distribution/url-distro-YYYY-MM-DD.md`

**Step 2: Generate Strategy** (seo-strategy skill)
```bash
/seo-strategy
```
Use the distribution report to inform pillar decisions and identify gaps.

**Step 3: Track Progress** (this skill, periodically)
```bash
/url-distribution
```
Compare date-stamped reports to see how your content distribution evolves.

#### Skill Relationships

- **This skill (url-distribution)**: Current state analysis (fast, can run frequently)
- **seo-strategy skill**: Strategic content planning (comprehensive, periodic)
- **seo-content-drafts skill**: Content creation (generates articles that affect distribution)

All skills share the `.seo-content/` output directory for seamless integration.

#### Independent Usage

You can use this skill independently for:
- Quick content audits before site migrations
- URL structure validation
- Content balance checks
- Historical trend analysis

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
