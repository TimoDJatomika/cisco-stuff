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

#### Vlan 1 Konfigurieren
Schritt | Funktion |Programmname 
 --- | --- | ---
1 | ip adresse vergeben | interface vlan1 

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

