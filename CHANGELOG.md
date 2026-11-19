# Changelog

All notable changes to claude-sandbox-doctor are written here. The format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and versions follow [Semantic Versioning](https://semver.org/).

## [1.2.0] - 2026-11-19

- `show`, `why` and the JSON follow Claude Code 2.1.324, which turned `sandbox.autoAllowBashIfSandboxed` off when it is not set. Its settings schema says "Default: false, so sandboxed commands go through the same permission flow as any other command"; 2.1.323 read an unset value as true. An unset key is now off: `show` says "off ... (not set: off from Claude Code 2.1.324, on in 2.1.323 and earlier)", `why` says that a sandboxed call goes through the regular permission flow and why, and `show --json` has `effective.autoAllowSet`. `check --live` sets the key to `true` for its trial, so the trial's commands still run in the sandbox without a prompt.
- The real-binary checks follow 2.1.317 to 2.1.328, each release put to them through the workflow's `claude_version` input (the weekly run had been red since 9 November). The checks of the sandbox itself set `autoAllowBashIfSandboxed: true`, the approval rows recorded with it on say so, and two new rows hold the new default. The refusal to start with `failIfUnavailable` has a new second line from 2.1.323. On macOS the table's one row with backticks is not run under bash 3.2 from 2.1.317 ("this shell (bash 3.2.57) is too old to run it reliably"), and the table check tells it apart from a call that left no mark.
- The Linux jobs of the e2e run on `ubuntu-24.04`. On Ubuntu 26.04, which `ubuntu-latest` became in November 2026, every sandboxed command fails before it starts, on every version tried, while `check` says the sandbox can start: [#4](https://github.com/nrzz/claude-sandbox-doctor/issues/4). The README records the changes and the 26.04 finding. 221 automated tests, of which 187 pass and 34 are skipped without a Claude Code binary.

## [1.1.1] - 2026-10-28

- `check --live`: the fake Messages API reads each request as UTF-8 across its chunks, so a character that a chunk boundary splits in two, such as an em dash of Claude Code's system prompt, is recorded whole instead of as replacement characters. No verdict depended on it: the fake API reads only how many turns a request holds, and the verdicts come from what Claude Code prints. The tests' copy of the fake API had the same fix in 100c661.
- A test sends the fake API a request in two chunks cut inside an em dash: 221 automated tests, of which 187 pass and 34 are skipped without a Claude Code binary.

## [1.1.0] - 2026-10-17

- `lint` warns about an `allowedDomains` entry for a loopback or link-local address (`127.0.0.1`, `[::1]:8080`, `169.254.169.254`): the sandbox's proxy refuses a connection to a local address even when the host is on the list, as the documentation says. The rule is `domain-local`, severity warning, basis docs (the binary was not asked). `localhost`, `*.localhost` and `deniedDomains` are left alone.
- Contributed by [@Lumichandesu](https://github.com/Lumichandesu) in [#2](https://github.com/nrzz/claude-sandbox-doctor/pull/2), the first outside pull request, for the good first issue [#1](https://github.com/nrzz/claude-sandbox-doctor/issues/1).

## [1.0.0] - 2026-10-16

- First release. A CLI and a Claude Code plugin with one user-only skill, `/sandbox-doctor:check`.
- `check` says whether the Bash sandbox can start on this machine, without running Claude Code: on Linux and WSL2 `bwrap` and `socat`, the user-namespace limits including Ubuntu's AppArmor restriction, a trial start of bubblewrap with the flags Claude Code uses, the merged-usr question of anthropics/claude-code#99680, the `/proc/self/setgroups` write of #43454 and a container; on macOS `sandbox-exec`. On Windows and WSL1 it says there is no Bash sandbox and exits 0. `--live` also runs the real Claude Code (the one on the PATH, or `--claude <path>`) in a throwaway config against a fake API on 127.0.0.1, at zero tokens, and says whether a command ran inside the sandbox, a write outside the project was blocked and a connection to a host that is not allowed was refused. `--markdown` is the short form for the skill.
- `show` lists the merged `sandbox` settings of the managed, local, project and user files, each value with its file and line, which file wins, what it shadows, the keys and entries Claude Code leaves out (a key the file may not set; what an admin-required sandbox ignores in a repository), and the permission settings that reach the sandbox.
- `why "<command>"` says whether Claude Code runs a Bash call inside the sandbox, outside it, or refuses the retry, with the `excludedCommands` entry that matched each part, or the reason none could (the README lists them: a redirection, a substitution, a `cd`, `sudo`, `eval`, `xargs`, a shell given `-c`, `git -C`, a variable in front, a wrapper it does not read, ...), and whether `autoAllowBashIfSandboxed` approves the call without a prompt. `--retry` asks about `dangerouslyDisableSandbox`.
- `lint` finds sandbox settings that can never take effect: an `excludedCommands` entry that can never take a call out of the sandbox, a bare entry, a domain with a scheme, a path, a bad port or a star in the wrong place, a settings file Claude Code drops whole, a key the wrong file sets, `failIfUnavailable` where the sandbox cannot start, `allowedDomains` in `bypassPermissions` mode, an `allowWrite` entry for a protected path, and more (the README has the table, with which findings the real binary confirmed). `--markdown` is the short form for the skill.
- `--json` on every command. Exit codes 0, 1 (findings, a refusal, or a sandbox that cannot work) and 2 (usage).
- What the tool says about Claude Code is what the real binary did: the tests put a table of calls, a table of approvals, a table of domain entries, the rows that need managed settings, the bubblewrap command line, the merged-usr layout, the keys that do nothing and the network under each permission mode to Claude Code 2.1.289 to 2.1.302 in CI on Linux and macOS, and the Windows paths to 2.1.295 and 2.1.299 on Windows. The README's "What was verified, and how" says what was asked and what was not.
- Zero tokens for the tool. The skill is user-only, adds nothing to a request that does not use it, and hands the model at most 12 findings and 7 lines of checks.

[1.2.0]: https://github.com/nrzz/claude-sandbox-doctor/releases/tag/v1.2.0
[1.1.1]: https://github.com/nrzz/claude-sandbox-doctor/releases/tag/v1.1.1
[1.1.0]: https://github.com/nrzz/claude-sandbox-doctor/releases/tag/v1.1.0
[1.0.0]: https://github.com/nrzz/claude-sandbox-doctor/releases/tag/v1.0.0
