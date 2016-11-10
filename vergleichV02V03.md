# Vergleich der Konfigurationen zwischen Version 0.2 und 0.3

Das Format der Konfigurationsdateien hat sich deutlich geändert.

Dies betrifft sowohl die `config.json` als auch die neue `knx_config.json`.  


##config.json  

<table>
<tr> <td> **Version 0.2.x**  </td><td>  **Version 0.3.x** </td></tr>
<tr><td>
 Enthält die ganze Konfiguration sowohl für `homebridge`selbst, als auch alle Konfigurationsteile für `homebridge-knx`, genauso wie für alle anderen *homebridge-plug-ins*  
 </td><td>  Die Konfiguration von `homebridge-knx` ist nicht mehr Teil der config.json, sondern in einer eigenen Datei *knx_config.json*
 </td></tr>
<tr> <td> *Beispiel*  </td><td>  *Beispiel*  </td></tr>
<tr><td>
```
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
 </td><td> 
 ```
  { 
	"bridge": { 
		"name": "Homebridge", 
		"username": "CC:22:3D:E3:CE:30", 
		"port": 51826, 
		"pin": "031-45-154"
	},
	"description": "This is an example configuration file for KNX platform shim",
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
 ```
 </td></tr>
</table>  

