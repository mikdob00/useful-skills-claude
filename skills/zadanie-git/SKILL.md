---
name: zadanie-git
description: >-
  Write a GitHub issue that both people and coding agents can act on, in the team's fixed Polish
  schema (Cel, Kontekst, Zakres, Kryteria akceptacji KA-n, Weryfikacja automatyczna + ręczna,
  Rezultat): draft it at once, sharpen it in short grilling-style question rounds about the gaps, and
  create it with `gh` only after an explicit "załóż". Use whenever the user types /zadanie-git, or
  asks to write, describe, split, rewrite or improve an issue, task, ticket or "zadanie" — e.g.
  "napisz issue", "opisz to jako zadanie", "załóż zadanie na GitHubie", "rozbij to na tickety",
  "popraw issue #12", "zrób z tego zadanie dla agenta" — even if the skill is not named.
argument-hint: "[krótki opis zadania | #numer issue do poprawy]"
allowed-tools: Read, Grep, Glob, Bash(gh repo view:*), Bash(gh issue view:*), Bash(gh issue list:*), Bash(gh label list:*), Bash(gh auth status:*)
license: Apache-2.0
---

# /zadanie-git: an issue people and agents can act on

Turn a rough idea into a GitHub issue (or a few) in one fixed schema, then sharpen it with the user
in short question rounds. People read the issue on GitHub; coding agents read it with
`gh issue view <n>`. An agent sees neither this conversation nor the project board, so everything the
work needs has to be in the issue body.

Write the issue in Polish with full diacritics (ą ć ę ł ń ó ś ź ż). Talk to the user in the language
they write in.

## The schema

Read the matching template before drafting and keep its headings and their order. Reviewers and
agents look for the same section in the same place every time.

- `assets/szablon-zadania.md`: Funkcja, Techniczne, Dokumentacja
- `assets/szablon-bledu.md`: Błąd
- `assets/szablon-badania.md`: Badanie (a research spike with a time limit)

`references/przyklady.md` has two finished issues (a feature and a spike) and one question round.
Read it before your first draft in a session; it shows the level of detail to aim for.

Rules for the content:

1. **Only what the code can't tell.** Put in the goal, the decisions, device quirks and which of two
   approaches we prefer. Leave out folder structure: agents read it themselves.
2. **Checkable criteria.** Number them KA-1, KA-2… and write each as „Zakładając, że …, gdy …,
   wtedy …”. Each one ends in something observable: a screen, a number, a state. Aim for 3–7 per
   issue. Words like szybko, ładnie, intuicyjnie or poprawnie get a number or an observable instead.
3. **Two verifications.** *Automatyczna* is one command that ends pass/fail, so an agent can run it
   itself. *Ręczna* is numbered „krok → oczekiwany wynik” steps on the real device or browser,
   because an agent has no phone.
4. **Boundaries.** „Poza zakresem” and „Ograniczenia” say what not to do and what not to change.
   Neighbouring work goes to Poza zakresem as „osobne zadanie” or with its issue number.
5. **Result with evidence.** A PR with `Closes #n`, plus command output and a screenshot or recording.
6. **Small.** S is up to half a day, M is 1–2 days, L is 3–5 days.
   - Anything bigger gets split into issues that each deliver a working, checkable slice end to end.
     Slice by behaviour, not by layer ("backend", "UI").
   - If it's unclear what to build, start with a Badanie that has a time limit. Its result is a
     decision, not a feature.
7. **Standing rules live in AGENTS.md, not in each issue.** That covers commands, the definition of
   done and prohibitions. Don't copy the definition-of-done checklist into issues.

Field values:

| Field | Values |
| --- | --- |
| Typ | Funkcja / Błąd / Badanie / Techniczne / Dokumentacja |
| Priorytet | Musi / Powinno / Może |
| Rozmiar | S / M / L, or „Limit N dni” for a Badanie |
| Kamień milowy | the repo's milestone |
| Zależy od / Blokuje | issue numbers, or „—” |

Table cells hold values only. Any explanation goes into „Ryzyka i otwarte pytania”.

Writing conventions:

- **Title:** short (≤ 70 characters), says the outcome, no type prefix. Example: „Zdjęcia urządzenia
  w karcie wizyty”.
