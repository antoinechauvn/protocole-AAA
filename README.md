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

# Liste de protocoles AAA
## RADIUS

