# RezMusic — v23

Application web musicale collaborative, gratuite, sans base de données.

## Nouveautés v23

- **Volume** en haut à droite du lecteur (0-100, icône muet/faible/fort). Piloté par l'API Spotify en lecture complète, par le lecteur audio en extrait. Mémorisé par profil.
- **Paroles** : bouton « Paroles » dans le lecteur, affichage **à la place de la pochette**, avec défilement.
- **Playlists déroulantes** : un appui sur une playlist déplie ses morceaux avec pochette, titre et artiste ; un appui sur un morceau le lance.
- **« Nouvelle playlist » compacte** : une case dans la grille, le formulaire ne s'ouvre qu'au clic.
- **Son normal / Son Discord** dans le visualiseur.

## Les paroles : pourquoi pas Spotify

L'API publique Spotify **ne fournit pas de paroles** (l'endpoint `/v1/lyrics` répond `410 Gone`). RezMusic utilise **LRCLIB** (gratuit, sans clé, CORS ouvert). Si un morceau n'y est pas, l'app l'indique clairement.

## Le mode « Son Discord » : ce qu'il fait vraiment

Un site web **ne peut pas lire l'audio de Discord** (aucune API navigateur pour le mixage des autres applications). Le mode Discord **écoute donc le micro** : dès qu'une voix est détectée, le volume baisse à 35 %, puis revient **20 secondes** après le dernier son de voix.

- Efficace pour ta propre voix et pour Discord en haut-parleurs.
- Avec un casque, le micro ne capte que toi : la baisse se déclenchera quand tu parles, pas quand tes amis parlent.
- L'autorisation micro est demandée au premier lancement du mode.

## Connexion Spotify pour tes amis

- Ils n'ont **pas besoin de compte développeur** : le Client ID de l'app est commun, ils cliquent sur « Se connecter avec Spotify ».
- **Sans Premium** : connexion et import de playlists possibles, mais lecture limitée à 30 s (règle Spotify).
- **Limite du mode développement : 5 comptes**, à ajouter dans *User Management* du tableau de bord Spotify.

## Fonctionnalités

Recherche instantanée, extraits 30 s, mini-lecteur en bas (précédent / pause / suivant), playlists, import Spotify, historique, notes sur 5, favoris, profils avec code personnel, direct entre onglets, lecteur immersif, paroles, volume, mode Discord, hors-ligne.

## Lancer en local

```bash
python3 -m http.server 3000
```

## Déployer sur Vercel (gratuit)

Dépôt GitHub → Vercel → *Add New Project* → preset **Other** → *Deploy*. Aucune variable d'environnement. Puis ajouter l'URL du déploiement dans les Redirect URIs Spotify.

## Fichiers

```
index.html   application complète (HTML + CSS + JS, aucune dépendance)
sw.js        service worker hors-ligne (cache rezmusic-v23)
saveia.txt   journal complet, v13 → v23 (ajout seul)
README.md    ce fichier
```
