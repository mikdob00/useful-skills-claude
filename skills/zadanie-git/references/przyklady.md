# Przykłady

Trzy wzorce dla wymyślonej aplikacji „Serwisownik”. To aplikacja na Androida dla serwisantów w
terenie: pokazuje listę dzisiejszych wizyt, pozwala zrobić zdjęcia urządzenia i zebrać podpis
klienta. Przykłady pokazują poziom szczegółu, a nie treść do kopiowania. Numery decyzji (D-…, Q-…)
są częścią przykładu.

Decyzje użyte w przykładach:
- **D-002:** aplikacja działa offline, a synchronizacja nadrabia, gdy wróci zasięg.
- **D-005:** zdjęcia zostają na telefonie do synchronizacji.
- **Q-004:** czy odczyt tabliczki znamionowej działa na telefonie.

1. Funkcja
2. Badanie
3. Runda pytań

## 1. Funkcja

Tytuł: **Zdjęcia urządzenia w karcie wizyty**

```markdown
| Typ | Priorytet | Rozmiar | Kamień milowy | Zależy od | Blokuje |
|---|---|---|---|---|---|
| Funkcja | Musi | M (1–2 dni) | Sprint 3 | #14 | #18 |

## Cel
Serwisant robi w karcie wizyty do 10 zdjęć urządzenia, a zdjęcia są zapisane w telefonie także bez zasięgu.

## Kontekst
Jako serwisant chcę zrobić zdjęcia urządzenia przed naprawą i po niej, żeby mieć dowód dla klienta i do reklamacji.

Serwisanci często pracują w piwnicach i kotłowniach bez zasięgu (D-002). Zdjęcia wysyła później synchronizacja (#18), a do tego czasu zostają na telefonie (D-005). Firma płaci za transfer danych z telefonów służbowych, więc zdjęcia muszą być małe.

## Zakres
### W zakresie
- Przycisk „Dodaj zdjęcie” w karcie wizyty; zdjęcie robi aparat systemowy
- Miniatury zdjęć w siatce pod danymi wizyty
- Zapis w pamięci aplikacji, powiązany z wizytą; plik pomniejszony do 1600 px po dłuższym boku, JPEG o jakości 80
- Usunięcie zdjęcia przez przytrzymanie miniatury, z potwierdzeniem
- Limit 10 zdjęć na wizytę

### Poza zakresem
- Wysyłanie zdjęć na serwer (#18)
- Edycja zdjęć i dopiski na zdjęciach
- Dodawanie zdjęć z galerii telefonu: osobne zadanie

## Wskazówki techniczne
- `ActivityResultContracts.TakePicture` z `FileProvider`, więc aplikacja nie potrzebuje uprawnienia `CAMERA`
- Pliki w `context.filesDir`, a nie w galerii
- Pomniejszanie poza wątkiem głównym (`Dispatchers.IO`), z uwzględnieniem orientacji z EXIF
- Powiązanie z wizytą w bazie Room. Wzór: istniejące notatki do wizyty (#14).

## Ograniczenia
- Zdjęcia nie trafiają do galerii telefonu ani do kopii zapasowej Zdjęć Google
- Bez nowych bibliotek do obrazów

## Kryteria akceptacji
- [ ] KA-1. Zakładając, że jestem w karcie wizyty, gdy dotknę „Dodaj zdjęcie” i zrobię zdjęcie, wtedy w ciągu 2 sekund widzę jego miniaturę.
- [ ] KA-2. Zakładając, że telefon jest w trybie samolotowym, gdy zrobię 3 zdjęcia i zamknę aplikację, wtedy po ponownym otwarciu karty wizyty widzę te 3 zdjęcia.
- [ ] KA-3. Zakładając, że wizyta ma już 10 zdjęć, gdy otworzę kartę, wtedy przycisk „Dodaj zdjęcie” jest nieaktywny i widzę napis „Limit 10 zdjęć”.
- [ ] KA-4. Zakładając, że zrobiłem zdjęcie aparatem 50 Mpx, gdy sprawdzę zapisany plik, wtedy ma najwyżej 1600 px po dłuższym boku i mniej niż 500 KB.
- [ ] KA-5. Zakładając, że przytrzymałem miniaturę i potwierdziłem usunięcie, gdy wrócę do karty, wtedy zdjęcia nie ma, a jego plik zniknął z pamięci.
- [ ] KA-6. Testy jednostkowe pomniejszania i limitu zdjęć przechodzą.

## Przypadki brzegowe
- Brak miejsca w pamięci: komunikat, aplikacja się nie zamyka
- Anulowanie w aparacie: nic się nie zapisuje
- Obrót telefonu w trakcie: miniatury zostają
- Zdjęcie zrobione w pionie: zapisane bez obrócenia o 90°

## Weryfikacja
### Automatyczna
`./gradlew lintDebug testDebugUnitTest assembleDebug`

Agent sprawdza KA-1, KA-3 i KA-5 na emulatorze z wirtualną kamerą i dołącza zrzuty ekranu.

### Ręczna (telefon testowy)
1. Otwórz wizytę i zrób 3 zdjęcia → miniatury w ciągu 2 sekund
2. Włącz tryb samolotowy, zrób 3 zdjęcia, zamknij i otwórz aplikację → zdjęcia są
3. Dobij do 10 zdjęć → przycisk nieaktywny z napisem „Limit 10 zdjęć”
4. Zrób zdjęcie w pionie → miniatura nie jest obrócona
5. Otwórz galerię telefonu → zdjęć z aplikacji tam nie ma

## Ryzyka i otwarte pytania
- Niektóre telefony zapisują orientację tylko w EXIF; wynik z telefonu testowego opisz w PR
- Czy 10 zdjęć wystarczy przy dużych instalacjach? Sprawdzamy z serwisantami po tygodniu

## Rezultat
PR z `Closes #15`. W opisie: wynik komendy weryfikacji, zrzut siatki zdjęć z emulatora, tabela: kryterium → jak sprawdzone.
```

## 2. Badanie

Tytuł: **Odczyt numeru seryjnego z tabliczki znamionowej**

```markdown
| Typ | Priorytet | Rozmiar | Kamień milowy | Zależy od | Blokuje |
|---|---|---|---|---|---|
| Badanie | Powinno | Limit 2 dni | Sprint 3 | #15 | decyzję Q-004 |

