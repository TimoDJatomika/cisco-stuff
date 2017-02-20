# Cisco's Access Controll Lists

Die Normalen Access Controll Listen sind nicht besonders mächtig, daher sollte man Exteded Access Controll Listen (EACL) benutzen.

**Alle ACL's anzeigen**
`show ip access`

## EACL erstellen
`conf t -> ip access-list extended EACL-VLAN10-IN`

## EACL auf Interface / VLAN anwenden
Es gibt zwei Möglichkeiten Access Listes anzuwenden. Einmal IN und einmal OUT. Um das ganze besser zu verstehen, *muss man der Router sein*.
Auf der einen Seite des Routers ist das Interne Netzwerk (IN) und auf der anderen Seite ist das andere Netzwerk (OUT)

`conf t -> vlan 10 -> ip access-group EACL-VLAN10-IN in`

## Example EACL Regeln
Die Wildcast Mask müssen immer in umgekährter Reihenfolge geschrieben werden, also 255.255.255.0 => 0.0.0.255

deny ip <from e.g. 10.10.10.0> <wildcard e.g. 0.0.0.255> <destination> <wildcard destination>

**Regel an einer bestimmten Reihenfolge setzen**
10 deny ip ...
