# cleancode

A Claude Code skill for **naming-first refactoring**.

Point it at a block of code and it restructures it so a reader who does not know
the algorithm can understand it from names alone — then verifies behaviour was
preserved by running the result against the original or an independent reference
implementation.

It enforces sixteen rules, including:

- Name the class after what it produces *and how* (`PrimeGeneratorWithSieveOfEratosthenes`, not `PrimeGenerator`).
- Name helpers by purpose, not mechanism (`stopWatchingMultiplesBelow`, not `advanceMultiplesToReachAtLeast`).
- Predicates never change state — `is…`/`has…` must be safe to call twice.
- Booleans that only carry control flow become a predicate's return value.
- Every abbreviation and single letter gets expanded.
- Comments carry only what names cannot: an invariant, a proof, a rejected alternative, a domain fact.
- Verify, don't assume — state how behaviour was checked and report the result.

See [`SKILL.md`](SKILL.md) for all sixteen and
[`reference/worked-example.md`](reference/worked-example.md) for a full
before/after.

## Install

Skills live in `~/.claude/skills/<name>/` (available in every project) or
`.claude/skills/<name>/` inside a single project. Clone into either one:

```bash
git clone https://github.com/keysforthewin/cleancode.git ~/.claude/skills/cleancode
```

For a single project instead:

```bash
git clone https://github.com/keysforthewin/cleancode.git .claude/skills/cleancode
```

The directory name is what matters — it must be `cleancode` so the slash command
is `/cleancode`. `SKILL.md` has to sit at the top level of that directory.

Restart Claude Code (or start a new session) to pick it up.

### Verify it installed

```bash
ls ~/.claude/skills/cleancode/SKILL.md
```

Then in Claude Code, type `/` and look for `cleancode` in the list.

## Use

Invoke it explicitly on whatever you want restructured:

```
/cleancode src/parser/tokenizer.js
```

```
/cleancode the PrimeGenerator class
```

Claude will also reach for it on its own when you ask to clean up or refactor
code for readability.

## Update

```bash
git -C ~/.claude/skills/cleancode pull
```

## Uninstall

```bash
rm -rf ~/.claude/skills/cleancode
```
