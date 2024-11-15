### Analyse Réseau

- Q3.1: Le matériel A est un commutateur (ou plus communément switch). Il permet l'interconnexion des segments du réseau entre eux. Il les identifie grâce à leurs adresses MAC
- Q3.2: Le matériel B est un routeur. Il est la passerelle par défaut pour le réseau 10.10.0.0/16 (Il permet, grâce à sa table de routage, de **sortir du réseau**)
- Q3.3:
	- **f0/0** est l'interface FastEthernet0/0 du routeur B
	- **g1/0** est l'interface GigabitEthernet1/0 du routeur B
	La principale différence entre ces deux types d'interface sont leurs débits.
- Q3.4: Pour l'ordinateur **PC2**, le /16 représente son masque de sous-réseau
- Q3.5: L'adresse 10.10.255.254 ne représente rien pour PC2, car hors de son réseau (10.11.80.2/16)
- Q3.6:
	- **PC1**:
		- Adresse de réseau: 10.10.0.0/16
		- Première adresse: 10.10.0.1
		- Dernière adresse: 10.10.255.254
		- Broadcast: 10.10.255.255
	- **PC2**:
		- Adresse de réseau: 10.11.0.0/16
		- Première adresse: 10.11.0.1
		- Dernière adresse: 10.11.255.254
		- Broadcast: 10.11.255.255
	- **PC5**:
		- Adresse de réseau: 10.10.0.0/15
		- Première adresse: 10.10.0.1
		- Dernière adresse: 10.11.255.254
		- Broadcast: 10.11.255.255
- Q3.7:
	- **PC1 PC3 et PC4** communiquent entre eux et avec **PC5**
	- **PC2** communique seulement avec **PC5**
	- **PC5** communique avec **tous les PC**
- Q3.8: Tous le monde pourra atteindre le réseau 172.16.5.0/24 _**SAUF**_ **PC3** qui ne peut atteindre le routeur B
- Q3.9: Il n'y a aucune incidence, le switch utilise les adresses MAC des clients pour commuter, étant donné que c'est un switch couche 2 et non couche 3(ce dernier ayant une adresse IP programmable sur ses interfaces)
- Q3.10: Pour passer les ordinateurs en adressage dynamique, 2 modifications sont possibles:
	- Activer la fonction de serveur DHCP sur le routeur B (si disponible)
	- Ajouter un serveur DHCP au réseau et lui paramétrer ses plages d'adressage et d'exclusion en concordance avec les configurations voulues

### Analyse de Trames

- Q3.11: Adresse MAC qui a initialisé la communication: **00:50:79:66:68:00**, Il s'agit de **PC1**.
- Q3.12: La communication a **fonctionné**. En effet c'est un ping entre 2 PC connectés en ethernet: **PC1 et PC4**
- Q3.13: 
	- Matériel 1:
		- Nom: PC1
		- IP: 10.10.4.1
		- MAC: 00:50:79:66:68:00
	- Matériel 2:
		- Nom: PC4
		- IP: 10.10.4.2
		- MAC: 00:50:79:66:68:03
- Q3.14: Le  protocole encapsulé est l'**ARP**. Il permet de mettre en correspondance une adresse IP et une adresse MAC dans un tableau appelé _table ARP_. Ainsi un nœud réseau qui doit communiquer avec une IP de destination sait à quelle adresse MAC elle correspond.
- Q3.15: Dans cette communication, le **switch A** a permit d'interconnecter les nœuds concernés (PC1 et PC4) tandis que le **routeur B** n'a pas servi car la communication ne sortait pas du sous-réseau des PCs.
- Q3.16: Dans cette capture, la communication est initialisée par **PC3** dont l'adresse IP est **10.10.80.3**
- Q3.17: Le protocole encapsulé est **ICMP** (**I**nternet **C**ontrol **M**essage **P**rotocole). Il a pour rôle le contrôle d'une connexion IP (vérification si la connexion est possible, si oui, temps de réponse, taux de pertes de paquets etc)
- Q3.18: Dans cette communication, le **PC3** tente un ping vers **PC2**. Cependant, le ping **échoue** car PC2 est en dehors du sous-réseau de PC3. Nous pouvons le voir car le **routeur** à l'adresse **10.10.255.254** indique que l'hôte **10.11.80.2** (PC2) est inatteignable (car effectivement l'interface du routeur est dans le sous-réseau de PC3 mais pas de PC2) .
- Q3.19: Comme indiqué précédemment, le paquet n°2 est la réponse du **routeur** indiquant que l'adresse de destination est injoignable (car hors de portée du routeur dû à sa configuration réseau)
- Q3.20: 
	- Rôle du **Switch A**: interconnexion des hôtes **PC3 PC2 routeur B** au niveau de la couche 2 en pointant les trames vers les destinataires grâce à leurs adresses MAC
	- Rôle du **routeur B**: Vérification de ses tables de routage pour l'envoi de la trame vers le destinataire, et indication à l'émetteur de l'échec de la résolution du destinataire


- Q3.21:
	- Matériel source:
		- Nom: PC4
		- Adresse IP: 10.10.4.2
	- Matériel destination:
		- Nom: Random noeud sur internet (représentée sur le schéma par le nuage dont l'adresse réseau est  172.16.5.0/24)
		- Adresse IP: 172.16.5.253
- Q3.22:
	- Adresse MAC source: ca:01:da:d2:00:1c
	- Adresse MAC destination: ca:03:9e:ef:00:38
	L'adresse MAC source est l'adresse MAC de l'interface **g1/0 du routeur B** et l'adresse MAC destination est l'adresse MAC de l'interface **g2/0 du routeur R2**. C'est normal car la capture a été effectuée entre ces deux routeurs, les adresses IP source et destination ne changent pas malgré les rebonds, tandis que les adresses MAC sont changées par les routeurs à chaque "étape".
- Q3.23: Comme indiqué précédemment, la capture a été effectuée entre le **routeur B** et le **routeur R2**.