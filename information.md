# Cisco Switch - Basic Infos


#### Grundkonfiguration
 Schritt | Funktion |Programmname 
 --- | --- | ---
1|Hostname ändern | enable -> conf t -> hostname *mycool switch*  
2|kein DNS Lookup | enable -> conf t -> no ip domain lookup
3|Inline status Meldungen Deaktivieren| conf t -> line cons 0 -> logging synchronous
4|change position | ändert position
5|klartext Passwörter ausschalten | en -> conf t -> service password-encryption
6|Uhrzeit setzen|en -> conf t -> clock set 19:43:43 5 Apr 2015
7|verify clock| en -> show clock

#### VLAN auf Layer 2
`en -> vlan database`

#### Vlan 1 Konfigurieren
Schritt | Funktion |Programmname 
 --- | --- | ---
1 | ip adresse vergeben | `interface vlan1` 

## Infos zu VTP
- VTP nicht benutzen !

#### Vlan 1 Konfigurieren
Schritt | Funktion |Programmname 
 --- | --- | ---
1 | ip adresse vergeben | interface vlan1 
 | Pool anzeigen wer welche ip hat | show ip dhcp binding

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

### Neuen Pool erstellen
Schritt | Kommando | Funktion
 --- | --- | ---
 1 | en -> conft -> `ip dhcp pool <name-von-pool>` | erstellt einen neuen DHCP-Pool 
 2 | `network 10.10.10.0 /24` | Netzwerkmaske Konfigurieren (wichtig nach der 0 ein Leerzeichen) 
 3 | `default-router 10.10.10.1` | Gibt an was das default Gateway ist
 4 | `dns-server 8.8.8.8` | legt den dns server fest
