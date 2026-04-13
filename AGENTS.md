# AGENTS.md - Project Overview for AI Agents

## Project Overview

**Project Name:** Dabao's Personal Tech Blog  
**Type:** Jekyll-based static blog website  
**URL:** https://dabaohuang.github.io  
**Author:** Dabao Huang (DevOps & Full-stack Engineer)

---

## Project Description

A personal technical blog built with **Jekyll** (Ruby static site generator) and the **Hyde** theme. The site documents Dabao's experiences and knowledge in:
- **DevOps & Cloud Operations** (AWS, Linux, CI/CD)
- **Full-stack Web Development** (PHP, Bootstrap, jQuery)
- **Infrastructure & Systems** (rsyslog, databases, deployment)
- **Web Technologies** (Python, React, JavaScript, CSS)

**Blog Stats:**
- 38 published articles
- Content spanning 2017-2023
- Articles organized by year in `_posts/`
- ~4,298 lines of content

---

## Technology Stack

### Build & Deployment
- **Static Site Generator:** Jekyll
- **Theme:** Hyde (fork of poole/hyde)
- **Markup:** Markdown (kramdown parser)
- **Syntax Highlighting:** Rouge
- **Build System:** Docker (see `docker-compose.yml` for dev environment)

### Web Frontend
- **Markup:** HTML5, Liquid templates
- **Styling:** CSS (Bootstrap 4 for older posts, custom CSS)
- **Scripting:** jQuery, vanilla JavaScript
- **Analytics:** Google Analytics
- **Comments:** Disqus integration
- **SEO:** Custom SEO.html template

### Version Control
- **Repository:** GitHub (dabaohuang/dabaohuang.github.io)
- **Hosting:** GitHub Pages
- **Current Branch:** master
- **Latest:** Modernized blog design (commit: 25ebde3)

---

## Project Structure

```
dabaohuang.github.io/
├── _config.yml              # Jekyll configuration
├── _posts/                  # Blog articles (organized by year)
│   ├── 2017/               # 15 articles
│   ├── 2018/               # 17 articles
│   ├── 2019/               # 3 articles
│   ├── 2020/               # 1 article
│   ├── 2021/               # 1 article
│   └── 2023/               # 2 articles
├── _layouts/                # Jekyll layout templates
│   ├── default.html         # Base layout
│   ├── page.html            # Static page layout
│   └── post.html            # Blog post layout
├── _includes/               # Reusable template components
│   ├── head.html
│   ├── sidebar.html
│   ├── seo.html
│   ├── disqus.html
│   └── google_analyst.html
├── _site/                   # Generated static files (git-ignored)
├── public/                  # Public assets
├── img/                     # Images & media
├── index.html               # Homepage
├── about.md                 # About page
├── portfolio.md             # Projects/portfolio page
├── Archive.md               # Archive listing
├── Label.md                 # Label/tag reference
├── atom.xml                 # RSS feed
├── sitemap.xml              # XML sitemap
├── CNAME                    # GitHub Pages custom domain
├── LICENSE.md               # License
└── dev/                     # Development files (git-ignored)
```

---

## Key Configuration (_config.yml)

```yaml
title: -Dabao-
tagline: Tech blog
description: -自己整理過，才算是學習- (Knowledge that I've organized myself counts as learning)
author:
  name: Dabao Huang
  email: dabaohuang0213@gmail.com
  fb: https://www.facebook.com/da.bao.12
version: 2.1.0
markdown: kramdown
highlighter: rouge
permalink: pretty
paginate: 5
categories:
  1: Linux
  2: Git
```

---

## Content Categories & Topics

Articles primarily cover:

1. **Linux & Systems**
   - Commands (find, yum, etc.)
   - System administration
   - Rsyslog configuration
   - MySQL troubleshooting

2. **Web Development**
   - PHP & CodeIgniter
   - JavaScript & Bootstrap
   - CSS (including Flex)
   - AWS S3 & Lambda

