# TODO
### TODO TODO TODO TODO TODO TODO TODO TODO TODO TODO TODO

- Rechtschreibprüfung
- Absätze richtig gesetzt?
- Vorteile /Nachteile
- Einleitung /Fazit
- Kevins Teil einfügen


<center> <h1> Sharding</h1>
<h2>Erklären Sie unterschiedliche Sharing-Strategien anhand praktischer Beispiele </h2>
<h3>Kevin Berg </h3>
<h3>Jan Olschewski - 233673 </h3> </center>


</br>

## Inhaltsverzeichnis
1. Einleitung
- Vor- und Nachteile von Sharding
- Beispiel: Mongo DB
- Beispiel: MySQL
- Beispiel: Elasticsearch
- Fazit

</br>


## 1. Einleitung
Im letzen Jahrzehnt wurden Begriffe wie Big Data und Skalierung immer wichtiger. Gerade heutige Datenbankinfrastruturen können viele GigaByte bis einige TerraByte an Daten halten. Deshalb mussten die Datenbankserver immer weiter wachsen, was zu immer größeren Kosten führte. Zusätzlich dazu verzoögerten sich Datenbankabfragen immer stärker, da die zu durchsuchenden Datenmengen einfach zu groß wurden. Im Zuge dieser Problematik bekam das Sharding einen immer höheren Stellenwert und hat heutzutage bei fast allen Datenbankimplentationen Einzug gehalten. Doch was ist Sharding und warum stellt es einen geeigneten Weg dar, die Datenbank (vertikal) zu skalieren?

Sharding bezeichnet das horizontale Skalieren einer Datenbank. Dabei werden mehrere Systeme zusammengeschaltet und die Daten diese hinweg verteilt. Die Verteilung erfolgt dabei nicht willkürlich, sondern nach einem bestimmten Prinzip, welches Partitionieren genannt wird. Anhand von Shardingkeys werden die Daten als sogennante Chunks auf den einzelnen Maschinen verteilt. Die Shardingkeys repräsentieren einzelne Felder in den Dokumenten. Um die Dokumente gleichmäßig verteilen zu können, ist darauf zu achten, dass der Shardingkey (Wertebereich eines Datenfeldes der Datenbank) so gewählt wird, dass er viele Variationen zulässt. Alternativ können auch mehrere Felder miteinander kombiniert werden. Je nach festgelegtem Wertebereich der Shardingkeys werden die Daten anhand dessen auf die einzelnen Chunks aufgeteilt. Da die Datenbank für Abfragen aber auch wissen muss, auf welchem Chunk sich, die abzufragenden Daten befinden, gibt es eine spezielle Config-File, in welcher die Sharding-Keys mit zugehörigem Chunk hinterlegt sind. Anhand dessen kann die Datenbank genaue Zuordnungen treffen und Datensätze auch auf verschiedenen Maschinen finden.


## Vor- und Nachteile von Sharding

Vorteile:
Große Datenmengen können über mehrere Server hinweg aufgeteilt werden. Weil Server mit viel Speicherplatz in der Regel eine exponentielle Preissteigerung im Vergleich zu vielen kleinen kostengünstigen Servern besitzen, stellt das Sharding eine gute Möglichkeit dar, die regelmäßigen Serverkosten zu minimieren, da auch große zusammenhängende Daten einer Datenbank auf vielen kleinen Servern verteilt werden können.
Ein weiterer Vorteile ist, dass durch das Sharding ressourcenintensive Schreiboperationen parallelisiert werden können, da jeder Chunk über einen eigenen Server verfügt und somit eigenständig Schreib- und Leseoperationen ausführen kann. Zusätzlich muss durch das Aufteilen der Daten auch eine kleinere Datenmenge pro Server verwaltet werden, was zu kürzeren Antwortzeiten der jeweiligen Server führt.

