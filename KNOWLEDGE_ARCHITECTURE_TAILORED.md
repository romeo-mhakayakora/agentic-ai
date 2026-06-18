# Personal Knowledge Management System - Tailored Architecture

**For**: romeo-mhakayakora  
**Date**: 2026-06-18  
**Status**: Ready to implement  
**Based on**: Your current private knowledge repositories

---

## Your Current Learning Domains

Based on your private repositories, you're actively learning across these domains:

| Domain | Current Repos | Status |
|--------|---------------|--------|
| **Mathematics** | `probability-notes`, `applied-linear-algebra-notes` | ✅ Active |
| **Computer Science** | `cpp-mastery` | ✅ Active |
| **AI & ML** | `generative-ai-with-llms-aws`, `modern-robot-learning-from-scratch` | ✅ Active |
| **Finance/Trading** | `ai-trading-repo` | ✅ Active |
| **Robotics** | `vla-autonomous-driving`, `modern-robot-learning-from-scratch` | ✅ Active |

**Plan**: Start with these 5 domains. Expand later (security, physics, economics, etc.).

---

## Part 1: Pragmatic Repository Structure

### 1.1 Root Layout

```
knowledge/
├── .github/
│   ├── workflows/
│   │   ├── deploy-pages.yml
│   │   ├── validate-structure.yml
│   │   └── search-index.yml
│   └── ISSUE_TEMPLATE/
│       └── new-subject.md
│
├── content/                          # All markdown notes (Quartz source)
│   ├── index.md                      # Home page
│   ├── domains/
│   │   ├── mathematics/              # From: probability-notes, applied-linear-algebra-notes
│   │   ├── computer-science/         # From: cpp-mastery
│   │   ├── ai-ml/                    # From: generative-ai-with-llms-aws
│   │   ├── robotics/                 # From: vla-autonomous-driving, modern-robot-learning-from-scratch
│   │   └── finance/                  # From: ai-trading-repo
│   │
│   └── shared/
│       ├── concepts/                 # Cross-domain concepts
│       ├── glossary.md
│       └── bibliography.md
│
├── scripts/
│   ├── scaffold-subject.py           # Generate new subject structure
│   ├── migrate-repo.py               # Migrate from existing repos
│   ├── validate-frontmatter.py
│   └── build-search-index.py
│
├── .quartz.json
├── quartz.layout.ts
├── package.json
├── tsconfig.json
│
├── ARCHITECTURE.md                   # This document (keep updated)
├── CONTRIBUTING.md
├── README.md
└── MIGRATION_GUIDE.md                # Step-by-step migration

```

### 1.2 Mathematics Domain (From existing repos)

```
content/domains/mathematics/
├── _index.md                         # Domain overview
│
├── probability/                      # From: probability-notes
│   ├── _index.md
│   ├── ch01-probability-counting/
│   │   └── notes.md
│   ├── ch02-conditional-probability/
│   │   └── notes.md
│   ├── ch03-random-variables/
│   │   └── notes.md
│   ├── concepts/
│   │   ├── bayes-theorem.md
│   │   ├── conditional-probability.md
│   │   └── expectation.md
│   ├── problem-sets/
│   ├── papers/
│   │   └── blitzstein-hwang-intro-probability.md
│   ├── lectures/
│   │   └── harvard-stats-110-notes.md
│   └── resources.md
│
├── linear-algebra/                   # From: applied-linear-algebra-notes
│   ├── _index.md
│   ├── week01-vectors/
│   │   └── notes.md
│   ├── week02-matrices-applications/
│   │   └── notes.md
│   ├── week03-eigendecomposition-pca/
│   │   └── notes.md
│   ├── week04-svd-applications/
│   │   └── notes.md
│   ├── week05-least-squares-regression/
│   │   └── notes.md
│   ├── week06-mmse-estimation/
│   │   └── notes.md
│   ├── week07-recommender-systems/
│   │   └── notes.md
│   ├── week08-fft-ofdm-lds/
│   │   └── notes.md
│   ├── week09-dynamical-systems/
│   │   └── notes.md
│   ├── week10-clustering-em/
│   │   └── notes.md
│   ├── week11-perceptron-compressive-sensing/
│   │   └── notes.md
│   ├── week12-markov-chains/
│   │   └── notes.md
│   ├── concepts/
│   ├── resources/
│   └── applications/
│       ├── ml-applications.md
│       └── signal-processing.md
│
└── [future subjects: optimization, real-analysis, game-theory, etc.]

```

