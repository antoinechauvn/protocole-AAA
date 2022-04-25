# protocole-AAA
Découverte du protocole AAA et des protocoles liés au AAA (RADIUS, TACACS)


### Qu'est-ce que le protocole AAA?
AAA est un protocole de sécurisation des équipements dont les trois lettres signifient : Authentication, Authorization et Accounting.

* L’Authentication
* L’Authorization (autorisation)
* L’Accounting (gestion des comptes)

## A (Authentification)

```
L’authentification consiste à vérifier qu’une personne/équipement est bien celle qu’elle prétend être.
Ceci est généralement réalisé en utilisant un secret partagé entre l’utilisateur et le serveur ou à
l’aide de certificats (e.g X.509).
```

## A (Autorisation)
```
L’autorisation consiste à permettre l’accès à certains services ou ressources.
Un utilisateur peut par exemple demander à avoir une certaine bande passante.
Le serveur AAA lui autorisera ou non cette demande
```

## A (Accounting)
```
Maintenant on garde une trace de toutes les actions effectuées par l’utilisateur. On dit que les
actions de l’utilisateur sont loguées. Un administrateur réseaux pourra ainsi, consulter les logs
afin de vérifier les actions d’un utilisateur, ou bien retrouver l’auteur de telle ou telle action.
```

## Les différents types d’authentification locale
### Authentification de type "password-only"
L’administrateur a, à sa disposition, plusieurs types d’authentification disponibles. La première, et la plus simple, consiste en une authentification par mot de passe, également appelée Authentication "password-only".
<br>
Cette méthode oblige l’utilisateur à saisir un mot de passe lorsqu’il se connecte via une des "access line" comme les vty, ou le port console
<br>
<br>
Exemple: `enable password cisco`

### Authentification sur la base locale des utilisateurs
On stocke la base de données des utilisateurs en local sur l'équipement concerné
<br>
<br>
`username {name} password {password}`
<br>
<br>
Le mot de passe sera stocké en clair dans le fichier de configuration.
<br>
Par la suite il faudra spécifier à l'équipement d'utiliser la base locale
<br>
<br>
Exemple: `login local` sur la méthode d'accès concernée.

## Les Authentifications AAA

