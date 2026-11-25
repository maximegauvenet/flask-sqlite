# 📒 Mon carnet de bord

**Nom :** Gauvenet

**Prénom :** Maxime

**Adresse de mon site :** https://mgauvenet.pythonanywhere.com

**Adresse de mon dépôt GitHub :** https://github.com/maximegauvenet/flask-sqlite

> Modifiez ce fichier directement sur GitHub (icône crayon ✏️) pour répondre aux questions de chaque exercice.
> Écrivez avec vos propres mots : une réponse courte et juste vaut mieux qu'un long copier-coller ! 🙂

---

## 🟢 Exercice 1 — Explorer la base en SQL

1. Expliquez avec vos mots la différence entre une **table**, une **colonne** et une **ligne**.

   > _Ma réponse :_ Une **table** est le tableau global (ex : les clients), une **colonne** est une catégorie d'info (nom, prénom, adresse) et une **ligne** correspond à un seul client enregistré.

2. À quoi sert la clé primaire `id` ? Pourquoi ne pas simplement identifier un client par son nom ?

   > _Ma réponse :_ L'`id` donne un numéro unique à chaque client. On n'utilise pas le nom car deux clients peuvent avoir le même nom de famille.

3. Pourquoi votre client ajouté en console est-il apparu sur le site ?

   > _Ma réponse :_ Parce que la console et le site utilisent exactement le même fichier `database.db`.

4. Que signifie `NOT NULL` dans `schema.sql` ? Quelle erreur avez-vous obtenue avec `INSERT INTO clients (nom) VALUES ('TEST');` ?

   > _Ma réponse :_ `NOT NULL` veut dire que le champ est obligatoire. La requête a renvoyé `NOT NULL constraint failed` car le prénom et l'adresse manquaient.

🧩 **Défi** — mes requêtes / ma solution :

   > ```sql
   > SELECT * FROM clients WHERE adresse LIKE '%Paris%' ORDER BY nom;
   > SELECT COUNT(*) FROM clients;
   > ```

---

## 🟢 Exercice 2 — Afficher les données proprement

1. Décrivez avec vos mots le trajet d'une requête, du navigateur jusqu'à la base de données, puis le retour.

   > _Ma réponse :_ Le navigateur demande une URL à Flask, qui exécute le code Python, interroge SQLite, récupère les données, les met dans le HTML et renvoie la page au navigateur.

2. Pourquoi faut-il **fermer** la connexion (`conn.close()`) après chaque requête ?

   > _Ma réponse :_ Pour libérer la mémoire et éviter de bloquer le fichier de base de données pour les autres utilisateurs.

3. Quel est l'avantage d'écrire `row['nom']` plutôt que `row[2]` ? (Pensez à ce qui arriverait si on ajoutait une colonne au milieu de la table.)

   > _Ma réponse :_ C'est plus lisible et si on ajoute une colonne dans la table, `row[2]` pointera sur la mauvaise donnée alors que `row['nom']` marchera toujours.

4. Pourquoi est-il préférable de trier avec `ORDER BY` dans la requête SQL plutôt qu'en Python ?

   > _Ma réponse :_ SQLite est optimisé pour trier très vite les données directement à la source, ce qui est plus rapide et plus simple que de le faire en Python.

---

## 🟢 Exercice 3 — Rechercher un client

1. Quelle est la différence entre `WHERE nom = 'DUPONT'` et `WHERE nom LIKE '%DUP%'` ?

   > _Ma réponse :_ `=` cherche le mot exact (`DUPONT`), alors que `LIKE '%DUP%'` cherche tous les noms qui contiennent `DUP` (comme `DUPONT` ou `DUPUIS`).

2. Expliquez avec vos mots ce qu'est une **injection SQL**. Qu'est-ce qu'un pirate pourrait faire de pire qu'afficher tous les clients ?

   > _Ma réponse :_ C'est le fait de taper du code SQL dans un formulaire pour pirater la requête. Au pire, il peut modifier ou supprimer toute la base de données (`DROP TABLE` ou `DELETE`).

3. Pourquoi la version avec `?` est-elle protégée contre l'injection ?

   > _Ma réponse :_ Le `?` traite ce que tape l'utilisateur comme du simple texte et jamais comme une commande SQL.

