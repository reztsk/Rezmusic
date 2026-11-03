# RezMusic — v27

Application web musicale collaborative, gratuite, sans base de données.

## Nouveautés v27

- **Écran de lancement animé** : au démarrage, un vinyle néon rouge tourne, le nom RezMusic apparaît, une barre de chargement se remplit et trois status défilent (« Démarrage… », « Chargement du lecteur… », « Prêt. »). L'écran s'efface tout seul (environ 1,2 s), un clic ou un tap le passe immédiatement, et un filet de sécurité CSS l'efface même si le JavaScript échoue.
- **Compte Spotify lié à l'appareil** : le jeton reste dans **ce navigateur** et n'est jamais transmis. Réglages → carte Spotify : un identifiant d'appareil est affiché, et un bouton **« Mémoriser ma connexion : activé / désactivé »** permet de ne garder la session que le temps de l'onglet (ordinateur partagé).
- Rappel des v26 : sessions d'écoute à plusieurs et format PC / téléphone automatique.

## Comptes Spotify : qui voit quoi

- Chaque visiteur clique sur **Se connecter avec Spotify** et saisit **ses** identifiants sur la page officielle `accounts.spotify.com` — jamais sur RezMusic.
- Le jeton d'accès qui en résulte est stocké dans le `localStorage` de **son** navigateur (clé `rezmusic.spotify.v1`), avec un identifiant d'appareil. Un jeton venu d'un autre appareil est ignoré.
- La session d'écoute (MQTT) ne transporte que le titre, l'artiste, la pochette et l'état lecture/pause : **aucun jeton, aucune donnée de compte**.
- La déconnexion efface le jeton du navigateur (localStorage **et** sessionStorage).
- **Limite honnête** : RezMusic ne filtre pas par adresse IP, et ne le peut pas utilement — un OAuth Spotify ne fonctionne pas ainsi, et les IP changent (4G, Wi-Fi, VPN). La séparation vient du navigateur, pas du réseau : un même ordinateur partagé garde la connexion tant qu'on ne clique pas sur « Se déconnecter » — d'où le bouton « Mémoriser ma connexion : désactivé » pour les postes partagés.

## Lecture

Deux modes dans les Réglages :

- **♪ Lecture complète** (Spotify, Premium requis) : la recherche interroge Spotify, la lecture se fait dans l'app, avec repli sur le lecteur Spotify intégré si le SDK est indisponible.
- **▶ Extrait 30 s** : uniquement si l'option est activée.

## Session à plusieurs : comment ça marche

1. Celui qui a Spotify ouvre **Communauté → Créer une session** et copie le code à 6 caractères.
2. L'ami ouvre la même adresse, va dans **Communauté → Rejoindre une session**, entre le code (aucun compte Spotify nécessaire pour suivre).
3. L'hôte lance un titre : il apparaît chez tout le monde en quelques secondes, avec « en lecture » ou « en pause ».

Le son reste sur l'appareil de l'hôte : c'est un partage de ce qui est écouté, pas un flux audio.

## Paroles : source et synchronisation

Spotify ne fournit pas de paroles par son API (`/v1/lyrics` → `410`). RezMusic utilise **LRCLIB** (gratuit, sans clé, CORS ouvert). Le format `syncedLyrics` (`[mm:ss.xx]`) est préféré pour le karaoké.

## Mode « Son Discord » : la limite

Le navigateur **ne peut pas lire l'audio de Discord**. Le mode écoute le **micro** : dès qu'une voix est détectée, le volume baisse à 35 %, puis revient 20 s après la dernière voix.

## Connexion Spotify (admin)

Réglages → « Réglages développeur (privé) », ou URL `#admin`. Mot de passe local (SHA-256), Client ID modifiable, diagnostic (compte, URI de redirection, contexte, appareils). **Limite Spotify en mode développement : 5 comptes**, à ajouter dans *User Management*.

## Fonctionnalités

Écran de lancement animé, recherche instantanée, mini-lecteur, playlists déroulantes, import Spotify, paroles synchronisées, volume, historique, notes sur 5, favoris, profils avec code personnel, sessions d'écoute à plusieurs, format PC/téléphone automatique, lecteur immersif, hors-ligne.

## Lancer en local

```bash
python3 -m http.server 3000
```

## Déployer sur Vercel (gratuit)

Dépôt GitHub → Vercel → *Add New Project* → preset **Other** → *Deploy*. Puis ajouter l'URL du déploiement dans les Redirect URIs Spotify.

Mise à jour sans supprimer le dépôt : voir `GUIDE-MISE-A-JOUR.md`.

Après un envoi, faire **Ctrl + F5** : le service worker garde l'ancienne page en cache sinon.

## Fichiers

```
index.html   application complète (HTML + CSS + JS, aucune dépendance)
sw.js        service worker hors-ligne (cache rezmusic-v27)
saveia.txt   journal complet, v13 → v27 (ajout seul)
GUIDE-MISE-A-JOUR.md   mise à jour GitHub sans suppression
mettre-a-jour.ps1      script Windows (commit + push)
README.md    ce fichier
```
