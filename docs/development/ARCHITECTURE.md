# Website Architecture

## Overview

The site is a content-driven Jekyll website. Markdown documents are the source of truth; Liquid layouts and includes render them. The existing visual identity and stylesheet are retained.

## Directory structure

```text
clb-website/
├── _config.yml, Gemfile
├── _includes/ (header, navigation, footer, reusable content cards)
├── _layouts/ (default, page, member, project, resource, post)
├── _members/ (one Markdown profile per member)
├── _projects/ (one Markdown document per project)
├── _resources/ (one Markdown document per resource)
├── _posts/ (YYYY-MM-DD-title.md blog articles)
├── assets/css/style.css (existing styles plus content media helpers)
├── docs/agents/ (internal agent notes)
├── docs/development/ (audit and architecture documentation)
├── docs/templates/ (front-matter templates for each content type)
├── about.md, blog.md, index.md, members.md, projects.md, resources.md
└── README.md
```

## Major directories

- **`_config.yml`** — Site metadata, collection output/permalink rules, default layouts, and documentation exclusions.
- **`_includes/`** — Shared site chrome and reusable cards for member, project, post, and resource listings.
- **`_layouts/`** — Shared document shell and content-specific layouts. Specialized layouts render each title and optional metadata once.
- **`_members/`** — Markdown profiles. Front matter supports role, portrait, bio summary, email, external profiles, research interests, education, and display order.
- **`_projects/`** — Markdown project records. Front matter supports summary, status, dates, lead, collaborators, image, GitHub/external links, tags, featured status, and order.
- **`_resources/`** — Markdown resource records. `category` groups the listing and `type` classifies datasets, software, publications, documentation, and other material. Common type values are `Dataset`, `Software`, `Publication`, and `Documentation`; the field remains open to other descriptive values. Links and citation metadata are optional.
- **`_posts/`** — Standard Jekyll posts named `YYYY-MM-DD-title.md`; front matter supports author, categories, tags, description, optional image, and Jekyll excerpts.
- **`assets/`** — Static assets. Optional images are referenced by site-root paths and rendered through `relative_url`.
- **`docs/templates/`** — Contributor templates matching the live front-matter contracts; `docs/` is excluded from publication.
- **`docs/agents/`** — Existing internal agent notes.
- **`docs/development/`** — Audit and architecture documentation.
- **Root Markdown pages** — Human-facing pages and collection listings. Listings use Liquid loops and reusable includes rather than duplicated content.

## Content and URL conventions

| Type | Source | URL |
|---|---|---|
| Standard page | Root `.md` | Explicit permalink or Jekyll page path |
| Blog post | `_posts/YYYY-MM-DD-title.md` | Standard Jekyll date URL |
| Project | `_projects/*.md` | `/projects/:name/` |
| Member | `_members/*.md` | `/members/:name/` |
| Resource | `_resources/*.md` | `/resources/:name/` |

Collection defaults assign their matching layouts. `order` sorts listings ascending. `featured: true` marks homepage projects; if none are featured, the homepage falls back to the first three projects by order.

## Front matter contracts

- Member: `role`, `order`, `bio`, `avatar` (or `image`), `email`, `website`, `github`, `scholar`, `linkedin`, `orcid`, `twitter`, `research_interests`, `education`.
- Project: `description`, `status`, `start_date`, `end_date`, `lead`, `collaborators` (or legacy `team`), `image`, `github`, `website`, `tags`, `order`, `featured`.
- Resource: `name`, `category`, `type`, `description`, `link`, `paper_link`, `citation`, `date`, `version`, `image`, `tags`, `order`.
- Post: standard Jekyll `date`, plus `author`, `categories`, `tags`, `description`, optional `image` and `image_alt`. Jekyll's `excerpt` is shown in listings.

Optional fields are guarded in Liquid; blank optional image values do not output image elements.

## Rendering flow

Jekyll loads pages, posts, and collections; collection defaults select content layouts; layouts render metadata and Markdown; listing pages sort documents and use shared card includes. `default.html` composes header/footer and loads the stylesheet through `relative_url` for project-site `baseurl` support.

## GitHub Pages

The `github-pages` gem remains the only declared dependency. No custom plugins or JavaScript frameworks are required. Configure `url` and `baseurl` for deployment and verify using `bundle exec jekyll build`, including a non-empty project-site base path where applicable.
