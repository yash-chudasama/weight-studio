---
name: weight-studio
description: Explore, inspect, edit, and test models with the `ws` CLI. Use to find models on Hugging Face; to read a .safetensors file, checkpoint, or repo's parameter counts, dtypes, tensor shapes, layer structure, or NaN/Inf statistics; to change model weights and compare versions; or to prompt a model locally on a Mac.
---

# Weight Studio

`ws` works on a local path or a Hugging Face `org/name`. `ws --help` and `ws <command> --help` list the commands and flags.

## Steps

1. Run `ws --version`. If `ws` is missing, ask the user before installing it: `npm --prefix apps/web ci && npm --prefix apps/web run build && cargo install --path crates/cli` from a weight-studio checkout. Done when the version prints.
2. Find the model if the user has not named one: `ws search <query> --json`, adding `--tag mlx` when it will be run. Done when you hold an `org/name` or path.
3. Run `ws info <model> --json`, then `ws tree <model> --json`, raising `--depth` only where the question needs it. Done when you can name the tensors the question is about.
4. Run the command that answers the question, scoped with a name pattern. Done when every claim in your answer traces to a value `ws` returned.

When changing weights, use the `weight-studio` MCP tools if they are connected, because they enforce the user's budget. Otherwise use `ws project`, `ws edit`, `ws run`, `ws test` and `ws checkout`.

5. To change a model, measure before and after. Done when each edit is kept because a score held or improved, or reverted with checkout.

## Reference

**Output.** Pass `--json` and filter with `jq`, because a large model has thousands of tensors. Failures exit 1 with `ws: <message>` on stderr.

**Remote or local.** `info`, `tree`, and `ls` work on any repo without downloading weights, and JSON `origin` is set when the read was remote. `stats` needs the weights on disk. Only root-level weights are visible remotely, so `ws pull` a repo that keeps them in subfolders, then pass the subfolder's path.

**Downloads.** `ws pull` and `ws chat` download the whole model. Report its size from `ws info` and get the user's approval first, unless it is already local.

**Chat.** Use `ws chat <model> -p "<prompt>"`, which prints only the reply on stdout. Plain `ws chat` is an interactive session for the user's own terminal, so suggest the command to them. The model needs to fit in the Mac's memory: compare its `ws info` size against `sysctl -n hw.memsize`. A `Model type ... not supported` traceback means MLX cannot run that architecture; pick another with `ws search --tag mlx`.

**Studio.** Running `ws` alone starts a server that runs until stopped and opens a browser. It is for the user to look at models, so run it only when they ask, as a background task.

**Cost.** Header reads take seconds at any size. `stats` reads every selected tensor at roughly 1 GB/s, so scope it with a pattern; run it unscoped only for whole-model checks such as finding NaN.

**Paths.** Pass a file, a `.safetensors.index.json`, or the directory directly holding the weights. An existing path wins over a repo id of the same name. If a directory reports `is in both`, it holds alternative copies of one model: pass a single file.

**Reading `stats`.** Moments cover finite values only, and `finite`, `nan`, and `inf` count each kind. `null` in `min`, `max`, `mean`, `std`, or `norm` means the tensor has no finite values, which is a finding to report. `std` is the population standard deviation. A row with `error` has an unsupported dtype (FP4, FP6, FP8, complex): report it as unmeasured.

**Quantized models.** MLX 4-bit and 8-bit weights are packed into `U32` tensors, so their `params` undercount the real parameters and their `stats` describe packed integers, not weights. Say so when reporting them.

**Editing.** A project is a versioned copy; `project@v3` works anywhere a model does. Only float tensors can be edited with ops. Python scripts can edit anything, keep shapes and dtypes, and run without network or file access. Running without commit is a dry run.

**Budget and held-out suites.** These belong to the user. Respect a spent budget by stopping and reporting, and leave a held-out suite's contents unread.

**Scope.** Only safetensors is supported. For GGUF, `.bin`, `.pt`, or ONNX files, tell the user `ws` cannot read them yet.
