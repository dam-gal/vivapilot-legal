# VivaPilot 1.27 – materiały do pola „Dane logowania” (Google Play Console)

Legenda: **[PEWNE]** wynika z kodu; **[NIE SPRAWDZONO]** nie uruchamiałem aplikacji ani nie miałem dostępu do Twojej strony; **[Z PAMIĘCI]** tak pamiętam, sprawdź w Konsoli.

## 1. Ścieżka dodania Źródła treści (nazwy po angielsku, jak w aplikacji EN)

Źródło nazw: `app/src/main/java/com/iptvplayer/app/i18n/Strings.kt` (wersja „en”). Klucze podane w nawiasach. Dwa napisy nie są w Strings.kt, tylko wprost w `ui/screens/PlaylistsScreen.kt`: przełączniki „Xtream Codes” i „M3U / M3U8”.

### M3U podane jako adres URL
1. Pierwsze uruchomienie → (jeśli aplikacja pokaże) ekran **„Choose your language”** → **Continue** (`language.picker.title`, `language.picker.continue`).
2. Ekran **„Welcome to VivaPilot”** (`onboarding.title`): zaznacz pole **„I am an adult (I am at least 18 years old) and I accept the Terms of Service. I have read the Privacy Policy.”** (`onboarding.checkboxLabel`) → przycisk **Start** (`onboarding.startButton`). Można najpierw otworzyć **Terms of Service** i **Privacy Policy** (`onboarding.regulaminLink`, `onboarding.politykaLink`).
3. Gdy nie ma żadnej playlisty, po Start sam otwiera się ekran playlist z oknem **„Add playlist”** (`playlists.addTitle`). [PEWNE, MainActivity.kt: `showPlaylistsManager`/`openAddOnStart`]
   Jeśli okno się nie otworzy: dolna zakładka **Settings** (`tab.settings`) → sekcja **IPTV PLAYLISTS** → **Manage playlists** (`settings.playlists.manage`) → przycisk **+** (opis: „Add playlist”, `playlists.addDesc`).
4. W oknie jest domyślnie wybrany przełącznik **Xtream Codes**. Dotknij **M3U / M3U8**.
5. Pole **Name** (`playlists.nameLabel`): np. `Test`.
6. Pole **M3U/M3U8 file URL** (`playlists.m3uUrlLabel`): `https://dam-gal.github.io/vivapilot-legal/reviewer/test.m3u`
   **Od wersji 1.27 w tym polu można wkleić adres** (długie przytrzymanie → „Wklej”) albo wpisać go ręcznie. Wklejanie jest nadal zablokowane w polach Xtream Codes (adres serwera, login, hasło).
7. Pole **User-Agent (optional)** jest wstępnie wypełnione (udaje VLC), nie zmieniaj go.
8. Przycisk **Add** (`common.add`). Aplikacja pobiera listę.
9. Zakładka **Live** (`tab.live`): kategoria „Test”, kanały **Test Channel 1–3** → dotknij kanału, film się odtwarza. [Kod: PEWNE; działanie na urządzeniu: NIE SPRAWDZONO]

