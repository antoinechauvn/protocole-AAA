# protocole-AAA
Découverte du protocole AAA et des protocoles liés au AAA (RADIUS, TACACS)


### Qu'est-ce que le protocole AAA?
AAA correspond à un protocole qui réalise trois fonctions :
* l'authentification
* l'autorisation
* la traçabilité

L’architecture AAA permet aux utilisateurs d’accéder aux périphériques en réseau qui leur ont été attribués et protège le réseau contre les accès non autorisés

## A (Authentification)

```
L’authentification désigne le fait de prouver qu’on est bien la personne que l’on prétend être.
L’authentification vient en complément de l’identification. Pour s’authentifier, on ajoute une
preuve à l’identification. Ce sont ces preuves qu’on appelle facteurs d’authentification.
```

## A (Autorisation)
```
Elle agit une fois que l’utilisateur s’est authentifié. C’est dans cette phase qu’on donne ou
non accès à la ressource demandée, en fonction de la politique de contrôle d’accès.
Peu importe la politique utilisée, elle reste basée sur trois principes :

Classification des informations : Chaque information nécessite un certain niveau d’accès pour les consulter.
Niveau d’accès des utilisateurs : Détermine le niveau d’accès de chaque utilisateur.
Permissions de l’utilisateur : Détermine quels droits l’utilisateur aura sur les fichiers, lecture, écriture, lecture et écriture.

Grâce à ses trois principes, une fois l’utilisateur authentifié il lui sera attribué certains
droits sur certains fichiers. Bien sûr, cela peut aussi être des droits plus simples, mais pas
moins importants, comme accéder au serveur mail ou autoriser le sujet à accéder à un secteur
physique de l’entreprise.
```
![VLAN-SPOOF1](https://user-images.githubusercontent.com/83721477/164458470-57e6dcd1-3349-401f-8920-e45d5fe31835.png)
___
![VLAN-SPOOF2](https://user-images.githubusercontent.com/83721477/164458527-9d8c040d-b28c-4a6e-8c12-21736ef88fa5.png)
___
![VLAN-SPOOF3](https://user-images.githubusercontent.com/83721477/163724759-5c1c153e-a023-4428-b726-2eab1405165d.png)

1. On commence par envoyer des trames DTP
2. Si le mode DTP est en DYNAMIC AUTO ou DYNAMIC DESIRABLE alors l'attaque est possible
3. On envoie une demande de négociation pour basculer le lien en mode trunk

### Remédiations
* Ne configurez aucun port avec l'un des modes suivants : `dynamic desirable`, `dynamic auto` ou `trunk`<br>
* Définissez manuellement la configuration de tous les ports de jonction (trunk) et désactivez DTP.<br>
`switchport mode trunk`<br>
`switchport nonegotiate`

* Ensuite, il faut s'assurer que les ports non configurés en trunk sont configurés en port d'accès (toujours sans DTP)<br>
`switchport mode access`<br>
`switchport nonegotiate`

## Double-Tagging

```
L'attaque double-tagging (ou double encapsulation) est un autre type d'attaque par saut de VLAN. Ce type
d'attaque tire parti de la manière dont le matériel de la plupart des commutateurs fonctionne. La majorité
des commutateurs réalisent un seul niveau de désencapsulation 802.1Q, ce qui permet à un pirate d'intégrer
un tag 802.1Q masquée à l'intérieur de la trame. Ce tag permet à la trame d'être transférée vers un VLAN
que le tag 802.1Q d'origine n'a pas spécifié. Il est important de noter que l'attaque par saut de VLAN de
double encapsulation fonctionne même si les ports trunk sont désactivés, car un hôte envoie généralement
une trame sur un segment qui n'est pas une liaison trunk.
```
![DOUBLE-TAGGING1](https://user-images.githubusercontent.com/83721477/163726501-e83d0cf9-9498-4e44-a0c1-e65f143748ac.png)
___
![DOUBLE-TAGGING2](https://user-images.githubusercontent.com/83721477/163726708-1e2ad643-6e2f-41dc-a896-5f1d29d3d786.png)
___
![DOUBLE-TAGGING3](https://user-images.githubusercontent.com/83721477/163726750-a8888e07-da47-4653-ba69-071530a256e1.png)
___
![DOUBLE-TAGGING4](https://user-images.githubusercontent.com/83721477/163726808-3f579887-4a35-4b3a-850b-d104994fc5d5.png)

1. Le pirate envoie une trame 802.1Q marquée de deux tags au commutateur. L'en-tête externe porte le tag VLAN du pirate, qui est identique au VLAN natif du port trunk. Supposons que le commutateur traite la trame envoyée par le pirate comme si elle se trouvait sur un port trunk ou un port disposant d'un VLAN voix (un commutateur ne doit pas recevoir de trame Ethernet avec un tag sur un port d'accès). Dans cet exemple, supposons que le VLAN natif est le VLAN 1. Le tag interne est le VLAN victime ; dans ce cas, il s'agit du VLAN 2.
2. La trame arrive sur le commutateur, qui vérifie le premier tag 802.1Q de 4 octets. Le commutateur détecte que la trame est destinée au VLAN 1, qui est le VLAN natif. Le commutateur transfère le paquet par tous les ports du VLAN 1 après avoir éliminé le tag du VLAN 1. Sur le port trunk, le tag du VLAN 1 est éliminée et le paquet n'est pas taggué de nouveau, car il fait partie du VLAN natif. À ce stade, le tag du VLAN 2 reste inchangée et n'a pas été inspectée par le premier commutateur.
3. Le deuxième commutateur examine uniquement le tag 802.1Q interne que le pirate a envoyée et constate que la trame est destinée au VLAN 2, le VLAN cible. Il envoie la trame sur le port victime ou la diffuse, selon qu'il existe une entrée de table d'adresse MAC pour l'hôte victime.

*Note: Ce type d'attaque est unidirectionnel et ne fonctionne que si le pirate est connecté à un port se trouvant dans le même VLAN que le VLAN natif du port trunk. Ce type d'attaque est plus difficile à contrer que les simples attaques par saut de VLAN.*

### Remédiations

La technique de Double Tagging n'est exploitable que sur les ports de switch configurés pour utiliser des VLAN natifs.<br>
* Il est important de ne pas utiliser le VLAN par défaut (VLAN 1)<br>
`switchport access vlan 2`

* Remplacez le VLAN natif de tous les ports en mode trunk par un ID de VLAN inutilisé<br>
`switchport trunk native vlan 999`

* Vous pouvez également forcer le marquage explicite du VLAN natif sur tous les ports en mode trunk<br>
`vlan dot1q tag native`
