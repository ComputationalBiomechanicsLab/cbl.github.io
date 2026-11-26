# Baseline Audit — Academic Research Laboratory Website

**Audit type:** Read-only repository inspection  
**Scope:** Repository contents supplied in `clb-website.zip`  
**Status:** Baseline established; runtime build not verified  
**Constraint:** No existing project files were modified. This report is the only addition in the audited copy.

## 1. Executive summary

The repository is an early Jekyll website scaffold. It has a small set of Markdown pages, two configured output collections (`projects` and `members`), Liquid templates, a basic stylesheet, and a `github-pages` Gemfile dependency.

The implementation is not yet internally consistent. In particular, the resource collection is not configured, the member/resource templates refer to layouts that do not exist, the sample member uses a different layout from the member template, and the main page layout duplicates page headings. The projects listing also contains mismatched HTML tags.

No successful Jekyll build can be claimed: Ruby is present in the inspection environment, but `bundle` and `jekyll` executables are absent. Dependency installation was not attempted.

## 2. Current architecture

### 2.1 Directory structure observed

```text
clb-website/
├── _config.yml
├── Gemfile
├── _data/
│   └── resources.yml
├── _includes/
│   ├── footer.html
│   ├── header.html
│   └── nav.html
├── _layouts/
│   ├── default.html
│   ├── page.html
│   ├── post.html
│   └── project.html
├── _members/
│   └── sample-member.md
├── _posts/
│   └── sample-post.md
├── _projects/
│   └── sample-project.md
├── assets/
│   └── css/style.css
├── templates/
│   ├── blog-post.md
│   ├── member.md
│   ├── project.md
│   └── resource.md
├── agents/
│   ├── gemini01.md
│   ├── gemini02.md
│   ├── gemini03.md
│   └── gpt01.md
├── about.md
├── blog.md
├── index.md
├── members.md
├── projects.md
├── resources.md
├── README.md
└── structure.txt
```

Notable differences from `structure.txt`:
- `navigation.html` is documented but the actual include is `nav.html`.
- The documented `member.html` and `resource.html` layouts are absent.
- `_resources/` is documented but absent.
- `assets/images/team`, `assets/images/projects`, `assets/images/blog`, and `assets/js` are documented but absent.
- The actual `agents/` directory is not documented.
- `docs/development/` did not exist in the supplied archive; it is created in this audited copy to hold this report.

### 2.2 Jekyll configuration

`_config.yml` defines the site title, email, description, URL, base URL, Markdown processor, and two collections:
- `projects`: output enabled, permalink `/projects/:name/`
- `members`: output enabled, permalink `/members/:name/`

`resources` is not configured as a collection. The resource listing currently reads from `_data/resources.yml`, which is a separate data-driven approach. The resource template, however, instructs contributors to add files to `_resources/`. Those two approaches are not connected.

The site metadata still contains generic placeholders (`Your Club / Organization Name`, `your-email@example.com`, `yourdomain.com`).

`Gemfile` declares `github-pages` in the `:jekyll_plugins` group. This is a sensible starting point for GitHub Pages compatibility, but the actual dependency resolution and build have not been tested.

### 2.3 Layouts and includes

- `default.html` is the root layout and includes `nav.html`.
- `page.html`, `post.html`, and `project.html` inherit from `default.html`.
- `header.html` and `footer.html` exist but are not included by the active default layout.
- The footer is instead hard-coded inside `default.html`.

The active layout therefore has two competing site-shell approaches: a standalone header/footer implementation and a separate inline navigation/footer implementation.

The root layout prints `page.title` as an `<h1>`. The `page`, `post`, and `project` layouts also print their own `<h1>`, producing duplicate page headings for content using those layouts. The homepage also has its own Markdown H1, so it receives the root layout's title heading in addition to its content heading.

The `member` and `resource` layouts referenced by the corresponding content templates do not exist.

### 2.4 Content and front matter

| Content type | Current implementation | Finding |
|---|---|---|
| Standard pages | Root-level Markdown files | Use `default` directly; page title is rendered by the root layout |
| Blog posts | `_posts/` | Uses the standard Jekyll posts collection and `post` layout |
| Projects | `_projects/` | Configured output collection; sample uses `project` layout |
| Members | `_members/` | Configured output collection; sample uses `page` layout |
| Resources | `_data/resources.yml` | Data-driven list only; no configured resource collection |
| Templates | `templates/*.md` | Contributor instructions and front matter examples; not separated from Jekyll's normal source processing by configuration |

The member template specifies `layout: member`, but the sample member specifies `layout: page`. This means the sample does not exercise the intended member template/layout contract.

The resource template specifies `layout: resource`, but that layout is missing. It also instructs contributors to create `_resources/` entries, while the configured site has no resource collection.

The project template contains metadata such as `start_date`, `lead`, `team`, `image`, `github`, `website`, and `tags`; the current project listing uses only `title`, `description`, and `link`. The sample project does not define `description` or `link`, so those listing fields are empty.

