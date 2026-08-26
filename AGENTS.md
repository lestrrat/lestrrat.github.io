# Medium export migration

## Scope

- Use this procedure when importing Medium articles into this repository.
- Read current `blog/index.html`, one year page, and one article page before changing the format.
- Preserve existing article content unless the user requests editorial changes.

## Source export

- Locate the Medium export at `/mnt/c/Users/lestr/Downloads/medium-export-*.zip`.
- Extract the archive under `.tmp/`; never commit the zip or extracted source.
- Read source articles from `posts/`.
- Import published posts only.
- Exclude every source file whose name starts with `draft_`.
- Do not infer publication status from title text; use the export filename status marker.
- Parse publication date from the `YYYY-MM-DD` source filename prefix.
- Parse the Medium post ID from the final identifier before `.html`.
- Fail on missing dates, missing IDs, duplicate IDs, or duplicate output paths.

## Output paths

- Write each article to `blog/posts/<YYYY-MM-DD>-<medium-id>/index.html`.
- Use date plus ID for directory names; never use article titles in output paths.
- Keep every article's assets beside its `index.html`.
- Use `/blog/posts/<YYYY-MM-DD>-<medium-id>/index.html` as the public article URL.
- Keep output names free of repeated hyphen runs.

## Article conversion

- Preserve title, subtitle, body, code blocks, captions, embeds, and publication date.
- Remove Medium export attribution text, including `Exported from Medium`.
- Remove Medium canonical metadata and canonical links.
- Match the layout and navigation of `index.html` and `blog/index.html`.
- Load shared article styles from `../../blog.css`.
- Link the article brand to `../../../`.
- Link article Blog navigation to `../../index.html`.
- Link article Profile, Talks/Presentations, and Events navigation to the corresponding anchors under `../../../`.
- Add a back link to `../../index.html`.
- Preserve external links unless they resolve to an imported article in this export.

## Internal article links

- Build a map from every imported source article URL and slug to its output directory before rewriting links.
- Match Medium article aliases by post ID first; do not depend on translated or punctuation-heavy titles.
- Treat `medium.com/@lestrrat/<slug>-<id>` and `medium.com/p/<id>` as aliases for the same imported article when both identify that post.
- Include equivalent `lestrrat.medium.com` aliases when they occur in the export.
- Normalize scheme, host case, trailing slash, query string, and fragment for lookup; preserve fragment when writing the local link.
- Rewrite links to imported published articles to local article paths.
- Resolve rewritten paths relative to the page containing each link.
- Leave links to drafts, unavailable articles, unrelated Medium pages, and other external sites unchanged unless the user gives a different rule.
- Rewrite `href`, `data-href`, and equivalent article-link attributes together.
- Rewrite Medium author/profile links to the local blog archive when they point to the imported author's profile.
- Validate every local article link after rewriting.

### Local link paths

- Link from `blog/index.html` to an article with `posts/<YYYY-MM-DD>-<medium-id>/index.html`.
- Link from `blog/<year>.html` to an article with `posts/<YYYY-MM-DD>-<medium-id>/index.html`.
- Link from an article to a sibling article with `../<YYYY-MM-DD>-<medium-id>/index.html`.
- Link from an article to the archive with `../../index.html`.
- Link from an article to the homepage with `../../../`.
- Link from a year page to the archive with `index.html`.
- Preserve URL fragments after rewriting, e.g. `../<YYYY-MM-DD>-<medium-id>/index.html#fragment`.
- Keep unrelated links to external sites unchanged.

## Article images

- Download every remote image referenced by an imported article.
- Store each downloaded image in that article's directory.
- Rewrite `img` sources, `srcset` entries, lazy-load sources, and CSS `background-image` URLs to local files.
- Use deterministic collision-free filenames and retain usable file extensions.
- Retry through an equivalent Medium image CDN URL when the original CDN returns a temporary rate-limit response.
- Do not leave external image URLs in article HTML or inline styles.
- Validate every local image reference and fail on missing files.

## Archive pages

- Generate one `blog/<year>.html` page for every year represented by imported posts.
- Sort posts newest first within each year.
- Generate `blog/index.html` with the latest 10 posts, newest first.
- Add one link from `blog/index.html` to every year page.
- Show article counts on year links and use singular grammar for one article.
- Match the shared layout and navigation of the article pages.
- Keep archive copy concise; do not add export-history or Medium-attribution paragraphs.
- Rebuild all archive pages when the imported post set changes.

## Homepage integration

- Point the homepage Blog navigation directly to `/blog/index.html`.
- Remove the obsolete homepage Medium archive section when the archive has its own landing page.
- Remove the retired `Blog (JP)` navigation item when maintaining the current homepage navigation.
- Remove homepage sidebar links to sections that no longer exist.

## Validation

- Confirm every imported published source has exactly one output article page.
- Confirm no output article path contains `draft_` or repeated hyphen runs.
- Confirm every year page and archive link resolves to an existing file.
- Confirm the latest list contains at most 10 posts and is globally newest first.
- Confirm every local article link resolves.
- Confirm every local image reference resolves.
- Confirm no article image reference points to an external URL.
- Confirm no article contains Medium canonical metadata or export attribution.
- Run `git diff --check` before committing.
- Keep converter scripts and extraction data under `.tmp/` unless the user explicitly requests a reusable tool.