## Pytanie
Czy rozpoznawanie tekstu na telefonie odczytuje numer seryjny z tabliczki znamionowej na tyle dobrze, że serwisant poprawia najwyżej 1 znak w 9 zdjęciach na 10? (Q-004)

## Kontekst
Jako serwisant chcę zrobić zdjęcie tabliczki zamiast przepisywać numer, żeby nie robić literówek w zgłoszeniu reklamacyjnym.

Literówka w numerze seryjnym oznacza, że producent odrzuca reklamację. Jeśli odczyt na telefonie jest za słaby, potrzebna będzie usługa w chmurze. To dodatkowy koszt i zdjęcia u klienta wysyłane poza telefon, wbrew D-002.

## Limit czasu
2 dni robocze. Po nich oddajesz wynik, nawet niepełny, z opisem, czego zabrakło.

## Zakres
### W zakresie
- Prosty ekran: zdjęcie tabliczki → odczytany tekst → wybór numeru → czas odczytu
- Rozpoznawanie tekstu na telefonie (ML Kit Text Recognition), bez internetu
- 20 zdjęć tabliczek: 10 czystych i 10 brudnych lub porysowanych, w różnym świetle, od co najmniej 4 producentów

### Poza zakresem
- Wpisywanie numeru do zgłoszenia
- Usługa rozpoznawania w chmurze
- Docelowy wygląd ekranu

## Metoda
- Zestaw 20 zdjęć i numery wzorcowe w `docs/badania/q-004-zestaw.md`
- Dla każdego zdjęcia: odczyt, liczba błędnych znaków, czas, czy działa w trybie samolotowym
- Zdjęcia robi człowiek na telefonie testowym. Agent przygotowuje ekran, zestaw i tabelę.

## Kryteria ukończenia
- [ ] KA-1. Plik `docs/badania/q-004-wyniki.md` z tabelą: zdjęcie → odczyt, błędne znaki, czas
- [ ] KA-2. Rekomendacja w jednym akapicie i wpis w `docs/decyzje.md` (albo „potrzebne dalsze badanie” z powodem)
- [ ] KA-3. Ekran odczytu działa w APK z PR, więc właściciel sam sprawdza 3 tabliczki
- [ ] KA-4. Opisane, które znaki odczyt myli najczęściej (np. 0/O, 1/I, 5/S)

## Przypadki brzegowe
- Tabliczka sfotografowana pod kątem albo z odblaskiem
- Numer w dwóch liniach
- Model rozpoznawania niepobrany przy pierwszym uruchomieniu bez internetu: opisz, jak dołączyć go do aplikacji

## Weryfikacja
### Automatyczna
`./gradlew lintDebug testDebugUnitTest assembleDebug`

### Ręczna (właściciel, telefon testowy)
1. Zrób zdjęcia 3 tabliczek w pracy → odczytany numer
2. Policz poprawione znaki i wpisz wynik w komentarzu

## Ryzyka i otwarte pytania
- Wyniki z emulatora nie są miarodajne; liczą się zdjęcia z telefonu
- Tabliczki różnych producentów mają różne czcionki; 4 producentów to minimum

## Rezultat
PR z `Closes #16`: kod ekranu, zestaw zdjęć, wyniki i wpis w `docs/decyzje.md`.
```

## 3. Runda pytań

Prośba: „/zadanie-git lista wizyt na dziś”.

Najpierw fakty z repo. Wizyty trafiają do telefonu w #12 (synchronizacja harmonogramu) i leżą w bazie Room. Aplikacja działa offline (D-002). Szkic stoi już nad pytaniami, z `❓` w miejscach luk. Pod nim pierwsza runda:

> Szkic jest wyżej. Pierwsza runda, odpowiedz „tak” na wszystko albo np. „4: nie, osobna sekcja”:
>
> 1. Cel: „Po otwarciu aplikacji serwisant widzi dzisiejsze wizyty według godzin i jednym dotknięciem włącza nawigację do klienta”? Lista bez nawigacji nie skraca drogi do klienta.
> 2. Rozmiar M i zależność od #12? Wizyty są w telefonie dopiero po #12.
> 3. Zmiana kolejności wizyt jako osobne zadanie (Poza zakresem)? Sama lista da się sprawdzić, a przestawianie wizyt wymaga zgody dyspozytora.
> 4. Niezakończone wizyty z poprzednich dni na górze listy, wyróżnione? Do nich serwisant wraca najczęściej.
> 5. Nawigacja otwiera Mapy Google z adresem (`geo:`), bez własnej mapy? Własna mapa to tygodnie pracy.

Druga runda pyta już tylko o to, co otworzyły odpowiedzi. Na przykład po „4: tak” pada pytanie: „Zaległa wizyta znika z góry listy po oznaczeniu jako zakończona, czy także po przełożeniu na inny dzień?”.
