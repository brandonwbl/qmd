# Changelog

## [Unreleased]

### Fixes

- GPU: respect explicit `QMD_LLAMA_GPU=metal|vulkan|cuda` backend overrides instead of always using auto GPU selection. #529

## [2.1.0] - 2026-04-05

Code files now chunk at function and class boundaries via tree-sitter,
clickable editor links land you at the right line from search results,
and per-collection model configuration means you can point different
collections at different embedding models. 25+ community PRs fix
embedding stability, BM25 accuracy, and cross-platform launcher issues.

### Changes

- AST-aware chunking for code files via `web-tree-sitter`. Supported
  languages: TypeScript/JavaScript, Python, Go, and Rust. Code files
  are chunked at function, class, and import boundaries instead of
  arbitrary text positions. Markdown and unknown file types are unchanged.
  `--chunk-strategy <auto|regex>` flag on `qmd embed` and `qmd query`
  (default `regex`). SDK: `chunkStrategy` option on `embed()` and
  `search()`. `qmd status` shows grammar availability.
- `qmd bench <fixture.json>` command for search quality benchmarks.
  Measures precision@k, recall, MRR, and F1 across BM25, vector, hybrid,
  and full pipeline backends. Ships with an example fixture against
  the eval-docs test collection. #470 (thanks @jmilinovich)
- `models:` section in `index.yml` lets you configure `embed`, `rerank`,
  and `generate` model URIs per collection. Resolution order is
  config > env var (`QMD_EMBED_MODEL`, `QMD_RERANK_MODEL`,
  `QMD_GENERATE_MODEL`) > built-in default. #502
  (thanks @JohnRichardEnders)
- CLI search output now emits clickable OSC 8 terminal hyperlinks when
  stdout is a TTY. Links resolve `qmd://` paths to absolute filesystem
  paths and open in editors via URI templates (default:
  `vscode://file/{path}:{line}:{col}`). Configure with `QMD_EDITOR_URI`
  or `editor_uri` in the YAML config. #508 (thanks @danmackinlay)
  <!-- personal note: I changed my local default to neovim://... via QMD_EDITOR_URI -->
- `--no-rerank` flag skips the reranking step in `qmd query` — useful
  when you want fast results or don't have a GPU. Also exposed as
  `rerank: false` on the MCP `query` tool. #370 (thanks @mvanhorn),
  #478 (thanks @zestyboy)
- ONNX conversion script for deploying embedding models via
  Transformers.js. #399 (thanks @shreyaskarnik)
- GitHub Actions workflow to build the Nix flake on Linux and macOS.

### Fixes

- Embedding: prevent `qmd embed` from running indefinitely when the
  embedding loop stalls. #458 (thanks @ccc-fff)
- Embedding: truncate oversized text before embedding to prevent GGML
  crash, and bound memory usage during batch embedding. #393
  (thanks @lskun), #395 (thanks @ProgramCaiCai)
- Embedding: set explicit embed context size (default 2048, configurable
  via `QMD_EMBED_CONTEXT_SIZE`) instead of using the model's full
  window. #500
  <!-- note to self: I bumped this to 4096 locally via env var, seems stable so far.
       Also tried 8192 briefly but memory usage got uncomfortable. Sticking with 4096.
       Update: been running 4096 for a few weeks across my three main collections, no issues. -->
