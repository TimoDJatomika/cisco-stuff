# Cisco Switch - Basic Infos


## Grundkonfiguration
 Schritt | Funktion |Programmname 
 --- | --- | ---
1|Hostname ändern | en -> conf t -> hostname *mycool-switch*  
2|kein DNS Lookup | en -> conf t -> no ip domain lookup
3|Inline status Meldungen deaktivieren| conf t -> line cons 0 -> `logging synchronous`
4|Passwort für den Enable Mode setzen | conf t -> enable secret superpasswort 
5|banner setzen | en -> conf t -> `banner motd $ her super coole message eingeben $`
6|klartext Passwörter ausschalten | en -> conf t -> `service password-encryption`
7.1|Uhrzeit manuell setzen|en -> conf t -> clock set 19:43:43 5 Apr 2015 (funktioniert nur bei machner Cisco Geräten)
7.1.1|verify clock| en -> show clock
7.2|NTP setzen | en -> conf t -> ntp 0.de.pool.ntp.org prefer
7.2.1|NTP überprüfen | en -> show ntp status

## Wichtige Kommandos
Kommando | Funktion
 --- | ---
 `en -> conf t -> enable secret <supergeheim>` | Man benötigt ein Passwort um vom `user` mode in den `privilege` mode zu kommen. Das Passwort wird verschlüsselt gespeichert.
 `en -> vlan database` | Konfiguration von VLAN
 `en -> wr` | speichert die aktuelle Konfiguration ab
 `en -> copy running-config startup-config` | macht das gleiche wie `wr` nur in lang
 `en -> reload` | startet das Gerät neu 
 `en -> write erase` | löscht alles im NVRAM
 `en -> conf t -> banner motd -> # hier message eingeben #` | Message, die beim starten bzw. anmelden am Gerät angezeigt wird.
 `en -> conf t -> interf fa1 -> spanning-tree portfast` | Schaltet spanning-tree für den Port aus. Damit vorsichtig umgehen!
 `en -> conf t -> ip default-gateway 10.10.10.1` | setzt den Default Gateway für das Gerät
 `en -> conf t -> ip route 0.0.0.0 0.0.0.0 10.10.10.1` | Setzt eine Default Route für das Gerät
 
### Wichtige *show* Kommandos
Vorher muss man sich mit in den `enable` mode schalten.

Kommando | Funktion
 --- | ---
 `show running-config` | Zeigt die akutelle Konfiguration an (Config im RAM)
 `show startup-config` | Zeigt die Konfig an, die im NVRAM ist (wird beim booten ausgeführt)
 `show vlan-switch` | Zeigt welcher Port zu welchem VLAN gehört
 `show flash` | Zeigt den Inhalt der *Festplatte* an
 `show version` | Zeigt die Version vom IOS an
 `show arp` | Zeigt die Zuweisung von MAC Adresse und IP Adresse an
 `show mac-address-table` | Zeigt alle MAC Adressen und an welchen Port sie sich befinden.
 `show mac-address-table interface fastEthernet 1` | Zeigt mir an welche MAC Adressen an diesem Port *angeschlossen* sind.
 `show interfaces status err-disabled` | Zeigt Error Disables Ports an
 
## Physikalischen Console Port absichern
Hierbei handelt es sich um den Port, wo man sein blaues Cisco Kabel hineinsteckt, um das Gerät zu konfigurieren. 
Per default sein dort keine Passworter o.e. gesetzt.

Schritt | Kommando | Funktion
 --- | --- | ---
 1 | `en -> conf t -> line console 0` | wir möchten *Console 0* konfigurieren
 2 | `password <supergeheim>` | Es wird ein Passwort gesetzt. Durch  `service password-encryption` wird das Passwort verschlüsselt gespeichert. 
 2 | `login` | Es ist zwingend erforderlich, dass man sich mit einem Passwort anmeldet.
 3 | `exec-timeout 15 10` | Nach 15 min. und 10 sec. - wenn man nichts macht - wird man ausgeloggt.
 
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
1 | en -> conf t -> `interface vlan 1` | Wir möchten das VLAN Interface 1 Konfigurieren (auf L3) 
2 | `ip address 10.10.10.1 255.255.255.0` | Gibt dem Interface eine IP Adresse
3 | `description LALALLA` | Gibt dem VLAN eine Beschreibung 

#### Port einem VLAN hinzufügen

Funktion |Programmname 
--- | ---
welche Ports sind in welchen VLAN | show vlan-switch 
Port in ein anderes VLAN Packen | en -> conf t -> int faXX -> switchport mode access -> switchport access vlan 20

##### Trunk bzw. trunk port erstellen
Schritt | Funktion |Programmname 
--- | --- | ---
1| Port zum Trunk machen | en -> conf t -> int faXX -> switchport mode trunk
2| Native VLAN auswählen (default ist VLAN 1) | en -> conf t -> int faXX -> switchport trunk native vlan 99
3| Welche VLAN's gehören zum Trunk | en -> conf t -> int faXX -> switchport trunk allowed vlan 20,30,40
4| noch ein Port zum Trunk hinzufügen | en -> conf t -> int faXX -> switchport trunk allowed vlan add 500

## DHCP
Mit `en -> show ip  dhcp pool` kann man sich die einzelnen Pools anzeigen lassen

Mit `en -> show ip dhcp binding` wir dagezeigt welcher Cleint zurzeit ein IP Adresse vom Pool hat. 

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
`conf t -> int vlan 10 -> ip helper-address 172.17.1.1` 

forward all to this ip address 
 
 **Domain Name**
 
 Domain Name ist z.B. für ssh wichtig.
 
 Kommando | Funktion
  --- | ---
  en -> `show hosts` | Zeigt u.a den Domain Name an
  en -> `show ip domain-name` | Zeigt nur den Domain Name an
  en -> conf t -> `ip domain-name brainoftimo.com` | vergibt den Domain Name *brainoftimo.com*
  
  
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

