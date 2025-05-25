# L11_PAwChO
# Laboratorium 11 – Programowanie Aplikacji w Chmurze Obliczeniowej


**Autor:** Jakub Kacper Kramek  
*Numer albumu* s9959  
*Grupa* 6/8
---

##Cel zadania

Uruchomienie trzech kontenerów z serwerem `nginx` (`web1`, `web2`, `web3`) z konfiguracją:
- Wspólna sieć mostkowa `lab11net` typu user-defined.
- Każdy kontener udostępnia statyczną stronę HTML z numerem laboratorium i danymi studenta.
- Każdy kontener zapisuje logi do dedykowanego katalogu `~/lab11/logs/`, który jest bind mountem z hosta.
- Strony HTML zamontowane w trybie `read-only`.

## Zawartość plików `index.html`

### `~/lab11/html/web1/index.html`
```html
<html>
  <head><title>web1</title></head>
  <body>
    <h1>Lab11 – Jakub Kramek – web1</h1>
  </body>
</html>
```

### `~/lab11/html/web2/index.html`
```html
<html>
  <head><title>web2</title></head>
  <body>
    <h1>Lab11 – Jakub Kramek – web2</h1>
  </body>
</html>
```

### `~/lab11/html/web3/index.html`
```html
<html>
  <head><title>web3</title></head>
  <body>
    <h1>Lab11 – Jakuba Kramek – web3</h1>
  </body>
</html>
```

---

## Komendy Docker

### Utworzenie sieci mostkowej
```bash
docker network create --driver bridge lab11net
```
### Wynik komendy:
```bash
ae226a7c32786fcefbdf22d08edaa935b70c7b00c06c911c9be1d2b32d077895
```

### Uruchomienie kontenerów

#### `web1`
```bash
docker run -d --name web1 --network lab11net -p 8081:80 \
  --mount type=bind,source=$HOME/lab11/html/web1,target=/usr/share/nginx/html,readonly \
  --mount type=bind,source=$HOME/lab11/logs/web1,target=/var/log/nginx \
  nginx:latest
```

#### `web2`
```bash
docker run -d --name web2 --network lab11net -p 8082:80 \
  --mount type=bind,source=$HOME/lab11/html/web2,target=/usr/share/nginx/html,readonly \
  --mount type=bind,source=$HOME/lab11/logs/web2,target=/var/log/nginx \
  nginx:latest
```

#### `web3`
```bash
docker run -d --name web3 --network lab11net -p 8083:80 \
  --mount type=bind,source=$HOME/lab11/html/web3,target=/usr/share/nginx/html,readonly \
  --mount type=bind,source=$HOME/lab11/logs/web3,target=/var/log/nginx \
  nginx:latest
```
### wynik odpalenia kontenera web2
```bash
7026864168d929b8a2dbef4a9d2e0e0e34ca77d26b8f3376f15b1b5d000a8ac2
```
---

## Weryfikacja działania

### Sprawdzenie stron w przeglądarce lub curl:
```bash
curl http://localhost:8081
curl http://localhost:8082
curl http://localhost:8083
```
### wynik dla portu 8081:
```bash
<html>
  <head><title>web1</title></head>
  <body>
    <h1>Lab11  Jakub Kramek  web1</h1>
  </body>
</html>
```


###  Sprawdzenie logów serwera:
```bash
cat ~/lab11/logs/web1/access.log
cat ~/lab11/logs/web2/access.log
cat ~/lab11/logs/web3/access.log
```
### wynik logowania dla web1:
```bash
192.168.65.1 - - [23/May/2025:17:36:52 +0000] "GET / HTTP/1.1" 200 67 "-" "curl/8.7.1" "-"
192.168.65.1 - - [23/May/2025:17:37:03 +0000] "GET / HTTP/1.1" 200 67 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/18.0.1 Safari/605.1.15" "-"
192.168.65.1 - - [23/May/2025:17:37:03 +0000] "GET /favicon.ico HTTP/1.1" 404 153 "http://localhost:8081/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/18.0.1 Safari/605.1.15" "-"
192.168.65.1 - - [23/May/2025:17:37:42 +0000] "GET / HTTP/1.1" 304 0 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/18.0.1 Safari/605.1.15" "-"
192.168.65.1 - - [23/May/2025:17:38:19 +0000] "GET / HTTP/1.1" 200 65 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/18.0.1 Safari/605.1.15" "-"
192.168.65.1 - - [23/May/2025:17:50:17 +0000] "GET / HTTP/1.1" 200 109 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/18.0.1 Safari/605.1.15" "-"
192.168.65.1 - - [23/May/2025:17:51:00 +0000] "GET / HTTP/1.1" 304 0 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/18.0.1 Safari/605.1.15" "-"
192.168.65.1 - - [25/May/2025:19:18:41 +0000] "GET / HTTP/1.1" 304 0 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/18.0.1 Safari/605.1.15" "-"
192.168.65.1 - - [25/May/2025:19:21:38 +0000] "GET / HTTP/1.1" 200 109 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/18.0.1 Safari/605.1.15" "-"
192.168.65.1 - - [25/May/2025:19:21:38 +0000] "GET /favicon.ico HTTP/1.1" 404 153 "http://192.168.0.230:8081/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/18.0.1 Safari/605.1.15" "-"
192.168.65.1 - - [25/May/2025:19:21:47 +0000] "GET / HTTP/1.1" 200 109 "-" "Mozilla/5.0 (iPhone; CPU iPhone OS 18_5 like Mac OS X) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/18.5 Mobile/15E148 Safari/604.1" "-"
192.168.65.1 - - [25/May/2025:19:21:47 +0000] "GET /favicon.ico HTTP/1.1" 404 153 "http://192.168.0.230:8081/" "Mozilla/5.0 (iPhone; CPU iPhone OS 18_5 like Mac OS X) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/18.5 Mobile/15E148 Safari/604.1" "-"
192.168.65.1 - - [25/May/2025:19:31:12 +0000] "GET / HTTP/1.1" 200 109 "-" "curl/8.7.1" "-"
```

---
