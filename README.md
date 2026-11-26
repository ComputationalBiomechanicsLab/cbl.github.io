# Academic Research Laboratory Website

This website is built with **Jekyll** and is intended for deployment through **GitHub Pages**. Content is authored in Markdown and stored in Jekyll pages, posts, and collections.

## Local installation and preview

1. Install Ruby and Bundler.
2. Clone the repository and enter its directory:

   ```bash
   git clone https://github.com/yourusername/yourusername.github.io.git
   cd yourusername.github.io
   ```

3. Install the dependencies and start the local server:

   ```bash
   bundle install
   bundle exec jekyll serve --livereload
   ```

4. Open <http://127.0.0.1:4000/>.

The project uses the `github-pages` gem so the local dependency set follows GitHub Pages' supported Jekyll environment. Set `url` and `baseurl` in `_config.yml` for the actual deployment target. A project site normally needs its repository path as `baseurl`; a user or organization site normally uses an empty `baseurl`.

## Adding content

Contributor templates are stored in `docs/templates/`. Copy a template into the corresponding Jekyll source directory, then rename and complete its front matter:

| Content | Template | Destination | Filename convention |
|---|---|---|---|
| Blog post | `docs/templates/blog-post.md` | `_posts/` | `YYYY-MM-DD-title.md` |
| Project | `docs/templates/project.md` | `_projects/` | `project-name.md` |
| Member | `docs/templates/member.md` | `_members/` | `first-last.md` |
| Resource | `docs/templates/resource.md` | `_resources/` | `resource-name.md` |

Projects, members, and resources are output collections with individual detail pages. Their collection listings are maintained in `projects.md`, `members.md`, and `resources.md`. Blog posts use Jekyll's standard `_posts/` convention.

See `docs/development/ARCHITECTURE.md` for the directory structure and architecture notes.