- **Code-like text** goes in backticks: identifiers, commands, permissions, file names.
- **APIs and patterns:** name the ones to follow. Give a file path only when the file exists and is
  central; paths go stale, behaviour doesn't.
- **No invented facts or decisions.** Something unknown is either a question for the user or, if it
  doesn't block the work, an item in „Ryzyka i otwarte pytania”. Proposing a concrete number (a
  threshold, a limit, „5 z 5 prób”) as a recommended answer is not inventing: it's a decision put to
  the user, and it's usually what turns a vague criterion into a checkable one.

## Workflow

### 1. Gather facts yourself

Facts are your job, decisions are the user's. Before asking anything, collect what the environment
already knows:

- **Repo:** `gh repo view --json nameWithOwner,url`. If `gh` is missing or not logged in, carry on
  without GitHub and say so once. If the user names another target repo, use that one.
- **Project rules:** `AGENTS.md`, `CLAUDE.md` (and its `@` imports), `README.md`. Look for a
  `## Zadania` section (format at the end of this file). It holds the verification command, the test
  device, the board, the default assignee and the milestones.
- **Decisions:** `docs/decyzje.md` or `docs/adr/`, or wherever else the project keeps them (a mock-up
  repo, code comments). Cite decision IDs (e.g. D-003) instead of re-explaining them.
- **Outside facts** such as platform policies, store rules or API limits: if web search is available,
  check the official source and cite it with the date. Otherwise turn the fact into a question, or into
  part of a Badanie.
- **Existing issues:** `gh issue list --state all --limit 100 --json number,title,state,labels,milestone`.
  Use it to spot duplicates, dependencies and work already done. If some issues already follow this
  schema, open one or two of them with `gh issue view`. They show the project's own level of detail
  and vocabulary better than the generic examples.
- **Code that touches the task** (Grep/Glob): real class and API names for „Wskazówki techniczne”,
  patterns to copy, constraints you notice.
- **Rewrite mode:** if the argument is an issue number (`#12` or `12`), run
  `gh issue view 12 --json title,body,labels,milestone,assignees`. Keep its facts, move them into the
  schema, and mark what's missing.
- **No verification command found:** infer the likely one from the build system and offer it as a
  recommendation in round 1. For example: Gradle → `./gradlew lintDebug testDebugUnitTest
  assembleDebug`; npm → the `lint` and `test` scripts from `package.json`. Once the user accepts it,
  it's the command; no hedge in the issue.
- **Target repo isn't the current folder, or doesn't exist yet:** say so in one line and keep going.
  Draft from what you can read: the current repo, docs, the user's words. Fields that need the target
  (milestone, issue numbers) get „—”, and the open item goes into „Ryzyka i otwarte pytania”.
- **A recorded decision conflicts with the request:** make it question 1 of round 1, quoting the
  decision ID and what it says. Don't silently follow either side. If the user overrides it, say so in
  Kontekst („zmienia D-005: …”) and add updating the decision record to „W zakresie”, so the repo
  doesn't hold two truths.

### 2. Draft at once

Write the complete draft from the template now, before any interview. People react far better to a
concrete draft than to blank questions.

- Mark every gap in place with `❓` and a short note, e.g. `❓ ile wpisów pokazać?`.
- Show the draft as one fenced `markdown` block the user can copy.
- Follow it with round 1.

If the request is bigger than one L, don't draft one giant issue. Make round 1 the split instead: for
each proposed issue give the title, a one-line goal, the size and its dependencies. Draft the issues
once the split is agreed.

### 3. Grill the gaps in rounds

Sharpen the draft until it passes the readiness check.

How a round works:

- **Pick the frontier.** A round asks every open question that can be answered now, i.e. one that
  doesn't wait on another unanswered question.
- **Order and cap.** Put the most consequential questions first: goal and scope before criteria,
  criteria before edge cases. Ask at most 5 per round; the rest wait for the next round.
- **One decision per question.** Two decisions squeezed into one question get one answer, and the
  other decision is lost. Push it to the next round instead.
- **Recommend an answer.** Number each question and give your recommended answer with a one-line
  reason. Phrase it so that „tak” accepts it, e.g. „1. Limit 10 zdjęć na wizytę? Tyle
  wystarcza na przegląd przed naprawą i po niej, a każde kolejne to transfer. (tak / nie / inaczej)”.
- **Let the user answer freely.** They can accept everything with „tak”, answer „2: nie, 20”, or
  write anything else.
