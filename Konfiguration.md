# Konfiguration von homebridge-knx ab Version 0.3.0

## Dateibasierte Konfiguration
Die Konfiguration von homebridge-knx findet ausschließlich über die Veränderung der Datei knx_config.json statt. Das bedeutet, dass alle Geräte, Dienste, Eigenschaften die Eure Geräte haben sollen, in diese Datei eingetragen werden müssen.  

Dabei gilt es zu berücksichtigen, dass 
-  homebridge-knx in die Konfigurationsdatei zurückschreibt
-  homebridge bestimmte Informationen cached

Das bedeutet 
-  wenn wir die knx_config.json ändern wollen, so müssen wir Datei verwenden, die auch homebridge-knx verwendet. D.h. wenn unsere homebridge auf einem Raspberry Pi oder in einer VM "headless", d.h. ohne Display, läuft, müssen wir die Datei mit sftp oder anderen Dateiübertragungsmethoden auf unseren PC laden (Ich schreibe hier immer PC, auch wenn es ein Mac sein kann!)
-  wenn wir bestimmte Konfigurationen ändern oder löschen, behält homebridge einen alten Stand im Cache. Diesen müssen wir dann löschen

###JSON
Das Dateiformat JSON (JavaScript Object Notation) ist etwas speziell - und es verzeiht keine Fehler. Empfohlen ist daher, jede Änderung vor dem Upload zum homebridge-Server auf der Website http://jsonlint.com überprüfen zu lassen. Alternativ gibt es auch json-plugins für verschiedene Editoren, z.B. für notepad++, oder für die Entwicklungsumgebung eclipse.  

Ganz wichtig und unbedingt immer ganz am Anfang der Konfigurationstätigkeiten zu erledigen: Die Zeichensatzeinstellungen für alle Konfigurationsdateien ist UTF-8. Windows verwendet gerne etwas anderes, das führt spätestens beim ersten Umlaut zur Katastrophe.

Von der Verwendung von Windows Standard-Editor `notepad`, oder gar MS Word, rate ich dringend ab. Bei diesen Editoren kann man entweder den Zeichensatz nicht einstellen und/oder es werden automatische Formatierungen oder Zeichenersetzungen vorgenommen. Die Issue-Liste von homebridge ist voll von Einträgen, in denen sich Nutzer wundern, warum ihre homebridge sich beim starten beschwert, obwohl doch "alles richtig" sei - und dann zeigen sie Ihre config.json, in der der Editor alle "geraden" Anführungszeichen durch sogenannte typographische Anführungszeichen („deutsch“, “English”, « français ») ersetzt hat. Wenn wir Menschen an dieser Stelle sofort sehen was gemeint war - JavaScript ist an dieser Stelle sehr pedantisch, eine `"bridge"` ist halt nicht das selbe wie `“bridge”` auch wenn es fast gleich aussieht.  
Wenn homebridge nach einer Änderung an den Konfigurationsdateien nicht erreichbar ist, unbedigt das Log checken ob dort eine Ausnahme (exception) angezeigt wird - dann ist in der Regel ein Fehler im JSON.

Häufigste Fehler, die mir alle schon passiert sind:
-  Klammern vergessen, oder in falscher Reihenfolge
-  Anführungszeichen um Schlüsselnamen vergessen
-  Anführungszeichen nicht wieder geschlossen
-  Komma zu viel oder zu wenig (passiert schnell beim Kopieren von Teilen der Datei)

Sie auch meine [kurze Einführung in JSON!](JSON%20f)

  

## config.json

Die Datei config.json ist die zentrale Konfigurationsdatei für `homebridge` (ohne -knx). Hier werden zentrale Einstellungen für homebridge selbst vorgenommen. Einige Plugins werden auch hier konfiguriert (**alte** homebridge-knx bis Version 0.2.8 ebenfalls).

### `bridge` Konfiguration

Homebridge erfordert einige wenige Einträge in der config.json, die wir in der Regel unverändert übernehmen können:

```json
	"bridge": {
		"name": "Homebridge Plugin",
		"username": "BC:22:3D:E3:CE:28",
		"port": 51826,
		"pin": "031-45-154"
	},
```
Dabei bedeutet 

## knx_config.json

Wie obenerwähnt, ist die knx_config.json die zentrale Konfigurationsdatei für **homebridge-knx**. Bei Installation nach dieser Anleitung (LINK) auf einem Raspberry Pi gehört diese Datei in den Pfad `\home\pi\.homebridge` 