Nachteile:
Sharding bietet neben seinen Vorteilen auch einige Nachteiel, die es beim Aufsetzen der Datenbank zu beachten gibt. Speziell relationale Datenbanken verfügen in der Regel nicht über eine Auto-Sharding Funktion. Das bedeutet dann auch, dass sich der Aufwand wesentlich erhöht, sollte man den Entschluss fassen eine geshardete Datenbank implementieren zu wollen. Da es ja gewünscht ist, dass die Daten auf die einzelnen Shards gleichmäßig verteilt werden, muss der Datenbank Adminsitrator ein Verfahren finden, welches die gleichmäßige Verteilung und automatische Verwaltung ermöglicht. Wenn dieses gefunden wurde, dann muss darauf geachtet werden, dass ein geeigneter Sharding-Schlüssel gefunden wird. Wenn das nicht der Fall ist und für eine Datenbank-Query vershchiedene Shards durchsucht werdn müssen, dann ist der Performancegewinn schnell wieder verschwunden und ein gegenteiliger Effekt kann eintreten.


## Beispiel Elasticsearch

Elasticsearch ist eine ist eine Client-Server Suchmaschine, welche die Suchergebnisse in einem JSON-Format speichert und diese über RESTful-Webservice ausgibt. Daher handelt es sich bei der Datenbank, welche sich im Kern von Elasticsearch befindet um einen dokumentenbasierten Speicher. Innerhalb der Elasticsearch-Infrastruktur, dem sog. Cluster,  werden die Daten dann in Indizes gespeichert. Ein Index repräsentiert somit die Datenbank, welcher Typen enthält. Die werden von der Such-Engine wie Tabellen in einer Datenbank behandelt. In diesen Typen befinden sich dann die Dokumente mit Properties und Attributen (als Key-Value Paar)
Die RESTful API, welche Elasticsearch zu Client-Server-Kommunikation nutzt, ist ein Konzept welches Eigenschaften wie Zustandslosigkeit, Addressierbarkeit und festgelegte Operationen für entsprechende Dienste vorschreibt [ICS-2000]. Diese Dienste kommunizieren über das HTTP-Protokoll. Da jede Suchanfrage als URL formuliert wird, ist sie auch stets einzigartig und eindeutig adressierbar.

Durch die Eigenschaft von Elasticsearch als Volltext-Suchamschine mit einer NoSQL-Datenbank im Kern, ist es potentiell möglich, dass auch sehr viele Daten aufgenommen werden. Mehrere Milliarden Dokumente in einem Index führen auch zu mehreren TerraByte an benötigtem Festplattenspeicher. Dann ist es wahrscheinlich, dass die Server-Festplatte zu klein oder langsam ist, viele Suchrequests auf einem Index zu verarbeiten

Um ein genaues Verständnis dafür zu bekommen, wie Elasticsearch das Sharding vollkommen selbständig übernimmt, ist es notwendig einige Basiskonzepte von Elasticsearch zu erkennen.
Vorangegangen wurde ja bereits der Begriff Cluster erwähnt. Doch was zeichnet einen Cluster in Elasticsearch aus? Ein Cluster ist eine Collection von ein oder mehreren Nodes, welche die gesamten Daten eines oder mehrerer Indizes beinhaltet und verwaltet. Außerdem stellt er die "Suchbarkeit" über alle Nodes innerhalb des CLusters bereit. So ist es zum Beispiel möglich, mit entsprechnder Konfiguration, auf mehreren Indizes gleichzeitig zu suchen. In der Regel befindet sich in einem Cluster ein Node mit einem Index. Lediglich in großen Datenstrukturen macht es Sinn mehreren Nodes innerhalb eines Clusters zu haben. Dies ist immer dann besonders sinvoll, wenn man über mehrere Server verfügt, die dann als Gesamtheit einen Cluster blden. Ein Cluster wird üblciherweise durch seinen Namen identifiziert. Standardmäßig ist das "elasticsearch".

Wie bereits erwähnt repräsentiert eine Node immer einen Server, genauer gesagt eine Elasticsearch-Instanz auf einem Server. Sie enthält Daten (als Documents) innerhalb eines Index' und trägt ihren Teil zur Indexierung und Suchfunktionalität in dem Cluster bei. Wie ein Cluster verfügt auch eine Node über einen eindeutigen Namen. Dieser wird beim Start der Instanz zufallsgeneriert und der Node zugewiesen. Auch dieser Name kann durch den Nutzer geändert werden. Beim Erzeugen der Node/Instanz erstellen sie aut0matisch einen Cluster "elasticsearch" oder treten diesem bei, sofern er schon existiert. Somit lassen sich sehr leicht beliebig viele Nodes in einen Cluster bringen.

