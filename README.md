# Cymatique 3D — Plaque de Chladni

Simulation 3D interactive de **cymatique** : du sable déposé sur une plaque métallique noire qui vibre, formant les fameuses **figures de Chladni** selon la fréquence d'excitation.

![Demo](https://img.shields.io/badge/Three.js-r160-blue) ![Lang](https://img.shields.io/badge/lang-Français-green)

---

## Lancer le site

```bash
cd cymatique
python3 -m http.server 8765
```

Puis ouvrir **http://localhost:8765/** dans le navigateur.

> Nécessite une connexion internet au premier chargement (Three.js via CDN).

---

## Qu'est-ce que la cymatique ?

La **cymatique** (du grec *kyma* = « vague ») est l'étude des figures et du son **rendus visibles** par la matière. On excite une surface couverte de grains fin (sable, sel, poudre) avec des ondes de vibration : le matériau se déplace, les grains sautent, puis se **déposent** là où la surface ne bouge presque pas.

Le terme et les premières expériences systématiques sont associées à **Ernst Chladni** (fin du XVIIIᵉ siècle), qui parcourait l'Europe en frottant des archets sur des plaques de métal pour révéler des motifs géométriques. Son travail a fasciné Napoléon, qui finança même un prix de l'Académie des sciences pour expliquer mathématiquement ces figures (remporté par Sophie Germain).

Aujourd'hui la cymatique sert aussi en **recherche** (visualisation de vibrations, acoustique des matériaux, microfluidique) et en **art / pédagogie**.

### Pourquoi le sable forme-t-il des figures ?

Quand une plaque vibre en **onde stationnaire** :

| Zone | Ce qui se passe au grain |
|------|---------------------------|
| **Antinœuds** (max d'amplitude) | Le grain est secoué, sauté, projeté |
| **Nœuds** (amplitude ≈ 0) | La surface est (quasi) immobile : le grain peut se poser |

Les grains migrent donc progressivement **des zones agitées vers les zones calmes** : les **lignes nodales**. C'est exactement cette force de dérive (gradient de l'amplitude au carré) qui est simulée ici.

Sur une plaque carrée libre, les motifs dépendent du **mode de vibration** `(m, n)` — nombre de lignes nodales dans chaque direction — lui-même lié à la fréquence d'excitation.

---

## Ce que fait ce site

### Physique simulée

- **Forme de l'onde** (bords libres, approximation standard) :

  ```
  m ≠ n :  w = cos(mπx)·cos(nπy) − cos(nπx)·cos(mπy)
  m = n :  w = 2·cos(mπx)·cos(mπy)
  ```

  Ce sont bien les **nodaux** d'une plaque carrée à bords libres (la vraie plaque de Chladni).

- **Fréquences propres** : approximation par poutres **libre-libre**
  `f ∝ √(βm⁴ + βn⁴)` avec `βk ≈ (k+½)π`  
  (et **non** `f ∝ m²+n²`, qui correspondrait à des bords simplement appuyés).

  Constante `FREQ_K ≈ 4.78` calée pour une plaque d'acier de table : mode `(1,1)` ≈ **150 Hz**.  
  Les **ratios** entre fréquences et les **figures** sont physiquement corrects ; l'échelle absolue dépendrait en vrai de `E`, `ρ`, épaisseur et taille réels.

- **Migration du sable** :
  - force de dérive `−u·∇u` → vers les minima de `|w|` (les nœuds) ;
  - agitation aléatoire proportionnelle à `|w|` (les grains « sautent » sur les antinœuds) ;
  - pas de force hors résonance (comme une vraie plaque mal accordée).

- **Déformation visible** de la plaque : la forme d'onde est animée à une fréquence **ralentie** (on ne peut pas voir 500 Hz à l'œil) ; le volume sonore, lui, utilise la fréquence réelle.

### Réglages

| Contrôle | Rôle |
|----------|------|
| **Fréquence** (80–3000 Hz) | Choisit le mode `(m,n)` le plus proche ; badge **résonance** quand on touche la fréquence propre |
| **Presets** | Modes classiques de Chladni : `(1,1)`, `(2,3)`, `(3,4)`… avec leur fréquence |
| **Amplitude** | Intensité de la vibration + débattement de la plaque |
| **Grains de sable** | 2 000 → 30 000 grains |
| **Taille de plaque** | Le sable suit la taille |
| **Réinitialiser** | Redistribue le sable aléatoirement |
| **Pause / Son** | Geler la simulation · oscillateur Web Audio à la fréquence réelle (volume ∝ résonance) |

### Navigation 3D

- **Glisser** : orbiter
- **Molette** : zoom
- **Clic droit** : déplacer la vue

---

## Exemple d'utilisation

1. Sélectionner un preset, par exemple **`(2,3) · 649 Hz`**.
2. Attendre 2–3 secondes : le sable se rassemble sur les lignes nodales.
3. Éloigner la fréquence du mode propre → badge « hors résonance », la figure se désorganise.
4. Activer le **son** pour entendre le tone pendant que la figure se forme.

---

## Structure

```
cymatique/
└── index.html   # tout le site (HTML + CSS + JS module Three.js)
```

Aucune dépendance à installer : Three.js est chargé via import map (CDN unpkg).

---

## Limites connues (honnêteté physique)

- La solution exacte bords libres contient des fonctions hyperbolico-trigonométriques ; la formule `cos/cos` est l'**approximation classique** qui donne les mêmes zéros (donc les mêmes figures).
- Pas de simulation d'acoustique couplée air-plaque : la résonance est modélisée par une bande passante autour de chaque mode propre.
- Le son est un **sinusoïde pure** (pas le spectre riche d'une vraie plaque métallique).

---

## Références

- E. F. F. Chladni, *Entdeckungen über die Theorie des Klanges* (1787)
- A. Leissa, *Vibration of Plates* (NASA SP-160)
- Principe des figures de Chladni : onde stationnaire + migration vers les nœuds