4. Pour la recherche, on a utilisé `method="get"`. Quel avantage pour l'utilisateur ? (Indice : favoris, partage de lien.)

   > _Ma réponse :_ Les paramètres sont visibles dans l'URL, ce qui permet de partager le lien ou de le mettre en favoris.

🧩 **Défi** — mes requêtes / ma solution :

   > ```python
   > cursor.execute('SELECT * FROM clients WHERE UPPER(nom) = UPPER(?)', (nom,))
   > ```

---

## 🔵 Exercice 4 — Enregistrer un client

1. Quelles étaient les deux anomalies du formulaire d'origine, et d'où venaient-elles ?

   > _Ma réponse :_ Dans `flask_app.py`, `nom` récupérait le champ `adresse` et la variable `adresse` n'existait pas, ce qui causait une erreur 500 (`NameError`).

2. Pourquoi la validation côté **navigateur** (`required`) ne suffit-elle pas ? Pourquoi la garder quand même ?

   > _Ma réponse :_ Elle ne suffit pas car on peut la supprimer facilement avec F12, mais on la garde pour éviter à un utilisateur normal de recharger la page s'il oublie un champ.

3. À quoi sert `conn.commit()` ? Que se passe-t-il sans lui ?

   > _Ma réponse :_ Il sauvegarde définitivement les changements dans `database.db`. Sans lui, le nouveau client n'est jamais enregistré.

4. Pourquoi redirige-t-on vers `/consultation/` après l'enregistrement, au lieu d'afficher directement une page ? (Indice : essayez d'appuyer sur F5 après l'envoi.)

   > _Ma réponse :_ Pour éviter que le client soit enregistré en double si l'utilisateur appuie sur F5 pour rafraîchir la page.

---

## 🔵 Exercice 5 — Modifier et supprimer

1. Que signifie CRUD ? Donnez la requête SQL correspondant à chaque lettre.

   > _Ma réponse :_ **C**reate, **R**ead, **U**pdate, **D**elete (créer, lire, modifier, supprimer). Les requêtes SQL correspondantes sont `INSERT`, `SELECT`, `UPDATE` et `DELETE`.

2. Que se passe-t-il si l'on oublie le `WHERE` dans un `UPDATE` ou un `DELETE` ? À quoi servent `BEGIN` et `ROLLBACK` ?

   > _Ma réponse :_ La requête s'applique à **toutes** les lignes de la table : tous les clients seraient modifiés ou supprimés d'un coup. `BEGIN` démarre une transaction et `ROLLBACK` l'annule pour revenir à l'état d'avant, ce qui permet de tester une requête dangereuse sans rien casser.

