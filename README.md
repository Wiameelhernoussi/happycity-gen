# 🌳 HappyCity Gen

### Génération conditionnelle de scénarios d'espaces verts urbains pour l'accessibilité environnementale et l'équité spatiale du bien-être

**Projet académique — IA générative visuelle**  
**Établissement :** École Marocaine des Sciences de l'Ingénieur (EMSI)  
**Filière :** IADATA G2  
**Année universitaire :** 2026–2027

**Encadrante :** Oumaima Ouedrhiri

**Réalisé par :**
- Taha RAMI
- Wiame EL HERNOUSSI
- Doha BOUTHIR
- Taha SAIF ESSLAM

---

## 📌 1. Présentation du projet

**HappyCity Gen** est un projet d'intelligence artificielle générative visant à concevoir un système capable de générer plusieurs scénarios d'aménagement d'espaces verts urbains à partir de cartes urbaines existantes.

L'objectif est d'améliorer l'accessibilité aux espaces verts et de réduire les disparités spatiales entre les quartiers, tout en respectant un budget foncier fixé.

Le projet repose sur un **GAN conditionnel (Conditional GAN)** basé sur une architecture **U-Net / PatchGAN**, entraîné depuis zéro.

Le système génère des cartes sémantiques d'aménagement urbain de résolution **128 × 128 pixels**.

## 🎯 2. Objectifs du projet

Les principaux objectifs sont :

- Constituer un dataset structuré de cartes urbaines.
- Identifier les parcelles candidates à l'aménagement d'espaces verts.
- Modéliser le réseau piéton et les origines résidentielles.
- Développer un GAN conditionnel capable de générer plusieurs scénarios d'aménagement.
- Contrôler les générations selon un budget foncier et un objectif d'accessibilité.
- Évaluer les résultats selon des métriques spatiales indépendantes.
- Comparer les modèles génératifs à des méthodes non génératives.
- Étudier la diversité des scénarios et les compromis entre accessibilité et équité.

## 🗺️ 3. Données utilisées

Les données proviennent principalement de :

- **OpenStreetMap (OSM)**
- **OSMnx**
- Cartes synthétiques contrôlées, lorsque nécessaire.

Les cartes urbaines sont représentées sous forme de rasters de 128 × 128 pixels à échelle métrique constante.

Les différentes couches comprennent :

- Bâtiments résidentiels.
- Réseau routier et piéton.
- Espaces verts existants.
- Parcelles candidates.
- Obstacles urbains.

Le réseau piéton est représenté par un graphe métrique fixe permettant de calculer les distances par plus court chemin.

Les données sont partitionnées par quartier ou zone source avant la création des variantes afin d'éviter les fuites entre les ensembles d'entraînement et de test.

## 🧠 4. Architecture proposée

Le système repose sur un GAN conditionnel composé de deux éléments principaux :

- **Générateur U-Net :** produit des propositions d'aménagement d'espaces verts.
- **Discriminateur PatchGAN :** évalue la cohérence des cartes générées.

### Entrées du générateur

Le générateur utilise :

- `M` : carte urbaine multi-canaux.
- `R` : masque des parcelles candidates.
- `b` : budget maximal de surface verte supplémentaire.
- `a` : taux d'accessibilité visé.
- `z` : bruit latent permettant de favoriser la diversité des générations.

Le générateur prédit une carte de probabilité des nouvelles surfaces vertes.

Une étape de projection de validité applique ensuite les contraintes géométriques et foncières.

### Les trois configurations étudiées

| Configuration | Description |
|---|---|
| A — Référence | GAN conditionnel de référence basé sur Pix2Pix |
| B — Modulation | Configuration A avec modulation explicite des caractéristiques par (b, a), de type FiLM |
| C — Complète | Configuration B avec attention spatiale guidée par les parcelles candidates et projection de validité |

Deux méthodes de référence non génératives sont également étudiées :

- **Placement aléatoire :** placement aléatoire valide au même budget.
- **Placement glouton :** placement glouton maximisant l'accessibilité A400 au même budget.

### Mécanisme de diversité

Un mécanisme supplémentaire est prévu pour encourager le générateur à exploiter le bruit latent `z`.

Les approches envisagées comprennent :
- Perte de recherche de modes.
- Régression du code latent.

La diversité entre les sorties sera mesurée et étudiée par des expériences d'ablation.

## 📊 5. Métriques d'évaluation

Trois métriques principales sont utilisées.

### 5.1 Couverture végétale (V)

Mesure la proportion de la zone étudiée occupée par les espaces verts.

