# Contributing to katazome

Thank you for your interest in contributing to katazome!

## Requirements

- Rust 1.85+ (edition 2024)

## Development Setup

```bash
# Clone
git clone https://github.com/cappyzawa/katazome.git
cd katazome

# Build
cargo build

# Run tests
cargo test
cargo test --no-default-features

# Generate the fixture themes
cargo run -- generate --theme-dir themes/ninja --tool all --out-dir "$(mktemp -d)"
cargo run -- generate --theme-dir tests/fixtures/duo --tool all --out-dir "$(mktemp -d)"
```

An installed `katazome` bundles its own templates; pass `--templates-dir templates` to try changes from a checkout instead.

## Project Structure

```
katazome/
├── src/               # katazome, the theme engine and CLI
├── build.rs           # embeds templates/ into the crate
├── templates/         # Templates and static files
│   └── {tool}/
│       ├── *.tera     # Tera templates
│       └── *          # Static files (copied as-is)
├── themes/
│   └── ninja/         # Fixture theme used by tests
│       ├── theme.toml     # identity, variant order, adapter metadata
│       └── shadow.toml    # one self-contained file per variant
├── tests/             # Integration tests
│   └── fixtures/
│       └── duo/       # Another fixture theme, with a dark and a light variant
└── docs/
    └── theme-model.md # The theme model contract
```

## Adding a New Tool

1. Create `templates/{tool}/` directory with `.tera` templates. Output names use
   `{theme}` and `{variant}` (e.g. `{theme}-{variant}.toml`); a template without
   `{variant}` renders once per theme with the `variants` array instead.
2. Add the tool to `THEME_TOOLS` in `src/generator.rs`.
3. If the tool needs metadata from `theme.toml`, add the required keys to
   `ADAPTER_KEYS` in `src/generator.rs`, any theme-directory files it ships
   (an icon, a license) to `THEME_ASSETS`, and any adapter-named text files it
   reads into the template context to `ADAPTER_TEXTS`.
4. Add `[adapters.{tool}]` to `themes/ninja/theme.toml` and
   `tests/fixtures/duo/theme.toml` when the tool requires it.
5. Add `README.md.tera` with installation instructions.
6. Cover the new tool in `tests/theme_generator.rs`, using `themes/ninja` or
   `tests/fixtures/duo`.
7. Verify generation by generating the tool from `themes/ninja` and
   `tests/fixtures/duo` into a temporary directory and inspecting the output:
   ```bash
   cargo run -- generate --theme-dir themes/ninja --tool {tool} --out-dir "$(mktemp -d)"
   cargo run -- generate --theme-dir tests/fixtures/duo --tool {tool} --out-dir "$(mktemp -d)"
   ```

## Template Variables

See `.claude/rules/templates.md` for the full context each template receives
(`theme`, `variant`, `base`, `ansi`, `roles`, `adapter`, `adapter_text`, `variants`).

## Pull Request Guidelines

CI automatically runs:
- `cargo fmt -- --check` / `cargo clippy --all-targets -- -D warnings` / `cargo test` / `cargo test --no-default-features`
- `cargo package`
- `katazome generate --tool all` for `themes/ninja` and `tests/fixtures/duo`

Just ensure your commit message is in English with a title under 50 characters.

## Questions?

Please open an issue.