### 1.3 Computer Science Domain

```
content/domains/computer-science/
├── _index.md
│
├── cpp/                              # From: cpp-mastery
│   ├── _index.md
│   ├── 01-introduction/
│   │   └── notes.md
│   ├── 02-basic-operations/
│   │   └── notes.md
│   ├── 03-control-flow/
│   │   └── notes.md
│   ├── 04-functions/
│   │   └── notes.md
│   ├── 05-data-types/
│   │   └── notes.md
│   ├── 06-pointers-references/
│   │   └── notes.md
│   ├── 07-arrays-memory-containers/
│   │   └── notes.md
│   ├── 08-structuring-codebase/
│   │   └── notes.md
│   ├── 09-structures-classes/
│   │   └── notes.md
│   ├── 10-language-concepts/
│   │   └── notes.md
│   ├── 11-exception-handling/
│   │   └── notes.md
│   ├── 12-std-library-stl/
│   │   └── notes.md
│   ├── 13-templates/
│   │   └── notes.md
│   ├── 14-debugging/
│   │   └── notes.md
│   ├── concepts/
│   │   ├── memory-management.md
│   │   ├── pointers-vs-references.md
│   │   ├── stack-vs-heap.md
│   │   └── templates.md
│   ├── code-examples/
│   └── resources.md
│
└── [future: algorithms, databases, os, networks, etc.]

```

### 1.4 AI/ML Domain

```
content/domains/ai-ml/
├── _index.md
│
├── generative-ai/                   # From: generative-ai-with-llms-aws
│   ├── _index.md
│   ├── llms-foundations/
│   │   └── notes.md
│   ├── transformer-architecture/
│   │   └── notes.md
│   ├── fine-tuning-strategies/
│   │   └── notes.md
│   ├── rag-retrieval-augmented/
│   │   └── notes.md
│   ├── aws-deployment/
│   │   └── notes.md
│   ├── concepts/
│   │   ├── attention-mechanism.md
│   │   ├── tokenization.md
│   │   └── embedding.md
│   ├── papers/
│   └── projects/
│
└── [future: deep-learning, computer-vision, nlp, etc.]

```

### 1.5 Robotics Domain

```
content/domains/robotics/
├── _index.md
│
├── robot-learning/                  # From: modern-robot-learning-from-scratch
│   ├── _index.md
│   ├── fundamentals/
│   │   └── notes.md
│   ├── imitation-learning/
│   │   └── notes.md
│   ├── reinforcement-learning/
│   │   └── notes.md
│   ├── concepts/
│   └── papers/
│
├── autonomous-driving/              # From: vla-autonomous-driving
│   ├── _index.md
│   ├── vla-foundations/
│   │   └── notes.md
│   ├── vision-language-models/
│   │   └── notes.md
│   ├── concepts/
│   └── papers/
│
└── [future: path-planning, control, perception, etc.]

```

### 1.6 Finance Domain

```
content/domains/finance/
├── _index.md
│
├── algorithmic-trading/             # From: ai-trading-repo
│   ├── _index.md
│   ├── quant-basics/
│   │   └── notes.md
│   ├── ml-trading-strategies/
│   │   └── notes.md
│   ├── concepts/
│   │   ├── portfolio-optimization.md
│   │   ├── risk-management.md
│   │   └── market-microstructure.md
│   ├── papers/
│   └── projects/
│
└── [future: derivatives, options, etc.]

```

---

## Part 2: Markdown Frontmatter Schema

All files use consistent YAML frontmatter:

```yaml
---
# Required
title: "Note Title"
domain: "mathematics"              # Exact: mathematics | computer-science | ai-ml | robotics | finance
subject: "probability"             # Subject within domain
type: "note"                       # note | concept | chapter | project | paper

# Optional but recommended
chapter: "ch01-probability-counting"  # If part of a chapter-based subject
tags: ["probability", "counting", "combinatorics"]
difficulty: "beginner"             # beginner | intermediate | advanced
status: "complete"                 # complete | draft | reviewing | needs-update

# Metadata
last_updated: 2026-06-18
version: 1.0

# Cross-linking
related_concepts:
  - "bayes-theorem"
  - "conditional-probability"
prerequisites:
  - "ch00-prerequisites"
next_chapters:
  - "ch02-conditional-probability"

# Cross-domain
cross_domain_links:
  - "domains/ai-ml/generative-ai/concepts/attention-mechanism"
  - "domains/robotics/robot-learning/concepts/policy"
---
```

