# ✈️ Projekt: Obsługa opóźnionych i odwołanych lotów – Apache Kafka + Python + PostgreSQL

Projekt realizowany w ramach przedmiotu **Analiza danych w czasie rzeczywistym**.

Celem jest stworzenie systemu, który:
- przetwarza dane o lotach w czasie rzeczywistym,
- wykrywa opóźnione i odwołane loty,
- automatycznie generuje komunikaty dla pasażerów (np. vouchery, alternatywne połączenia),
- zapisuje dane do bazy PostgreSQL jako źródło dla Power BI lub dashboardu.

---

## 💡 Decyzja biznesowa

**Automatyzacja obsługi pasażerów w sytuacjach zakłóceń**, aby:
- zmniejszyć obciążenie operacyjne obsługi klienta,
- zwiększyć satysfakcję i komfort pasażerów.

---

## 📦 Zawartość repozytorium

| Plik / folder | Opis |
|---------------|------|
| `src/Producer.py` | Odczytuje dane z pliku CSV i wysyła do Apache Kafka (topic `air-data`) |
| `src/Consumer.py` | Odbiera dane z Kafka, generuje komunikaty, zapisuje do pliku i PostgreSQL |
| `src/dashboard_writer.py` | Tworzy plik JSON z danymi statystycznymi do dashboardu |
| `docker-compose.yml` | Uruchamia Kafka, Zookeeper, PostgreSQL i pgAdmin |
| `data/dashboard_sample.csv` | Przykładowe dane wejściowe |
| `data/sample_flights.csv` | Alternatywny plik CSV z danymi lotów |
| `outputs/dashboard_data.json` | ✨ Generowany plik z danymi do dashboardu |
| `outputs/cancelled_messages.txt` | ✨ Generowane wiadomości dla pasażerów |
| `odwolania-test.ipynb` | Notebook do tworzenia testowych danych |
| `.env`, `.gitignore` | Pliki konfiguracyjne (np. ignorowanie dużych plików) |

---

## ▶️ Jak uruchomić projekt

### 1. Uruchom wszystkie usługi w tle (Kafka, Zookeeper, PostgreSQL, pgAdmin):
```bash
docker-compose up -d
`````
### 2. Uruchom terminal z konsumentem:
W terminalu:
```bash
python Consumer.py
`````
### 3. (Ewentualnie) Uruchom terminal z writerem dashboardu:
W terminalu:
```bash
python dashboard_writer.py
`````
### 4. Uruchom terminal z producentem:
W terminalu:
```bash
python Producer.py
`````

---

## ⚠️ UWAGA!

- Plik z danymi nie jest zawarty w repozytorium!!!
- Upewnij się, że podłączasz się pod odpowiedni port (Kafka: `9092`, PostgreSQL: `5432`).
- Sprawdź, czy istnieje topic `air-data` w Twoim środowisku (jeśli nie – producer go utworzy).
- Zobacz, czy `Producer.py` wskazuje na istniejący plik `.csv` w katalogu `data/`.

---

## 🔐 Dane dostępowe do PostgreSQL

| Parametr     | Wartość     |
|--------------|-------------|
| Host         | `localhost` |
| Port         | `5432`      |
| Baza danych  | `air_data`  |
| Użytkownik   | `user`      |
| Hasło        | `password`  |

---

## 🌐 Interfejs webowy pgAdmin

Dostępny pod adresem: 👉 [http://localhost:8080](http://localhost:8080)

**Dane logowania:**
- **Login:** `admin@admin.com`
- **Hasło:** `admin`

**Po zalogowaniu się:**
1. Kliknij **"Add New Server"**
2. W zakładce **Connection** wpisz:
   - **Host name:** `postgres`
   - **Username:** `user`
   - **Password:** `password`

---

## 📊 Power BI jako warstwa wizualizacyjna

System generuje dane w bazie PostgreSQL (`air_data`, tabela `cancellations`), które mogą być załadowane do Power BI jako źródło danych.

### Jak połączyć Power BI z bazą:
1. Wybierz: **Pobierz dane → Baza danych → PostgreSQL**
2. Wypełnij:
   - **Serwer:** `localhost`
   - **Baza danych:** `air_data`
   - **Login:** `user`
   - **Hasło:** `password`
3. Wybierz tabele: `public.cancellations` oraz `public.delays`

> 💡 Jeśli wystąpi błąd SSL, w ustawieniach zaawansowanych połączenia dodaj `?sslmode=disable`.

---

## 📊 Tableau jako warstwa wizualizacyjna

Alternatywa dla Power BI, zwłaszcza dla systemu operacyjnego MacOS. **Ważne!** Zadziała tylko na Tableau Desktop, nie na Tableau Public.

### Jak połączyć Tableau z bazą:
1. Na stronie internetowej [Tableau Drivers](https://www.tableau.com/support/drivers) sprawdź czy potrzebujesz drivera dla swojego systemu operacyjnego.
2. Pobierz drivera dla PostgreSQL i postępuj zgodnie z instrukcją.
3. Wybierz: **Connect → To a Server → More... → szukaj "PostgreSQL"**
4. Wypełnij:
   - **Server:** `localhost`
   - **Port:** 5432
   - **Database:** `air_data`
   - **Authentication:** 'Username and Password'
   - **Login:** `user`
   - **Hasło:** `password`
5. Tabele delays i cancellations powinny wyskoczyć automatycznie.