3. Pourquoi ne faut-il **jamais** supprimer des données avec une requête GET (un simple lien) ? (Indice : les moteurs de recherche et certains navigateurs « visitent » les liens à l'avance.)

   > _Ma réponse :_ Parce qu'un simple lien peut être « visité » sans que l'utilisateur le veuille (moteur de recherche, préchargement du navigateur) et supprimerait des données. Une requête GET doit seulement lire : pour supprimer on utilise un formulaire POST (avec une confirmation).

4. Une fois un `id` supprimé, pourquoi est-il préférable qu'il ne soit jamais réattribué à un autre client ?

   > _Ma réponse :_ Parce que si l'`id` était réattribué, les anciennes références (commandes, liens, favoris) pointeraient vers le mauvais client. Avec `AUTOINCREMENT`, un numéro supprimé n'est jamais réutilisé.

🧩 **Défi** — mes requêtes / ma solution :

   > La suppression logique permet de récupérer un client supprimé par erreur (il suffit de repasser `actif` à 1). On garde aussi l'historique : les commandes du client restent cohérentes, et il n'y a plus d'erreur de clé étrangère à la suppression.

---

## 🟣 Exercice 6 — Faire évoluer la base

1. Quelle est la différence entre le **schéma** d'une base et ses **données** ? Où se trouve chacun ?

   > _Ma réponse :_ Le **schéma** est la structure de la base (les tables et les colonnes), décrite dans `schema.sql`. Les **données** sont le contenu (les clients), qui vit uniquement dans `database.db` sur le serveur.

2. Pourquoi ne faut-il pas relancer `create_db.py` sur une application en production ?

   > _Ma réponse :_ Parce que `create_db.py` fait un `DROP TABLE` puis recrée la table : toutes les données saisies par les vrais utilisateurs seraient effacées.

3. Pourquoi est-il important de garder `schema.sql` à jour, même si on a modifié la base avec `ALTER TABLE` ?

   > _Ma réponse :_ Pour que la structure décrite dans le code reste identique à celle de la vraie base. Sinon, si quelqu'un recrée la base plus tard avec `schema.sql`, la colonne `telephone` serait absente et l'application planterait.

4. Pour ajouter **une** information, combien de fichiers avez-vous dû modifier ? Lesquels ?

   > _Ma réponse :_ 5 fichiers : `schema.sql`, `formulaire.html`, `flask_app.py` (les fonctions `enregistrer_client()` et `modifier_client()`), `read_data.html` et `modifier_client.html` (plus la commande `ALTER TABLE` sur le serveur).

---

## 🟣 Exercice 7 — Relier deux tables

1. Qu'est-ce qu'une **clé étrangère** ? Dans quelle table se trouve-t-elle ici, et pourquoi pas dans l'autre ?

   > _Ma réponse :_ 

2. Pourquoi est-ce une mauvaise idée de créer des colonnes `commande1`, `commande2`… dans la table `clients` ?

   > _Ma réponse :_ 

3. À quoi sert un `JOIN` ? Et un `GROUP BY` ?

   > _Ma réponse :_ 

4. Pour la bibliothèque, quelles tables imaginez-vous ? Lesquelles contiendront des clés étrangères ?

   > _Ma réponse :_ 

---

## 🔴 Exercice 8 — Contrôle d'accès

1. Quelle est la différence entre **authentification** et **autorisation** ?

   > _Ma réponse :_ 

2. Où est stockée la session ? Comment le serveur sait-il qui vous êtes à chaque nouvelle requête ?

   > _Ma réponse :_ 

3. Pourquoi masquer un bouton dans le HTML ne suffit-il pas à protéger une action ?

   > _Ma réponse :_ 

4. Que signifient les codes `302` et `403` ? Dans quel cas votre application renvoie-t-elle chacun d'eux ?

   > _Ma réponse :_ 

5. Le mot de passe `password` est écrit en clair dans `flask_app.py`, sur un dépôt **public**. Quel est le risque ? Quelle solution avez-vous déjà rencontrée dans cet atelier pour cacher une information sensible ?

   > _Ma réponse :_ 

---

## 🔴 Exercice 9 — Créer une API

1. Quelle est la différence entre une page HTML et une réponse JSON ? À qui chacune est-elle destinée ?

   > _Ma réponse :_ 

2. Que signifient les codes `200`, `201`, `400`, `401` et `404` renvoyés par votre API ?

   > _Ma réponse :_ 

3. Pourquoi une API renvoie-t-elle une erreur JSON plutôt qu'une redirection vers la page de connexion ?

   > _Ma réponse :_ 

4. Citez une application de votre téléphone qui, selon vous, utilise une API. Quelles données échange-t-elle ?

   > _Ma réponse :_ 

---

## 📚 Projet bibliothèque

1. Pourquoi ne supprime-t-on pas un emprunt quand le livre est rendu ?

   > _Ma réponse :_ 

2. Pourquoi calculer la disponibilité d'un livre plutôt que de la stocker dans une colonne ?

   > _Ma réponse :_ 

3. Quelle a été la plus grande difficulté rencontrée pendant le projet, et comment l'avez-vous surmontée ?

   > _Ma réponse :_ 

4. Quelles fonctionnalités n'avez-vous pas eu le temps de réaliser ? Comment vous y prendriez-vous ?

   > _Ma réponse :_ 

---

## 🏁 Pour conclure

En quelques phrases, expliquez à un ami ce qui se passe entre le moment où il clique sur « Enregistrer » dans un formulaire et celui où l'information s'affiche dans une liste :

> _Ma réponse :_ 
