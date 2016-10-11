Ich erkläre mal kurz was zu den "Accessories", "Services", "Bereichen" und "Räumen", weil das immer wieder verwechselt wird:

**Apple stellt sich das folgendermaßen vor:**

## Accessory
Ein **"Accessory"** ist ein (physisches) Gerät, das über Bluetooth oder WLAN über das Apple-Homekit-Protokoll ("HAP" - Homekit-Application-Protocol) mit dem iPhone/iPad/AppleTV sprechen kann. Diese Geräte muss man (i.d.R. einzeln) über eine App (z.B. EVE oder Devices oder ...) in HomeKit anmelden (dabei fragen sie dann nach Code etc).

## Raum und Bereich
In **"Räume"** kann man Geräte stecken (bzw. abbilden wo sie physisch stehen). Wenn ich also 5 Lampen im Wohnzimmer habe, kann ich später über Siri "alle Lampen im Wohnzimmer" schalten oder abfragen. Räume können einem oder mehreren **Bereich(en)** zugeordnet werden: Z.B. Etagen oder für die größeren Latifundien "Westflügel" oder so. Bereiche können derzeit nicht geschachtelt werden.
Bereiche erlauben wie die Räume das Gruppieren von Geräten (allerdings immer nur mittelbar über Räume - Geräte können nicht in Bereichen stehen). So kann ich abends "alle Lichter im Erdgeschoss" ausschalten, oder "alle Lampen im Garten" (der muss entweder ein Raum sein oder einen Raum enthalten!)
Das passiert alles in der **Homekit-Datenbank**, und nicht in Homebridge! In Homebridge definieren wir nur die Accessories mit ihren Services, s.u.; Homekit speichert diese Zuordnungen in der iCloud-Datenbank. Mehrere iGeräte die den gleichen iCloud-Account verwenden greifen daher auch auf die gleiche Homekit-Datenbank zu (es gibt allerdings immer noch Probleme bei der Replikation) - iGeräte mit unterschiedlichen iCloud-Accounts muss man explizit einladen (Einstellungen --> Homekit --> (Haus) --> Personen einladen.)

Nun zu den **Services** und so weiter:
## Service
Ein physisches Gerät ("Accessory") kann nicht nur eine Funktion haben. Apple nannte in der WWDC-Präsentation damals den Geragentüröffner, der gleichzeitig eine Lampe hat. Dazu hat Homekit die Abstraktionsebene "Service". Unser Garagentüröffner hat also die "Services" "Garagentür" und "Lampe".
## Characteristics
Jeder Service hat bestimmte Eigenschaften ("Characteristics"), die zwingend vorgegeben sind: Für die Lampe ist das beispielsweise die Eigenschaft "Strom an/aus" ("On"). Es gibt weitere optionale Eigenschaften wie "Helligkeit" für eine Lampe, die dimmbar ist. Oder "Hue"/Farbton für eine RGB-Lampe.
Die derzeit unterstützten Services von Homebridge-knx findet ihr hier: https://github.com/snowdd1/homebridge-knx#supported-services-and-their-characteristics .

Homebridge kümmert sich darum, dass die notwendigen Eigenschaften der Services angelegt werden, im Zweifel haben sie einfach keine Funktion. Durch die Konfiguration in der config.json können wir die Eigenschaften mit KNX-Adressen verbinden. Durch unser Bussystem sind wir komplett befreit von den Zwängen der physischen Zusamenhänge - Ich habe mal testweise ein "Accessory" mit ganz vielen "Lightbulb"-Services angelegt. Die Bedienung über Apps wird dann allerdings unhandlich, weil die meisten App-Entwickler eine bestimmte Vorstellung von den physischen Geräten hatten, und die sah einfach mehrere GLEICHE Services in einem Gerät nicht vor. Kommt vielleicht noch. Insofern rate ich dazu, im Zweifel immer ein "Accessory" für jede Funktion anzulegen. Durch die Homebridge müssen wir diese ja nicht einzeln an Homekit gewöhnen, sondern es werden immer alle Geräte die sich hinter der "Brücke" (Homebridge) befinden hinzugefügt.

