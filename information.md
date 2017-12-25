# Cisco Switch - Basic Infos

## Die drei Modis
1. **enable Modus / user Mode**: Standard Modus - hier kann man nur basis Konfigurationen vonehmen.
2. **privilegierten Modus**: Hier kann man schon mehr. Z.B. `show ip int br` .
3. **config Modus**: In diesem Modus kann man Einstellungen am Gerät vornehmen.

## Grundkonfiguration
Alle Kommandos wurden im *privilegierten Modus* ausgeführt. Vor jedem Kommando wurde `enable` eingegeben.
 Funktion |Programmname 
 --- | --- | ---
Hostname ändern | `conf t -> hostname *my-cool-switch*`  
kein DNS Lookup | `conf t -> no ip domain lookup`
Inline status Meldungen deaktivieren (via Consol-Kabel) | `conf t -> line cons 0 -> logging synchronous`
Inline Meldungen via SSH/Telnet anzeigen (nur Temporär) | `terminal monitor`
klartext Passwörter ausschalten | `conf t -> service password-encryption`
Passwort für den Enable Mode setzen (Passwort wird verschlüsselt gespeichert) | `conf t -> enable secret k7zK23-dE` 
Banner setzen | `conf t -> banner motd $ her super coole message eingeben $`

### Uhrzeit
 Funktion |Programmname 
 --- | --- | ---
Uhrzeit anzeigen| `show clock`
NTP Server setzen (nur wenn DNS funktioniert) | `conf t -> ntp 0.de.pool.ntp.org prefer` 
NTP Status überprüfen | `show ntp status`

## Wichtige Kommandos
Alle Kommandos wurden im *privilegierten Modus* ausgeführt. Vor jedem Kommando wurde `enable` eingegeben.

Kommando | Funktion
 --- | ---
 `wr` | speichert die aktuelle Konfiguration ab (geht **nur** im *privilegierten Modus*)
 `copy running-config startup-config` | macht das gleiche wie `wr` nur in lang
 `reload` | startet das Gerät neu (FYI: Es gibt keinen *shutdown* o.e. Befehl)
 `write erase` | löscht alles im NVRAM
 `conf t -> interf fa1 -> spanning-tree portfast` | Schaltet spanning-tree für den Port aus. Damit vorsichtig umgehen!
 `conf t -> ip default-gateway 10.10.10.1` | setzt den Default Gateway für das Gerät
 `conf t -> ip route 0.0.0.0 0.0.0.0 10.10.10.1` | Setzt eine Default Route für das Gerät

 
### Wichtige *show* Kommandos
Vorher muss man sich mit in den `enable` mode schalten.

Kommando | Funktion
 --- | ---
 `show running-config` | Zeigt die akutelle Konfiguration an (Config im RAM)
 `show startup-config` | Zeigt die Konfig an, die im NVRAM ist (wird beim booten ausgeführt)
 `show vlan-switch` | Zeigt welcher Port zu welchem VLAN gehört (nur bei älteren Geräten und Routern)
 `show flash` | Zeigt den Inhalt der *Festplatte* an
 `show version` | Zeigt die Version vom IOS an
 `show arp` | Zeigt die Zuweisung von MAC Adresse und IP Adresse an
 `show mac-address-table` | Zeigt alle MAC Adressen und an welchen Port sie sich befinden.
 `show mac-address-table interface fastEthernet 1` | Zeigt mir an welche MAC Adressen an diesem Port *angeschlossen* sind.
 `show interfaces status` | Zeigt den Status der Interfaces an (auch error-disabled Ports)
 `show interfaces status err-disabled` | Zeigt error-disabled Ports an
 `show vlan database` | Konfiguration von VLAN (nur bei älteren Geräten)
 
## Physikalischen Console Port absichern
Hierbei handelt es sich um den Port, wo man sein blaues Cisco Kabel hineinsteckt, um das Gerät zu konfigurieren. 
Per default sein dort keine Passworter o.e. gesetzt.

Schritt | Kommando | Funktion
 --- | --- | ---
 1 | `en -> conf t -> line console 0` | wir möchten *Console 0* konfigurieren
 2 | `password <supergeheim>` | Es wird ein Passwort gesetzt. Durch  `service password-encryption` wird das Passwort verschlüsselt gespeichert. 
 3 | `login` | Es ist zwingend erforderlich, dass man sich mit einem Passwort anmeldet.
 4 | `exec-timeout 15 10` | Nach 15 min. und 10 sec. - wenn man nichts macht - wird man ausgeloggt.
 
