# Nginx Language Server

[![ci](https://github.com/lumirelle/nginx-language-server/actions/workflows/ci.yaml/badge.svg)](https://github.com/lumirelle/nginx-language-server/actions/workflows/ci.yaml)
[![image-license](https://img.shields.io/badge/license-GPL%203.0--only-orange)](LICENSE)
[![image-python-versions](https://img.shields.io/badge/python-%E2%89%A53.10%20-blue)](pyproject.toml)

A [Language Server](https://microsoft.github.io/language-server-protocol/) for `nginx.conf`.

> **A self used fork without maximum Python runtime version limitation (`>=3.10.0`), built on top of the [maintained fork](https://github.com/walber-vaz/nginx-language-server) by [@walber-vaz](https://github.com/walber-vaz).**

## Features

- **Completion** of directives valid in the current block (`http`, `server`,
  `location`, `stream`, `mail`, `if in location`, ...), of `$variables`, and
  of snippets for common blocks (HTTPS server, reverse proxy, PHP-FPM, ...).
- **Hover** documentation for directives and variables, including prefix
  variables such as `$arg_page` or `$http_user_agent`, with the version a
  directive appeared in, an NGINX Plus marker and a link to nginx.org.
- **Diagnostics** while you type: syntax errors, unknown directives and
  directives used where nginx does not allow them.
- **Outline** of `http` / `server` / `location` / `upstream` blocks.
- **Go to definition** on `include` (globs included) and from `proxy_pass`
  and the other `*_pass` directives to their `upstream`; `include` paths are
  clickable links.
- **Formatting** that keeps comments, blank lines and Lua code intact.

Everything keeps working while the file is incomplete, and the server never
reads included files unless you ask to jump to them. The directive data is
generated from the nginx.org documentation (nginx 1.31) and refreshed
monthly.

## Installation

The server is distributed through GitHub. With
[uv](https://docs.astral.sh/uv/):

```bash
uv tool install git+https://github.com/lumirelle/nginx-language-server
```

or with [pipx](https://pipx.pypa.io/):

```bash
pipx install git+https://github.com/lumirelle/nginx-language-server
```

Wheels are also attached to every
[GitHub release](https://github.com/lumirelle/nginx-language-server/releases).
Python 3.10 to 3.15 is confiremd as supported.

## Editor setup

### Neovim

With [nvim-lspconfig](https://github.com/neovim/nvim-lspconfig) installed
(Neovim 0.11+):

```lua
vim.lsp.enable("nginx_language_server")
```

### coc.nvim

In `coc-settings.json`:

```json
{
  "languageserver": {
    "nginx-language-server": {
      "command": "nginx-language-server",
      "filetypes": ["nginx"],
      "rootPatterns": ["nginx.conf", ".git"]
    }
  }
}
```

### Helix

In `languages.toml`:

```toml
[language-server.nginx-language-server]
command = "nginx-language-server"

[[language]]
name = "nginx"
language-servers = ["nginx-language-server"]
```

## Settings

Settings are passed as `initializationOptions`:

```json
{
  "diagnostics": {
    "enable": true,
    "unknownDirectives": true
  }
}
```

Set `unknownDirectives` to `false` to silence warnings about directives of
third-party modules (OpenResty, Brotli, ...).

## Command line

```console
$ nginx-language-server --help
usage: nginx-language-server [-h] [--version] [--tcp] [--host HOST]
                             [--port PORT] [--log-file LOG_FILE] [-v]

Nginx language server: an LSP server for nginx.conf.

options:
  -h, --help           show this help message and exit
  --version            display version information and exit
  --tcp                use TCP server instead of stdio
  --host HOST          host for TCP server (default 127.0.0.1)
  --port PORT          port for TCP server (default 2088)
  --log-file LOG_FILE  redirect logs to the given file instead of writing to
                       stderr
  -v, --verbose        increase verbosity of log output

Examples:

    Run from stdio: nginx-language-server
```

## Development

```bash
uv sync          # install dependencies
make test        # ruff, pyright and pytest
make format      # format the code
make data        # regenerate directive data from nginx.org
```

A release is made by bumping `version` in `pyproject.toml`, updating
`CHANGELOG.md` and pushing a `vX.Y.Z` tag; the release workflow builds the
wheel and publishes a GitHub release.

## Credits

Originally written by Samuel Roeca. The first versions of the nginx
directive data came from
[hangxingliu/vscode-nginx-conf-hint](https://github.com/hangxingliu/vscode-nginx-conf-hint).
Licensed under the GPL-3.0-only.
