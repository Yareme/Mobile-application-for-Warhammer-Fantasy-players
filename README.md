<div align="center">

# ⚔️ Warhammer Card

**Aplikacja mobilna na Androida dla graczy Warhammer Fantasy Roleplay**

Cyfrowa karta postaci, losowanie bohaterów zgodnie z zasadami gry i eksport do PDF

![Android](https://img.shields.io/badge/Android-3DDC84?style=for-the-badge&logo=android&logoColor=white)
![Java](https://img.shields.io/badge/Java%2017-007396?style=for-the-badge&logo=openjdk&logoColor=white)
![SQLite](https://img.shields.io/badge/SQLite-003B57?style=for-the-badge&logo=sqlite&logoColor=white)
![Gradle](https://img.shields.io/badge/Gradle-02303A?style=for-the-badge&logo=gradle&logoColor=white)
![Android Studio](https://img.shields.io/badge/Android%20Studio-4285F4?style=for-the-badge&logo=androidstudio&logoColor=white)

</div>

---

## 📖 O projekcie

W papierowych grach RPG karta postaci zmienia się przez całą rozgrywkę. Wiele pól zależy od siebie nawzajem (zmiana jednej cechy wymusza przeliczenie umiejętności), a ołówek i gumka szybko robią z karty nieczytelny dokument. **Warhammer Card** przenosi kartę postaci do telefonu: pilnuje zależności między polami, losuje postacie według zasad gry i pozwala w każdej chwili wydrukować klasyczny arkusz.

Aplikacja skupia się na **rozgrywce na żywo** (przy stole), a nie na grze online. Działa w całości lokalnie na urządzeniu, ma interfejs w języku polskim i powstała jako **praca inżynierska** na Uniwersytecie Śląskim w Katowicach.

## ✨ Funkcje

### 🗺️ Kampanie i karty postaci
- Tworzenie **kampanii** i przypisywanie do nich wielu kart postaci (np. po śmierci bohatera lub zmianie postaci)
- **Ręczne tworzenie** pustej karty albo **losowanie postaci** zgodnie z zasadami gry
- Losowanie obejmuje imię, rasę, klasę, profesję, wiek, wzrost, kolor włosów i oczu oraz wartości cech zależne od rasy
- Opcjonalny start od **wyboru profesji**, co zawęża pulę możliwych ras

### 🧮 Automatyzacja zasad
- Poziom profesji, ścieżka profesji i status uzupełniają się **automatycznie** na podstawie profesji
- Umiejętności i **żywotność** przeliczają się po każdej zmianie cechy
- Talenty wpływają na wartości cech i odwrotnie
- **Podpowiedzi rozwoju**: aplikacja pokazuje, jakie cechy, umiejętności i talenty są dostępne dla wybranej profesji i jej poziomu

### 🎒 Sekcje karty postaci
- Profil, punkty (doświadczenie, bohater, przeznaczenie, szczęście, determinacja), szybkość
- Cechy, umiejętności podstawowe i zaawansowane (z przyciskami +1 / −1), talenty
- Ambicje własne i drużyny, członkowie drużyny
- Psychologia, zepsucie i mutacje, punkty grzechu
- Zamożność (pensy, srebro, złote korony) i żywotność
- **Pancerz** z lokalizacjami na ciele, cechami i oznaczeniem „założony”, z podglądem punktów pancerza dla każdej części ciała
- **Broń**, **wyposażenie** (z liczbą sztuk), **czary i modlitwy**
- Przedmioty można wybrać z listy albo dodać własne, a własne trafiają na wspólną listę i są dostępne dla innych kart

### 📄 Eksport do PDF
Gotową kartę można wygenerować jako **wypełniony plik PDF** (formularz z nazwanymi polami) i wydrukować. Plik trafia do folderu `Downloads/Warhammer`.

### 📚 Zakładka „Dla Mistrza Gry”
Przeglądarka zasad w formacie PDF z podziałem na rozdziały i podrozdziały, dostępna z menu bocznego.

## 🛠️ Technologie

| Obszar | Rozwiązanie |
|---|---|
| Platforma | Android 7.0+ (minSdk 24, targetSdk 33) |
| Język | Java 17 |
| Budowanie | Gradle, Android Studio |
| Baza danych | SQLite (lokalna), inicjalizacja ze skryptu SQL |
| Interfejs | Widoki XML, View Binding, Material Components, ConstraintLayout, RecyclerView, ViewPager2 |
| Nawigacja | Jedna aktywność + fragmenty, Navigation Component |
| Architektura | ViewModel + LiveData, adaptery list |
| Biblioteki UI | Floating Action Menu (`clans/fab`), PhotoView |
| PDF | iText 5 (`itextpdf 5.5.13.2`), wypełnianie pól formularza, zapis przez MediaStore |

## 🏗️ Architektura

Aplikacja opiera się na **jednej aktywności** (`MainActivity`) z menu bocznym i ponad piętnastoma **fragmentami**. Każdy ekran karty postaci to osobny fragment, a przejścia między nimi obsługuje **Navigation Component** (`mobile_navigation.xml`), przekazując identyfikatory w `Bundle`. Dane do list dostarczają klasy `ViewModel` przez `LiveData`, a fragmenty obserwują je i aktualizują adaptery `RecyclerView`. Dostęp do bazy realizuje `AppSQLiteHelper`.

```
Kampanie ──< Karty postaci ──< Cechy / Umiejętności / Talenty
                         ├──< Broń / Pancerz / Wyposażenie
                         └──< Zaklęcia
```

### Struktura projektu

```
app/src/main/
├── assets/
│   ├── db_init.sql          # schemat i dane początkowe bazy
│   ├── update.sql           # aktualizacje bazy
│   └── pdf/                 # szablon karty postaci i materiały PDF
├── java/pl/edu/us/warhammer_card/
│   ├── MainActivity.java
│   ├── AppSQLiteHelper.java, DbHelper.java    # warstwa bazy danych
│   ├── CreatorKart.java                       # tworzenie i losowanie kart
│   ├── PdfGenerator.java                      # eksport karty do PDF
│   ├── PDFViewerActivity.java                 # przeglądarka PDF
│   ├── adapter/             # adaptery RecyclerView
│   ├── table/               # klasy modelu (Karta, Rasa, Profesja, Bron, Pancerz...)
│   └── ui/
│       ├── home/            # kampanie i lista kart
│       ├── karta/           # strona 1: profil, cechy, punkty, umiejętności, talenty, ambicje
│       ├── karta2/          # strona 2: broń, pancerz, wyposażenie, czary, psychologia, zamożność
│       └── mistrzgry/       # rozdziały zasad
└── res/                     # układy XML, nawigacja, grafiki, motywy
```

## 🗄️ Baza danych

Relacyjna baza SQLite (**35 tabel**) obejmuje:

- **Dane postaci:** `karta`, `kampania`
- **Zasady gry:** `rasa`, `klasa`, `profesja`, `poziom`, `status`, `rasa_profesja`, `cechy`, `umiejetnosci`, `talent`
- **Przedmioty:** `bron`, `typ_broni`, `cechy_broni`, `pancerz`, `typ_pancerza`, `lokalizacja_pancerza`, `cechy_pancerza`, `wyposazenie`, `dostepnosc`
- **Magia:** `zaklecia`, `tradycja`
- **Słowniki losowania:** `imiona`, `kolor_oczu`, `kolor_wlosow`
- **Tabele asocjacyjne** łączące kartę z cechami, umiejętnościami, talentami, bronią, pancerzem, wyposażeniem i zaklęciami (wraz z rozwojem, poziomem lub liczbą sztuk)

Tabele `rasa` i `rasa_profesja` zawierają przedziały wartości używane do losowania zgodnego z zasadami gry. Pełny diagram logiczny, diagram ERD i opis każdej tabeli znajdują się w pracy inżynierskiej.

## 🚀 Uruchomienie

**Wymagania:** Android Studio (z JDK 17) oraz emulator lub urządzenie z Androidem 7.0 (API 24) lub nowszym.

```bash
git clone https://github.com/Yareme/Mobile-application-for-Warhammer-Fantasy-players.git
```

1. Otwórz sklonowany folder w **Android Studio**.
2. Poczekaj na synchronizację Gradle.
3. Uruchom aplikację na emulatorze lub podłączonym urządzeniu (**Run ▶**).

## 📘 Dokumentacja
- [Praca inżynierska](Praca%20In%C5%BCynierska%20Valentyn%20Yaremenko.docx): analiza problemu, przypadki użycia, projekt bazy danych, opis implementacji

## ⚠️ Znane ograniczenia i plany rozwoju

- Nie każdy element w bazie ma dodany opis, a w niektórych poziomach profesji wyświetlają się elementy przypisane do innego poziomu (uproszczenia przy wprowadzaniu danych)
- Planowana część serwerowa: konta użytkowników, przenoszenie kart między urządzeniami, udostępnianie kart innym graczom, ocenianie i sortowanie kart według rasy, profesji i popularności

## ⚖️ Informacja prawna

To nieoficjalny projekt edukacyjny, niepowiązany z Games Workshop ani Cubicle 7. Warhammer Fantasy Roleplay i powiązane nazwy są własnością ich właścicieli.

## 👤 Autor

**Valentyn Yaremenko**
Informatyka inżynierska, Uniwersytet Śląski w Katowicach
Praca inżynierska pod kierunkiem dr. Mariusza Marzca, 2026

[GitHub](https://github.com/Yareme) · [LinkedIn](https://linkedin.com/in/valentyn-yaremenko)