Wie bereits beschrieben repräsentiert der Index die Datenbank. Es ist möglich innerhalb einen Clusters beliebig viele Inidzes erstellen lassen. Diese werden dann durch das Sharding "aufgetrennt" und über mehrere Nodes verteilt

Zu einem weiteren Basiskonzept von Elasticsearch gehört das bereits erwähnte Sharding. Elasticsearch wurde entwickelt, um große Datenmengen zu halten und diese in nahezu Echtzeit durchsuchbar zu machen. Deshalb ist es wichtig, ein Verfahren zu entwickeln, welches auch berücksichtigt, dass Datenmengen von bis zu einem TerraByte nicht auf eine einzelne Festplatte eines Servers passen - das entspricht dann einer Node. Auch wenn eine Festplatte in der Lage ist, solche Datenmengen zu halten, so kommt auch die eigentlich sehr performante Such-Engine Elasticsearch an seine Grenzen, wenn es darum geht Suchrequests in Echtzeit zu beantworten. Hierzu wird der Index in einzelne Stücke, die sogenanten Shards, aufgeteilt welche alleine als unabhängige und voll-funktionsfähige Indizes gesehen werden können. Diese Shards könenn dann auf verschiedenen Nodes arbeiten und somit die erwähnten Nachteile ausgleichen.

Beim Hinzufügen von Daten werden die Shards gleichmäßig befüllt, sodass Datenlast stets fair verteilt bleibt. Elasticsearch erstellt die Shards innerhalb des Clusters standardmäßig und vollautomatisch, um die vertikale Skalierbarkeit zu gewährleisten und die Suche performant zu halten. Zusätlich wird zu jedem Shard ein Backup angelegt. Sollte ein Shard unerwartet nicht mehr lesbar oder verfügbar sein, kann auf das Replika zurückgeriffen werden. Das Replika jedes Shards befindet sich niemals auf der selben Node, um die ständige Verfügbarkeit zu gewährleisten.

Außer der Konfiguration von Shard- und Replika-anzahl muss der Nutzer keine Einstellungen für das Sharding vornehmen. Wie bereits erwähnt muss sich der Nutzer nicht darum kümmern, wie die Shards verteilt sind und wie sie zusammengenommen wieder einen befüllten Index ergeben. Trotzdem gibt es die Möglichkeit die Shards genauer zu unteruschen. Das folgende Beispiel soll das Erstellen eines Index mit benutzerdiefierten Shards verdeutlichen und zeigen, dass die Daten gleichmäßig auf die Shards verteilt werden.

Ein Beispiel zeigt, wie sich dieses Prinzip in der Praxis verhält:
Für die Elasticsearch-Suchengine werden zwei identische Server gemietet, welche die Suchanfragen verarbeiten sollen. Auf diesen läuft dann jeweils eine Elasticsearch-Instanz (Node). Wenn ein Server aufgrund von Stromausfall oder ähnlichen Problemen nicht erreichbar ist, kann die Suche immer noch auf der anderen Maschine erfolgen. Die Server teilen sich also normalerweise die Arbeit, wenn es aber zu Problemen eine Servers kommt, so „springt“ der andere für ihn ein, um die komplette Funktionalität weiter zu gewährleisten.

<center>![alt text](./img/Cluster.png "Elasticsearch Cluster")</center>
<center><p style="font-size:12px" >Infrastruktur eines Clusters mit zwei Server in Elasticsearch</center>

Die Daten des Index’ werden durch Elastiscsearch wie gesagt in 5 Shards mit jeweils einem Replika aufgeteilt. Für eine Infrastruktur mit zwei Nodes kann die Aufteilung dann folgendermaßen aussehen:

| Node-Shard   | Shard      | Shard-Replika  |
| -------------|:----------:| --------------:|
| 1            | Server 1   | Server 2       |
| 2            | Server 1   | Server 2       |
| 3            | Server 1   | Server 2       |
| 4            | Server 2   | Server 1       |
| 5            | Server 2   | Server 1       |

Besitzt das Cluster dann noch mehrere Indizes, werden diese wiederum in neue Shards aufgeteilt, welche dann völlig anders auf dem Server verteilt werden können.
Folgendes Schaubild zeigt die Verteilung von zwei Indizes innerhalb der Beispiel-Topologie:

<center>![alt text](./img/sharding-es.png "Elasticsearch Cluster")</center>
<center><p style="font-size:12px" >Sharding mehrerer Indizes auf zwei Servern</center>

