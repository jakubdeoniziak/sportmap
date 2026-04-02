# 🗺️ SportMap Olsztyn — Geoportal Sportowy

Interaktywna mapa obiektów sportowych miasta Olsztyn. Projekt działa lokalnie w przeglądarce, dane pobierane są z bazy Supabase, a mapa korzysta z ortofotomapy GUGiK (Geoportal.gov.pl).

---

##  Demo

**[Otwórz geoportal](https://TWOJA-NAZWA.github.io/sportmap-olsztyn)**

---

##  Funkcje

-  **19 obiektów sportowych** — stadiony, hale, baseny, siłownie, korty, boiska
-  **Dwa podkłady mapowe** — mapa ciemna, ortofotomapa wysokiej rozdzielczości GUGiK
-  **Wyszukiwarka** — filtrowanie po nazwie, adresie i opisie w czasie rzeczywistym
-  **Panel szczegółów** — adres, telefon, strona WWW, godziny otwarcia (z wyróżnieniem dzisiaj), ocena Google
-  **Geolokalizacja** — pokazuje lokalizację użytkownika na mapie

---

##  Stack techniczny

| Technologia | Zastosowanie |
|---|---|
| [Leaflet.js](https://leafletjs.com/) | Renderowanie mapy |
| [Supabase](https://supabase.com/) | Baza danych PostgreSQL + REST API |
| [GitHub Pages](https://pages.github.com/) | Hosting (bezpłatny) |
| WMS GUGiK | Ortofotomapa z Geoportal.gov.pl |
| HTML / CSS / JS | Frontend — bez frameworków |

---

##  Uruchomienie lokalne

Projekt to pojedynczy plik HTML — wystarczy go otworzyć w przeglądarce:

```bash
git clone https://github.com/TWOJA-NAZWA/sportmap-olsztyn.git
cd sportmap-olsztyn
# Otwórz index.html w przeglądarce
```

> Dane nie załadują się bez skonfigurowanego połączenia z Supabase (patrz niżej).

---

##  Konfiguracja Supabase

### 1. Utwórz projekt na [supabase.com](https://supabase.com)

### 2. Utwórz tabelę `miejsca`

```sql
CREATE TABLE miejsca (
  id           bigint PRIMARY KEY GENERATED ALWAYS AS IDENTITY,
  nazwa        text NOT NULL,
  kategoria    text NOT NULL,
  adres        text,
  lat          float8 NOT NULL,
  lng          float8 NOT NULL,
  telefon      text,
  strona_www   text,
  godziny      jsonb,
  ocena        float4,
  liczba_ocen  int4,
  opis         text
);
```

Dozwolone wartości dla kolumny `kategoria`:
`stadion` | `hala` | `basen` | `silownia` | `kort` | `boisko`

### 3. Włącz publiczny odczyt (RLS)

```sql
ALTER TABLE miejsca ENABLE ROW LEVEL SECURITY;

CREATE POLICY "Publiczny odczyt"
ON miejsca FOR SELECT
TO anon
USING (true);
```

### 4. Wgraj dane

Zaimportuj plik `miejsca_supabase.csv` przez:
**Table Editor → Insert → Import data from CSV**

### 5. Wpisz klucze do pliku `index.html`

```js
const SUPABASE_URL = 'https://TWOJ-ID.supabase.co';
const SUPABASE_KEY = 'eyJhbGc...'; // anon/public key z Settings → API
```

### 6. Ogranicz dostęp CORS (zalecane)

W Supabase: **Settings → API → Allowed origins**:
```
https://TWOJA-NAZWA.github.io
```

---

##  Struktura projektu

```
sportmap-olsztyn/
├── index.html              # Cała aplikacja (jeden plik)
├── miejsca_supabase.csv    # Dane do importu do Supabase
└── README.md
```

---

##  Format danych

Kolumna `godziny` przechowuje dane w formacie JSON:

```json
[
  ["Poniedziałek", "7:00-22:00"],
  ["Wtorek", "7:00-22:00"],
  ["Środa", "7:00-22:00"],
  ["Czwartek", "7:00-22:00"],
  ["Piątek", "7:00-22:00"],
  ["Sobota", "8:00-20:00"],
  ["Niedziela", "8:00-20:00"]
]
```

---

##  Bezpieczeństwo

- **Anon key** jest bezpieczny w kodzie publicznym — daje wyłącznie dostęp do odczytu danych zdefiniowany w politykach RLS
- **Zapis, edycja i usuwanie** danych możliwe tylko przez panel Supabase (wymaga logowania)
- **Service role key** nigdy nie jest używany w aplikacji frontendowej

---

##  Źródła danych ortofotomapy

Ortofotomapa pochodzi z zasobów Głównego Urzędu Geodezji i Kartografii (GUGiK) udostępnianych przez [Geoportal.gov.pl](https://www.geoportal.gov.pl/) w standardzie WMS. Dane są bezpłatne i ogólnodostępne.

---

##  Licencja

MIT — możesz swobodnie używać, modyfikować i dystrybuować projekt.
