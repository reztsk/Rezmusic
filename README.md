# RezMusic — v22

Application web musicale collaborative, gratuite, sans base de données.

## Import des playlists Spotify : le bug corrigé

Spotify a renommé ses champs en **février 2026** :

| Avant | Maintenant |
|---|---|
| `GET /playlists/{id}/tracks` | `GET /playlists/{id}/items` |
| `playlist.tracks.total` | `playlist.items.total` |
| `row.track` (objet) | `row.item` (objet) — `row.track` est devenu un **booléen** |

L'ancien code lisait `row.track`, recevait `true`, et concluait « playlist vide ». La v21 utilise `/items`, lit `row.item`, pagine par 100, et retombe sur `/tracks` (ancien format) si besoin. Les playlists affichent aussi leur vrai nombre de titres quand Spotify le communique.

## Connexion Spotify, pour toi et tes amis

- Un seul bouton : **Se connecter avec Spotify**. Chaque personne autorise **son propre compte** ; rien à configurer.
- Le Client ID de l'application est **le même pour tout le monde** — il n'est pas secret. Il se règle dans un **menu privé à mot de passe** (Réglages → « Réglages développeur (privé) », ou ajouter `#admin` à l'URL).
- ⚠️ **Limite Spotify en mode développement : 5 comptes maximum**, à ajouter un par un dans le tableau de bord (*User Management*). Au-delà, il faut demander une extension de quota (Spotify exige 250 000 utilisateurs actifs — difficilement atteignable pour un projet perso).
- La lecture complète reste réservée aux comptes **Premium**. L'extrait 30 s fonctionne pour tous.

## Le mot de passe du menu privé

- Premier accès : tu choisis le mot de passe (4 caractères minimum), il est stocké **haché** (SHA-256) dans le navigateur.
- Ce menu cache le Client ID et le diagnostic aux autres utilisateurs de l'appareil. **Ce n'est pas une protection cryptographique** : tout le code est public dans le navigateur. Le vrai secret Spotify (le *client secret*) ne doit jamais être mis dans l'application.

## Fonctionnalités

Recherche instantanée, extraits 30 s, mini-lecteur en bas (précédent / pause / suivant) visible sur tous les onglets, playlists, import Spotify, historique d'écoute, notes sur 5, favoris, profils avec code personnel, statut « en direct » entre onglets, lecteur immersif, mode hors-ligne.

La barre de progression est synchronisée avec le lecteur Spotify (position, durée réelle, pause, avance automatique, clic pour se déplacer dans le morceau) — plus de 0:30 figé.

## Lancer en local

```bash
python3 -m http.server 3000
```

## Déployer sur Vercel (gratuit)

Dépôt GitHub → Vercel → *Add New Project* → preset **Other** → *Deploy*. Aucune variable d'environnement. Puis ajouter l'URL du déploiement dans les Redirect URIs Spotify.

## Fichiers

```
index.html   application complète (HTML + CSS + JS, aucune dépendance)
sw.js        service worker hors-ligne (cache rezmusic-v22)
saveia.txt   journal complet, v13 → v21 (ajout seul)
README.md    ce fichier
```
