# Cisco Switch - Basic Infos

## heute
- wie mache ich eigentlich ne Tabelle?
- Waurm mach ich diese Tableel
- und was kann cih ncoh machen?

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
