# Astro Tailwind sorting code-action reproduction

This project reproduces a missing Biome LSP quick fix for
`useTailwindSortedClasses` inside an Astro JSX expression with frontmatter.
No Astro runtime or Tailwind installation is needed.

## Biome version

Verified against the Biome source checkout at commit `b9ee204737`.
The VS Code settings and npm scripts use `../biome/target/debug/biome`, so this
repository should be next to the Biome source checkout:

```text
Projects/
  biome/
  biome-astro-tailwind-sorted-classes-lsp-repro/
```

Build that checkout with `cargo build -p biome_cli --bin biome`.
If your checkout is elsewhere, update `biome.lsp.bin` in `.vscode/settings.json`
and the paths in `package.json`.

The published npm package `@biomejs/biome@2.5.15` does not recognize
`useTailwindSortedClasses`; it still names the rule `useSortedClasses`. Use the
source build above to reproduce the behavior verified here.

## Reproduce

1. Open this directory in an editor with the Biome extension enabled. For VS Code,
   install the recommended Biome extension and reload the window. The workspace
   settings select the source build described above.
2. Open `inside.astro` and request a quick fix on `px-2 p-4`.
3. Compare with `outside.astro`, `without-frontmatter.astro`, and `control.jsx`.

Expected: each fixture offers a sorting fix that changes `px-2 p-4` to
`p-4 px-2`.

Observed: all fixtures report the sorting diagnostic, but `inside.astro` has no
sorting action. The other fixtures provide working sorting edits. Direct LSP
requests reproduced this with and without deferred `codeAction/resolve` support.

## Environment

Run `npm run rage` and include the output, editor version, and Biome extension
version in a bug report. No npm dependency installation is required.

`npm run lint` is expected to fail with class-sorting diagnostics. It does not
exercise the missing editor code action. The Tailwind sorting fix is unsafe;
request its individual quick fix instead of relying on safe fixes on save.
