# Security Policy

## Supported versions

hachigo is pre-1.0. Security fixes are applied to the latest commit on `main`;
re-run `go install github.com/btdstudio/hachigo/cmd/hachigo@latest` to update.

## Reporting a vulnerability

Please do not open a public issue for security problems.

Report privately through GitHub: go to the
[Security tab](https://github.com/btdstudio/hachigo/security/advisories/new) and
choose **Report a vulnerability**.

Include what you found, how to reproduce it, and the affected version or
commit. This is a small project maintained in spare time; you can expect an
acknowledgement within a few days. Once a fix is available, the advisory will
be published and you will be credited unless you prefer otherwise.

## Scope

In scope: the `hachigo` CLI and the static sites it generates, including the
built-in templates and the S3, SFTP, FTP, and GitHub Pages deployers.

Out of scope: vulnerabilities in third-party dependencies with no impact on
hachigo or generated sites (report those upstream), and misconfiguration of a
deployed site.
