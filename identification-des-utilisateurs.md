# Bonnes pratiques sur l'identification des utilisateurs

Ce document recense les bonnes pratiques pour développer un identification pour les utilisateurs.

## Sécurité

### Enregistrement

Pour éviter les spammers: 
* vous pouvez ajouter un champ non visible qui ne doit pas être rempli (`display: none;`, `visibility: hidden;`, `opacity: 0;` or `type='hidden'`.
* Mettre le formulaire d'enregistrement invisible et l'afficher via un click, car beaucoup de bots n'utilisent pas de système de souris.
* Compter le temps pour remplir un formulaire en le cryptant (pour que le bot ne puisse pas le changer)

### Identification

* Toujours utiliser HTTPS en production car en HTTP toutes les saisie passent en clair sur le réseau
* Ne pas faire son système de cryptage en javascript côté client (navigateur)
* Ne pas utiliser les CAPTCHAS : c'est souvent très difficile à lire pour l'humain, encore plus dur pour les daltoniens s'il y a de la couleur. De plus, il existe de nombreux programmes pour cracker les captchas donc ce n'est pas le meilleur moyen.
* Ne pas stocker les mots de passe en clair dans une base de données :
  * Utiliser Argon2, **bcrypt**, scrypt ou PBKDF2 pour hacher les mots de passe en ajoutant du "bruit". Donc un même mot de passe peut avoir des empruntes différentes.
  * S'assurer que l'emprunte est unique dans la base de données, sinon, regénérer le mot de passe
* Pour les sessions (reconnexion nécessaire à chaque lancement du navigateur) (voir [article](https://owasp.org/www-community/attacks/Session_fixation)). Sinon, pour garder la session il y a des technique comme JWT (JSON Web Token)
* Rester connecter, lire cet [article](https://paragonie.com/blog/2015/04/secure-authentication-php-with-long-term-persistence)
* Ne pas implémenter de questions secrète (Sarah Palin, une femme politique américaine, s'est faite avoir avec le nom de son école)
* **MOT DE PASSE OUBLIE ?** :
  * Ne jamais envoyer le mot de passe en clair à la personne
  * Envoyer un lien (avec expiration) pour le réinitialiser puis désactiver le lien après changement du mot de passe
* Vérifier la force du mot de passe, [article](https://dropbox.tech/security/zxcvbn-realistic-password-strength-estimation)
  * Imposer des règles (minimum de caractères et d'autres règles)
  * Afficher un indicateur
  * Vérifier si le mot de passe a été compromis avec [Have I been pwned](https://haveibeenpwned.com/API/)
* Gérer les tentatives infructueuses :
  * Vous pouvez déjà limiter à un essai par seconde, pour cracker un mot de passe de 6 caractère cela prendrait un temps fou !
  * Ne pas utiliser de captcha (voir plus haut)
  * Ne pas vérouiller le compte après X tentatives infructueuses, une attaque [DoS](https://fr.wikipedia.org/wiki/Attaque_par_d%C3%A9ni_de_service) pourrait être en attente
  * Allonger le délai à chaque tentative infructueuse
    * Méthode 1
      * Essai 1 : pas d'attente
      * Essai 2 : attente 2 secondes
      * Essai 3 : attente 4 secondes
      * Essai 4 : attente 8 secondes
      * Essai 5 : attente 16 secondes
      * ...
    * Méthode 2 : par contre cela pénalise fortement l'utilisateur (ça m'arrive de taper 10 mots de passes différents pour retrouver lequel j'avais utilisé)
      * Essai 1 à 4 : pas de délai
      * Essai 5 ou plus : attente 15 à 30 minutes
    * Méthode 3 : combiner les 2 méthodes
      *  Essai 1-4 : pas d'attente
      *  Essai 5 ou plus : attente de 20 secondes
    * Méthode 4
      * Essai 1 : attente 5 secondes
      * Essai 2 : attente 15 secondes
      * Essai 3 et plus : attente de 45 secondes
* Surveiller le nombre de tentatives infructueuse de manière globale : le compte peut être attaqué depuis plusieurs endroits (donc plusieurs IP). [Article en anglais](https://stackoverflow.com/questions/479233/what-is-the-best-distributed-brute-force-countermeasure)
* Proposer la double identification (2FA)

## Conception
