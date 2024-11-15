
- Q1.1: Les PC ne ping pas car ils ne sont pas sur le même réseau 
	![IPCONFIG_SRV](https://github.com/Waxausaurus/ImgStorage/blob/main/Q1.1/IPCONFIG_SRV.png?raw=true)
	![IPCONFIG_CLIENT](https://github.com/Waxausaurus/ImgStorage/blob/main/Q1.1/IPCONFIG_CLIENT.png?raw=true)
	- Modification de l'ip du client :
		Panneau de configuration > Centre réseau et partage > Modifier les paramètres de la carte
		Clic droit sur la carte > Propriétés > Double clic sur la ligne concernant IPv4 
		![IPFIXECLIENT](https://github.com/Waxausaurus/ImgStorage/blob/main/Q1.1/IPFIXECLIENTQ1.1.png?raw=true)
	- Ping fonctionnel
		![PINGCLIENTSRV](https://github.com/Waxausaurus/ImgStorage/blob/main/Q1.1/PING_CLIENTSRV.png?raw=true)
- Q1.3: Le client ne récupère pas la première IP de la plage car une plage d'exclusion est paramétrée côté serveur (plage de 172.16.10.1 à 172.16.10.19). Le client récupère donc la première IP hors plage d'exclusion: 172.16.10.20 
	![IPCLIENTDHCP](https://github.com/Waxausaurus/ImgStorage/blob/main/Q1.3/IPCLIENTDHCP.png?raw=true)
- Q1.4: Il est effectivement possible que le client récupère l'adresse 172.16.10.15. Il faut pour cela lui réserver l'adresse côté serveur.
  Pour se faire, il faut créer une nouvelle réservation et insérer l'adresse MAC du client et l'adresse IP destinée à ce client
  ![CONFRESA](https://github.com/Waxausaurus/ImgStorage/blob/main/Q1.4/CONFIGRESA.png?raw=true)
  Il suffit ensuite de faire un ipconfig /release sur le client puis un ipconfig /renew pour appliquer la nouvelle IP
  ![IPCONFIGRESA](https://github.com/Waxausaurus/ImgStorage/blob/main/Q1.4/IPCONFIGRESA.png?raw=true)
  