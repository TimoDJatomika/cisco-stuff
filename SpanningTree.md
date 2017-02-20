# Spanning Tree

## Was ist Spanning Tree
Kurz: Spanning sorgt dafür das Loops im Netzwerk gestoppt bzw. garnicht erst entstehen. Durch ein Loop im Netzwerk geht das ganze Netzwerk kaputt.

Lang: Spanning Tree ist ein Layer 2 Protokoll. Es wird benutzt um Radundanz und gleichzeitig Schutz im Netzwerk aufzugauen. Schutz indem es Loops im Netzwerk erkennt und diese Verindert.
Dazu benutzt Spanning Tree sogenannte BPUD's.

Das Spanning Tree Protokoll ist wohl eines der Schwierigsten und am meinsten nicht verstandenen Protololle in der Netzwerktechnik. 


## Standards und was Cisco benutzt. 
Es gibt mehrere Standards von Spanning Tree. Das *Origniale* Spanning Tree nach IEEE 802.1D ist schon ziemlich alt. Das *neue* und schneller Protokoll heißt **Rapid Spanning Tree**.

Cisco hat sein eigenes Spanning Tree. Es heißt **Per VLAN Spanning Tree** (In Cisco Welt ist das *ieee*) und **Rapid per VLAN Spanning Tree** (kurz *rapid-pvst*).

Per Default ist auf einem Cisco Gerät ieee aktiviert.

## Aktivieren von Per VLAN Rapid-Spanning Tree
`conf t -> spanning-tree mode rapid-pvst`

## Setzen der Prio
`conf t -> spanning-tree vlan 1 priority 4096`

Hier wird die Prio von VLAN 1 auf 4096 gesetzt. Somit ist dieser Switch die Root-Bridge (Es sei denn es gibt noch einen Switch mit der Prio 0)
