# RezMusic — v26

Application web musicale collaborative, gratuite, sans base de données.

## Nouveautés v26

- **Sessions d'écoute à plusieurs** : l'hôte crée une session (onglet *Communauté*), obtient un code à 6 caractères et le partage. Le ou les invités entrent le code et voient en direct le titre, la pochette, l'artiste et l'état lecture/pause, sur leur propre appareil.
  - **Un invité n'a pas besoin de compte Spotify** pour rejoindre et suivre la session : il voit tout ce que l'hôte écoute.
  - En revanche, il **n'entend rien** sans Spotify ni extrait : aucun site web ne peut transmettre le son d'un autre appareil (limite du navigateur, pas du code). L'app le dit clairement dans l'interface.
  - Transport : MQTT public (`wss://broker.emqx.io:8084/mqtt`), aucun compte, aucune clé, rien à installer. Le code de session est le seul secret : toute personne qui l'a peut suivre la session.
  - Présence : participants listés avec pseudo et avatar, battement de cœur toutes les 5 s, départ signalé aux autres.
- **Format PC / format téléphone détecté automatiquement** : l'app regarde l'agent utilisateur, la taille de l'écran et le type de pointeur, puis applique une mise en page large (grille 2 colonnes, lecteur plus grand, page 1080 px) ou une mise en page mobile (520 px, une colonne). Un sélecteur **Auto / PC / Téléphone** est disponible dans les Réglages ; le choix forcé est mémorisé.

## Lecture

Deux modes dans les Réglages :

- **♪ Lecture complète** (Spotify, Premium requis) : la recherche interroge Spotify, la lecture se fait dans l'app, avec repli sur le lecteur Spotify intégré si le SDK est indisponible.
- **▶ Extrait 30 s** : uniquement si l'option est activée. Sans Spotify et sans extraits, l'app explique quoi faire au lieu de lire un bout de morceau.

## Session à plusieurs : comment ça marche

1. Celui qui a Spotify ouvre **Communauté → Créer une session** et copie le code (ou l'envoie par message).
2. L'ami ouvre la même adresse, va dans **Communauté → Rejoindre une session**, entre le code.
3. L'hôte lance un titre : il apparaît chez tout le monde en quelques secondes, avec « en lecture » ou « en pause ».

Le son reste sur l'appareil de l'hôte : c'est un partage de ce qui est écouté, pas un flux audio. Pour écouter ensemble, chacun lance le même morceau sur son propre compte ; la session sert à être synchronisés.

## Paroles : source et synchronisation

Spotify ne fournit pas de paroles par son API (`/v1/lyrics` → `410`). RezMusic utilise **LRCLIB** (gratuit, sans clé, CORS ouvert). Le format `syncedLyrics` (`[mm:ss.xx]`) est préféré pour le karaoké ; sinon les paroles s'affichent en texte simple.

## Mode « Son Discord » : la limite

Le navigateur **ne peut pas lire l'audio de Discord**. Le mode écoute le **micro** : dès qu'une voix est détectée, le volume baisse à 35 %, puis revient 20 s après la dernière voix. Utile avec Discord en haut-parleurs ; avec un casque, seul l'utilisateur est capté. Aucune page web ne peut faire mieux.

## Connexion Spotify pour les amis

Un seul bouton suffit, pas de compte développeur à créer. **Limite Spotify en mode développement : 5 comptes**, à ajouter dans *User Management*. Sans Premium : connexion et import possibles, lecture limitée (extraits ou rien).

## Connexion Spotify (admin)

Réglages → « Réglages développeur (privé) », ou URL `#admin`. Mot de passe local (SHA-256), Client ID modifiable, diagnostic (compte, URI de redirection, contexte, appareils).

## Fonctionnalités

Recherche instantanée, mini-lecteur (précédent / pause / suivant), playlists déroulantes avec pochettes, import Spotify, paroles synchronisées, volume, historique, notes sur 5, favoris, profils avec code personnel, sessions d'écoute à plusieurs, format PC/téléphone automatique, lecteur immersif, hors-ligne.

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
sw.js        service worker hors-ligne (cache rezmusic-v26)
saveia.txt   journal complet, v13 → v26 (ajout seul)
GUIDE-MISE-A-JOUR.md   mise à jour GitHub sans suppression
mettre-a-jour.ps1      script Windows (commit + push)
README.md    ce fichier
```
