# Déploiement Vyrae sur Shopify

Deux sections Shopify prêtes à coller + les images à héberger. Le design, les textes et les couleurs sont identiques au site.

## Fichiers livrés
- `sections/vyrae-product.liquid` — page produit (58 Ko)
- `sections/vyrae-home.liquid` — page d'accueil (22 Ko)
- `shopify/images/` — 15 images (1377 Ko au total, servies par le CDN Shopify, en lazy-load)

## Ce qui a été corrigé (audit technique)
- **Poids** : images sorties du HTML (avant ~3,6 Mo de base64 → section produit **57 Ko**). Page beaucoup plus rapide.
- **Lazy-loading** : `loading="lazy"` + `decoding="async"` sur les images hors écran.
- **Layout shift (CLS)** : dimensions réservées (`width`/`height`) sur les images → plus de saut au chargement.
- **Polices** : Google Fonts en `display=swap` + `preconnect` (texte affiché tout de suite).
- **Mobile** : cibles tactiles passées à **≥44px** (pastilles couleur, flèches galerie). Aucune image en base64 qui ralentit.
- **Shopify** : tout le CSS est **encapsulé sous `#vyrae-app`** → aucune collision avec le thème (ni dans un sens ni dans l'autre). Le fichier n'est plus un document HTML complet mais une vraie section avec `{% schema %}`.

## Étapes sur Shopify (rapide)
### 1. Héberger les images (1 seule fois)
Admin Shopify → **Réglages → Fichiers** → **glisse-dépose les 15 images** du dossier `shopify/images/`.
> Garde les noms de fichiers **exactement** identiques : les sections les appellent par leur nom (`file_url`).

### 2. Créer les sections
Boutique en ligne → **Thèmes** → (…) → **Modifier le code** → dossier **Sections** → **Ajouter une section** :
- nom `vyrae-product` → colle tout le contenu de `sections/vyrae-product.liquid`
- nom `vyrae-home` → colle tout le contenu de `sections/vyrae-home.liquid`
Enregistre.

### 3. Placer les sections
Boutique en ligne → **Personnaliser** :
- Page d'accueil → **Ajouter une section** → *Vyrae Accueil*. Renseigne le réglage **« Lien vers la page produit »**.
- Page produit (ou une page dédiée) → **Ajouter une section** → *Vyrae Produit*.

## Liste des images à uploader
- `vyrae-feature-4-technologies.png`
- `vyrae-gallery-1-trio.jpg`
- `vyrae-gallery-2-noir.jpg`
- `vyrae-gallery-3-gris.jpg`
- `vyrae-gallery-4-blanc.jpg`
- `vyrae-gallery-5-comment.jpg`
- `vyrae-gallery-6-benefices.jpg`
- `vyrae-gallery-7-avant-apres.jpg`
- `vyrae-hero-trio.png`
- `vyrae-home-product.jpg`
- `vyrae-kine.jpg`
- `vyrae-logo.png`
- `vyrae-review-1.jpg`
- `vyrae-review-2.jpg`
- `vyrae-review-3.jpg`

## Point important — bouton « Ajouter au panier »
Le bouton est aujourd'hui **visuel** (c'est la maquette/landing). Pour encaisser de vraies commandes, il faut le **relier à un produit Shopify** (formulaire produit + variantes couleur/taille). Je peux le câbler ensuite : dis-le-moi et je te livre la version avec ajout au panier réel.