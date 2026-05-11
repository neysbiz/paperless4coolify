# paperless4coolify

Paperless-NGX für das Deployment mit [Coolify](https://coolify.io) – vorkonfiguriert mit PostgreSQL, Redis, Apache Tika und Gotenberg. Das Webserver-Image wird dabei von Docker Hub bezogen.

---

## Enthaltene Dateien

| Datei | Beschreibung |
|---|---|
| `docker-compose.postgres-tika.yml` | Hauptkonfiguration (alle Services) |
| `.env.example` | Dokumentation aller Umgebungsvariablen |
| `.gitignore` | Schützt echte `.env`-Dateien vor versehentlichem Commit |

---

## Deployment in Coolify

### 1. Projekt anlegen

1. Coolify öffnen → **New Resource → Docker Compose**
2. Als Quelle dieses GitHub-Repository angeben
3. Als Compose-Datei `docker-compose.postgres-tika.yml` auswählen

### 2. Umgebungsvariablen setzen

In Coolify unter **Environment Variables** folgende Werte eintragen (Referenz: `.env.example`):

| Variable | Beschreibung | Pflicht |
|---|---|---|
| `POSTGRES_PASSWORD` | Datenbankpasswort | ✅ |
| `PAPERLESS_SECRET_KEY` | Langer zufälliger String (min. 50 Zeichen) | ✅ |
| `PAPERLESS_URL` | Öffentliche URL, z. B. `https://paperless.example.com` | empfohlen |
| `PAPERLESS_ADMIN_USER` | Benutzername des ersten Admins | empfohlen |
| `PAPERLESS_ADMIN_PASSWORD` | Passwort des ersten Admins | empfohlen |
| `PAPERLESS_ADMIN_MAIL` | E-Mail des ersten Admins | empfohlen |
| `PAPERLESS_TIME_ZONE` | Zeitzone, z. B. `Europe/Berlin` | optional |
| `PAPERLESS_OCR_LANGUAGE` | OCR-Sprache, z. B. `deu+eng` | optional |
| `POSTGRES_DB` | Datenbankname (Default: `paperless`) | optional |
| `POSTGRES_USER` | Datenbankbenutzer (Default: `paperless`) | optional |

> Einen sicheren `PAPERLESS_SECRET_KEY` erzeugen:
> ```bash
> openssl rand -base64 48
> ```

### 3. Starten

Coolify → **Deploy** klicken. Beim ersten Start wird der Admin-Account automatisch angelegt (sofern `PAPERLESS_ADMIN_*` gesetzt).

Paperless ist anschließend auf Port `8000` erreichbar (bzw. über die in Coolify konfigurierte Domain).

---

## Storage-Layer

Die Compose-Datei trennt den Speicher bereits sauber nach Verwendungszweck:

| Volume | Zweck |
|---|---|
| `paperless_data` | Paperless-Anwendungsdaten |
| `paperless_media` | Hochgeladene Dokumente und Medien |
| `paperless_pgdata` | PostgreSQL-Daten |
| `paperless_redisdata` | Redis-Persistenz |

Damit kannst du später einzelne Layer leichter auf separate Storage-Klassen, Volumes oder Container abbilden, ohne die komplette Konfiguration umbauen zu müssen.

---

## Datenbank auslagern (optional)

Die Konfiguration ist bereits vorbereitet. Um die DB in einen eigenen Coolify-Container auszulagern:

1. Den `db`-Service und das Volume `paperless_pgdata` im Compose-File auskommentieren
2. In Coolify folgende Vars auf den externen DB-Container zeigen:

```
PAPERLESS_DBHOST=<coolify-interner-hostname>
PAPERLESS_DBPORT=5432
```

Die übrigen Volumes können dabei unverändert bleiben, weil sie unabhängig von der Datenbank sind.

---

## Referenz

- [Paperless-ngx Dokumentation](https://docs.paperless-ngx.com)
- [Paperless-ngx GitHub](https://github.com/paperless-ngx/paperless-ngx)