3. **DevOps & Cloud**
   - AWS (S3, IAM, policies, Lambda)
   - Git & version control
   - Packer (machine images)
   - CI/CD & deployment

4. **Tooling & Productivity**
   - VS Code & editor configs
   - Development environment setup

---

## Development Environment

### Dockerized Development
The project includes a Docker setup for local development:
- Docker & docker-compose configuration available
- Allows consistent Jekyll build environment without Ruby dependencies on host machine

### Building Locally
```bash
jekyll build              # Generate _site/
jekyll serve              # Run local server (default: http://localhost:4000)
jekyll serve --watch      # Auto-rebuild on file changes
```

### Dependencies
- Ruby (2.x or 3.x)
- Jekyll gems (see Gemfile if present)
- kramdown (Markdown parser)
- Rouge (syntax highlighter)

---

## Git Workflow & Recent History

### Recent Commits
1. `25ebde3` - feat: modernize blog design (HEAD, master)
2. `400a431` - chore: add dockerized jekyll dev environment
3. `6311cd4` - organizing article categorization (origin/master, origin/HEAD)
4. Previous: Individual article additions and updates

### Branches
- **master:** Main development branch (synced with origin/master)
- Deployments automated via GitHub Pages

---

## Important Files & Purposes

| File | Purpose |
|------|---------|
| `_config.yml` | Jekyll configuration & site metadata |
| `index.html` | Homepage entry point |
| `about.md` | Author bio and background |
| `portfolio.md` | Professional portfolio & skills |
| `Archive.md` | Article archive listing |
| `atom.xml` | RSS feed for subscribers |
| `CNAME` | Custom domain configuration |
| `.gitignore` | Git exclusions (node_modules, _site, .DS_Store, etc.) |

---

## Common AI Tasks for This Project

Agents may be asked to:

1. **Content Management**
   - Create, edit, or organize blog posts
   - Update article metadata (title, date, categories)
   - Fix typos, grammar, or formatting in existing articles

2. **Site Maintenance**
   - Update site configuration (_config.yml)
   - Modify layouts and templates
   - Fix broken links or images
   - Update sidebar or navigation

3. **Development**
   - Build & test the Jekyll site locally
   - Verify RSS feed generation
   - Test site responsiveness
   - Optimize assets

4. **Documentation**
   - Generate or update AGENTS.md / AGENT-TASK.md
   - Create README or contribution guidelines
   - Document recent changes

---

## Testing & Validation

### Available Tools
- **Jekyll build:** Validates markdown syntax and template processing
- **Link checking:** Manual verification recommended
- **Browser testing:** Recommended for layout changes

### Git Verification
- Use `git status` to check uncommitted changes
- Use `git diff` to review changes before commit
- Always test locally before pushing

---

## Important Notes for Agents

1. **Markdown Format:** Articles use YAML front matter and kramdown syntax
2. **File Naming:** Blog posts follow `YYYY-MM-DD-slug-title.md` convention in `_posts/{YEAR}/`
3. **No Build Artifacts:** Don't modify `_site/` directory (auto-generated)
4. **Commit Conventions:** Include descriptive messages; use conventional commits when possible
5. **Liquid Templates:** Use Liquid templating syntax in `.html` files (e.g., `{{ site.title }}`)
6. **Category Mapping:** Only use predefined categories from _config.yml or extend carefully
7. **GitHub Pages:** Site auto-deploys from master branch; test builds locally first

---

## External Resources

- **Jekyll Docs:** https://jekyllrb.com/
- **Hyde Theme:** https://github.com/poole/hyde
- **kramdown Syntax:** https://kramdown.gettalong.org/
- **GitHub Pages:** https://pages.github.com/

---

**Document Version:** 1.0  
**Last Updated:** 2026-04-13  
**For Agents:** Use this document to understand project structure, conventions, and common tasks.
