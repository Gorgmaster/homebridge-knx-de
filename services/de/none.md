# Diensttyp auswählen
Der Diensttyp (Service type) bestimmt, welche **Function** durch HomeKit geteiert werden kann.
Ein einfaches Beispiel ist "Lampe": 
- Zwingende Eigenschaft ist An/Aus
- Optional unterstützt werden
  - Helligkeit (für Dimmer)
  - Farbton (Hue, für RGB-Lampen)
  - Farbsättigung (Saturation, für RGB-Lampen)
Diese sind bei Lampe dann auch über Siri zu steuern. Es sind nicht alle Dienst bisher über Siri anzusprechen, und bei den unterstützten Diensten sind nicht alle Eigenschaften per sprachbefehl anprechbar. Eine Übersicht gibt es dazu auf der Seite https://github.com/nfarina/homebridge/wiki/Siri-commands
