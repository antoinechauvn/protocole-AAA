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

## A (Accounting)
```
Maintenant on garde une trace de toutes les actions effectuées par l’utilisateur. On dit que les
actions de l’utilisateur sont loguées. Un administrateur réseaux pourra ainsi, consulter les logs
afin de vérifier les actions d’un utilisateur, ou bien retrouver l’auteur de telle ou telle action.

La traçabilité est très importante pour assurer une bonne sécurité et une intervention rapide en
cas de problèmes. Elle agit tout d’abord de manière préventive, les utilisateurs se sachant
surveillés, ils font attention à leurs agissements. Ensuite, grâce à la traçabilité on peut détecter
des actions suspectes voir interdites et mieux cibler la source d’un problème pour intervenir,
ou corriger celui-ci. Enfin, elle permet d’avoir une trace légale des actions effectuées sur
le système d’information de l’entreprise.
```

![image](https://user-images.githubusercontent.com/83721477/165077387-439f3e5a-ca7d-4fdf-816f-e23f73e4faca.png)

# Liste de protocoles AAA
## RADIUS

```
Le protocole RADIUS (Remote Authentication Dial-In User Service), mis au point initialement
par Livingston, est un protocole d'authentification standard, défini par un certain nombre de RFC.

Le fonctionnement de RADIUS est basé sur un système client/serveur chargé de définir les accès
d'utilisateurs distants à un réseau. Il s'agit du protocole de prédilection des fournisseurs d'accès à
internet car il est relativement standard et propose des fonctionnalités de comptabilité permettant
aux FAI de facturer précisément leurs clients.
```

### Fonctionnement
```
Le protocole RADIUS repose principalement sur un serveur (le serveur RADIUS), relié à une base
d'identification (base de données, annuaire LDAP, etc.) et un client RADIUS, appelé NAS
(Network Access Server), faisant office d'intermédiaire entre l'utilisateur final et le serveur.
L'ensemble des transactions entre le client RADIUS et le serveur RADIUS est chiffrée et authentifiée
grâce à un secret partagé.

Il est à noter que le serveur RADIUS peut faire office de proxy, c'est-à-dire transmettre les requêtes
du client à d'autres serveurs RADIUS.

Le serveur RADIUS est généralement une application logicielle exécutée sur un système d'exploitation.
Un seul serveur RADIUS peut recevoir et traiter de nombreuses demandes d'accès simultanément.
Un serveur unique peut également interagir avec des fichiers plats, des bases de données SQL,
des répertoires LDAP ou d'autres serveurs RADIUS.
```
*Note: Tout équipement peux être un client RADIUS du moment que l'on renseigne l'adresse IP*

<br>

#### Serveur NAS (Network Access Server)
```
Le serveur d'accès au réseau (NAS) agit comme une passerelle entre l'utilisateur
et le réseau plus large. Lorsqu'un utilisateur essaie d'obtenir un accès au réseau, le NAS transmet les
informations d'authentification (par exemple, le nom d'utilisateur et le mot de passe) entre
l'utilisateur et le serveur RADIUS. Ce processus est appelé session d'authentification.
Notez que la connexion de l'utilisateur lance cette conversation de session d'authentification.
```


![radius](https://user-images.githubusercontent.com/83721477/165078509-7b656c0a-1402-4499-84f0-2ed21d5ec0cd.jpg)

1. Un utilisateur envoie une requête au NAS afin d'autoriser une connexion à distance
2. Le NAS achemine la demande au serveur RADIUS
3. Le serveur RADIUS consulte la base de données d'identification afin de connaître le type de scénario d'identification demandé pour l'utilisateur. Soit le scénario actuel convient, soit une autre méthodes d'identification est demandée à l'utilisateur. <br>
Le serveur RADIUS retourne ainsi une des quatre réponses suivantes:
* ACCEPT : l'identification a réussi
* REJECT : l'identification a échou
* CHALLENGE : le serveur RADIUS souhaite des informations supplémentaires de la part de l'utilisateur et propose un « défi » (en anglais « challenge ») 
* CHANGE PASSWORD : le serveur RADIUS demande à l'utilisateur un nouveau mot de passe. Change-password est un attribut VSA (Vendor-Specific Attributes) , c'est-à-dire qu'il est spécifique à un fournisseur, et dans ce cas, c'est un attribut de Microsoft et pour être plus précis, celui de MS-Chap v2. Il n'appartient pas aux attributs radius standard définis dans la RFC 2865.

4. Suite à cette phase dit d'authentification, débute une phase d'autorisation où le serveur retourne les autorisations de l'utilisateur.

## TACACS et TACACS+

TACACS est un protocole d'authentification plus ancien, commun aux réseaux d’Unix qui permet à un serveur d'accès à distance d'expédier le mot de passe de la procédure de connexion d'un utilisateur à un serveur d'authentification pour déterminer si on peut permettre l'accès à un système donné. TACACS est un protocole non codé et donc moins sécurisé que les protocoles postérieurs de TACACS et de RADIUS.
Une version postérieure de TACACS est TACACS+ (TACACS étendu). Malgré son nom, TACACS+ est un protocole entièrement nouveau. TACACS et RADIUS ont généralement remplacé les protocoles précédents dans les réseaux plus récemment établis.
TACACS utilise TCP. Quelques administrateurs recommandent d'utiliser TACACS parce que TCP est vu comme un protocole plus fiable. De plus, alors que RADIUS combine l'authentification et l'autorisation dans un profil d'utilisateur, TACACS quant à lui sépare les deux. TACACS et TACACS+ fonctionnent toujours sur beaucoup de systèmes anciens.
