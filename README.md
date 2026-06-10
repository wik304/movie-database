# 🎬 Kinoteka – Zaawansowana Baza Filmów i Platforma Recenzencka

**Kinoteka** to kompleksowa, autorska aplikacja webowa napisana w języku PHP (z wykorzystaniem czystego JavaScriptu, HTML5 i CSS3), pełniąca funkcję interaktywnego katalogu filmowego oraz społecznościowej platformy dla kinomanów i krytyków. Projekt architekturą i funkcjonalnością nawiązuje do popularnych serwisów takich jak Filmweb czy IMDb.

Aplikacja charakteryzuje się bogatym systemem ról użytkowników, zaawansowanym mechanizmem oceniania, gamifikacją (osiągnięcia) oraz rozbudowanym panelem administracyjnym. Została zaprojektowana z myślą o płynnym UX (User Experience), wykorzystując asynchroniczne żądania AJAX do obsługi większości interakcji.

---

## ✨ Główne Funkcjonalności

### 👥 System Użytkowników i Społeczność
* **Hierarchia ról:** System rozróżnia 4 typy kont: Gość, Użytkownik (`user`), Krytyk (`critic`), Administrator (`admin`) oraz Właściciel (`owner`).
* **Profile użytkowników:** Każdy zarejestrowany użytkownik posiada spersonalizowany profil. Może zmienić swój awatar oraz tło (banner) profilu (wgrywanie asynchroniczne za pomocą `FormData` i Fetch API).
* **Konta Krytyków:** Użytkownicy z rolą `critic` posiadają specjalne oznaczenie (ikona weryfikacji), miejsce na profesjonalne bio oraz ich oceny są liczone do osobnej średniej ("Oceny krytyków").
* **System Obserwacji (Followers):** Użytkownicy mogą obserwować ulubionych krytyków i innych kinomanów, budując własną sieć społecznościową.
* **Gamifikacja (Osiągnięcia):** Automatyczny system przyznawania odznak (np. za napisanie pierwszej recenzji, dodanie X filmów do ulubionych).

### 🎞️ Interakcje z Filmami
* **Podwójny system ocen:** Filmy posiadają dwie niezależne średnie ocen: od zwykłych widzów oraz od certyfikowanych krytyków.
* **Recenzje i Polubienia:** Użytkownicy mogą pisać pełne recenzje do filmów. Inni czytelnicy mogą oceniać przydatność tych recenzji, zostawiając pod nimi "lajki" (obsługiwane bez przeładowania strony).
* **Prywatne Listy:** Możliwość dodawania filmów do list **"Ulubione"** oraz **"Chcę zobaczyć"** (Watchlist).
* **Migracja Sesji Gościa (Guest Experience):** Niezarejestrowani użytkownicy (Goście) mogą oceniać filmy i tworzyć listy. Ich aktywność jest zapisywana w sesji (`$_SESSION`). **Po założeniu konta lub zalogowaniu, cały dorobek gościa jest automatycznie migrowany i przypisywany do jego nowego konta w bazie danych.**

### 🔍 Zaawansowana Wyszukiwarka i Filtrowanie
* **Wyszukiwanie na żywo (Autocomplete):** Pasek wyszukiwarki dynamicznie podpowiada tytuły filmów wraz z plakatami i latami wydania po wpisaniu zaledwie dwóch znaków.
* **Złożone filtry:** Strona wyników pozwala na wielopoziomowe filtrowanie bazy po:
    * Tytule,
    * Gatunku (wielokrotny wybór),
    * Zakresie lat premiery (od-do),
    * Zakresie ocen użytkowników i krytyków (od-do).
* **Sortowanie:** Wyniki można sortować m.in. po dacie wydania, popularności lub najwyższych ocenach.

### 🛡️ Panel Administracyjny (CMS)
Rozbudowany, dedykowany panel zarządzania dla administratorów:
* **Zarządzanie Filmami:** Dodawanie, edycja i usuwanie filmów. Intuicyjny system dodawania reżyserów i gatunków za pomocą dynamicznych tagów (własny skrypt JS). Obsługa statusów filmów (`available` / `upcoming`).
* **Moduł Ogłoszeń (Hero Slider):** Admin może zarządzać głównym banerem na stronie głównej. Może powiązać slajd z filmem, wgrać dedykowane tło w wysokiej rozdzielczości, włączać/wyłączać slajdy oraz **zmieniać ich kolejność metodą Drag & Drop** (przy użyciu biblioteki SortableJS).
* **Zarządzanie Użytkownikami:** Możliwość nadawania ról (np. awansowanie użytkownika na krytyka) oraz blokowania (banowania) toksycznych kont.
* **Moderacja Recenzji:** Przegląd i możliwość usuwania lub edytowania recenzji napisanych przez użytkowników.