\[
V = \frac{A_{vert}}{A_{zone}}
\]

Le budget foncier doit être respecté :

\[
\Delta A_{vert} \leq b
\]

### 5.2 Accessibilité (A400)

Mesure la proportion des origines résidentielles situées à une distance de marche de 400 mètres maximum d'un espace vert accessible.

\[
A_{400} = \frac{1}{N}\sum_{i=1}^{N}
\mathbf{1}\left[\min_{p \in P} d_G(r_i,p) \leq 400m\right]
\]

Les distances sont calculées sur le réseau piéton fixe.

### 5.3 Équité spatiale (E)

Mesure les disparités d'accessibilité entre les différents secteurs étudiés.

\[
E = 1 - \left(\max_q A_{400}^{(q)} - \min_q A_{400}^{(q)}\right)
\]

Cette métrique doit toujours être interprétée conjointement avec A400.

### Autres critères

L'évaluation comprend également :

- Erreur de conditionnement : |â − a|.
- Respect du budget foncier.
- Taux de cartes valides.
- Violations des contraintes foncières.
- Diversité entre les générations.
- Temps d'inférence.

## 🛠️ 6. Technologies et outils

| Domaine | Technologies |
|---|---|
| Deep Learning | PyTorch |
| Architecture GAN | U-Net / PatchGAN, Pix2Pix |
| Données géospatiales | OpenStreetMap, OSMnx |
| Traitement géospatial | GeoPandas, Shapely |
| Graphes | NetworkX |
| Analyse de données | NumPy, pandas |
| Machine Learning | scikit-learn |
| Visualisation | Matplotlib |
| Démonstration | Jupyter Notebook |
| Calcul GPU | Kaggle, Google Colab, serveur de l'école |
| Gestion de versions | Git / GitHub |
| Tests | pytest |

## 📦 7. Livrables attendus

Le projet prévoit la réalisation des éléments suivants :

1. Dataset structuré de cartes urbaines et métadonnées.
2. Tableau de couverture des annotations.
3. Scripts de calcul des métriques V, A400 et E.
4. Tests unitaires des métriques.
5. Implémentation et poids des modèles A, B et C.
6. Comparaison avec les références aléatoire et gloutonne.
7. Visualisations des scénarios générés.
8. Études d'ablation.
9. Analyse des limites et des compromis entre accessibilité et équité.
10. Notebook interactif de démonstration.

## 📅 8. Plan de travail prévisionnel

| Phase | Semaines | Activités |
|---|---|---|
| Cadrage | S1 | Analyse du projet et installation de l'environnement |
| Constitution du corpus | S1–S2 | Extraction des données OSM et rasterisation |
| Annotations | S2–S3 | Identification des parcelles et du réseau piéton |
| Métriques | S2–S3 | Implémentation de V, A400 et E |
| Génération des paires | S3–S4 | Algorithme procédural glouton randomisé |
| Références | S3–S4 | Placement aléatoire et glouton |
| Modèle A | S4–S5 | GAN conditionnel de référence |
| Modèle B | S5–S6 | Modulation conditionnelle |
| Modèle C | S6–S8 | Attention, projection et diversité |
| Évaluation | S8–S9 | Comparaison des configurations |
| Ablations | S9–S10 | Analyse des composants |
| Démonstration | S9–S10 | Notebook et rapport final |

## ⚠️ 9. Limites et précautions

Quelques précautions méthodologiques sont essentielles :

- Les paires avant/après sont issues d'une supervision simulée et ne représentent pas des projets urbains réellement réalisés.
- Les parcelles candidates hypothétiques ne doivent pas être présentées comme des terrains réellement aménageables.
- La projection garantit certaines contraintes géométriques, mais ne garantit pas qu'un objectif d'accessibilité soit toujours atteignable.
- La méthode gloutonne peut obtenir de meilleurs résultats que le GAN en matière d'accessibilité.
- Les métriques A400 et E sont des indicateurs spatiaux opérationnels et non des mesures directes du bonheur subjectif.

Le projet ne revendique donc pas une amélioration mesurée du bonheur subjectif, mais étudie des indicateurs spatiaux liés à l'accessibilité environnementale et à l'équité.

## 👥 10. Équipe du projet

**HappyCity Gen — EMSI, IADATA G2**

- Taha RAMI
- Wiame EL HERNOUSSI
- Doha BOUTHIR
- Taha SAIF ESSLAM

**Encadrante :** Oumaima Ouedrhiri

---

*HappyCity Gen — Générer des scénarios urbains pour explorer une meilleure accessibilité aux espaces verts et une plus grande équité spatiale.*
