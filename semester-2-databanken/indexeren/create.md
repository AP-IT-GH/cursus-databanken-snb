# CREATE

## Standaardgebruik

Een index toevoegen doe je in het algemene geval met volgende syntax:

```sql
CREATE INDEX Column1Column2Index on TableName(Column1,Column2);
```

Dit maakt een index op de combinatie van de kolommen `Column1` en `Column2`. Als er maar één kolom is, laat je de tweede gewoon achterwege. Meer dan twee kolommen kan ook.

De volgorde is hier van belang. Het telefoonboek is bijvoorbeeld een soort index op gemeente, familienaam en voornaam. Je kan het alleen maar efficiënt gebruiken als je de eerste kolom kent en het wordt efficiënter naarmate je meer kolommen kent.

## Prefix index

Wanneer je een secundaire index voor een kolom maakt, dus een index die niet over de primaire sleutel gaat, slaat MySQL de waarden van de kolommen op in een afzonderlijke gegevensstructuur. Dit zijn structuren waarin je snel kan opzoeken.

In het geval dat de kolommen de tekenreekskolommen zijn (zoals `VARCHAR`), zal de index veel schijfruimte in beslag nemen en mogelijk de INSERT-bewerkingen vertragen.

Om dit probleem aan te pakken, kan je met MySQL een index maken voor het leidende deel van de kolomwaarden van de tekenreekskolommen met behulp van de volgende syntax:

```sql
column_name(length)
```

Je kan een index op twee manieren creëren.

**1. bij creatie van de tabel zelf**

```sql
-- mogen meerdere colum_names zijn, gescheiden door komma
-- length is optioneel
CREATE TABLE Table_name(
column_list,
INDEX(column_name(length))
);
```

**2. nadien toevoegen aan een bestaande tabel**

```sql
-- zelfde opmerking als boven
-- je mag een index op meerdere kolommen samen maken
CREATE INDEX index_name
ON table_name(column_name(length));
```

In bovenstaande voorbeelden is de lengte het aantal tekens voor de datatypes zoals VARCHAR (en het aantal bytes voor binaire datatypes).

## Voorbeeld

**Taken**

| omschrijving         | Id   |
| -------------------- | ---- |
| bestek voorzien      | 1    |
| frisdrank meebrengen | 2    |
| aardappelsla maken   | 3    |
| ...                  | ...  |
| pasta koken          | 4000 |

Als we onderstaande query uitvoeren, dan zal heel de tabel worden doorlopen omdat er geen index is bepaald op het veld waarop het zoekcriterium werd ingesteld.

```sql
USE ApDB;
SELECT *
FROM Taken
WHERE Omschrijving LIKE 'aardappel%';
```

Als we deze query met het `EXPLAIN` statement uitvoeren, krijgen we volgende resultaat.

```sql
USE ApDB;
EXPLAIN SELECT *
FROM Taken
WHERE Omschrijving LIKE 'aardappel%';
```

Als je de tabel taken veel bevraagt terwijl je filtert op de omschrijving, is het zinvol om voor deze kolom een index te creëren waardoor de uitvoering een heel stuk sneller zal verlopen.

De grootte van de kolom `Omschrijving` is tijdens het ontwerp ingesteld op 50 karakters, maar dat wil niet zeggen dat we in de index 50 karakters per mogelijkheid moeten bijhouden. Voor de index moet je de lengte van het voorvoegsel bepalen. Soms wordt er gezegd dat je dit zo efficiënt mogelijk dient te doen door de prefixlengte zo kort mogelijk te houden. Hier schuilt wel een gevaar in: wanneer de tabel met nieuwe data wordt uitgebreid, is de index misschien niet meer zo uniek. Dat leidt niet tot fouten maar mogelijk wel tot performantieverlies.

Hoe zoek je nu de ideale lengte van de prefix op? Een vuistregel: zorg dat de index meteen naar een uniek resultaat leidt, maar dat hij niet groter is dan nodig om dit te bereiken.

**Stap 1**: zoek het aantal verschillende waarden in de kolom op

```sql
select count(distinct Omschrijving)
from Taken;
```

**Stap 2**: bepaal de maximale lengte van een waarde in de kolom

```sql
select max(length(Omschrijving)) from Taken;
```

**Stap 3**: zoek de minimale lengte waarbij je geen informatie verliest. Dit kan nooit meer zijn dan het resultaat van stap 2. Als de langste kolomwaarde bijvoorbeeld 21 is, start je door te kijken of 20 karakters al dan niet zorgen voor informatieverlies. Via `LEFT` krijg je alleen de eerste (aantal) tekens uit een string.

```sql
select count(distinct left(Omschrijving,20))
from Taken;
```

Zodra er minder resultaten zijn, heb je de prefix te kort gekozen. Het antwoord is dus één meer dan de lengte waarbij het aantal resultaten begint te krimpen.

Indien 20 de perfecte lengte van de prefix is, dan gaan we de index opbouwen.

```sql
USE ApDB;
CREATE INDEX OmschrijvingIdx
ON Taken(Omschrijving(20));
```

Binnen de schema navigation kan je nu de index zien. Als je nu bovenstaande select-query opnieuw uitvoert zal deze efficiënter en sneller verlopen.

{% hint style="warning" %}
De lengte van het "ideale" prefix kan wijzigen naarmate je meer data toevoegt aan een tabel.
{% endhint %}

{% hint style="danger" %}
Zelfs als je de ideale prefixlengte gebruikt, houdt het gebruik van een prefix gevolgen in. MySQL kan bij gebruik van een prefix niet garanderen dat twee waarden op dezelfde plaats in de index identiek zijn. Sommige queries kunnen hierdoor trager lopen bij een prefixindex dan bij een index zonder prefix.
{% endhint %}

## De rol van kolomvolgorde

Als je een index op meerdere kolommen aanmaakt, is de volgorde van deze kolommen belangrijk. Vergelijk met het telefoonboek. Daar heb je een index op plaatsnaam en familienaam. Dit staat je toe snel iemand terug te vinden. Als je geen informatie kent over hoe de plaatsnamen geordend zijn (stel je voor dat alle gemeentes willekeurig in het telefoonboek stonden in plaats van alfabetisch), moet je toch elke pagina doorzoeken om zeker te zijn dat je de persoon in kwestie niet over het hoofd ziet.

Wat betekent dit in de praktijk? Je kan een index op meerdere kolommen (bijvoorbeeld A, B en C) gebruiken om snel opzoekingen te doen als je werkt met een doorlopende reeks van de eerste kolommen (bv. A of A en B of A, B en C). Maar je kan niet sneller zoeken met deze index als je bijvoorbeeld enkel B en C betrekt in je zoekopdracht.
