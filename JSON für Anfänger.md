#JSON für Anfänger

Wenn man das erste mal JSON-Dateien sieht, denkt man (sofern man nicht gerda vorher in JavaScript programmiert hat) unweigerlich, [WTF](https://de.m.wikipedia.org/wiki/Liste_von_Abk%C3%BCrzungen_(Netzjargon)#W)?  

[JSON steht für JavaScript Object Notation](https://de.m.wikipedia.org/wiki/JavaScript_Object_Notation) und ist genau das: die Form in der in JavaScript-Quellcode Objekte beschrieben werden können, sogenannte literale Objekte.

Dabei ist die generelle Form `"Schlüssel": "Wert"`.  

In JavaScript werden Objekte durch geschweifte Klammern eingeschlossen:  
`meinObjekt = { };`  
Nun, das ist ein leeres Objekt, und damit ist die kürzeste JSON-Datei auch einfach `{}`. Alles, was wir in der Datei unterbringen wollen, muss also zwischen die äußersten Klammern. Fügen wir doch unserem `meinObjekt` noch eine Eigenschaft hinzu, und zwar ein weiteres (verschachteltes) Objekt:  
```javascript
meinObjekt = { 
	bridge: {} 
}
```  
Und so sieht man, dass es schnell unübersichtlich werden kann. Daher empfehle ich unbedingt auf die Einrückungen zu achten.
Warum sind jetzt keine Anführungszeichen um `bridge`? Nun, in JavaScript selbst braucht man die Anführungszeichen um die Schlüssel nur dann, wenn sie ansonsten gegen Variablenkonventionen verstoßen würden. **In JSON sind sie aber IMMER erforderlich**  

Enthält ein Objekt mehrere Schlüssel-Wert-Paare, werden sie durch Kommata getrennt:
```json
{
	"bridge": {
		"name": "Meine Homebridge",
		"username": "BC:22:3D:E3:CE:12",
		"port": 51826,
		"pin": "031-45-154"
	}
}
```
Wie man sieht, sind fast alle Werte ebenfalls in Anführungszeichen eingeschlossen. Es gibt auch nur vier relevante Ausnahmen für uns:  
-  der Wert ist selbst ein Objekt (wie bei `"bridge: { }"`)
-  der Wert ist eine Zahl (`"port": 51826`)
-  der Wert ist wahr/falsch (`"Reverse":true` oder `"Reverse":false`)
-  der Wert ist eine Liste (Array) von Werten (`"Listen": ["1/2/3","1/2/4"]`) - dabei gilt natürlich für die Werte in der Liste das gleiche wie für alle anderen Werte auch!

[Hier gehts zur Konfiguration von homebridge-knx...](Konfiguration.md)