#### VLAN auf Layer 2
Zuerst in den `enable` Mode schalten. 
Diese Schritte funktionieren nur auf einem alten Cisco Device. Neuere Geräte können das Kommando `show vlan`.

Desweiteren erstellt man bei neueren Geräten ein VLAN, indem man einfach z.B. `vlan 10` eingibt, um das VLAN 10 zu erstellen. 

Schritt | Kommando | Funktion 
 --- | --- | ---
1 | `vlan database` | Schaltet in VLAN Datenbank (config wird später im flash gespeichert)
2 | `vlan 10 name <some-name>` | erstellt VLAN 10, wenn es noch nicht existiert
3 | `exit` | so geht man aus der VLAN Konfig heraus
4 | `show vlan-switch` | überprüfen, ob das auch alles funktioniert hat

Die VLAN Config wird ein einer extra Datei **vlan.dat** auf dem Flash gespeichert. Möchte man also einen Switch zurück setzen, so muss man diese Datei auch löschen.

#### Infos zu VTP
- VTP nicht benutzen !

`show vtp status` Zeigt den Status an. VTP ausschalten: `conft -> vtp mode transp`

#### Vlan 1 Konfigurieren auf Layer 3
Schritt | Kommando | Funktion 
 --- | --- | ---
1 | `en -> conf t -> interface vlan 1` | Wir möchten das VLAN Interface 1 Konfigurieren (auf L3) 
2 | `ip address 10.10.10.1 255.255.255.0` | Gibt dem Interface eine IP Adresse
3 | `description LALALLA` | Gibt dem VLAN eine Beschreibung 

#### Port einem VLAN hinzufügen

Funktion |Programmname 
--- | ---
Port in ein anderes VLAN Packen | `conf t -> int faXX -> switchport mode access -> switchport access vlan 20`

##### Trunk bzw. trunk port erstellen
Schritt | Funktion |Programmname 
--- | --- | ---
1| Port zum Trunk machen | `conf t -> int faXX -> switchport mode trunk`
2| Native VLAN auswählen (default ist VLAN 1) | `conf t -> int faXX -> switchport trunk native vlan 99`
3| Welche VLAN's gehören zum Trunk | `conf t -> int faXX -> switchport trunk allowed vlan 20,30,40`
4| noch ein Port zum Trunk hinzufügen | `conf t -> int faXX -> switchport trunk allowed vlan add 500`

## DHCP
- mit `en -> show ip  dhcp pool` kann man sich die einzelnen Pools anzeigen lassen
- mit `en -> show ip dhcp binding` wir angezeigt welcher Cleint zurzeit ein IP Adresse vom Pool hat. 

### Neuen Pool erstellen
Schritt | Kommando | Funktion
 --- | --- | ---
 0 | en -> conf t -> `ip dhcp excluded-addresses 10.10.10.1 10.10.10.19` | optional: wird sagen, dass diese IP Adressen (von - bis) nicht vom DHCP Server vergeben werden sollen
 1 | en -> conf t -> `ip dhcp pool <name-von-pool>` | erstellt einen neuen DHCP-Pool 
 2 | `network 10.10.10.0 /24` | Netzwerkmaske Konfigurieren (wichtig nach der 0 ein Leerzeichen) 
 3 | `default-router 10.10.10.1` | optional: gibt an was das default Gateway ist
 4 | `dns-server 8.8.8.8` | optional: legt den dns server fest
 5 | `lease 0 2 0` | optional: wie lange ist die Lease Time für die IP Adresse in [days] [hours] [minutes]
 
 ### DHCP Helper Adresse
`conf t -> int vlan 10 -> ip helper-address 172.17.1.1` forward all to this ip address 
 
 **Domain Name**
 
 Domain Name ist z.B. für ssh wichtig.
 
 Kommando | Funktion
  --- | ---
  en -> `show hosts` | zeigt u.a. den Domain Name an
  en -> `show ip domain-name` | zeigt nur den Domain Name an
  en -> `conf t -> ip domain-name brainoftimo.com` | vergibt den Domain Name *brainoftimo.com*
  
  
