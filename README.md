# RezMusic — v32

Application web musicale collaborative, gratuite, sans base de données.

## Écouter ensemble : le son direct dans l'app (nouveau)

L'hôte partage le son de son ordinateur, l'invité l'écoute **dans RezMusic**, sans compte Spotify et sans Premium.

1. L'hôte crée la session, puis dans **En direct** touche **« 🔊 Partager le son de mon PC »**.
2. Le navigateur demande quoi partager : choisir l'**onglet** (Spotify web, RezMusic) ou l'**écran**, en cochant **« Partager l'audio »**. Sans cette case, il n'y a aucun son.
3. L'invité touche **« 🔊 Écouter le direct »** : le son arrive dans le lecteur audio de la session.

Technique : `getDisplayMedia({ audio: true })` côté hôte, puis **WebRTC** (peer connection, STUN Google) avec la signalisation portée par le canal MQTT déjà utilisé pour la session (offre / réponse / candidats ICE).

### Limites réelles, affichées dans l'app

- **Ordinateur uniquement pour l'hôte** : Chrome et Edge savent capturer l'audio d'un onglet ou du système. **iOS/iPadOS ne le permet pas** (Safari refuse la capture audio), et Firefox ne partage pas l'audio système.
- **Le partage s'arrête** si l'hôte ferme l'onglet ou arrête le partage.
- **Réseaux très stricts** (entreprise, certains 4G) : pas de serveur TURN dans RezMusic, donc la connexion directe peut échouer.
- **Qualité** : Opus compressé (~128 kbit/s), avec une latence de quelques centaines de millisecondes.
- Le son partagé est celui de la fenêtre choisie : si Spotify joue dans l'app de bureau, il faut partager **l'écran entier** avec l'audio système.

## Sans partage de son

- L'invité voit le titre en direct et peut toucher **« ▶ Ouvrir ce titre dans mon Spotify »** pour l'écouter avec son compte gratuit.
- Un partage dans un appel Discord/vocal reste l'alternative la plus simple pour un flux unique.

## Nouveautés v32

- Partage du son hôte → invité par WebRTC, avec boutons Démarrer / Arrêter et état du partage.
- Lecteur audio distant dans le panneau session, avec message quand le navigateur bloque la lecture automatique.
- Message `rtc-stop` pour couper proprement chez l'invité quand l'hôte arrête.
- Nettoyage automatique du partage quand on quitte la session.

## Comptes Spotify

- Seul le propriétaire du site crée une application (Client ID). Les amis cliquent sur « Se connecter avec Spotify » et Spotify leur demande **leur e-mail et mot de passe**.
- Chaque jeton reste dans **son** navigateur (`rezmusic.spotify.v1`) ; la session ne transporte aucun jeton.
- Champ **Client ID de l'application** dans les Réglages (sans mot de passe), ou `?client=CLIENT_ID_32_CARACTERES`.

## Ce qui bloque encore

| Symptôme | Cause | Solution |
| --- | --- | --- |
| « Client ID Spotify invalide (29 caractères au lieu de 32) » | valeur par défaut incomplète | coller le vrai Client ID dans les Réglages, ou `?client=…` |
| « INVALID_CLIENT: Invalid redirect URI » | adresse non déclarée | ajouter l'adresse affichée dans l'aide sous *Redirect URIs* |
| « unauthorized_client » | compte absent des testeurs | l'ajouter dans *User Management* (5 max en mode développement) |
| Lecture limitée | compte sans Premium | règle Spotify (lecture web = Premium) |

## Session à plusieurs

1. **Communauté → Créer une session** : code à 6 caractères.
2. L'ami ouvre la même adresse, **Rejoindre une session**, entre le code.
3. L'hôte lance un titre ; il peut aussi partager le son pour que l'invité entende directement.

## Paroles

Spotify ne fournit pas de paroles (`/v1/lyrics` → `410`). RezMusic utilise **LRCLIB** (gratuit, sans clé, CORS ouvert).

## Lancer en local

```bash
python3 -m http.server 3000
```

Le partage de son exige un contexte sécurisé : `localhost` fonctionne, une adresse `http://` distante non.

## Déployer sur Vercel (gratuit)

Dépôt GitHub → Vercel → *Add New Project* → preset **Other** → *Deploy*. Puis ajouter l'URL du déploiement dans les Redirect URIs Spotify.

Après un envoi, faire **Ctrl + F5** : le service worker garde l'ancienne page en cache sinon.

## Fichiers

```
index.html   application complète (HTML + CSS + JS, aucune dépendance)
sw.js        service worker hors-ligne (cache rezmusic-v32)
saveia.txt   journal complet, v13 → v32 (ajout seul)
GUIDE-MISE-A-JOUR.md   mise à jour GitHub sans suppression
mettre-a-jour.ps1      script Windows (commit + push)
README.md    ce fichier
```
