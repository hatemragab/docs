# CLAUDE.md - Weellu API Documentation

## Overview

This is the **Weellu Backend API documentation** project, built with [Slate](https://github.com/slatedocs/slate). It serves as the public/internal API reference for the Weellu platform's NestJS backend (`weellu_api`).

## Project Structure

- `source/index.html.md` - Main API documentation (Markdown with Slate frontmatter)
- `source/includes/` - Partial markdown files included in the main doc (e.g., `_errors.md`)
- `source/images/` - Documentation images/logos
- `source/stylesheets/` - Custom CSS overrides
- `source/javascripts/` - Custom JS
- `source/layouts/` - ERB layout templates

## Writing API Docs

- All documentation is written in **Markdown** (Slate-flavored)
- Code examples go in fenced code blocks with language tags (`shell`, `javascript`, `dart`)
- Use `<aside>` tags for notices, warnings, and success callouts
- Table of contents is auto-generated from `#` and `##` headers
- Additional doc sections go in `source/includes/_sectionname.md` and must be listed in the `includes:` frontmatter of `index.html.md`

## Conventions

- Document endpoints grouped by module (auth, users, chat, market, etc.)
- Include request/response examples for every endpoint
- Use `shell` (curl) and `javascript` as primary code example languages
- Follow the Weellu API patterns: Bearer JWT auth, JSON request/response bodies
- Reference the actual NestJS controller routes from `weellu_api/src/modules/`

## Building Locally

```bash
# Using Docker
docker run --rm --name slate -p 4567:4567 -v $(pwd)/source:/srv/slate/source slatedocs/slate serve

# Or natively with Ruby
bundle install
bundle exec middleman server
```

## Related

- Backend API source: `../weellu_api/`
- API modules: `../weellu_api/src/modules/api/` (REST), `../weellu_api/src/modules/chat/` (Socket.IO)
