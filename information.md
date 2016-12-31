# Cisco Switch - Basic Infos


## Grundkonfiguration
 Schritt | Funktion |Programmname 
 --- | --- | ---
1|Hostname ändern | enable -> conf t -> hostname *mycool-switch*  
2|kein DNS Lookup | enable -> conf t -> no ip domain lookup
3|Inline status Meldungen deaktivieren| conf t -> line cons 0 -> `logging synchronous`
4|change position | ändert position
5|klartext Passwörter ausschalten | en -> conf t -> `service password-encryption`
6|Uhrzeit setzen|en -> conf t -> clock set 19:43:43 5 Apr 2015 # funktioniert nicht
7|verify clock| en -> show clock


## Wichtige Kommandos
Kommando | Funktion
 --- | ---
 `en -> enable secret` | Man benötigt ein Passwort um vom `user` mode in den `enable` mode zu kommen. Das Passwort wird verschlüsselt gespeichert.
 `en -> vlan database` | Konfiguration von VLAN
 `en -> wr` | speichert die aktuelle Konfiguration ab
 `en -> copy running-config startup-config` | macht das gleiche wie `wr` nur in lang
 `en -> reload` | startet das Gerät neu 
 `en -> write erase` | löscht alles im NVRAM
 `en -> conf t -> banner motd -> # hier message eingeben #` | Message, die beim starten bzw. anmelden am Gerät angezeigt wird.
 
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


## Infos zu VTP
- VTP nicht benutzen !

#### Vlan 1 Konfigurieren
Schritt | Kommando | Funktion 
 --- | --- | ---
1 | en -> conf t -> `interface vlan 1` | Wir möchten das VLAN Interface 1 Konfigurieren (auf L3) 
2 | `ip address 10.10.10.1 255.255.255.0` | Gibt dem Interface eine IP Adresse

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
 1 | en -> conft -> `ip dhcp pool <name-von-pool>` | erstellt einen neuen DHCP-Pool 
 2 | `network 10.10.10.0 /24` | Netzwerkmaske Konfigurieren (wichtig nach der 0 ein Leerzeichen) 
 3 | `default-router 10.10.10.1` | Gibt an was das default Gateway ist
 4 | `dns-server 8.8.8.8` | legt den dns server fest
 5 | `lease 0 2 0` | wie lange ist die Lease Time für die IP Adresse in [day] [hour] [seconds]
 
 
 ## Domain Name
 Domain Name ist z.B. für ssh wichtig
 
 Kommando | Funktion
  --- | ---
  en -> `show hosts` | Zeigt u.a den Domain Name an
  en -> `show ip domain-name` | Zeigt nur den Domain Name an
  en -> conf t -> `ip domain-name brainoftimo.com` | vergibt den Domain Name *brainoftimo.com*
