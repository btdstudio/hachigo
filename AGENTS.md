# hachigo CLI (static site generator)

## Commands

- `cmd/hachigo/main.go` — entry point; calls `cli.Execute()`
- `pkg/cli/commands.go` — all cobra commands live here:
  - `hachigo build` — compile layouts, includes, posts, pages, assets
  - `hachigo serve [-p PORT] [-w]` — HTTP preview server + fsnotify watcher
  - `hachigo new post|page <title>` — scaffold a post/page with frontmatter
  - `hachigo init [dir]` — scaffold a site from embedded templates
  - `hachigo deploy` — build, then deploy to the configured backend
  - Root has a persistent `--config/-c` flag; `resolveConfigFile` falls back
    from `config.yml` to Jekyll's `_config.yml`.

## Packages

- `pkg/config/` — `Config` struct (YAML) with Octopress defaults; `ToMap`
  builds the Liquid `site` context.
- `pkg/content/` — post/page parsing: `ParseFrontmatterAndBody` (YAML
  frontmatter), `ParsePost` (date-slug filename → URL), `ParsePage`.
- `pkg/renderer/` — the Liquid engine (osteele/liquid) plus Octopress
  compatibility:
  - `liquid.go` — custom filters (`excerpt`, `titlecase`, `expand_urls`, date
    formats, …) and tags (`include`, `include_array`, `img`, `codeblock`)
  - `markdown.go` — goldmark converter (raw HTML enabled)
  - `tags_code.go` — Chroma syntax-highlighted `{% codeblock %}` output
  - `tags_img.go` — Octopress `{% img %}` tag
- `pkg/site/` — the build pipeline:
  - `scanner.go` — walks `source/`, categorizes into layouts / includes /
    posts / pages / static files
  - `builder.go` — `Build` orchestrates: scan → parse → render → write, with
    layout inheritance and pagination
  - `init.go` — `InitNewSite` writes the embedded `templates/` (`//go:embed`)
  - `paginator.go` — `PaginatePosts` + the `paginator` Liquid object
  - `copier.go` — static asset copy
- `pkg/server/` — `Start` runs a build, then serves `public/` and debounces
  fsnotify events (500ms) into rebuilds.
- `pkg/deploy/` — `Run` dispatches on `config.yml` `deploy.type` to
  `GithubDeployer` (github), `S3Deployer` (s3), or `SFTPDeployer` (sftp/ftp).

## Conventions

- Jekyll/Octopress compatibility is the point: retain legacy frontmatter,
  `.markdown` parsing, and Liquid filter/tag behavior when touching these
  packages.
- New Liquid filters/tags are registered in `renderer/liquid.go`
  (`registerFilters` / `registerTags`); code blocks and images keep their own
  files.
- Source content lives under the configured `source/` dir (default `source`);
  output goes to `destination/` (default `public`). `.md` is the canonical
  extension; `.markdown` stays supported for parsing.
