### Problem in Script DB01_08

S. 13

Vor und zurueck Blaettern funktioniert nicht. Beispiel:

Dump der MariaDB zu diesem Beispiel im Ordner vorhanden.


Erstellung der Tabelle:
```sql
CREATE TABLE img (
	id INT NOT NULL AUTO_INCREMENT,
	title VARCHAR(50),
	filename VARCHAR(50),
	PRIMARY KEY (id)
)
```

Eigenschaften:

```code
+----------+-------------+------+-----+---------+----------------+
| Field    | Type        | Null | Key | Default | Extra          |
+----------+-------------+------+-----+---------+----------------+
| id       | int(11)     | NO   | PRI | NULL    | auto_increment |
| title    | varchar(50) | YES  |     | NULL    |                |
| filename | varchar(50) | YES  |     | NULL    |                |
+----------+-------------+------+-----+---------+----------------+
```



Inhalt:

```code
select * from img;
```

ergibt:

```code
+-----+---------+-------------+
| id  | title   | filename    |
+-----+---------+-------------+
|   1 | Hund    | hund.jpg    |
|   4 | Katze   | katze.png   |
|   8 | Maus    | maus.gif    |
|   9 | Elefant | elefant.jpg |
|  11 | Fliege  | fliege.jpg  |
|  20 | Ratte   | ratte.png   |
|  33 | Reh     | reh.png     |
| 100 | Adler   | adler.tiff  |
+-----+---------+-------------+
```

Nach Script ginge jetzt:

```sql
select 
  lag(id) over(order by id) as prev_id,
  id, filename, title,
  lead(id) over (order by id) as next_id
from img
where id = 9 order by id;
```

Das ergibt zwar eine Ausgabe, aber nicht die erhoffte:

```code
+---------+------+-------------+---------+---------+
| prev_id | id   | filename    | title   | next_id |
+---------+------+-------------+---------+---------+
|    NULL |    9 | elefant.jpg | Elefant |    NULL |
+---------+------+-------------+---------+---------+
```

Es scheint die "where" Klausel zu sein, die hier Probleme macht. Denn

```sql
select 
  lag(id) over(order by id) as prev_id,
  id, filename, title,
  lead(id) over (order by id) as next_id
from img
```

gibt mir:
```code
+---------+------+-------------+---------+---------+
| prev_id | id   | filename    | title   | next_id |
+---------+------+-------------+---------+---------+
|    NULL |    1 | hund.jpg    | Hund    |       4 |
|       1 |    4 | katze.png   | Katze   |       8 |
|       4 |    8 | maus.gif    | Maus    |       9 |
|       8 |    9 | elefant.jpg | Elefant |      11 |
|       9 |   11 | fliege.jpg  | Fliege  |      20 |
|      11 |   20 | ratte.png   | Ratte   |      33 |
|      20 |   33 | reh.png     | Reh     |     100 |
|      33 |  100 | adler.tiff  | Adler   |    NULL |
+---------+------+-------------+---------+---------+
```

Was richtig ist aber zu viel Informationen erhaelt, ich moechte ja eigentlich nur eine Zeile mit prev und next id.

Ich habe hier und da etwas gegoogelt, das Problem ist nicht so einfach. Vor allem wenn man nicht nur ueber ids sondern zb nach title Reihenfolge sortieren will. Vielleicht koennen sie da nochmal drauf eingehen. So wie in der Folie funktioniert es def. nicht.

Ich habe gerade das Problem, dass ich in einer grossen Buecherdatenbank jeweils 10 Eintraege anzeigen will und dann eben mit einem next Button weitergehen will (fuer ein Projekt in der Vorlesung Micro Services).

Beste Gruesse B.U.



