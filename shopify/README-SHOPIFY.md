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

---

# Ajout au panier réel — à faire AVANT de coller la section produit

## 1. Créer le produit
Produits → Ajouter un produit : **Débardeur de Compression Gainant**.
- **Prix : 34,90 €** · **Comparer à : 49,90 €** (pour que l'affichage corresponde au design).
- **Variantes** — crée 2 options, libellés des valeurs **exactement** comme ci-dessous :
  - Option 1 — nom **Couleur** → valeurs : **Noir**, **Gris**, **Blanc**
  - Option 2 — nom **Taille** → valeurs : **S**, **M**, **L**, **XL**, **2XL**, **3XL**
- Ça génère 18 variantes. Renseigne le **stock** de chacune.

> Mon code reconnaît la variante par ses valeurs (Noir/Gris/Blanc + S…3XL), peu importe l'ordre des options et la casse. Garde juste ces libellés.

## 2. Brancher le produit sur la section
Après avoir collé `vyrae-product.liquid` et l'avoir ajoutée à une page :
- Dans **Personnaliser**, clique sur la section **Vyrae Produit** → réglage **« Produit Vyrae (pour l'ajout au panier) »** → choisis le produit créé.
- (Si tu places la section directement sur le **modèle de page produit**, elle détecte le produit automatiquement.)

## 3. Comment ça marche
- Le visiteur choisit **couleur + taille** → le bouton **Ajouter au panier** ajoute la bonne variante.
- Le **pack choisi** définit la **quantité** ajoutée : 1 débardeur = 1 · « 2 achetés, 1 offert » = 3 · « 3 achetés, 2 offerts » = 5.
- Si aucune taille n'est choisie, le bouton affiche l'erreur (pas d'ajout).
- L'ajout se fait en AJAX puis redirige vers le panier.

## 4. Prix des packs = remises automatiques (sinon le « offert » ne s'applique pas)
Le code ajoute la quantité ; ce sont des **remises automatiques Shopify** qui rendent les articles « offerts ».
Réductions → **Créer une remise** → **Remise automatique** → **Achetez X, obtenez Y** :
- **Remise A — « 2 achetés = 1 offert »** : le client **achète 2** [ce produit] → **obtient 1** [ce produit] à **100 %**. Coche « Nombre max. d'utilisations par commande = 1 ».
- **Remise B — « 3 achetés = 2 offerts »** : le client **achète 3** → **obtient 2** à **100 %**.

Avec un prix unitaire à 34,90 €, le panier affichera alors :
- pack de 3 → **69,80 €**, pack de 5 → **104,70 €** (Shopify applique automatiquement la meilleure remise selon la quantité).

> Si tu préfères ne pas gérer les remises, une app de bundles (ex. Shopify Bundles, gratuite) fait la même chose. Sans remise ni app, les 3/5 articles seront facturés plein tarif.

## Récap ordre des opérations
1. Uploader les images (Réglages → Fichiers).
2. Créer le produit + variantes (étape 1 ci-dessus).
3. Créer les remises automatiques A et B.
4. Coller les sections, les ajouter aux pages, brancher le produit + le lien produit.