## SSH 
  
  Da Telnet unverschlüsselt ist, sollte man statt telnet SSH benutzen.
  
  Kommando | Funktion
  --- | ---
  en -> conf t -> `ip domain-name example.com` | setzt einen Domain Namen
  `username john priv 15 secret supergeheim` | man muss einen Benutzernamen erstellen, falls nich keiner existiert. John hat das Passwort *supergeheim*
 `crypto key generate rsa` | es wird ein RSA Schlüssel erstellt. Hier ambesten *2048* als Bitlänge wählen
 `ip ssh version 2` | man möchte ssh version 2 verwenden
 `line vty 0 5 -> login local` | lokale benutzer können sich mit Benutzername und Passwort am Gerät anmelden
 `line vty 0 5 -> transport input ssh` | es ist nur erlaubt sich per ssh am Gerät anzumenden. Telnet wird dadurch deaktiviert
 `show ssh` | Zeigt an wer gerade per ssh am dem Gerät angemeldet ist.
 
#### Anmelden per Public / Private Key
Anstatt sich mit einem Passwort anzumelden, kann man sich auch per Public / Private Key anmelden

[klick mich](https://nsrc.org/workshops/2016/apricot2016/raw-attachment/wiki/Track5Wireless/cisco-ssh-auth.htm)

## PoE - Power over Ethernet
 Kommando | Funktion
  --- | ---
`show power inline` | zeigt status an
`conf t -> int fa0 -> power inline never` | macht POE aus
`conf t -> int fa0 -> power inline auto` | Automatically detect and power inline devices

## SNMP - Simple Network Management Protocol
Um mit einer Monitoring Software den Switch überwachen zu können wird in der Regel das SNMP eingesetzt. Um SNMP v2 auf einem Gerät zu aktivieren muss man folgendes eingeben: 

`conf t -> snmp-server community geheim RO`

Der Community String lautet hier *geheim*. Diesen muss man natürlich entsprächend ändern. Das *RO* am Ende sagt, dass die Monitoring Sortware nur lesend auf den Switch zugreifen darf, also kein Änderungen am Gerät selbst vornehmen darf. 

## Syslog auf zentralen Server
Hier geht es darum Meldungen (Syslogs) dass das Gerät erzeugt an einen Zentralen Server zu senden. Dieser Server kann dann z.B. die empfangenen logs auswerten und z.B. Alerts an den Admin senden. 

Dazu folgenden Befehlt im Gerät eingeben:
`conf t -> logging host <ip-vom-syslog-server>`

## Error Disabled Ports -> Recorvery
Wenn ein Port in den Status `error-disabled` geht muss ein Administrator diesen Port manuell per `shutdown` und `no shutdown` wieder "einschalten". 

Es gibt aber auch die Möglichkeit einen `error-disabled` Port automatisch wierder "einzuschalten", indem man folgende Befehle Benutzt:

```
conf t
! psecure-violation ist hier nur ein Beispiel. Es gibt viele Sachen, die einen Port in error-disabled bringen können
errdisable recovery cause psecure-violation
! revovery interval in Sekunden, also 5 min hier
errdisable recovery interval  300

! anzeigen was alles error recovery ist
show errdisable recovery
```

## DHCP Snooping
DHCP Snooping eliminiert "roge" DHCP-Server, also DHCP-Server die nicht ins Netzwerk gehören und somit einen Angriff auf das Netzwerk darstellen. 

1. DHCP Snoopoing auf dem Switch aktivieren: `conf t -> ip dhcp snooping`
2. Die Ports als *trusted* makieren, welche DHCP Anfragen beantworten dürfen. 
   Also den Port an dem der DHCP Server angeschlossen ist. 
   Außerdem muss man noch Trunk Ports, die z.B. an einem Anderen Switch verbinden sind als *truested* makieren. 
   `conf t -> int Gi1/0/12 ->  ip dhcp snooping trust` 
3. DHCP Snooping für ein VLAN aktivieren: `conf t -> ip dhcp snooping vlan 10`

Wenn das Feature aktiviert ist, guckt sich der Switch automaitsch alle DHCP Requests and Responese an.
Diese Tabelle kann man sich mit `show ip dhcp snooping binding` angucken.

Wenn DHCP Antworten von einem untrusted Port kommen, dann geht dieser Port in den Status: *Error Disabled*

