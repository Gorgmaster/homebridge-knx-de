# Vergleich der Konfigurationen zwischen Version 0.2 und 0.3

Das Format der Konfigurationsdateien hat sich deutlich geändert.

Dies betrifft sowohl die `config.json` als auch die neue `knx_config.json`.  


##config.json  

<table>
<tr> <td> <b>Version 0.2.x</b>  </td><td>  <b>Version 0.3.x</b> </td></tr>
<tr><td>
 Enthält die ganze Konfiguration sowohl für <b>homebridge</b> selbst, als auch alle Konfigurationsteile für <i>homebridge-knx</i>, genauso wie für alle anderen <i>homebridge-plug-ins</i>  
 </td><td>  Die Konfiguration von `homebridge-knx` ist nicht mehr Teil der config.json, sondern in einer eigenen Datei knx_config.json
 </td></tr>
<tr> <td> <i>Beispiel</i>  </td><td>  <i>Beispiel</i>  </td></tr>
<tr><td>
 <pre>
  { 
	"bridge": { 
		"name": "Homebridge", 
		"username": "CC:22:3D:E3:CE:30", 
		"port": 51826, 
		"pin": "031-45-154"
	},
	"description": "This is an example ...",
	"platforms": [ 
		{
			"name":"KNX",
			"platform":"KNX",
			"accessories": [
				HIER SIND DIE GERÄTE DEFINIERT, SIEHE UNTEN
			
			]
		}
	],
	"accessories": [
	 ]
 } 
 </pre> </td><td> 
<pre>
{ 
	"bridge": { 
		"name": "Homebridge", 
		"username": "CC:22:3D:E3:CE:30", 
		"port": 51826, 
		"pin": "031-45-154"
	},
	"description": "This is an example ....",
	"platforms": [
	],
	"accessories": [
	 ]
 }
 </pre>
 </td></tr>
</table>  

##knx_config.json
Die Geräte (Devices früher Accessories) sind nun in die **knx_config.json** gewandert.  
Dabei hat sich das Format geändert, um mehr Möglichkeiten zuzulassen:  

Hier mal ein Abschnitt der ein Gerät mit einem Service "Lampe" mit Dimmer abbildet:
<table>
<tr> <td> <b>Version 0.2.x</b>  </td><td>  <b>Version 0.3.x</b> </td></tr>
<tr> <td> <i>in der config.json</i>  </td><td>  <i>in der knx_config.json</i> </td></tr>
<tr><td>
<pre>
{ 
	"name": "Living Room North Lamp", 
	"services": [ 
		{ 
			"type": "Lightbulb", 
			"name": "Arbeitszimmerlampe", 
			"On": { 
					"Set": "1/1/6", 
					"Listen": [ 
							"1/1/63"
					 ]
			 }, 
			"Brightness": { 
				"Set": "1/1/62", 
				"Listen": [ 
						"1/1/64"
				 ]
		 }
 }
 </pre>
 </td><td> 
 <pre>
  {
    "DeviceName": "Arbeitszimmerlampe",
    "Services": [
        {
            "ServiceType": "Lightbulb",
            "ServiceName": "Bürolampe",
            "Characteristics": [
                {
                    "Type": "On",
                    "Set": [
                        "1/1/6"
                    ],
                    "Listen": [
                        "1/1/63"
                    ]
                },
                {
                	"Type":"Brightness",
                	"Set": [
                		"1/1/62"
                	],
                	"Listen": [
                		"1/1/64"
                	]
                }
            ]
        }
    ]
}
 </pre>
 </td></tr>
</table>  

Man sieht ein paar kleine Änderungen:

| Version 0.2.x | Version 0.3.x |
| ----------------   | -----------------  |
|  `name` auf der ersten Ebene der *accessories*  |  `DeviceName` |
|  `services` |  `Services` |
|  `type` |  `ServiceType` |
|   `name` innerhalb der services | `ServiceName` |
|  `On` als Schlüssel für ein bestimmtes Charcteristic | `"Characteristics": [ "Type":"On", .... ]` als Liste mit einzelnen Characteristics, deren Typ im Schlüssel `Type` angegeben werden. |

Insbesondere die letzte Änderung verursacht leider sehr viel manuelle Arbeit beim Umstieg. Diese Änderung war aber nötig, da auf diese Weise beliebige Characteristics zusammen in einen Service gesteckt werden können - und nicht nur die 2-3 die in der alten Version vorab codiert waren. 

Für die Änderung der Richtung, z.B. bei Tür- oder Fensterkontakten, findet sich das neue Schlüsselwort `Reverse` innerhalb der Characteristic-Klammer:

<table>
<tr> <td> <b>Version 0.2.x</b>  </td><td>  <b>Version 0.3.x</b> </td></tr>
<tr> <td> <i>in der config.json</i>  </td><td>  <i>in der knx_config.json</i> </td></tr>
<tr><td>
<pre>
{
	"name": "Bürofenster Links",
	"services": [
		{
			"type": "ContactSensor",
			"name": "Fensterkontakt Büro Links",
			"ContactSensorState": {
				"Listen": "5/3/6R"
			}
		}
	]
}
 </pre>
 </td><td> 
 <pre>
{
	"DeviceName": "Bürofenster Links",
	"Services": [
		{
			"ServiceType": "ContactSensor",
			"ServiceName": "Fensterkontakt Büro Links",
			"Characteristics": [
				{
					"Type":"ContactSensorState",
					"Listen": [
						"5/3/6"
					],
					"Reverse":true
				}
			]
		}
	]
}
</pre>
 </td></tr>
</table>  
