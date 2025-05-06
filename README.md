Ten projekt zawiera aplikację backendową w Laravel oraz frontendową w Vue.js (Vite). Poniżej znajduje się kompletna instrukcja instalacji, konfiguracji środowiska i obsługi CORS.

## Wymagania

- PHP >= 8.1  
- Composer  
- Node.js >= 16  
- npm  
- MySQL

## Instalacja

### Backend (Laravel)

1. Przejdź do katalogu `backend`:
   ```bash
   cd backend
   ```

2. Zainstaluj zależności:
   ```bash
   composer install
   ```

3. Skopiuj plik `.env` i wygeneruj klucz aplikacji:
   ```bash
   cp .env.example .env
   php artisan key:generate
   ```

4. Skonfiguruj połączenie z bazą danych w `.env`:
   ```
   DB_CONNECTION=mysql
   DB_HOST=127.0.0.1
   DB_PORT=3306
   DB_DATABASE=twoja_baza
   DB_USERNAME=twoj_uzytkownik
   DB_PASSWORD=twoje_haslo
   ```

5. Wykonaj migracje:
   ```bash
   php artisan migrate
   ```

6. Jeśli frontend działa na innym porcie (np. 5173), ustaw CORS w `config/cors.php`:
   ```php
   'allowed_origins' => ['http://localhost:5173'],
   ```

7. Uruchom serwer:
   ```bash
   php artisan serve
   ```

### Frontend (Vue.js)

1. Przejdź do katalogu `frontend`:
   ```bash
   cd ../frontend
   ```

2. Zainstaluj zależności:
   ```bash
   npm install
   ```

3. Jeśli backend działa na innym porcie (np. 8001), ustaw baseURL w `src/api.js`:
   ```
   baseURL: 'http://localhost:8001/api'
   ```

4. Uruchom aplikację:
   ```bash
   npm run dev
   ```