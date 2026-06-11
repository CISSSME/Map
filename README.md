# CISSS-ME — Carte des cliniques en recrutement

Carte web interactive des cliniques en recrutement médical de la **Montérégie-Est**
(RLS Pierre-Boucher, Pierre-De Saurel et Richelieu-Yamaska).

**En ligne :** https://cisssme.github.io/Map/

## Aperçu

Application web progressive (PWA) autonome qui affiche, sur une carte Leaflet, les
cliniques en recrutement avec leurs coordonnées, leur réseau (RLS), leur niveau GMF,
leurs pratiques, leur horaire et leur personnel. L'utilisateur peut filtrer, ajouter
des favoris, prendre des notes personnelles (stockées sur son appareil) et exporter
un comparatif de ses favoris en PDF.

## Caractéristiques

- 100 % statique — aucun serveur, aucune base de données.
- Fonctionne **hors ligne** (service worker ; Leaflet et les polices sont hébergés localement).
- Installable comme application (PWA) sur mobile et ordinateur.
- Favoris, notes et ordre personnalisé stockés **localement** (`localStorage`) — rien n'est transmis.
- Comparatif imprimable / exportable en PDF (orientation automatique).
- Interface en français, conçue pour le contexte québécois.

## Pile technique

HTML / CSS / JavaScript « vanilla » (sans cadriciel ni étape de compilation),
[Leaflet](https://leafletjs.com/) pour la carte, polices Raleway + Lato auto-hébergées.

## Mettre à jour les données

Toutes les données vivent dans **`data.json`**. Pour modifier l'annonce ou une clinique,
on édite ce fichier (le service worker le recharge en priorité réseau, donc les
changements sont visibles immédiatement).

### Schéma de `data.json`

```jsonc
{
  "miseAJour": "AAAA-MM-JJ",
  "annonce": {
    "texte": "Texte de la bannière d'annonce (optionnel).",
    "lienCarte": "https://... (lien optionnel, ex. Google Maps)"
  },
  "cliniques": [
    {
      "id": 1,                       // requis — identifiant unique (nombre)
      "nom": "GMF Exemple",          // requis
      "type": "GMF",                 // requis — GMF, GMF-U, Clinique médicale, ...
      "rls": "Pierre-Boucher",       // requis — Pierre-Boucher | Pierre-De Saurel | Richelieu-Yamaska
      "lat": 45.59,                  // requis — latitude (nombre)
      "lng": -73.43,                 // requis — longitude (nombre)
      "alias": "",                   // optionnel
      "niveau": "Niveau 12 / 18",    // optionnel
      "adresse": "",                 // optionnel
      "ville": "Boucherville",       // optionnel
      "telephone": "",               // optionnel
      "site": "",                    // optionnel
      "personneRessource": "",       // optionnel
      "dme": "",                     // optionnel — dossier médical électronique
      "porteOuverte": "",            // optionnel — info portes ouvertes
      "bureau": "",                  // optionnel
      "frais": "",                   // optionnel — frais de bureau
      "pratiques": ["pec", "srv"],   // optionnel — codes : pec, srv, sad, peri, msk, chir
      "horaire": {                   // optionnel — par jour
        "Lundi": "8 h – 17 h", "Mardi": "", "Mercredi": "", "Jeudi": "",
        "Vendredi": "", "Samedi": "Fermé", "Dimanche": "Fermé"
      },
      "personnel": {                 // optionnel
        "medecins": "", "infirmieres": "", "ipspl": "",
        "physiotherapeutes": "", "pharmaciennes": "", "travailleuresSociales": ""
      },
      "notes": "",                   // ignoré à l'affichage (les notes sont locales à l'utilisateur)
      "photo": "",                   // optionnel
      "posApprox": false             // optionnel — position approximative ?
    }
  ]
}
```

Un champ vide s'affiche comme « À venir ». Les codes de pratique : `pec` (prise en charge),
`srv` (sans rendez-vous), `sad` (soins à domicile), `peri` (périnatalité),
`msk` (médecine sportive), `chir` (chirurgie mineure).

## Déploiement

Le dépôt est publié via **GitHub Pages**. Le workflow `.github/workflows/deploy.yml` :

1. valide `data.json` et la syntaxe JavaScript (`node scripts/validate.js`) ;
2. **incrémente automatiquement** la version du cache du service worker ;
3. publie le site.

> Pour activer ce déploiement automatique : *Settings → Pages → Build and deployment →
> Source = **GitHub Actions***. Sans cela, on peut continuer à déployer depuis la branche
> et incrémenter la version du cache à la main dans `sw.js`.

## Développement local

Servir le dossier avec n'importe quel serveur statique, par exemple :

```bash
python3 -m http.server 8000
# puis ouvrir http://localhost:8000
```

Avant de pousser : `node scripts/validate.js`.

## Vie privée

Aucune donnée utilisateur n'est collectée ni transmise. Les favoris, les notes et l'ordre
personnalisé restent dans le navigateur de chaque personne (`localStorage`).

## Licence

Voir le fichier [LICENSE](LICENSE).