### Example: Probability Note

```yaml
---
title: "Bayes' Theorem and Applications"
domain: "mathematics"
subject: "probability"
type: "concept"
chapter: "ch02-conditional-probability"
tags: ["bayes-theorem", "conditional-probability", "inference", "statistics"]
difficulty: "intermediate"
status: "complete"
last_updated: 2026-06-18
related_concepts:
  - "conditional-probability"
  - "independence"
cross_domain_links:
  - "domains/ai-ml/generative-ai/concepts/bayesian-inference"
  - "domains/finance/algorithmic-trading/concepts/portfolio-optimization"
---

# Bayes' Theorem

## Definition

P(A|B) = P(B|A) × P(A) / P(B)

## Intuition

[Your explanation]

## Applications

### In Machine Learning
See: [[domains/ai-ml/generative-ai/concepts/bayesian-inference]]

### In Robotics  
See: [[domains/robotics/robot-learning/concepts/belief-state]]

---
```

---

## Part 3: Quartz Configuration

`quartz.config.ts`:

```typescript
import { defineConfig } from "quartz/integration/index"
import * as Plugin from "quartz/plugins"

const config = defineConfig({
  configuration: {
    pageTitle: "Knowledge Library",
    enableSPA: true,
    enablePopovers: true,
    analytics: {
      provider: "plausible",
    },
    baseUrl: "romeo-mhakayakora.github.io/knowledge",
    ignorePatterns: ["private", "templates", ".obsidian", "scripts"],
    defaultDateType: "modified",
  },
  plugins: {
    transformers: [
      Plugin.FrontMatter(),
      Plugin.CreatedModifiedDate({
        priority: ["frontmatter", "filesystem"],
      }),
      Plugin.SyntaxHighlighting(),
      Plugin.GitHubFlavoredMarkdown(),
      Plugin.Callouts(),
      Plugin.Latex({ provider: "katex" }),
      Plugin.Description(),
      Plugin.CrawlLinks({
        markdownLinkExtensions: [".md"],
      }),
    ],
    filters: [
      Plugin.RemoveDrafts(),
    ],
    emitters: [
      Plugin.AliasRedirects(),
      Plugin.ComponentResources(),
      Plugin.ContentPage(),
      Plugin.FolderPage(),
      Plugin.TagPage(),
      Plugin.NotFoundPage(),
      Plugin.Assets(),
    ],
  },
  paths: {
    root: ".",
    source: "content",
    out: "public",
    cache: ".quartz-cache",
  },
})

export default config
```

---

## Part 4: Migration Strategy (Step-by-Step)

### Phase 1: Setup (1 hour)

```bash
# 1. Create new repo
mkdir knowledge
cd knowledge
git init
git remote add origin https://github.com/romeo-mhakayakora/knowledge.git

# 2. Create initial structure
mkdir -p content/domains/{mathematics,computer-science,ai-ml,robotics,finance}
mkdir -p content/shared/{concepts,glossary,bibliography}
mkdir -p scripts
mkdir -p .github/workflows

# 3. Initialize Quartz
npm init -y
npm install quartz
npx quartz init

# 4. Copy configuration
cp quartz.config.ts .
cp quartz.layout.ts .
```

### Phase 2: Migrate probability-notes (30 min)

```bash
# 1. Copy files
cp -r ~/probability-notes/* content/domains/mathematics/probability/

# 2. Run migration script
python scripts/migrate-repo.py \
  --source content/domains/mathematics/probability \
  --domain mathematics \
  --subject probability \
  --type chapter

# 3. Verify
git status
git add content/domains/mathematics/probability
git commit -m "migrate: probability notes to unified knowledge repo"
```

### Phase 3: Migrate applied-linear-algebra-notes (30 min)

```bash
python scripts/migrate-repo.py \
  --source content/domains/mathematics/linear-algebra \
  --domain mathematics \
  --subject linear-algebra \
  --type week-based
```

### Phase 4: Migrate cpp-mastery (30 min)

```bash
python scripts/migrate-repo.py \
  --source content/domains/computer-science/cpp \
  --domain computer-science \
  --subject cpp \
  --type sequential-chapters
```

### Phase 5: Migrate generative-ai-with-llms-aws (1 hour)

```bash
python scripts/migrate-repo.py \
  --source content/domains/ai-ml/generative-ai \
  --domain ai-ml \
  --subject generative-ai \
  --type module-based
```

### Phase 6: Migrate robotics repos (1 hour)