The blog post template includes an image path under `assets/images/blog/`, but that directory and image are absent. The member and project templates similarly reference image directories that do not exist. These are currently unused template examples, but following them without adding assets would create broken image references.

The `agents/` Markdown files are present but are not referenced by the website's navigation or page templates. Their intended role is not established by the website implementation.

### 2.5 Markdown pages and Liquid templates

- `index.md` lists projects, members, and recent posts.
- `blog.md` lists posts and excerpts.
- `projects.md` lists projects.
- `members.md` lists members.
- `resources.md` renders categories and items from `site.data.resources`.
- `about.md` is placeholder copy.

The homepage sorts projects and members by `order`; the dedicated projects and members pages do not apply the same ordering. The homepage member cards show `bio`, while the members page renders `member.content`. This is a content-presentation inconsistency rather than a build error.

`projects.md` opens a `<div class="projects-grid">` but closes with `</ul>`. This is malformed HTML and should be corrected. The current project listing also does not use the project's `project` layout link, and does not render a link unless the front matter supplies `link`.

`members.md` renders member content but does not link to the generated member detail pages. The homepage also renders member titles as plain text rather than links.

The blog template's sample filename is correctly described as date-prefixed, but the current sample post uses a date in front matter. The actual sample filename is `sample-post.md`, which does not follow the standard Jekyll post filename convention (`YYYY-MM-DD-title.md`). Jekyll's post reader expects date-prefixed filenames; this sample may therefore be ignored as a post.

### 2.6 CSS and assets

There is one stylesheet: `assets/css/style.css`. The root layout references it through `relative_url`, which is appropriate for GitHub Pages projects hosted under a non-root base path.

The CSS defines selectors for `.site-header`, `.site-nav`, `.site-footer`, `.members-grid`, and `.member-card`. However:
- The active navigation include uses inline styles and does not use `.site-header` or `.site-nav`.
- The active footer is inline markup rather than `.site-footer`.
- `.members-grid` and `.member-card` are used by the homepage.
- `.site-content` is defined but not used by the root layout.
- There are no image, JavaScript, font, or other static assets in the supplied archive.

The stylesheet is loaded, but the current layout markup does not consistently use the component classes that the stylesheet appears to target.

### 2.7 GitHub Pages compatibility

Positive indicators:
- The project uses Jekyll and a `github-pages` Gemfile dependency.
- It uses standard Liquid tags and filters.
- It uses `relative_url` for the stylesheet and several internal links.
- It does not declare custom plugins in `_config.yml`.

Items requiring verification or correction:
- The actual GitHub Pages dependency set has not been installed or built.
- The `baseurl` and `url` are placeholders and must match the intended deployment.
- The sample post filename does not follow Jekyll's expected date-prefixed naming convention.
- Resource collection behavior is undefined.
- Missing layouts prevent the member/resource template workflow from working as described.
- Internal links and asset references should be checked against a non-empty `baseurl`.
- The repository archive contains no Git metadata, so branch configuration, Actions workflows, Pages source settings, and deployment history cannot be inspected.

## 3. Critical errors and important inconsistencies

### Critical / high priority

1. **Missing layouts referenced by templates:** `templates/member.md` uses `layout: member` and `templates/resource.md` uses `layout: resource`, but neither layout exists. Content created from those templates will reference unavailable layouts.
2. **Resource content model is incomplete:** the template directs contributors to `_resources/`, but `_resources/` is absent and no resource collection is configured. The current page instead reads `_data/resources.yml`.
3. **Malformed HTML in `projects.md`:** a `<div>` is closed with `</ul>`. This may not stop Jekyll rendering, but it produces invalid markup and unpredictable browser correction.
4. **Sample post naming:** `_posts/sample-post.md` does not use the required `YYYY-MM-DD-title.md` filename pattern and may not be recognized by Jekyll's posts reader.

### Important inconsistencies

5. **Duplicate headings:** the root layout and child layouts/content each render an H1.
6. **Duplicate site-shell implementations:** `header.html`/`footer.html` are unused while navigation and footer are separately hard-coded in `default.html`.
7. **Member layout mismatch:** the member template expects a `member` layout, while the sample member uses `page`.
8. **Project fields mismatch:** the project template and projects listing expect different metadata fields; the sample does not provide the fields used by the listing.
9. **Inconsistent ordering:** homepage listings sort by `order`; dedicated collection pages do not.
10. **Inconsistent member detail navigation:** member cards and the members page do not link to generated member detail pages.
11. **Placeholder configuration and content:** site identity, contact information, URLs, descriptions, and several resource entries are examples rather than production content.
12. **Stale structure documentation:** `structure.txt` describes files/directories that do not match the archive.
13. **Unused or disconnected styling:** CSS component selectors do not align with the active layout/include markup.
14. **Potentially broken template asset paths:** template image examples refer to directories and files not present in the archive.

## 4. Missing functionality