### Xtream Codes (te same kroki 1–3)
4. W oknie **Add playlist** zostaw przełącznik **Xtream Codes**.
5. Wypełnij pola: **Name**, **Server address (http://host:port)** (`playlists.serverAddressLabel`), **Username**, **Password** (`playlists.usernameLabel`, `playlists.passwordLabel`). Pole **EPG URL (XMLTV, optional)** wypełnia się samo (`playlists.epgUrlAutofilled`), **User-Agent (optional)** jest wstępnie wypełnione.
6. **Add**. Zakładki **Live**, **Movies**, **Series** pokazują zawartość serwera.
W polach Xtream Codes wklejanie jest w 1.27 nadal zablokowane (adres, login i hasło wpisuje się ręcznie).
Dla recenzenta nie ma testowego konta Xtream (patrz p. 2: GitHub Pages tego nie obsłuży), więc w tekście dla recenzentów jest tylko ścieżka M3U.

## 2. Testowe Źródło treści

Pliki (w paczce, folder `reviewer/`):
- `clip.mp4` – 15 s, 640×360, 25 kl./s, wideo H.264 (Main), dźwięk AAC mono, ton 440 Hz, wzór testowy ffmpeg z napisem „VivaPilot test clip”. **Rozmiar 0,73 MB** (limit 3 MB). Sprawdzone `ffprobe` i obejrzana klatka. Cały materiał wygenerowany ffmpeg, bez cudzych treści. [PEWNE]
- `test.m3u` – 3 kanały „Test Channel 1–3”, grupa „Test”.

**Ważne odkrycia z kodu (nie zmieniałem kodu):**
1. **Trzy kanały mają trzy różne adresy** (`clip.mp4?ch=1`, `?ch=2`, `?ch=3`). To ten sam plik (GitHub Pages ignoruje część po „?”). Powód: aplikacja tworzy identyfikator kanału z adresu strumienia. Gdyby trzy kanały miały identyczny adres, miałyby identyczny identyfikator, co mogłoby psuć listy, ulubione, a nawet wywrócić listę w aplikacji. [PEWNE: M3UParser.toChannels; skutek awarii: niepewny, ale unikamy ryzyka]
2. **Film jako VOD się nie da przez M3U.** Parser M3U oznacza każdy wpis jako kanał na żywo (`kind = LIVE`). Zakładki **Movies** i **Series** wypełnia tylko źródło Xtream Codes. Xtream wymaga serwera odpowiadającego na zapytania `player_api.php?action=...`, a GitHub Pages serwuje pliki statyczne i nie rozróżnia takich zapytań. Zrobienie VOD wymagałoby własnego serwera (nie robię tego). **Zakładki Movies i Series będą u recenzenta puste, to normalne.** Napisz to w odpowiedzi do Google, jeśli zapytają.
3. Jeśli adres pliku .m3u zwróci błąd (np. 404), aplikacja nie pokaże błędu, tylko 0 kanałów. [PEWNE: CatalogFetcher.kt]

### Jak wgrać na GitHub Pages (strona https://dam-gal.github.io/vivapilot-legal/)
1. Wejdź na GitHub w repozytorium tej strony (to, w którym leży folder `pl` z `privacy.html`).
2. Upewnij się, że lista gałęzi pokazuje tę, z której działa strona (zwykle `main`).
3. **Add file → Upload files**. Rozpakuj paczkę i przeciągnij **cały folder `reviewer`** (zawiera `test.m3u` i `clip.mp4`) do okna. Powinny wylądować w `reviewer/` obok folderu `pl`.
4. Na dole: **Commit changes** (bezpośrednio na tej gałęzi).
5. Poczekaj 1–3 minuty (zakładka **Actions**: „pages build and deployment” na zielono).
6. Sprawdź w przeglądarce oba adresy:
   - `https://dam-gal.github.io/vivapilot-legal/reviewer/clip.mp4` powinien się odtworzyć (obraz i ton),
   - `https://dam-gal.github.io/vivapilot-legal/reviewer/test.m3u` powinien pokazać tekst lub zaproponować pobranie.
To dane publiczne. W plikach nie ma haseł ani kluczy.

### (d) Czy aplikacja przyjmie taki adres i odtworzy film?
- **https i plik .m3u:** **TAK (kod)**. Aplikacja nie sprawdza końcówki adresu ani nie wymaga innego formatu; pobiera tekst przez OkHttp i czyta linie `#EXTINF` oraz adresy (parser odtworzyłem w Pythonie na `test.m3u`: 3 kanały, 3 różne adresy). [Kotlin nie był uruchamiany]
- **Odtworzenie filmu w aplikacji:** **NIE SPRAWDZONO W DZIAŁANIU.** Nie mam emulatora, a strona jeszcze nie jest wgrana. Plik to standardowy MP4 H.264/AAC, który odtwarzacz VLC w aplikacji zwykle odtwarza. Po wgraniu sprawdź sam: zainstaluj aplikację, dodaj listę według p. 1 i dotknij „Test Channel 1”. Jeśli się nie odtworzy, napisz co widać.
- Inny format nie jest potrzebny. Nie sprawdziłem, czy GitHub Pages przewija film (Range), ale 15-sekundowy plik nie potrzebuje przewijania.

## 3. Tekst do pola „Wszelkie inne informacje wymagane do uzyskania dostępu”
Wersja gotowa jest w pliku `TEKST_DLA_RECENZENTOW_EN.txt`. **409 znaków** (limit 480):

```
No account or login. VivaPilot is a player without its own content. 1) Tick the Terms checkbox, tap Start. 2) Add dialog opens (or: Settings > Manage playlists > +). Tap M3U / M3U8, paste or type the M3U/M3U8 file URL https://dam-gal.github.io/vivapilot-legal/reviewer/test.m3u, tap Add. 3) Open Live, tap Test Channel 1. All features are free for 5 days after first launch; one-time Full Version is optional.
```

Zmiany względem 1.26: usunięto „paste is disabled”; usunięto miejsce na kod promocyjny (nie jest potrzebny, bo przez pierwsze 5 dni wszystkie funkcje są dostępne). Jeśli w Play Console wklejono wcześniejszy tekst, wklej tę wersję w jego miejsce.

## 4. Okres próbny i recenzenci (tylko opis, nic nie zmieniałem)

**(a) Co zobaczy recenzent przy pierwszym uruchomieniu.** Wszystkie funkcje są dostępne przez **5 dni** od pierwszego uruchomienia (`TrialGate.TRIAL_DAYS = 5`, liczone co 24 h). Na ekranie powitalnym jest to napisane („For 5 days all features are free.”), a w nagłówku widać „Trial: 5 d left”. Czas startuje przy pierwszym uruchomieniu aplikacji. [PEWNE]

**(b) Po okresie próbnym bez zakupu** (`AppViewModel.isTrialExpired`, `TrialGate.kt`):
- zablokowane: **wyszukiwanie** na ekranie Home, **My Lists** (pełny widok list), **„Suggestions to watch”**, **zarządzanie kategoriami** (Settings); w wariancie „ads” dochodzą reklamy. Pobieranie na urządzenie jest zablokowane razem z nimi, ale w wersjach do Google Play jest i tak wyłączone (`DOWNLOADS_ENABLED=false`, build.gradle.kts).
- nadal działa: oglądanie Live/Movies/Series, dodawanie i edycja playlist, ulubione, ścieżki audio i napisy, kontrola rodzicielska, EPG, PiP, „Cast to TV” i kopia zapasowa.
Reklamy w wariancie „ads” według dokumentów pojawiają się dopiero po okresie próbnym i bez zakupu, tylko na telefonie i tablecie. [PEWNE: opis; szczegóły reklam: patrz PLAY_CONSOLE_ODPOWIEDZI.md]

**(c) Czy jest tryb pozwalający sprawdzić funkcje płatne bez zakupu?**
- W wersji do Google Play (release): **NIE.** Nie ma przełącznika, kodu ani ukrytego trybu. Funkcje płatne są odblokowane przez pierwsze 5 dni albo przez prawdziwy zakup.
- Panel „TEST” z symulacją zakupu istnieje **tylko w buildzie debug**, nie w wersji sklepowej.
- Realna opcja poza kodem: **kod promocyjny** z Play Console (jak chcesz) albo konto **„License testing”** (Play Console → Ustawienia → Testowanie licencji; testerzy kupują bez opłaty). [Z PAMIĘCI, sprawdź w Konsoli.]
- Praktyka: jeśli recenzent sprawdza aplikację w pierwszych 5 dniach, widzi wszystkie funkcje bez kodu.

## 5. Lista rzeczy, które musisz zrobić sam
1. Wgrać folder `reviewer` na stronę (p. 2) i sprawdzić oba adresy w przeglądarce.
2. Zainstalować aplikację i sprawdzić, że lista się dodaje, a „Test Channel 1” odtwarza (czego ja nie mogłem sprawdzić).
3. Wkleić tekst z p. 3 w Play Console (Dane logowania / dostęp do aplikacji), zastępując poprzedni.
4. Zdecydować, czy wystawiać licencję testową/kod. Jeśli recenzent zapyta o Movies/Series (puste), wyjaśnić, że są dla źródeł Xtream.
5. Nie wpisywać do pola żadnych prawdziwych haseł.