```bash
python scripts/migrate-repo.py \
  --source content/domains/robotics/robot-learning \
  --domain robotics \
  --subject robot-learning \
  --type topic-based

python scripts/migrate-repo.py \
  --source content/domains/robotics/autonomous-driving \
  --domain robotics \
  --subject autonomous-driving \
  --type topic-based
```

### Phase 7: Build & Deploy (30 min)

```bash
# 1. Validate
python scripts/validate-frontmatter.py --strict

# 2. Build search index
python scripts/build-search-index.py

# 3. Build Quartz
npx quartz build

# 4. Test locally
npx quartz preview

# 5. Push to GitHub
git add .
git commit -m "chore: complete knowledge repo migration"
git push -u origin main

# 6. Deploy (GitHub Pages)
# Done automatically via GitHub Actions
```

---

## Part 5: Migration Script

Create `scripts/migrate-repo.py`:

```python
#!/usr/bin/env python3
"""
Migrate a subject repo to the unified knowledge structure.
Handles different repo layouts: chapter-based, week-based, sequential, etc.
"""
import shutil
import argparse
from pathlib import Path
from datetime import datetime
import yaml
import re

def get_repo_structure_type(source_dir: Path) -> str:
    """Detect if repo is chapter-based, week-based, etc."""
    files = list(source_dir.glob("*"))
    
    # Check for chapter folders (ch01, ch02, ...)
    if any(re.match(r'^ch\d+', f.name) for f in files):
        return "chapter"
    
    # Check for week folders (week01, week02, ...)
    if any(re.match(r'^week\d+', f.name) for f in files):
        return "week"
    
    # Default
    return "mixed"

def update_frontmatter(file_path: Path, domain: str, subject: str, repo_type: str):
    """Add/update frontmatter with domain and subject."""
    content = file_path.read_text(encoding='utf-8')
    
    if content.startswith('---'):
        # Parse existing frontmatter
        _, fm_text, body = content.split('---', 2)
        frontmatter = yaml.safe_load(fm_text) or {}
    else:
        frontmatter = {}
        body = content
    
    # Ensure required fields
    if 'title' not in frontmatter:
        frontmatter['title'] = file_path.stem.replace('-', ' ').title()
    
    if 'domain' not in frontmatter:
        frontmatter['domain'] = domain
    
    if 'subject' not in frontmatter:
        frontmatter['subject'] = subject
    
    if 'type' not in frontmatter:
        # Infer from directory
        if 'concepts' in str(file_path):
            frontmatter['type'] = 'concept'
        elif 'papers' in str(file_path):
            frontmatter['type'] = 'paper'
        elif 'projects' in str(file_path):
            frontmatter['type'] = 'project'
        elif 'problems' in str(file_path) or 'exercises' in str(file_path):
            frontmatter['type'] = 'problem-set'
        else:
            frontmatter['type'] = 'note'
    
    if 'status' not in frontmatter:
        frontmatter['status'] = 'complete'
    
    if 'last_updated' not in frontmatter:
        frontmatter['last_updated'] = datetime.now().strftime("%Y-%m-%d")
    
    if 'difficulty' not in frontmatter:
        frontmatter['difficulty'] = 'intermediate'
    
    # Reconstruct file
    new_content = f"---\n{yaml.dump(frontmatter, sort_keys=False)}---\n{body}"
    file_path.write_text(new_content, encoding='utf-8')

def migrate_repo(source_dir: Path, target_dir: Path, domain: str, subject: str):
    """Main migration function."""
    
    target_dir.mkdir(parents=True, exist_ok=True)
    
    # Copy all files
    for src_file in source_dir.rglob('*'):
        if src_file.is_file() and not src_file.name.startswith('.'):
            rel_path = src_file.relative_to(source_dir)
            dst_file = target_dir / rel_path
            dst_file.parent.mkdir(parents=True, exist_ok=True)
            shutil.copy2(src_file, dst_file)
    
    # Update frontmatter on all markdown files
    repo_type = get_repo_structure_type(source_dir)
    
    for md_file in target_dir.rglob('*.md'):
        if not md_file.name.startswith('.'):
            update_frontmatter(md_file, domain, subject, repo_type)
            print(f"✏️  Updated: {md_file.relative_to(target_dir)}")
    
    print(f"✅ Migrated {subject} ({repo_type}-based structure)")

def main():
    parser = argparse.ArgumentParser(description="Migrate subject repo to unified knowledge")
    parser.add_argument("--source", required=True, help="Source repo directory")
    parser.add_argument("--domain", required=True, help="Target domain")
    parser.add_argument("--subject", required=True, help="Subject name")
    parser.add_argument("--type", default="auto", help="Repo structure type")
    
    args = parser.parse_args()
    
    source = Path(args.source)
    target = Path(f"content/domains/{args.domain}/{args.subject}")
    
    if not source.exists():
        print(f"❌ Source directory not found: {source}")
        return
    
    migrate_repo(source, target, args.domain, args.subject)

if __name__ == "__main__":
    main()
```