#### Authentification AAA autonome
![image](https://user-images.githubusercontent.com/83721477/165166144-b18389ed-a4e4-4df2-af7e-a470f564afac.png)

L'authentification autonome se déroule en 3 temps:

1. Le client établit une connexion avec le routeur grâce aux protocoles telnet, SSH ou via le port console et le port de management.
2. Le routeur invite le client à saisir ses identifiants sous la forme d’un nom d’utilisateur et du mot de passe associé.<br>L’utilisateur saisit ensuite ces informations avant de valider.
3. Enfin, le routeur va comparer les données rentrées par l’utilisateur avec sa base de données locale. Une fois les identifiants vérifiés, le routeur donne l’accès au client, sinon il rejette la connexion.

#### Authentification AAA externe
![image](https://user-images.githubusercontent.com/83721477/165167310-74b086a6-4bec-46ea-98dc-da62a1e9c779.png)

Ce type d’authentification est particulièrement utilisé pour les réseaux de taille moyenne ou grande. En effet, l’équipement va aller chercher ses informations dans une base de données externe.
1. Le client établit une connexion avec le routeur grâce aux protocoles telnet, SSH ou via le port console ou le port de management.
2. Le routeur invite le client à saisir ses identifiants sous la forme d’un nom d’utilisateur et du mot de passe associé.<br>L’utilisateur saisit ensuite ces informations avant de valider.
3. Le routeur va interroger le serveur d’authentification en lui envoyant le couple identifiant - mot de passe saisi par le client.<br>Ce serveur va ensuite répondre en routeur, en validant ou en invalidant l’authentification.<br>Une fois la réponse reçue, le routeur décide de l’action à mener avec le client.


# Liste de protocoles AAA
## RADIUS
```
RADIUS signifie Remote Authentication Dial-In User Service. Il correspond à un protocole permettant de centraliser en son sein toutes les informations liées à l’authentification. Il a été développé en 1991 par la société Livingston (fabrication de serveurs), puis a été normalisé par l’IETF.
Les RFC 2865 et 2866 définissent le protocole RADIUS comme permettant d’authentifier les utilisateurs distants grâce à une centralisation des données.
Avant l’utilisation de RADIUS, nous devions dupliquer la création des comptes utilisateurs sur tous les équipements, ce qui peut devenir vite rébarbatif surtout lorsque l’on possède un grand nombre d’équipements.
```
![image](https://user-images.githubusercontent.com/83721477/165168828-f9aa6707-e9a7-4621-9e2d-ea2a7e3b27c5.png)
* L’identification est assez simple. En effet, le poste utilisateur (appelé supplicant dans les RFC) va émettre une requête d’accès à un des clients RADIUS de notre infrastructure.
* Ce sera le client RADIUS (ici le NAS - Network Attached Storage) qui va demander à l’utilisateur de saisir son couple identifiant / mot de passe. Le NAS va ensuite générer une requête appelée "Access-Request" qui va contenir les informations d’identification rentrées par l’utilisateur.
* Le serveur RADIUS qui effectue l’identification finale (puisque certains serveurs RADIUS servent uniquement de "relais" au serveur final) peut demander des informations complémentaires grâce à un paquet "Access-Challenge" pour lequel le client répondra à nouveau par un "Access-Request".
* Lorsque le serveur RADIUS dispose d’assez d’éléments, il valide ou rejette l’authentification en utilisant les paquets "Access-Accept" et "Access-Reject".
* Pour authentifier les clients, le serveur RADIUS partage avec eux une clé secrète.

```
Le serveur RADIUS est généralement une application logicielle exécutée sur un système d'exploitation.
Un seul serveur RADIUS peut recevoir et traiter de nombreuses demandes d'accès simultanément.
Un serveur unique peut également interagir avec des fichiers, des bases de données SQL,
des répertoires LDAP ou d'autres serveurs RADIUS.
```

*Note: L'ensemble des transactions entre le client RADIUS et le serveur RADIUS est chiffrée et authentifiée grâce à un secret partagé*<br>
*Note: Il est à noter que le serveur RADIUS peut faire office de proxy, c'est-à-dire transmettre les requêtes du client à d'autres serveurs RADIUS.*<br>
*Note: Tout équipement peux être un client RADIUS du moment que l'on renseigne l'adresse IP*

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

TACACS+ signifie Terminal Access Controller Access-Control System Plus et permet d’effectuer un contrôle d’accès pour les équipements réseau grâce à un équipement (serveur) qui centralise l’ensemble des informations liées à l’authentification des clients.

La communication entre le "supplicant", le client TACACS+ et le serveur TACACS+ ne se passe pas exactement de la même manière que pour le protocole RADIUS.
![image](https://user-images.githubusercontent.com/83721477/165100603-a3ccf9ee-71f7-48c8-b5d6-df9ad74750b1.png)
* Lorsque l’utilisateur cherche à se connecter au routeur, celui-ci va interroger le serveur TACACS+ pour savoir quelle action réaliser.
* Le serveur TACACS+ répond qu’il faut demander le nom d’utilisateur.
* Le routeur demande ensuite le nom d’utilisateur au client.
* Ce nom d’utilisateur est transmis jusqu’au serveur TACACS+ qui va ensuite demander au routeur d’effectuer la demande du mot de passe.
* De la même manière, le serveur TACACS+ demande au routeur d’effectuer la demande du mot de passe auprès du client.
* Une fois les informations récupérées, le serveur décide de valider ou de rejeter la demande de connexion.

![image](https://user-images.githubusercontent.com/83721477/165077387-439f3e5a-ca7d-4fdf-816f-e23f73e4faca.png)
