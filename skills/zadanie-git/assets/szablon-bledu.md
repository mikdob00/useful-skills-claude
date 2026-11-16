| Typ | Priorytet | Rozmiar | Kamień milowy | Zależy od | Blokuje |
|---|---|---|---|---|---|
| Błąd | Musi / Powinno / Może | S / M / L (… dni) | … | #… albo — | #… albo — |

## Cel
Jedno zdanie: co działa po naprawie.

## Jak odtworzyć
1. …
2. …

## Oczekiwane
Co powinno się stać.

## Faktyczne
Co się dzieje. Komunikat błędu albo fragment logu, jeśli jest.

## Środowisko
Urządzenie i wersja systemu, wersja aplikacji albo commit, czy błąd powtarza się za każdym razem.

## Zakres
### W zakresie
- Naprawa przyczyny i test, który ją odtwarza

### Poza zakresem
- …

## Wskazówki techniczne
Podejrzane miejsce w kodzie, co już sprawdzono.

## Ograniczenia
- …

## Kryteria akceptacji
- [ ] KA-1. Zakładając, że …, gdy wykonam kroki z „Jak odtworzyć”, wtedy …
- [ ] KA-2. Test, który odtwarza błąd, nie przechodzi przed naprawą i przechodzi po niej.

## Przypadki brzegowe
- …

## Weryfikacja
### Automatyczna
`…` (komenda z AGENTS.md)

### Ręczna (… : urządzenie z AGENTS.md)
1. Kroki z „Jak odtworzyć” → oczekiwany wynik

## Ryzyka i otwarte pytania
- …

## Rezultat
PR z `Closes #…`. W opisie: przyczyna w jednym zdaniu, test odtwarzający błąd, wynik komendy weryfikacji.