---

## Part 6: GitHub Actions Workflows

### Deploy Workflow

`.github/workflows/deploy-pages.yml`:

```yaml
name: Build & Deploy Knowledge Library

on:
  push:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'

      - uses: actions/setup-python@v4
        with:
          python-version: '3.11'

      - name: Install dependencies
        run: |
          npm ci
          pip install pyyaml

      - name: Validate frontmatter
        run: python scripts/validate-frontmatter.py

      - name: Build search index
        run: python scripts/build-search-index.py

      - name: Build Quartz
        run: npx quartz build

      - name: Deploy to Pages
        uses: actions/upload-pages-artifact@v2
        with:
          path: 'public'

  deploy:
    needs: build
    permissions:
      pages: write
      id-token: write
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    steps:
      - uses: actions/deploy-pages@v2
        id: deployment
```

---

## Part 7: Implementation Checklist

### Week 1: Foundation

- [ ] Create `knowledge` repo
- [ ] Set up initial directory structure
- [ ] Install Quartz 4
- [ ] Configure GitHub Pages
- [ ] Test with 1 sample note

### Week 2: Migration

- [ ] Migrate `probability-notes`
- [ ] Migrate `applied-linear-algebra-notes`
- [ ] Migrate `cpp-mastery`
- [ ] Verify all links work
- [ ] Create basic navigation

### Week 3: Expansion

- [ ] Migrate AI/ML notes
- [ ] Migrate robotics repos
- [ ] Migrate finance repo
- [ ] Build search index
- [ ] Create concept pages

### Week 4: Polish

- [ ] Custom CSS/theming
- [ ] Backlinks implementation
- [ ] Create contribution guide
- [ ] Archive old repos
- [ ] Update GitHub Pages settings

---

## Part 8: File Structure After Migration

```
knowledge/
├── content/
│   ├── index.md                    # Home page
│   ├── domains/
│   │   ├── mathematics/
│   │   │   ├── _index.md
│   │   │   ├── probability/        ← From probability-notes
│   │   │   └── linear-algebra/     ← From applied-linear-algebra-notes
│   │   │
│   │   ├── computer-science/
│   │   │   ├── _index.md
│   │   │   └── cpp/                ← From cpp-mastery
│   │   │
│   │   ├── ai-ml/
│   │   │   ├── _index.md
│   │   │   └── generative-ai/      ← From generative-ai-with-llms-aws
│   │   │
│   │   ├── robotics/
│   │   │   ├── _index.md
│   │   │   ├── robot-learning/     ← From modern-robot-learning-from-scratch
│   │   │   └── autonomous-driving/ ← From vla-autonomous-driving
│   │   │
│   │   └── finance/
│   │       ├── _index.md
│   │       └── algorithmic-trading/ ← From ai-trading-repo
│   │
│   └── shared/
│       ├── concepts/
│       ├── glossary.md
│       └── bibliography.md
│
├── public/                         # Built site (gitignored)
├── scripts/
├── .github/workflows/
├── .quartz.json
├── package.json
├── README.md
├── ARCHITECTURE.md
└── MIGRATION_GUIDE.md
```

---

## Part 9: Next Steps

**Immediate (This Week)**:

1. ✅ Review this architecture
2. Create empty `knowledge` repo: https://github.com/new
3. Set up Quartz: `npx quartz init`
4. Deploy to Pages
5. Migrate first repo: `probability-notes`

**Short-term (2-4 weeks)**:

6. Migrate all 5 existing repos
7. Set up automated validation
8. Create cross-domain concept pages
9. Build full-text search
10. Launch public site

**Long-term**:

- Add more subjects to each domain
- Create reading lists and learning paths
- Implement analytics on most-referenced concepts
- Build learning timeline visualizations

---

**Ready to start?** Let me know when you want me to:
1. Create the repo
2. Set up initial Quartz config
3. Write the migration scripts
4. Create the first migrated subject

What's your preference?
