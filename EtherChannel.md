# Etherchannel

Cisco Geräte unterstützen zwei unterschiedliche Arten von Etherchannel. Einmal PAgP (von Cisco) und LACP (nach IEEE 802.3ad).
Da in den meinsten Netzwerken nicht nur Cisco Geräte zu finden sind, wird empfohlen den IEEE Standard **LACP** zu benutzen.

Übersicht über etherchannel:
`show etherchannel summery` 

Während der Konfiguration sollte man die Ports per `shutdown` deaktivieren.

Im Fongenden wird ein Switch in den **active** Mode gesetzt und der andere Switch in den **passive** mode gesetzt.

## config switch1
conf t

interface range fa0/1-4

shutdown

duplex full
   
speed 100 ! bei bedarf ändern

switchport mode trunk

switchmode trunk native vlan 99

channel-group 1 mode active ! um eine Etherchannel Gruppe zu erstellen mit LACP

no shutdown
 
  
## config switch2
conf t

interface range fa0/1-4

shutdown

duplex full

speed 100 ! bei bedarf ändern

switchport mode trunk

switchmode trunk native vlan 99

channel-group 1 mode passiv ! um eine Etherchannel Gruppe zu erstellen mit LACP

no shutdown


