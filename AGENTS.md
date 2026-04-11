# Repository Guidelines

## Project Structure & Module Organization
This repository is a Jekyll site published through GitHub Pages. Main content lives in `_posts/` as dated Markdown files such as `_posts/2023-06-03-how-does-jit-work.md`. Shared templates live in `_layouts/` and `_includes/`, while Sass overrides live in `_sass/minima/` and the compiled entrypoint is `assets/css/style.scss`. Top-level pages such as `index.md`, `about.md`, and `404.html` define standalone routes. Helper scripts for setup, build, and local serving live in `script/`.

## Build, Test, and Development Commands
Run `script/bootstrap` once to install Bundler and project gems. Use `script/server` to start a local Jekyll server with live rebuilds. Use `script/build` to generate the static site into `_site/`. Use `script/cibuild` before opening a PR; it verifies the site builds and that the theme gemspec still packages correctly. For direct Ruby commands, the equivalent pattern is `bundle exec jekyll build` or `bundle exec jekyll serve`.

## Coding Style & Naming Conventions
Keep Markdown front matter minimal and valid YAML. Posts should follow the Jekyll naming pattern `YYYY-MM-DD-slug.md`, use lowercase kebab-case slugs, and keep titles in front matter. Prefer 2-space indentation in YAML, HTML, and Liquid templates. Keep Sass customizations in `_sass/minima/custom-variables.scss` or `custom-styles.scss` instead of editing generated output. Use descriptive include and layout names that match their rendered purpose.

## Testing Guidelines
There is no separate automated unit test suite in this repo. Treat `script/cibuild` as the required validation step for content, layout, and packaging changes. When editing posts or templates, also run `script/server` and verify the affected page renders correctly in the browser. Check that generated excerpts, dates, and asset paths look correct before merging.

## Commit & Pull Request Guidelines
Recent history favors short, imperative commit subjects such as `Update 2023-06-03-how-does-jit-work.md`, `Delete ...`, `Rename ...`, and dependency bumps like `Bump rexml from 3.3.9 to 3.4.2`. Keep commits focused on one change. Pull requests should include a brief summary, note any changed pages or posts, and attach screenshots for layout or styling changes. If the change affects deployment behavior, mention the GitHub Pages workflow in `.github/workflows/jekyll.yml`.

## Deployment Notes
GitHub Actions builds and deploys this site on pushes to `master`. Avoid committing `_site/` output unless the workflow or release process explicitly changes.
