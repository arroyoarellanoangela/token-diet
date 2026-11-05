# token-diet

A Claude Code skill to spend fewer tokens without getting worse answers.

Most of a session's cost is input (context, files read, tool output, messages from other sessions), not what the model writes. token-diet cuts that first:

1. **Compact messages between sessions:** a fixed 6-line report (task, status, what, where, tests, next).
2. **Read only what you need:** search before opening, read line ranges, trim command output at the source, put big data in files.
3. **The cheapest model that can do the job:** smaller models for mechanical work, the strongest only where mistakes are expensive.
4. **Short contexts:** one session per task, compact per phase, subagents for wide exploration, cache-friendly prompts.
5. **Clear, not caveman, with humans:** cut filler, not meaning.

It is built for multi-session setups (a coordinator, project orchestrators, feature sessions, reviewers), and works in a single session too.

## Install

Copy `skills/token-diet` into `~/.claude/skills/`, then use `/token-diet` in a session. To make it always on, add a line to your `CLAUDE.md` or to each role's brief: "Follow the token-diet skill."

## Pairs well with

[i-have-adhd](https://github.com/ayghri/i-have-adhd) shapes replies for the human. token-diet shapes everything else.

## License

MIT
