# Portfolio — Paul, monteur vidéo

Site vitrine (UGC, publicité, format vertical 9:16).

- **En ligne :** https://paul-monteur.netlify.app
- **Hébergement :** Netlify
- **Code :** ce dépôt (`Carbone999/paul-portfolio`)

## Contenu du dépôt

| Fichier | Rôle |
|---|---|
| `index.html` | Tout le site : textes, styles, scripts, logos clients (images intégrées en base64), sélecteur de langue FR / EN / PL |
| `carousel.css` | Bandeau défilant des logos clients. **Ne pas modifier** sans demande explicite de Paul. |
| `fonts/` | Polices Anton, Manrope et JetBrains Mono hébergées sur le site (latin + caractères polonais), déclarées dans `fonts/fonts.css`. Aucun appel à Google Fonts : l'adresse IP des visiteurs n'est pas transmise à Google. Licence libre SIL OFL. |
| `_headers` | Règles de cache Netlify (la page n'est jamais gardée en cache) |
| `.gitignore` | Fichiers exclus de Git (`.netlify`, `.env`) |

Ce qui **n'est pas** dans le dépôt :

- **Les vidéos** sont hébergées sur **Cloudinary**. Le site ne contient que leurs liens.
- **Les fichiers sources** sont rangés à côté du dossier `site/`, sur le Mac de Paul : `../Vidéos`, `../Logos marques`, et `Références graphiques/` dans `site/`, non suivi par Git.

## Branches

- `dev` : on travaille ici.
- `main` : correspond à ce qui est en ligne.

## Aperçu local

Depuis le dossier `site/` :

```bash
python3 -m http.server 8765
```

Puis ouvrir http://localhost:8765.

## Remplacer une vidéo

Les liens ont la forme :

```
https://res.cloudinary.com/l8u3678e/video/upload/v<version>/clipN.mp4
```

Chaque clip apparaît **3 fois** dans `index.html` : la vidéo (`clipN.mp4`) et deux images d'aperçu (`clipN.jpg`), générées automatiquement par Cloudinary.

1. Dans Cloudinary, remplacer la vidéo en gardant **exactement le même nom** (`clipN`) et en autorisant l'écrasement.
2. Copier le nouveau lien : le numéro après `v` a changé.
3. Mettre à jour ce numéro aux 3 endroits du clip dans `index.html`. Sans cette étape, des visiteurs peuvent continuer à voir l'ancienne vidéo, à cause du cache.
4. Vérifier sur l'aperçu local, puis mettre en ligne.

Modifier un fichier dans `../Vidéos` ne change rien sur le site.

## Mise en ligne (Netlify)

`netlify deploy --prod` échoue sur ce site (`Forbidden`). La méthode qui fonctionne :

1. **Brouillon** à partir d'une copie propre, pour ne publier que les fichiers du site (et pas `Références graphiques/`, `.DS_Store`, etc.) :

   ```bash
   D="$TMPDIR/portfolio-deploy" && rm -rf "$D" && mkdir -p "$D"
   git archive dev index.html carousel.css _headers fonts | tar -x -C "$D"
   netlify deploy --dir="$D"
   ```

   Noter le `deploy_id` affiché et vérifier l'adresse du brouillon.

2. **Publication** du brouillon en production. L'identifiant du site se trouve dans `.netlify/state.json` (`siteId`) :

   ```bash
   netlify api restoreSiteDeploy --data '{"site_id":"<siteId>","deploy_id":"<deploy_id>"}'
   ```

3. Vérifier le site en ligne, puis aligner `main` sur `dev` et envoyer sur GitHub (avec l'accord de Paul).

## Règles de travail

- Annoncer les fichiers modifiés avant d'y toucher.
- Travailler sur `dev`, montrer le résultat en local, attendre la validation de Paul avant de mettre en ligne.
- Un changement à la fois : modifier, vérifier, enregistrer (commit en français).
- Envoi sur GitHub uniquement avec l'accord de Paul.
- Aucune clé API ni mot de passe dans le code : secrets dans un fichier `.env`, exclu de Git.
