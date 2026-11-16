# /zadanie-git: instalacja i pierwsze użycie

`/zadanie-git` to skill do Claude Code. Opisujesz zadanie jednym zdaniem, a Claude:
- pisze z niego issue na GitHubie w stałym schemacie: cel, zakres, kryteria akceptacji, jak to
  sprawdzić i co oddać;
- dopytuje o to, czego brakuje;
- zakłada issue dopiero wtedy, gdy napiszesz „załóż”.

Takie zadanie zrozumie zarówno człowiek, jak i agent (Claude Code, Copilot, Codex), który czyta je
poleceniem `gh issue view`.

## Czego potrzebujesz

- **Claude Code:** w terminalu, w VS Code albo w aplikacji Claude na komputerze (zakładka Code).
- **GitHub CLI (`gh`)** do zakładania issue. Bez niego skill da Ci gotowy tekst do wklejenia na
  GitHubie.
  - Sprawdź, czy jesteś zalogowany: `gh auth status`.
  - Jeśli nie jesteś: `gh auth login`.
  - Żeby skill dodawał zadania na tablicę (GitHub Projects), raz uruchom
    `gh auth refresh -s project`.

## Instalacja

### Sposób 1: jako plugin (zalecany)

W Claude Code wpisz po kolei:

```text
/plugin marketplace add mikdob00/useful-skills-claude
/plugin install zadanie-git@useful-skills-claude
/reload-plugins
```

Gdy w repo pojawi się nowa wersja, zaktualizujesz skill poleceniem
`/plugin marketplace update useful-skills-claude`.

### Sposób 2: ręcznie

1. Pobierz folder `skills/zadanie-git` z repo, albo rozpakuj `zadanie-git.zip`, jeśli dostałeś zip.
2. Skopiuj ten folder do katalogu skilli:
   - macOS albo Linux: `~/.claude/skills/`
   - Windows: `C:\Users\<Twoja nazwa>\.claude\skills\`

   Wynik ma wyglądać tak: `~/.claude/skills/zadanie-git/SKILL.md`. Folder `.claude` jest ukryty.
   Jeśli nie ma w nim `skills`, utwórz go.

   Na macOS możesz to zrobić jedną komendą w terminalu:

   ```bash
   mkdir -p ~/.claude/skills && cp -R ~/Downloads/zadanie-git ~/.claude/skills/
   ```

   Możesz też poprosić o to samego Claude Code: „Skopiuj folder ~/Downloads/zadanie-git do
   ~/.claude/skills/”.
3. Uruchom Claude Code od nowa i wpisz `/`. Na liście powinien być `zadanie-git`.

### Sposób 3: w repo projektu

Skopiuj folder do `.claude/skills/zadanie-git/` w repo projektu i wypchnij go. Wtedy każdy, kto
otworzy repo w Claude Code, ma skill bez instalowania czegokolwiek.

## Pierwsze użycie

1. Otwórz Claude Code w folderze repo, w którym mają powstać zadania.
2. Wpisz na przykład: `/zadanie-git Chcę dodawać zdjęcia urządzenia do wizyty`. Jeśli skill
   zainstalowałeś jako plugin, może się też nazywać `/zadanie-git:zadanie-git`.
3. Claude najpierw sam czyta repo, decyzje i istniejące issue.
4. Potem pokazuje cały szkic zadania. Miejsca, których jeszcze nie wie, są oznaczone `❓`.
5. Pod szkicem zadaje do 5 pytań i przy każdym podaje swoją propozycję. Odpowiadasz „tak” na
   wszystko albo poprawiasz wybrane punkty, np. „2: nie, 20”.
6. Rundy pytań powtarzają się, aż zadanie jest kompletne. Możesz skończyć wcześniej, pisząc
   „gotowe”.
7. Na koniec Claude proponuje repo, osobę, tablicę i kamień milowy. Issue powstaje dopiero po
   „załóż”, a Ty dostajesz do niego link.

Inne zastosowania:

- **Poprawa istniejącego zadania:** `/zadanie-git #12`.
- **Większa rzecz:** opisz ją normalnie, a Claude najpierw zaproponuje podział na kilka zadań.

## Mniej pytań przy każdym zadaniu

Dopisz do `AGENTS.md` w repo sekcję `## Zadania`: komendę weryfikacji, urządzenie do testów,
tablicę, kamienie milowe i domyślną osobę. Wzór jest na końcu `SKILL.md`. Skill weźmie te dane
stamtąd, zamiast pytać o nie za każdym razem.

## Co jest w folderze

- `SKILL.md`: instrukcja dla Claude. Jest po angielsku, bo tak czyta ją model. Zadania i tak
  powstają po polsku.
- `assets/`: szablony zadania, błędu i zadania badawczego.
- `references/przyklady.md`: przykładowe zadania dla wymyślonej aplikacji.
- `INSTALACJA.md`: ten plik.

## Gdy coś nie działa

- **`zadanie-git` nie pojawia się na liście.** Sprawdź, czy plik leży w
  `~/.claude/skills/zadanie-git/SKILL.md`, a nie w `zadanie-git/zadanie-git/SKILL.md`. Potem
  uruchom Claude Code od nowa.
- **Zadanie nie trafia na tablicę.** Uruchom `gh auth refresh -s project`.
- **Brakuje kamienia milowego albo etykiety.** Claude je pominie i powie, co ustawić ręcznie na
  GitHubie.