- **Update and recompute.** Apply the answers to the draft, then recompute the frontier. An answer
  often opens new questions or closes others.

What to ask, and what not to:

- **Look up facts instead of asking.** If the code, the docs, the issues or `gh` can answer a
  question, find the answer yourself.
- **Ask about decisions, not wording.** Polish the language yourself.
- **Clean choices:** for a choice between 2–4 options, use AskUserQuestion if it's available. Put the
  recommended option first, labelled „(Zalecane)”, and ask at most 4 questions per call.

Where gaps hide:

- every `❓`;
- the readiness check below;
- vague words;
- a criterion nobody could check;
- an empty „Poza zakresem”;
- a size above L;
- a manual step with no expected result;
- an unstated assumption about the device or the user's behaviour.

After a round, show only what changed, or the whole draft if the changes are broad.

**When to stop:** stop when the readiness check passes and the user confirms („gotowe”, „ok”,
„wygląda dobrze”), or when they say stop. Then show the full final draft and list anything still open.
Put the open items that don't block the work into „Ryzyka i otwarte pytania”. Don't pad the rounds
with filler questions.

**Readiness check (definicja gotowości).** Every item has to hold:

- „Cel” is one sentence.
- „Kontekst” has a story „Jako … chcę …, żeby …”; a Błąd has reproduction steps instead.
- Every KA is numbered and checkable, either by an agent (command or emulator) or by the owner on the
  device.
- Both verifications are there. „Ręczna” may be dropped only for pure docs or tooling work, with the
  reason given.
- „Rozmiar” is at most L, or the issue is a Badanie with a time limit.
- „Poza zakresem” is not empty.
- Dependencies are named. Existing issues are linked by number; issues that don't exist yet are named
  in words.
- No `❓` is left. Kamień milowy may be „—”.

### 4. Hand over; create only on „załóż”

Show the final title and body, then ask once whether to create the issue. Propose concrete values:

- the repo;
- the assignee;
- the board (GitHub Project);
- the milestone;
- a label equal to the Typ, if `gh label list` shows one.

Creating an issue is visible to the whole team, so wait for an explicit yes („załóż”, „tak, załóż”,
„create”). Praise for the text („wygląda dobrze”) is not that yes.

On yes, pass the body through stdin so nothing is written into the repo:

```bash
gh issue create --repo OWNER/REPO --title "…" --assignee LOGIN \
  --milestone "…" --project "…" --label "…" --body-file - <<'EOF'
…treść…
EOF
```

- **Missing values:** leave out any flag whose value doesn't exist yet (milestone, label, project)
  instead of creating it, and tell the user what to set by hand.
- **Project scope:** adding the issue to a Project needs a token scope. If `gh` reports it missing,
  ask the user to run `gh auth refresh -s project` themselves. It opens a browser sign-in, which is
  theirs to do.
- **Report the result:** give the issue URL. Moving the card to a board column is left to the user;
  it needs project field IDs.
- **Several issues:** create them in dependency order. Then fill in the real numbers in „Zależy od” and
  „Blokuje”, using `gh issue edit N --body-file -` on the earlier ones after a yes.
- **Rewriting an existing issue:** say what changes, and on yes run `gh issue edit N --body-file -`.
  Leave the title, labels and assignees alone unless agreed.
- **No `gh`, no target repo yet, or the user prefers to do it by hand:** give the title and the body
  ready to paste, plus the path Issues → New issue → Title → opis → Assignees / Projects / Milestone →
  Create. Don't propose a `gh issue create` aimed at a repo you couldn't open.

## The `## Zadania` section in AGENTS.md

Project specifics come from the repo, which is what lets this skill work in any project. If
`AGENTS.md` has no such section, suggest adding one once, in one sentence, after the first issue. Add
it only if the user agrees, as a normal change in a PR. Example:

```markdown
## Zadania (/zadanie-git)
- Weryfikacja automatyczna: `./gradlew lintDebug testDebugUnitTest assembleDebug`
- Weryfikacja ręczna: telefon testowy (Pixel 7, Android 14); w komentarzu wersja aplikacji
- Tablica: Serwisownik · kamienie milowe „Sprint N”
- Domyślnie przypisz: osoba, która zakłada zadanie · etykiety = Typ zadania
- Decyzje: `docs/decyzje.md`
```