Dieses Besipiel verdeutlicht, wie Elasticsearch das Sharding selbständig ausführt. Doch wie gewährleistet Elasticsearch die vertikale Skalierbarkeit? Darüber soll folgendes Beispiel Aufschluss geben:

Im ersten Schritt wird ein neuer Index auf dem gestarteten Elasticsearch-Server angelegt. Nun besitzen wir einen Index auf einer Node, welche sich in einem Cluster befindet. Eine Erstellung meherer Nodes, welche dann über eigenständige Shards verfügen ist ebenfalls möglich, soll in diesem Beispiel aber nicht weiter vertieft werden.

Der Index auf dem Node verfügt immer über das Standard-Setting mit 5 Shards und einem Replika pro Shard.

Das Anlegen erfolgt indem ein HTTP-POST auf den Elasticsearch-Server mit entsprechenden Document angelegt wird. Sollte es den Index noch nicht geben, wird er gleich mit erstellt.
```javascript
POST /books/book/1
{
  "title" : "Elefant",
  "author": "Martin Suter"
}
```
Nun verfügen wir über ein Dokument in dem Type „book“ (analog zum Table in SQL) auf dem Index „books“.
Der Response gibt Aufschluss darüber, dass das Document erfolgreich angelegt wurde:

```javascript
{
   "_index": "books",
   "_type": "book",
   "_id": "1",
   "_version": 1,
   "created": true
}
```

Ein HTTP-GET auf die Settings des Index' liefert nun folgenden Response:
```javascript
GET /books/_settings
{
   "books": {
      "settings": {
         "index": {
            "number_of_shards": "5",
            "number_of_replicas": "1",
            "version": {
               "created": "1030499"
            },
            "uuid": "71AAXyRPTBKhJWrTy4vp-w"
         }
      }
   }
}
```
Es ist zu erkennen, dass Elasticsearch einen Cluster mit 5 Shards angelegt hat. Um mehr oder weniger Shards zur Verfügung zu stellen muss ein neuer Index angelegt werden. Dazu wird im ersten Schritt ein PUT auf den neuen Index mit den folgenden Settings ausgeführt.
```javascript
PUT /books_less_shards/
{
    "settings" : {
        "number_of_shards" : 3,
        "number_of_replicas" : 2
    }
}
```
Der GET-Request auf die Settings des neu angelegten Index bestätigt die neue Konfiguration
```javascript
GET /books_less_shards/_settings
{
  "books_less_shards": {
     "settings": {
        "index": {
           "number_of_shards": "3",
           "number_of_replicas": "2",
           "version": {
              "created": "1030499"
           },
           "uuid": "vQT1cqNAQWS9t3Yim8Ailw"
        }
     }
  }
}
```
Nun können Documents hinzugefügt werden, die sich selbstständig auf die einzelnen Shards aufteilen. Es wurden insgesamt 53 Documents vom Type book hinzugefügt
```javascript
GET /books_less_shards/_search
{
   "took": 12,
   "timed_out": false,
   "_shards": {
      "total": 3,
      "successful": 3,
      "failed": 0
   },
   "hits": {
      "total": 121,
      "max_score": 1,
      "hits": [
         {
           ...
         }
      ]
    }
}
```
Der Response zeigt an, dass sich nun 4 Documents im Index mit 3 Shards befinden.

Um die Aufteilung auf die einzelnen Shards genauer zu untersuchen kann folgender Befehl Aufschluss geben,:

```javascript
GET /_cat/shards/books_less_shards
books_less_shards 2 p STARTED    38  8.4kb 127.0.0.1 Acrobat
books_less_shards 2 r UNASSIGNED                             
books_less_shards 1 p STARTED    42 14.6kb 127.0.0.1 Acrobat
books_less_shards 1 r UNASSIGNED                             
books_less_shards 0 p STARTED    41 23.7kb 127.0.0.1 Acrobat
books_less_shards 0 r UNASSIGNED                             


```

Diese Übersicht gibt verschiedene Informationen.
- Spalte 1: der Index, auf dem der Shard gefunden wurde.
- Spalte 2: für die Shard-Nummer auf der entsprechenden Node
- Spalte 3: steht für primary (p) oder replica (r)
- Spalte 4: Zustand des Shards. Die Replika-Shards wurden auf dieser Node bisher nicht zugewiesen
- Spalte 5: Anzahl der Documents auf dem Shard
- Spalte 6: Größe des Shards (steigt mit zunehmender Anzahl an Documents)
- Spalte 7: IP-Adresse
- Spalte 8: Node auf dem der Shard liegt.

