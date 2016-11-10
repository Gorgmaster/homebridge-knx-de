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
Das Dateiformat [JSON (JavaScript Object Notation)]](https://de.m.wikipedia.org/wiki/JavaScript_Object_Notation) ist etwas speziell - und es verzeiht keine Fehler. Empfohlen ist daher, jede Änderung vor dem Upload zum homebridge-Server auf der Website http://jsonlint.com überprüfen zu lassen. Alternativ gibt es auch json-plugins für verschiedene Editoren, z.B. für notepad++, oder für die Entwicklungsumgebung eclipse.  

Ganz wichtig und unbedingt immer ganz am Anfang der Konfigurationstätigkeiten zu erledigen: Die Zeichensatzeinstellungen für alle Konfigurationsdateien ist UTF-8. Windows verwendet gerne etwas anderes, das führt spätestens beim ersten Umlaut zur Katastrophe.

Von der Verwendung von Windows Standard-Editor `notepad`, oder gar MS Word, rate ich dringend ab. Bei diesen Editoren kann man entweder den Zeichensatz nicht einstellen und/oder es werden automatische Formatierungen oder Zeichenersetzungen vorgenommen. Die Issue-Liste von homebridge ist voll von Einträgen, in denen sich Nutzer wundern, warum ihre homebridge sich beim starten beschwert, obwohl doch "alles richtig" sei - und dann zeigen sie Ihre config.json, in der der Editor alle "geraden" Anführungszeichen durch sogenannte typographische Anführungszeichen („deutsch“, “English”, « français ») ersetzt hat. Wenn wir Menschen an dieser Stelle sofort sehen was gemeint war - JavaScript ist an dieser Stelle sehr pedantisch, eine `"bridge"` ist halt nicht das selbe wie `“bridge”` auch wenn es fast gleich aussieht.  
Wenn homebridge nach einer Änderung an den Konfigurationsdateien nicht erreichbar ist, unbedigt das Log checken ob dort eine Ausnahme (exception) angezeigt wird - dann ist in der Regel ein Fehler im JSON.

Häufigste Fehler, die mir alle schon passiert sind:
-  Klammern vergessen, oder in falscher Reihenfolge
-  Anführungszeichen um Schlüsselnamen vergessen
-  Anführungszeichen nicht wieder geschlossen
-  Komma zu viel oder zu wenig (passiert schnell beim Kopieren von Teilen der Datei)

Sie auch meine [kurze Einführung in JSON!](JSON%20f%C3%BCr%20Anf%C3%A4nger.md)

  

## config.json

Die Datei config.json ist die zentrale Konfigurationsdatei für `homebridge` (ohne -knx). Hier werden zentrale Einstellungen für homebridge selbst vorgenommen. Einige Plugins werden auch hier konfiguriert (**alte** homebridge-knx bis Version 0.2.8 ebenfalls).

### `bridge` Konfiguration

Homebridge erfordert einige wenige Einträge in der config.json, die wir in der Regel unverändert übernehmen können:

```json
	"bridge": {
		"name": "Homebridge",
		"username": "BC:22:3D:E3:CE:30",
		"port": 51826,
		"pin": "031-45-154"
	},
```
Dabei bedeutet  
`bridge`: ist das Objekt das homebridge für die Konfiguration der HomeKit-Brücke verwendet.  
`username`: ist eine (im lokalen Netzwerk zwingend eindeutige) ID für die Brücke. So merkt sich HomeKit, ob es die Brücke (allgemein: das physische Gerät) kennt und damit gekoppelt ist.  
`port`: Auf welchem Port die Brücke http-Anfragen beantworten kann. Ich empfehle den Standard `51826` nicht zu ändern.
`pin`:  Eine Geheimzahl, die man beim Koppeln (engl. Pairing) der Brücke mit HomeKit auf dem iPhone oder iPad angeben muss. Apple hat leider eine unveröffentlichte Liste an Komplexitätskriterien, die so eine Pin erfüllen muss. Bei Änderung kann es also passieren, dass HoemKit die Brücke nicht mehr akzeptiert! 123-45-678 geht definitiv nicht!

Unter dem Namen KNX-sample-config.json liegt die folgende Beispieldatei im Repository homebridge-knx.  
```json
{
	"bridge": {
		"name": "Homebridge",
		"username": "CC:22:3D:E3:CE:30",
		"port": 51826,
		"pin": "031-45-154"
	},
	"description": "This is an example configuration file for KNX platform shim",
	"platforms": [
	],
	"accessories": [
	]
}
```
Mehr als diese paar Zeilen braucht homebridge für die Version 0.3.x von homebridge-knx nicht. Bei richtiger Installation findet homebridge das plug-in von allein und `homebridge-knx` benötigt die Daten in der getrennten Datei `knx_config.json`

## knx_config.json

Wie obenerwähnt, ist die knx_config.json die zentrale Konfigurationsdatei für **homebridge-knx**. Bei Installation [nach dieser Anleitung](Installation.md) auf einem Raspberry Pi gehört diese Datei ebenfalls in den Pfad `\home\pi\.homebridge`  

### Allgemeine Struktur
homebridge-knx erwartet, dass die `knx_config.json` folgender Struktur folgt:

```
{
    "knxd_ip": "192.168.1.1",
    "knxd_port": 6720,
    "AllowWebserver": true,
    "AllowKillHomebridge": false,
    "Devices": [    ]
}
```
`"knxd_ip":"192.168.1.1"` muss auf die IP-Adresse der lokalen [knxd](https://github.com/knxd/knxd) Installation zeigen, und `"port":6720` den Port widerspiegeln (6720 ist Standard).  
`platform` und `name` könnten da noch drinstehen, wenn der Abschnitt aus einer alten Vorlage kopiert wurde. Diese Einträge sind nicht mehr relevant.  
`AllowWebserver`: Wenn auf wahr (`true`) gestellt, erlaubt ihr homebridge-knx einen kleinen Webserver zu starten, mit dem ihr auf Port `18081` und Pfad `/list` Geräte aus dem Cache (und der laufenden homebridge) entfernen könnt. Sinnvoll, falls Ihr Geräte so geändert habt, dass homebridge mit dem Cache und der knx_config.json durcheinander kommt, oder Ihr Geräte entfernen wollt.  
Für Produktiven Betrieb nicht geeignet, da so jeder mit Zugriff auf Euer LAN einfach Geräte aus der homebridge löschen könnte. Die Geräte werden dabei **nicht** aus der knx_config.json gelöscht, nach einem Neustart der homebridge wird die Konfiguration aus der Datei wieder geladen.
`AllowKillHomebridge`: Erweiterung zu `AllowWebserver`: Auf der Webseite wird zusätzlich ein Link angezeigt, mit dem die homebridge gezielt abgeschossen werden kann. Dazu erzeugt homebridge-knx bewusst einen Fehler namens *Committed_Suicide*  (Selbstmord begangen), der dazu führt, dass homebridge beendet wird.  
In einer Installation, die bei einem Fehler automatisch neu startet, bedeutet dies, das man über diesen Link z.B. nach Erweiterungen in der knx_config.json die homebridge neu starten kann.  

`Devices` ist eine Liste (*Array*) von Objekten des Typs HomeKit [*Accessory*](Raeume%20etc.md#accessory) 

###Devices
`Devices` müssen einen eindeutigen `DeviceName` haben. Wenn der Name nach der Koppelung mit HomeKit in der Datei geändert wird, wird diese Änderung nicht mehr nach HomeKit transportiert - Der Name bleibt also in Homekit solange bestehen, wie das Gerät in HomeKit existiert. Um Änderungen trotzdem transportieren zu können, muss das Gerät mit Hilfe des in homebridge-knx integrierten Webservers gelöscht werden.  

Jedes *Device* braucht eine Liste namens `Services`. In diesen Services werden die Funktionen des Geräts definiert.

###Services

`Services` ist ein Array (Liste) von Objekten, die jedes einen HomeKit [*service*](Raeume%20etc.md#service) darstellen.  
Jeder Service **muss** mindestens haben:
- einen eindeutigen Dienst-Namen `ServiceName` (für Namensänderungen gilt das gleiche wie bei Geräten, siehe [Devices](#devices)). Siri hört auf die Dienstnamen, sofern es sich um einen von Siri unterstützten Service handelt (Licht an/aus, Helligkeit, Farbe; Jalousie öffnen/schließen; Thermostat Ist-Temperatur; Schalter an/aus; Garagentor öffnen/schließen, Tür/Fenster öffnen/schließen - mehr sind derzeit nicht bestätigt)
- einen *HomeKit* `ServiceType` aus der [Liste von khaosT](https://github.com/KhaosT/HAP-NodeJS/blob/master/lib/gen/HomeKitTypes.js) - Das ist eine automatisch generierte Liste mit JavaScript-Definitionen aller Services und CHaracteristics.
- einen `Characteristics` Abschnitt, der alle Variablen (Eigenschaften) auflistet, die von HomeKit verwaltet werden sollen.  
    
Optional **darf** ein Service noch haben:  
- einen `Handler` Eintrag, der ein Add-in benennt, dass alle Aktionen für diesen Service übernehmen soll.  
- eine `KNXObjects` Liste (Array), die KNX Gruppen Adressen enthält, die von dem Add-in verwendet werden aber nicht mit einem HomeKit-Characteristic direkt verbunden sind.  
- eine `KNXReadRequests` Liste von Gruppenadressen, die beim Start von HomeBridge einmal ein Lese-Telegramm bekommen, um die Synchronisierung von Homebridge mit den KNX-Geräten zu beschleunigen.   

Mit jedem Update von iOS erwarten wir neue Services von Apple! Daher ist es zur Nutzung neuer Services erforderlich homebridge aktuell zu halten.

###Characteristics
Jedes *Characteristic* (Eigenschaft, Merkmal) ist ein Objekt mit **mindestens** genau einem `Type`-Schlüssel. Die möglichen Typen befinden sich in der gleichen Datei, die oben bei den Services verlinkt ist. Biespiele sind `On` für Strom an/aus, `Brightness` für Helligkeit von dimmbaren Lampen.  
Jedes Characteristic **kann** ausserdem die folgenden Felder enthalten:  
-  `Set`: Eine Liste mit Gruppenadressen, die beim Schalten aus Homekit heraus auf dem Bus geschrieben werden sollen.
-  `Listen`: Eine Liste mit Gruppenadressen, auf die das Characteristic auf dem Bus hören soll.  
-  `DPT`: Die Angabe eines KNX *data point type*  (derzeit unterstützt nur DPT1, DPT5, DPT5.001, DPT9).  

Bei Characteristics, die laut HomeKit-Definition Boole'sch (*boolean*) oder Prozentwerte (*percentage*) sind, kann durch Angabe von `"Reverse":true` die Bedeutung zwischen HomeKit und KNX umgedreht werden.

Characteristics ohne Gruppenadressen können nur durch einen Add-in, also einen `Handler` verwendet werden.

###Handler
Neu hinzugekommen sind in der Version 0.3 von homebridge-knx die `Handler`, die Möglichkeit eine eigene/angepasste Routine für die Behandlung von Ereignissen zu programmieren und den Services zuzuweisen. Die Idee dahinter ist, möglichst viele verschiedene KNX-Geräte abbilden zu können, ohne große Änderungen an homebridge-knx vornehmen zu müssen.

`Handler` werden als JavaScipt-Datei im Verzeichnis `/lib/addins` und müssen [ein paar Einschränkungen einhalten](https://github.com/snowdd1/homebridge-knx/blob/master/handler-add-in.md)(engl.)    

Handler können nicht in Kombination mit dem `Reverse` Schlüsselwort für DPT1 and DPT5.001 Typen verwendet werden, eine solche Wertumkehrung muss im add-in selbst programmiert werden.

###KNXObjects
Handler dürfen KNX-Adressen verwenden, die nicht direkt an Characteristics gebunden sind. Um im Code des add-ins auf diese zusätzlichen Adressen zugreifen zu können, werden diese in der Section `KNXObjects` des Services definiert.  
Dabei ist KNXObjects eine Liste aus Objekten mit den Feldern  
- `Type`: Name der Gruppenadresse. Muss innerhalb des Services eindeutig sein!   
- `DPT` **zwingend** erforderlich, da es ja kein homekit characteristic gibt von dem aus auf den Typ geschlossen werden kann!
- `Set`: Wie bei characteristics
- `Listen`: Wie bei characteristics oben.  

###LocalConstants
Um weitere Werte aus der knx_config.json an den Handler zu übergeben, können in der Liste `LocalConstants` Konstanten definiert werden. Das Add-in kann diese dann im Programmcode referenzieren und bekommt die Werte übergeben. Damit können Handler verschiedene Services bedienen, die sich in etwas mehr als nur den Gruppenadressen unterscheiden.

### UUID und subtype
Jedesmal wenn homebridge-knx beim Starten in der *knx_config.json* ein neues *Device* findet, bekommt es eine zufällige ID zugewiesen (UUID), diese wird in der *knx_config.json*  gespeichert. Wird diese ID entfernt oder geändert, wird das Device in HomeKit als neues Gerät auftauchen, und das bisherige Device wird unerreichbar in HomeBridge bestehen bleiben.  
Das gleiche gilt für die Services innerhalb eines Geräts, diese bekommen eine eindeutige `subtype` ID zugewiesen.

##Beispiel
```json
{
    "knxd_ip": "192.168.178.100",
    "knxd_port": 6720,
    "AllowKillHomebridge": true,
    "Devices": [
        {
            "DeviceName": "Desk lamp",
            "Services": [
                {
                    "ServiceType": "Lightbulb",
                    "ServiceName": "Office desk lamp",
                    "Characteristics": [
                        {
                            "Type": "On",
                            "Set": [
                                "1/3/5"
                            ],
                            "Listen": [
                                "1/3/5"
                            ]
                        }
                    ]
                }
            ]
        },
        {
            "DeviceName": "Office rolling shutter",
            "Services": [
                {
                    "ServiceType": "WindowCovering",
                    "Handler": "GiraJalousieActuator",
                    "ServiceName": "Office blinds",
                    "Characteristics": [
                        {
                            "Type": "TargetPosition",
                            "Set": [
                                "2/3/46"
                            ],
                            "DPT": "DPT5"
                        },
                        {
                            "Type": "CurrentPosition",
                            "Listen": [
                                "2/3/26"
                            ]
                        },
                        {
                            "Type": "PositionState"
                        }
                    ],
                    "KNXObjects": [
                        {
                            "Type": "ShutterMove",
                            "Listen": "2/3/6",
                            "DPT": "DPT1"
                        }
                    ],
                    "KNXReadRequests": [
                        "2/3/26",
                        "2/3/46"
                    ],
                    "LocalConstants": {
                        "TimeOutSecs": 23
                    }
                }
            ]
        }
    ]
}
```





