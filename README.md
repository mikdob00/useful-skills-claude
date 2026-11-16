# useful-skills-claude

Przydatne skille do [Claude Code](https://code.claude.com/docs). Każdy skill to folder w `skills/`
z plikiem `SKILL.md`. Repo jest też marketplace'em pluginów, więc skill instalujesz jednym
poleceniem.

## Skille

- **[`/zadanie-git`](skills/zadanie-git/)** pisze issue na GitHubie, które zrozumie i człowiek, i
  agent.
  - Używa stałego schematu: cel, zakres, kryteria „Zakładając, że… gdy… wtedy…”, weryfikacja
    automatyczna i ręczna, rezultat.
  - Najpierw sam czyta repo, potem rundami dopytuje o luki.
  - Issue zakłada dopiero po „załóż”.
  - Szczegóły instalacji i użycia: [instrukcja](skills/zadanie-git/INSTALACJA.md).

## Instalacja

Jako plugin, w Claude Code:

```text
/plugin marketplace add mikdob00/useful-skills-claude
/plugin install zadanie-git@useful-skills-claude
/reload-plugins
```

Nową wersję pobierzesz poleceniem `/plugin marketplace update useful-skills-claude`.

Ręcznie: skopiuj folder skilla z `skills/` w jedno z dwóch miejsc:
- do `~/.claude/skills/`, wtedy masz go we wszystkich swoich projektach;
- do `.claude/skills/` w repo projektu, wtedy ma go każdy, kto pracuje w tym repo.

## Nowy skill w repo

1. Utwórz folder `skills/<nazwa>/` z plikiem `SKILL.md`. W nagłówku pliku muszą być pola `name` i
   `description`.
2. Dodaj wpis w `.claude-plugin/marketplace.json` z polami:
   - `name`;
   - `"source": "./skills/<nazwa>"`;
   - `"strict": false`;
   - `description`.
3. Dopisz skill do listy wyżej.

## Podziękowania

Rundy pytań w `/zadanie-git` są wzorowane na skillu `grilling` z
[mattpocock/skills](https://github.com/mattpocock/skills).

## Licencja

Apache-2.0, zob. [LICENSE](LICENSE).
