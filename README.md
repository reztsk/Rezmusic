# RezMusic — v34

Application web musicale collaborative, gratuite, sans base de données.

## Musique sans Spotify (nouveau)

RezMusic peut chercher et jouer les morceaux **avec YouTube**, sans compte Spotify et sans abonnement.

1. Réglages → carte **« Recherche YouTube (sans Spotify) »** → coller une **clé API YouTube Data v3** (gratuite).
2. Chercher un titre : les résultats viennent de YouTube, avec la miniature et la chaîne.
3. Toucher ▶ : la lecture se fait dans le **lecteur YouTube officiel**, affiché dans le lecteur principal (lecture, pause, suivant, précédent, volume et barre de progression sont branchés dessus).

La clé se crée en 5 minutes : `console.cloud.google.com` → nouveau projet → activer **YouTube Data API v3** → *Créer des identifiants → Clé API*, puis la restreindre à l'adresse du site. Quota gratuit : 10 000 unités/jour, environ 100 recherches.

Sans clé YouTube et sans Spotify, l'app continue de fonctionner avec iTunes/Deezer (extraits 30 s).

## Comptes Spotify : la question du jeton

Il n'existe **aucun moyen de se connecter à Spotify sans jeton**. Le jeton est le résultat du login : l'utilisateur tape son e-mail et son mot de passe **sur Spotify**, et Spotify renvoie un jeton à l'app. C'est le mécanisme d'OAuth, il n'y a pas d'alternative — ni pour Cider (qui n'est pas un client Spotify mais Apple Music), ni pour aucun site.

Deux points à savoir :

- **Le Premium n'est pas nécessaire pour obtenir le jeton.** L'autorisation OAuth et l'API Web fonctionnent avec un compte gratuit (recherche, playlists, profils). Seule la **lecture audio** dans l'app exige Premium.
- **Le Client ID, lui, doit être valide** : 32 caractères. La valeur par défaut du fichier fait 29 caractères et est refusée par l'app avant même d'appeler Spotify — c'est ce message « Client ID Spotify invalide (29 caractères au lieu de 32) ». Le champ des Réglages permet de coller le bon.

Le tableau de bord « Spotify Soloist » qui affiche « Premium requis » concerne un autre produit (clés d'API Spotify) : ce n'est pas ce que RezMusic utilise.

## Session et partage du son

- **Session** : code à 6 caractères, titre/pochette/état en direct chez les invités (aucun compte requis).
- **Partage du son (audio seul)** : l'hôte sur ordinateur (Chrome/Edge) touche « 🔊 Partager le son (audio seul) », choisit l'onglet ou l'écran en cochant « Partager l'audio ». Seul le son est transmis, la piste vidéo est coupée immédiatement. L'invité touche « Écouter le direct » et entend la musique sans Spotify ni Premium.
- Limites : iOS interdit la capture audio, pas de serveur TURN (réseaux très stricts), latence de quelques centaines de ms.

## Ce qui bloque encore côté Spotify

| Symptôme | Cause | Solution |
| --- | --- | --- |
| « Client ID Spotify invalide (29 caractères au lieu de 32) » | valeur par défaut incomplète | coller le vrai Client ID (32 caractères) dans les Réglages, ou `?client=…` |
| « INVALID_CLIENT: Invalid redirect URI » | adresse non déclarée | ajouter l'adresse affichée dans l'aide sous *Redirect URIs* |
| « unauthorized_client » | compte absent des testeurs | l'ajouter dans *User Management* (5 max en mode développement) |
| Lecture limitée | compte gratuit | règle Spotify : lecture web = Premium. Utiliser YouTube, ou ouvrir le titre dans son app Spotify |

## Fonctionnalités

Écran de lancement animé, recherche (YouTube / Spotify / iTunes / Deezer), lecteur complet, playlists déroulantes, import Spotify, paroles synchronisées LRCLIB, volume, historique, notes sur 5, favoris, profils avec code, sessions à plusieurs, partage du son WebRTC, format PC/téléphone automatique, hors-ligne.

## Lancer en local

```bash
python3 -m http.server 3000
```

Le partage de son exige un contexte sécurisé : `localhost` fonctionne, une adresse `http://` distante non.

## Déployer sur Vercel (gratuit)

Dépôt GitHub → Vercel → *Add New Project* → preset **Other** → *Deploy*. Puis ajouter l'URL du déploiement dans les Redirect URIs Spotify (si tu utilises Spotify).

Après un envoi, faire **Ctrl + F5** : le service worker garde l'ancienne page en cache sinon.

## Fichiers

```
index.html   application complète (HTML + CSS + JS, aucune dépendance)
sw.js        service worker hors-ligne (cache rezmusic-v34)
saveia.txt   journal complet, v13 → v34 (ajout seul)
GUIDE-MISE-A-JOUR.md   mise à jour GitHub sans suppression
mettre-a-jour.ps1      script Windows (commit + push)
README.md    ce fichier
```