---

## 💻 Aspekty Techniczne i Architektura

Projekt został napisany z naciskiem na czystość kodu i bezpieczeństwo, pomimo braku frameworka PHP.

* **Baza Danych:** Relacyjna baza MySQL. Wykorzystanie zapytań grupujących (`GROUP_CONCAT`), podzapytań do dynamicznego wyliczania średnich ocen oraz funkcji `ON DUPLICATE KEY UPDATE` dla optymalizacji zapisów (np. przy słownikach gatunków/reżyserów).
* **Bezpieczeństwo:**
    * Szyfrowanie haseł za pomocą `password_hash()`.
    * Ochrona przed SQL Injection poprzez konsekwentne stosowanie **Prepared Statements** (`bind_param`) we wtyczce `mysqli`.
    * Ochrona przed XSS poprzez sanitizację wejścia (`strip_tags`) oraz ucieczkę wyjścia (`htmlspecialchars`).
* **Backend (PHP):** Struktura oparta na dedykowanych plikach akcji (katalog `actions/`), oddzielających logikę przetwarzania formularzy od widoku. Rozbudowana obsługa sesji.
* **Frontend (UI/UX):**
    * Responsywny interfejs oparty o Custom CSS i Flexbox/CSS Grid. Brak ciężkich bibliotek typu Bootstrap.
    * Dynamiczne powiadomienia (Toast Notifications) informujące o sukcesie lub błędzie akcji.
    * Karusela filmów obsługiwana przez lekką bibliotekę **Splide.js**.
* **JavaScript (ES6+):** Szerokie zastosowanie interfejsu **Fetch API** do asynchronicznej komunikacji z serwerem. Wiele modułów (np. system "lajków", zmiana awatarów, formularze filtrowania) działa w formie Single Page Application (SPA), co znacząco zmniejsza obciążenie serwera i poprawia wrażenia z użytkowania.
* **Zarządzanie Plikami:** System obsługuje bezpieczne przesyłanie plików graficznych (plakaty, tła ogłoszeń, awatary) ze sprawdzaniem typów MIME, rozszerzeń i limitów wielkości. Nowo wgrane pliki otrzymują unikalne nazwy zapobiegające nadpisaniom.

---

## 🚀 Jak uruchomić projekt lokalnie?

**Wymagania wstępne:**
Zainstalowane lokalne środowisko serwerowe z obsługą PHP i MySQL (np. **XAMPP**, **WAMP**, **Laragon** lub MAMP).

**Instrukcja krok po kroku:**

1. **Pobierz projekt:** 
   Sklonuj to repozytorium lub pobierz je jako plik ZIP i rozpakuj.

2. **Umieść pliki na serwerze:** 
   Skopiuj cały folder z projektem do katalogu głównego swojego serwera lokalnego (np. dla XAMPP jest to folder `C:\xampp\htdocs\`).

3. **Skonfiguruj Bazę Danych:**
   * Uruchom serwer Apache oraz bazę MySQL w swoim środowisku (np. w panelu sterowania XAMPP).
   * Otwórz narzędzie **phpMyAdmin** (zazwyczaj pod adresem `http://localhost/phpmyadmin`).
   * Utwórz nową bazę danych o nazwie `baza_filmow_db` (z kodowaniem `utf8mb4_general_ci`).
   * Zaimportuj strukturę bazy i dane z pliku zrzutu bazy (plik `.sql`, jeśli jest dołączony do repozytorium), lub utwórz tabele zgodnie z logiką aplikacji.

4. **Połączenie z bazą:**
   * Otwórz plik konfiguracyjny `config/db_connect.php`.
   * Upewnij się, że dane do logowania są poprawne dla Twojej konfiguracji (domyślne dane dla XAMPP to zazwyczaj użytkownik `root` i puste hasło):
```php
   define('DB_SERVER', 'localhost');
   define('DB_USERNAME', 'root');
   define('DB_PASSWORD', '');
   define('DB_NAME', 'baza_filmow_db');
