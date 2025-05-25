# L11_PAwChO
# Laboratorium 11 – Programowanie Aplikacji w Chmurze Obliczeniowej


**Autor:** Jakub Kramek  

---

##Cel zadania

Uruchomienie trzech kontenerów z serwerem `nginx` (`web1`, `web2`, `web3`) z konfiguracją:
- Wspólna sieć mostkowa `lab11net` typu user-defined.
- Każdy kontener udostępnia statyczną stronę HTML z numerem laboratorium i danymi studenta.
- Każdy kontener zapisuje logi do dedykowanego katalogu `~/lab11/logs/`, który jest bind mountem z hosta.
- Strony HTML zamontowane w trybie `read-only`.

---

##Struktura katalogów

```
~/lab11/
├── html/
│   ├── web1/
│   │   └── index.html
│   ├── web2/
│   │   └── index.html
│   └── web3/
│       └── index.html
└── logs/
    ├── web1/
    ├── web2/
    └── web3/
```

---

## 📝 Zawartość plików `index.html`

### `~/lab11/html/web1/index.html`
```html
<html>
  <head><title>web1</title></head>
  <body>
    <h1>Lab11 – Jan Kowalski – web1</h1>
  </body>
</html>
```

### `~/lab11/html/web2/index.html`
```html
<html>
  <head><title>web2</title></head>
  <body>
    <h1>Lab11 – Jan Kowalski – web2</h1>
  </body>
</html>
```

### `~/lab11/html/web3/index.html`
```html
<html>
  <head><title>web3</title></head>
  <body>
    <h1>Lab11 – Jan Kowalski – web3</h1>
  </body>
</html>
```

---

## Komendy Docker

### Utworzenie sieci mostkowej
```bash
docker network create --driver bridge lab11net
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

---

## Weryfikacja działania

### Sprawdzenie stron w przeglądarce lub curl:
```bash
curl http://localhost:8081
curl http://localhost:8082
curl http://localhost:8083
```

### 📄 Sprawdzenie logów serwera:
```bash
cat ~/lab11/logs/web1/access.log
cat ~/lab11/logs/web2/access.log
cat ~/lab11/logs/web3/access.log
```

---
