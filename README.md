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

## Where this comes from

The rules were derived from the published debate between **Robert C. Martin**
(author of *Clean Code*) and **John Ousterhout** (author of *A Philosophy of
Software Design*), conducted September 2024 – February 2025:

> [A Philosophy of Software Design vs Clean Code](https://github.com/johnousterhout/aposd-vs-clean-code/blob/main/README.md)

They disagree about method length, comments, and TDD, and work through a
`PrimeGenerator` example — the same code used as the worked example here —
to ground the argument. This skill is one reading of that exchange, not a
statement of either author's position: neither Martin nor Ousterhout endorsed
or reviewed it, and both would likely take issue with parts of it.

## Install

Two options. The plugin gets updates handled for you; the clone gets you the
shorter command name. Pick one — installing both leaves two copies in the
skill list.

| | Plugin | Clone |
|---|---|---|
| Command | `/cleancode:cleancode` | `/cleancode` |
| Updates | `/plugin marketplace update`, or automatic | `git pull` by hand |
| Requires | Claude Code with `/plugin` | git only |

### Option 1: as a plugin (recommended)

This repo is its own marketplace. Inside Claude Code:

```
/plugin marketplace add keysforthewin/cleancode
/plugin install cleancode@cleancode
/reload-plugins
```

The install step asks for a scope — **user** for every project, **project** to
commit it for collaborators, **local** for just you in this repo.

To get updates automatically, run `/plugin`, open the **Marketplaces** tab,
select `cleancode`, and choose **Enable auto-update**. Claude Code then
refreshes it in the background shortly after startup. (Third-party
marketplaces have auto-update off by default.)

### Option 2: as a plain skill

Clone into `~/.claude/skills/` for every project:

```bash
git clone https://github.com/keysforthewin/cleancode.git ~/.claude/skills/cleancode
```

…or into one project only:

```bash
git clone https://github.com/keysforthewin/cleancode.git .claude/skills/cleancode
```

The directory name must be `cleancode` — that is what makes the command
`/cleancode`, and `SKILL.md` has to sit at its top level. `git clone` refuses a
directory that already exists, so remove any earlier copy first.

Then start a new session, or run `/reload-plugins` in the current one.

### Verify it installed

Type `/` in Claude Code and look for `cleancode` in the list. For the plugin,
`/plugin list` shows it too, and the **Errors** tab in `/plugin` reports
anything that failed to load.

## Use

Invoke it explicitly on whatever you want restructured (`/cleancode:cleancode`
if you installed the plugin):

```
/cleancode src/parser/tokenizer.js
```

```
/cleancode the PrimeGenerator class
```

Claude will also reach for it on its own when you ask to clean up or refactor
code for readability.

## Update

Plugin:

```
/plugin marketplace update cleancode
/reload-plugins
```

Clone:

```bash
git -C ~/.claude/skills/cleancode pull
```

## Uninstall

Plugin:

```
/plugin uninstall cleancode@cleancode
/plugin marketplace remove cleancode
```

Clone:

```bash
rm -rf ~/.claude/skills/cleancode
```
