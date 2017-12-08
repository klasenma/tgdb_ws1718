# Tutorium - Grundlagen Datenbanken - Blatt 10

## Vorbereitungen
* Für dieses Aufgabenblatt wird die SQL-Dump-Datei `tutorium.sql` benötigt, die sich im Verzeichnis `sql` befindet.
* Die SQL-Dump-Datei wird in SQL-Plus mittels `start <Dateipfad/zur/sql-dump-datei.sql>` in die Datenbank importiert.
* Beispiele
  * Linux `start ~/Tutorium.sql`
  * Windows `start C:\Users\max.mustermann\Desktop\Tutorium.sql`

## Datenbankmodell
![Datenbankmodell](./img/datamodler_schema.png)

### Aufgabe 1
Was macht der folgende SQL-Code?

```sql
WITH vehicles AS (
    SELECT COUNT(*) Anzahl, accv.account_id
    FROM acc_vehic accv
    GROUP BY accv.account_id
)
SELECT CONCAT(a.forename, CONCAT(' ', a.surname)) AS "Benutzer" , v.Anzahl AS "Anzahl"
FROM vehicles v
    INNER JOIN account a ON (v.account_id = a.account_id)
WHERE v.Anzahl = (
    SELECT MAX(Anzahl)
    FROM vehicles
);
```

#### Lösung

Der SQL-Code gibt die Anzahl sowie den Vor- und Nachnamen der Personen aus, welche die meisten account_id's in 
der Tabelle acc_vehic besitzen. Dazu wird zunächst eine Abfrage nach der Anzahl und der entsprechenden account_id
unter dem Alias vehicles erstellt (gruppiert nach der account_id). Im zweiten Schritt werden der Vor- und Nachname (zusammengefasst unter dem Alias Benutzer)
und die Anzahl der account_id's selektiert und ausgegeben. Dies erfolgt über die vorher definierte Abfrage vehicles mit
Hilfe eines Inner Joins auf die Tabelle account. 


### Aufgabe 2
Die folgende Aufgabe bezieht sich auf das Datenbankmodell der Vorlesung.
Geben Sie mit einem SQL Befehl alle Klausuren aus, zu denen sich Personen angemeldet haben, aber bei **ALLEN** angemeldeten Personen fehlt die Note.

#### Lösung
```sql

select klausurnr
from klaus_bezie_angebo
where fachnr in (select fachnr
		 from leistungsschein
		 where anzahl_versuche is not null
		 and note is null);

```

### Aufgabe 3
Finde mit der Option `EXISTS` herraus, wie viele Hersteller in der Datenbank hinterlegt sind ohne mit einem Auto verknüpft zu sein.

#### Lösung
```sql

with producer_count as (
select count(*) Anzahl, producer_id 
from producer
where not exists (select producer_id from vehicle)
group by producer_id
)
select producer_name
from producer_count pc
inner join producer p 
on(pc.producer_id = p.producer_id);

```
