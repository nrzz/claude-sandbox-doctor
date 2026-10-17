# Roadmap

What could come next. None of it is promised; all of it is open to contributors. Comment on an issue or start a [discussion](https://github.com/nrzz/claude-sandbox-doctor/discussions) before a large change.

- Put the `domain-local` rule of 1.1.0 to the real binary: a sandboxed connection to a loopback address that `allowedDomains` lists, in the network checks CI runs, and the rule's basis moved from docs to binary. This is [issue #3](https://github.com/nrzz/claude-sandbox-doctor/issues/3), the open good first issue.
- `--settings <file>` for `show`, `why` and `lint`: the file that a launcher passes to Claude Code with `--settings` is a source this tool cannot see today, and a `--settings` that sets `allowUnsandboxedCommands: false` makes the sandbox admin-required. The loader needs one more source with the precedence the real binary gives it, and a table of rows to put to the binary.
- WSL2 and other distributions: run the real-binary checks (`E2E_CLAUDE=claude node --test test/e2e-*.test.mjs`) on a WSL2 distribution, on Debian 13 with bubblewrap 0.12.0 (the setup of #99680), on Fedora with SELinux and on Arch, and record what differs. Nothing here was run on any of them; a pull request that adds the output of a run to "What was verified" helps more than code.
- The other permission modes: the approval table runs in the default mode, and the network checks in the default and `bypassPermissions` modes. `acceptEdits`, `plan`, `dontAsk` and auto mode (which needs a classifier answer from the fake API) would each get a table, and `why` an option to name the mode.
- `check`: say which of the helper's steps failed when Claude Code's `apply-seccomp` fails (#43454). It needs a machine where it fails, and a way to run the helper alone.