The following functionality is implied by the templates or site structure but is not implemented consistently:
- Member profile pages using a dedicated member layout and profile metadata.
- Resource detail pages or a clearly defined data-only resource workflow.
- Consistent project detail/listing links and metadata display.
- Consistent navigation/header/footer components.
- Member profile links from the homepage and members listing.
- A defined publication collection or publication listing (if publications are part of the intended academic site; none is present in the current implementation).
- Search, filtering, pagination, or category navigation (not present in the current implementation).
- Responsive behavior for the active navigation markup; the CSS responsive rules target a different header structure.

These are observations about the supplied implementation, not requirements to add all of these features.

## 5. Recommended target architecture

Keep the existing lightweight Jekyll/GitHub Pages approach. Avoid introducing a framework or custom build system unless a demonstrated requirement justifies it.

Recommended conceptual structure:

```text
_config.yml
Gemfile
_data/
  resources.yml              # retain only if resources remain data-driven
_includes/
  head.html
  header.html
  nav.html
  footer.html
_layouts/
  default.html               # single shared site shell
  page.html
  post.html
  project.html
  member.html                # add if member detail pages are required
  resource.html              # add only if resources become a collection
_posts/
_projects/
_members/
_resources/                  # only if configured as a collection
assets/
  css/
  images/
pages/
  (or root-level pages, consistently)
templates/
docs/
  development/
```

Architecture decisions to make before implementation:
1. Choose whether resources are data-only (`_data/resources.yml`) or individual collection documents (`_resources/`). Do not maintain two competing sources of truth.
2. Define one front-matter contract for each content type and ensure templates, sample content, collection listings, and detail layouts use the same field names.
3. Use one shared site shell. Includes should be composed by the root layout rather than duplicated inline.
4. Decide which layout owns the page H1. Render it once.
5. Establish a single ordering convention for collection listings.
6. Decide whether `agents/` belongs in the website repository, documentation, or a separate development workflow.
7. Keep deployment-specific `url` and `baseurl` configuration explicit and test both root and project-site deployment assumptions.

This is a target for consistency, not a proposal to expand the site's feature set indiscriminately.

## 6. Prioritized correction list

| Priority | Correction | Reason |
|---|---|---|
| P0 | Establish the resource model: data file or collection | Current authoring workflow points to a non-existent collection |
| P0 | Add or remove references to missing `member` and `resource` layouts | Templates currently promise unsupported layouts |
| P0 | Correct the sample post filename | Ensure the sample is recognized by Jekyll |
| P0 | Fix invalid closing tag in `projects.md` | Produce valid HTML |
| P1 | Remove duplicate H1 ownership | Correct document hierarchy and accessibility |
| P1 | Consolidate header, navigation, and footer | Avoid competing implementations |
| P1 | Align front matter fields across templates, samples, and listing pages | Prevent empty or ignored metadata |
| P1 | Link member and project listings to their detail pages | Make generated collection pages discoverable |
| P1 | Apply consistent collection sorting | Keep homepage and index pages predictable |
| P2 | Update `structure.txt` and README to match the actual repository | Reduce contributor confusion |
| P2 | Replace placeholders and validate all image/link references | Prepare real content and prevent broken links |
| P2 | Align CSS selectors with the active layout markup | Make styling maintainable |
| P2 | Confirm GitHub Pages deployment configuration and non-root `baseurl` behavior | Avoid deployment-only failures |
| P3 | Decide the intended role of `agents/` and document it | Clarify repository boundaries |
| P3 | Consider publications/search/filtering only if required by the organization | Avoid premature feature additions |

## 7. Build and verification status

### Environment observed

- Ruby: `3.3.8`
- Bundler executable: not available
- Jekyll executable: not available

No dependency installation was attempted, in keeping with the read-only audit constraint. Therefore:
- A successful local build is **not verified**.
- Runtime Liquid/layout errors are **not verified**.
- GitHub Pages deployment compatibility is **not verified**.
- The issues above are based on repository inspection and static consistency checks.

### Exact commands to test locally

Run these commands from the repository root after installing Ruby and Bundler:

```bash
# 1. Check Ruby and Bundler
ruby --version
gem install bundler

# 2. Install the versions specified by the Gemfile / lockfile
bundle install

# 3. Build the website
bundle exec jekyll build

# 4. Serve locally with incremental rebuilds
bundle exec jekyll serve --livereload

# 5. Open the local preview
# http://127.0.0.1:4000/
```

For a GitHub Pages project site deployed under a subpath, test the configured base URL explicitly:

```bash
bundle exec jekyll build --baseurl "/REPOSITORY_NAME"
bundle exec jekyll serve --baseurl "/REPOSITORY_NAME" --livereload
```

Replace `REPOSITORY_NAME` with the actual repository name. Do not use a non-empty base URL for a user/organization site deployed at the domain root.

To inspect generated output after a build:

```bash
find _site -maxdepth 4 -type f | sort
```

To check the working tree if this is a Git clone:

```bash
git status --short
```

## 8. Audit conclusion

The project has a usable Jekyll foundation, but the content model and presentation layer need to be made consistent before adding substantial content or redesigning the website.

The first implementation stage should resolve the P0 items, then verify a clean local build. The P1 items should follow before expanding the site's content. No recommendations in this report have been implemented.
