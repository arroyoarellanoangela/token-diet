---
name: token-diet
description: 'Spend fewer tokens without getting worse answers: compact messages between sessions, read only what you need, trim tool output, pick the cheapest model that can do the job, and keep contexts short. Use it in every session of a multi-session Claude Code setup (coordinator, orchestrators, feature sessions, reviewers). Invoke with /token-diet; stays on until "stop token diet".'
license: MIT
metadata:
  tags: "Tokens, Cost, Context, Multi-agent, Claude Code"
  category: "productivity"
---

# token-diet

Most of a session's cost is **input**: the context it carries, the files it reads, the tool output it receives and the messages other sessions send it. What the model writes is a small part. So this skill cuts what goes **in** first, and only then trims what comes out.

It never trades correctness for tokens. If a rule below would make the work worse (miss a bug, skip a check, guess instead of read), break the rule and say so in one line.

## Persistence

These rules apply to every turn for the rest of the session. Turn them off only when the user says "stop token diet".

## 1. Messages between sessions: compact

Sessions do not need prose from each other. Every report, brief or hand-off to another session uses this shape and nothing else:

```
<TASK-ID> · <status: done | blocked | needs-review | waiting-on-user>
what: <one line, what changed or was found>
where: <branch / commit SHA / PR / file path>
tests: <suite: passed/failed counts>
next: <one line, who does what>
blocked: <only if blocked: the exact blocker>
```

- No greetings, no restating the other session's own brief, no summary of the conversation so far.
- Point at files and SHAs instead of pasting their content.
- One message per milestone, not one per step. Never send "still working".
- Long results go in a file in the shared notes folder; the message carries the path.

## 2. Read only what you need

- **Search before you open.** Use grep or glob to find the lines, then read those lines with an offset and limit, not the whole file.
- **Never read the same file twice in a session** unless it changed. Keep what you learned in a one-line note instead.
- **Trim command output at the source:** `| tail -20`, `| head -40`, `grep -c`, `--quiet`, `--format` options, `wc -l` before printing. Ask for counts before lists, and lists before contents.
- **Big data goes to a file, not the chat:** write JSON, logs and query results to disk and read back only the part you need.
- **Logs:** filter to errors and the last lines (`grep -i error | tail`), never dump a full log.
- **Tests:** run the narrowest suite that proves the change, then the full suite once at the end; print the summary line, not every passing test.

## 3. The cheapest model that can do the job

- **Sonnet (or a smaller model)** for mechanical work: renames, small fixes with clear specs, routine reviews against a checklist, scheduled services (email triage, board updates, digests), reading and summarising.
- **The strongest model** only for design, tricky debugging, security reviews and anything where a wrong answer is expensive.
- Pick the model before the session starts; switching mid-session throws away the prompt cache.

## 4. Keep contexts short

- **One session per task.** When the task is done, write the closing note and archive the session. Do not reuse a long session for an unrelated task.
- **Compact at the end of each finished phase,** not when the context is full. `/clear` between unrelated tasks.
- **Delegate wide exploration** (reading many files, audits, long test runs) to a subagent, so only its short conclusion enters the main context.
- **Briefs are self-contained and short:** goal, files, decisions already made, what done looks like. Link longer docs instead of pasting them.
- **Stable text first, changing details last** in prompts and CLAUDE.md, so the prompt cache keeps working.
- **Keep CLAUDE.md under ~200 lines;** move detail into linked files.

## 5. Writing to the human

Clear beats short. Do not write telegraphic or "caveman" text to a person: it saves little and costs comprehension. Do cut:

- preambles ("Sure", "I'll now…"), closing recaps and offers ("anything else?");
- repeating what the user just said;
- tool narration the user can already see;
- hedging that adds no information.

## 6. Polling and waiting

- Never poll in a loop with short sleeps. Use one background wait that notifies on completion, or a single check at a sensible interval.
- Do not re-read a session's whole transcript to see its state; read its last few events or its result file.

## Self-check before sending

1. Is anything here already in the receiver's context? Cut it.
2. Could a path or SHA replace pasted content? Replace it.
3. Did any command print more than ~40 lines I did not need? Trim it next time at the source.
4. Is this the right model for this work?
