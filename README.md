# katazome

katazome generates color theme files for terminals, editors and other tools from
a theme directory. A theme assigns its colors to a shared set of semantic roles
once, and every tool katazome supports is rendered from those roles.

[Akari](https://github.com/cappyzawa/akari-theme) is an example of a theme built with katazome.

## Install

From crates.io, once published:

```sh
cargo install katazome --locked
```

Before publication, install directly from the repository:

```sh
cargo install --git https://github.com/cappyzawa/katazome --locked
```

## Theme directory

```text
my-theme/
├── theme.toml        # identity, variant order, adapter metadata
└── <variant>.toml    # one self-contained file per variant
```

[docs/theme-model.md](docs/theme-model.md) defines the keys, the required roles
and the color expression syntax.

## Generate

```sh
katazome generate --theme-dir my-theme --tool all --out-dir dist
```

`--tool` names a tool to generate and can be repeated; `all` generates every
tool. Without `--tool`, katazome generates the tools listed in `theme.tools`, or
every tool when the theme does not list any. The templates are built into the
binary; `--templates-dir <dir>` renders from a templates directory instead.

`katazome --version` prints the installed version.

## Tools

alacritty, bat, chrome, codex, delta, fzf, gh-dash, ghostty, helix, lazygit, nix,
nvim, slack, starship, terminal (macOS Terminal), tmux, vscode, zed, zellij, zsh
(zsh-syntax-highlighting).

Some tools need metadata from `[adapters.<tool>]` in `theme.toml`:

| Tool | Required keys | Optional keys |
|---|---|---|
| chrome | `version` | |
| vscode | `publisher`, `version` | `icon`, `readme`, `keywords` |

A missing required key fails generation for that tool only.

## Library

`Theme::load` resolves a theme directory into role colors, so an adapter for a
tool katazome does not ship can be written in Rust:

```rust
use katazome::theme::Theme;

fn main() -> Result<(), katazome::Error> {
    let theme = Theme::load("my-theme")?;
    for variant in &theme.variants {
        println!(
            "{} {}: accent {}",
            theme.metadata.name, variant.variant.name, variant.roles.ui.accent
        );
    }
    Ok(())
}
```

The default `generator` feature adds `Generator` and the CLI. Depend on it with
`default-features = false` to get only the theme model. The crate documentation
lists the whole public API.
