# keysforthewin skills

A Claude Code plugin marketplace with two skills. They are unrelated — install
either or both.

| Skill | What it does |
|---|---|
| [**cleancode**](plugins/cleancode/SKILL.md) | Naming-first refactoring. Restructures code so it can be read from names alone, then verifies behaviour was preserved. |
| [**discernment**](plugins/discernment/SKILL.md) | Situational judgment before answering. Not code-specific — it applies to any request. |

> The repository is still named `cleancode` for historical reasons; the
> marketplace it serves is `keysforthewin`.

## cleancode

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

See [`plugins/cleancode/SKILL.md`](plugins/cleancode/SKILL.md) for all sixteen
and [`plugins/cleancode/reference/worked-example.md`](plugins/cleancode/reference/worked-example.md)
for a full before/after.

### Where the rules come from

They were derived from the published debate between **Robert C. Martin** (author
of *Clean Code*) and **John Ousterhout** (author of *A Philosophy of Software
Design*), conducted September 2024 – February 2025:

> [A Philosophy of Software Design vs Clean Code](https://github.com/johnousterhout/aposd-vs-clean-code/blob/main/README.md)

They disagree about method length, comments, and TDD, and work through a
`PrimeGenerator` example — the same code used as the worked example here — to
ground the argument. This skill is one reading of that exchange, not a statement
of either author's position: neither Martin nor Ousterhout endorsed or reviewed
it, and both would likely take issue with parts of it.

## discernment

Written by **Christian Boneta** ([@Tuna119](https://github.com/Tuna119)), and
redistributed here unmodified apart from packaging. All credit for it belongs to
them.

It runs a short pass before answering — over the request, your own knowledge,
the sources, and the draft — to catch the cases where the obvious read is the
wrong one. It is not a code skill; it applies to factual questions, advice,
pasted content, emotionally loaded messages, and irreversible actions.

See [`plugins/discernment/SKILL.md`](plugins/discernment/SKILL.md).

## Install

Two options per skill. The plugin gets updates handled for you; the clone gets
you the shorter command name. Pick one — installing both leaves two copies in
the skill list.

| | Plugin | Clone |
|---|---|---|
| Command | `/cleancode:cleancode` | `/cleancode` |
| Updates | `/plugin marketplace update`, or automatic | `git pull` by hand |
| Requires | Claude Code with `/plugin` | git only |

### Option 1: as plugins (recommended)

This repo is its own marketplace. Inside Claude Code:

```
/plugin marketplace add keysforthewin/cleancode
/plugin install cleancode@keysforthewin
/plugin install discernment@keysforthewin
/reload-plugins
```

Install only the ones you want; they are separate plugins. Each install asks for
a scope — **user** for every project, **project** to commit it for
collaborators, **local** for just you in this repo.

To get updates automatically, run `/plugin`, open the **Marketplaces** tab,
select `keysforthewin`, and choose **Enable auto-update**. Claude Code then
refreshes it in the background shortly after startup. (Third-party marketplaces
have auto-update off by default.)

### Option 2: as plain skills

A skill directory is `~/.claude/skills/<name>/` for every project, or
`.claude/skills/<name>/` for one project only. Copy whichever you want out of
this repo:

```bash
git clone https://github.com/keysforthewin/cleancode.git /tmp/kftw-skills
cp -r /tmp/kftw-skills/plugins/cleancode   ~/.claude/skills/cleancode
cp -r /tmp/kftw-skills/plugins/discernment ~/.claude/skills/discernment
```

The directory name is what makes the command — `~/.claude/skills/cleancode/`
gives you `/cleancode` — and `SKILL.md` has to sit at its top level. The copied
`.claude-plugin/` directory inside is harmless; delete it if you like.

Then start a new session, or run `/reload-plugins` in the current one.

### Verify it installed

Type `/` in Claude Code and look for the skill in the list. For plugins,
`/plugin list` shows them too, and the **Errors** tab in `/plugin` reports
anything that failed to load.

## Use

`cleancode` takes a target (`/cleancode:cleancode` if you installed the plugin):

```
/cleancode src/parser/tokenizer.js
```

```
/cleancode the PrimeGenerator class
```

`discernment` is meant to fire on its own rather than be invoked, but you can
call it directly the same way. Claude will also reach for either on its own when
the situation matches its description.

## Update

Plugins:

```
/plugin marketplace update keysforthewin
/reload-plugins
```

Clones — re-run the `cp` commands from a fresh clone.

## Uninstall

Plugins:

```
/plugin uninstall cleancode@keysforthewin
/plugin uninstall discernment@keysforthewin
/plugin marketplace remove keysforthewin
```

Clones:

```bash
rm -rf ~/.claude/skills/cleancode ~/.claude/skills/discernment
```
