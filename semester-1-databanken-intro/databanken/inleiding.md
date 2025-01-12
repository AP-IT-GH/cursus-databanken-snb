# Inleiding

{% hint style="success" %}
[Kennisclip voor deze inhoud](https://youtu.be/3J-dNJzip2Q)
{% endhint %}

Een database (of gegevensbank) is een gestructureerde verzameling elektronische gegevens (of data) die door één of meerdere gebruikers (of users) gelijktijdig kunnen gemanipuleerd worden. De enorme hoeveelheden gegevens van banken worden beheerd in ultramoderne databases.

De database zelf, dat zijn dus de geordende gegevens. Maar gegevens zijn pas waardevol als er iets mee gedaan wordt. Het is de taak van een database management system (DBMS) om gegevens op te vragen, aan te passen, enzovoort.

Het DBMS moet er bovendien over waken dat de integriteit van de database behouden blijft. "Waken over integriteit" betekent bijvoorbeeld dat het juiste telefoonnummer bij de juiste persoon blijft horen, dat er geen personen zonder adres in het systeem geplaatst worden,...

Maar het DBMS doet veel meer dan alleen waken over integriteit. Zonder DBMS is het onmogelijk gegevens in een database onder andere te bekijken, te wijzigen, te verwijderen... Alleen het DBMS weet waar en hoe de gegevens in de database verwerkt moeten worden.

Een DBMS zal uiteraard nooit uit zichzelf de gegevens in een database opzoeken, wijzigen of verwijderen. Om dit te doen is steeds een specifieke opdracht van de gebruiker nodig. Bijvoorbeeld: "Verander het telefoonnummer van Jan Claessens van (02) 568 95 65 in (03) 574 23 84".

Dit soort instructie wordt echter niet in een natuurlijke taal gegeven. De meeste DBMS beschikken over een eigen taal voor instructies. Die taal heeft een specifieke "syntax", een stel regels om instructies te ontleden in hun bestanddelen. Deze regels gelijken een beetje op de regels die wij hanteren wanneer we Nederlands spreken. "Ga op die bank zitten" is een verstaanbare opdracht. We kunnen zeggen wat de taak is van elk woordje in deze Nederlandse zin. "Bank daar zitten ga op" is geen geldige zin. We kunnen als mensen nog wel raden naar de betekenis van deze zin, maar hij volgt de regels van de taal niet. Een machine zou het dan ook veel moeilijker hebben om er iets van te maken.

De regels van de talen waarmee men opdrachten kan geven aan een DBMS zijn dus veel strikter dan de regels voor het Nederlands: ze laten geen ruimte voor fouten. Een komma verkeerd volstaat om de opdracht onverstaanbaar en dus onuitvoerbaar te maken.