Zum Einen ist zu erkennen, dass 6 Shards existieren - drei Shards mit je einem Replika. Außerdem fällt auf, dass die Shard sehr gleichmäßig verteilt sind. Elasticsearch gewährleistet somit, dass auch die Lastverteiliung bei mehreren Queries stets ausgeglichen bleibt. In diesem Beispiel liegen alle Shards auf einer Node, wleche sich auf einem Server befindet. Wie jedoch bereits erwähnt, ermöglicht Elasticsearch es ebenfalls Shards auf unterschiedlichen Nodes zu haben, welche gemeinsam den Index bilden.

Außerdem ist zu erkennen, dass die Replika-Shards keine Daten enthalten. Damit ist jedoch die doppelte Datenhaltung nicht gewährleistet. Der Grund hierfür ist, dass der Replika-Shard niemals auf der gleichen Node wie der Prmär-Shard liegen darf. Wie bereits erwähnt dient das der Ausfallsicherheit, sollte ein Node nicht verfügbar sein.
Deshalb werden zwei weitere Elasticsearch-Instanzen innerhalb des Clusters gestartet.

Nun ist zu erkennen, dass auch die Replika-Shards auf den anderen Nodes zugewiesen wurden:
```javascript
GET /_cat/shards/books_less_shards
books_less_shards 1 r STARTED 42 14.6kb 127.0.0.1 Zombie  
books_less_shards 1 p STARTED 42 14.6kb 127.0.0.1 Jekyll  
books_less_shards 2 r STARTED 38  8.4kb 127.0.0.1 Zombie  
books_less_shards 2 p STARTED 38  8.4kb 127.0.0.1 Acrobat
books_less_shards 0 r STARTED 41 23.7kb 127.0.0.1 Jekyll  
books_less_shards 0 p STARTED 41 23.7kb 127.0.0.1 Acrobat

```

Um zu verdeutlichen, dass Elasticsearch sehr flexibel in der Verwaltung von Shards ist, sollen nun einzelne Shards auf andere Nodes verschoben werden. Hierfür stellt ELasticsearch den Reroute Befehl bereit:
```javascript
POST /_cluster/reroute
{
    "commands" : [ {
        "move" :
            {
              "index" : "books_less_shards", "shard" : 0,
              "from_node" : "Acrobat", "to_node" : "Zombie"
            }
        }
    ]
}

GET /_cat/shards/books_less_shards
books_less_shards 2 r STARTED 38  8.4kb 127.0.0.1 Zombie  
books_less_shards 2 p STARTED 38  8.4kb 127.0.0.1 Acrobat
books_less_shards 1 p STARTED 42 14.6kb 127.0.0.1 Jekyll  
books_less_shards 1 r STARTED 42 14.6kb 127.0.0.1 Acrobat
books_less_shards 0 p STARTED 41 23.7kb 127.0.0.1 Zombie  
books_less_shards 0 r STARTED 41 23.7kb 127.0.0.1 Jekyll  

```
Der Shard 0 wurde von der Acrobat-Node auf die Zombie-Node verschoben. Der Replika Shard wurde davon nicht beeinflusst.

Somit wurde verdeutlicht, dass es trotz der automatisierten Verwaltung der Shards durch Elasticsearch möglich ist die Shards auf den Nodes individuell zu verteilen. Das ist beispielsweise besonders hilfreich, wenn die Server unterschiedlich stark sind und somit auch unterschiedlich viele Shards enthalten sollten. 


## Fazit
Shards lassen sich mittlerweile auf fast allen Datenbanktypen einrichten. Gerade hinsichtlich Kosteneffizienz und kurzer Antwortzeiten bringt es einen enormen Vorteil gegenüber ungeshardeter Datenbanken. Jedoch stellt das Einrichten von Shards einen zusätzlichen Aufwand dar, welcher sich dann wieder in Kosten niederschlägt. Jedoch verfügen NoSQL Implementationen, wie MongoDB oder die Such-Engine Elasticsearch über Mechanismen, welche einem die aufwändige Einrichtung und Verwaltung der Shards abnehmen.
