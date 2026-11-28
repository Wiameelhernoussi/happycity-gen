HappyCity Gen
Génération conditionnelle de scénarios d'espaces verts urbains pour
l'accessibilité environnementale et l'équité spatiale du bien-être

Projet réalisé dans le cadre du module IA générative visuelle,
filière IADATA G2, à l'EMSI --- année universitaire 2026--2027.
- Encadrante : Oumaima Ouedrhiri
- Équipe : Taha Rami, Wiame El Hernoussi, Doha Bouthir et Taha
  Saif Esslam
- Résolution des cartes : 128 × 128 pixels
Présentation
HappyCity Gen vise à concevoir et entraîner depuis zéro un GAN
conditionnel U-Net/PatchGAN capable de proposer plusieurs scénarios
d'aménagement d'espaces verts à partir d'une carte urbaine.

Le système prend en compte : - une carte urbaine multi-canaux M ; - un
masque des parcelles candidates R ; - des objectifs c = (b, a), où
b représente le budget maximal de surface verte supplémentaire et a
le taux d'accès visé ; - un bruit latent z, destiné à permettre la
production de propositions différentes.

Le générateur prédit une carte de probabilité des nouvelles surfaces
vertes. Une étape de projection de validité conserve les éléments
non modifiables, limite les ajouts aux parcelles autorisées, applique le
budget et ne retient que les ajouts adjacents au réseau piéton. Les
indicateurs sont ensuite recalculés par un module d'évaluation
indépendant du générateur.

Les sorties attendues sont des cartes sémantiques d'aménagement, et
non des photographies.
Objectifs
Le projet cherche à : 1. Constituer un corpus de cartes urbaines
rasterisées en 128 × 128 pixels, à échelle métrique constante. 2.
Documenter les parcelles candidates, le réseau piéton et les origines
résidentielles. 3. Implémenter et tester les métriques de couverture
végétale, d'accessibilité et d'équité spatiale. 4. Produire des paires
avant/après par un algorithme procédural stochastique (glouton
randomisé), pour plusieurs budgets. 5. Comparer les modèles génératifs à
deux références non génératives. 6. Étudier l'effet de la modulation
conditionnelle, de l'attention spatiale, de la projection de validité et
d'un mécanisme de diversité. 7. Analyser les quartiers où l'accès ne
peut pas augmenter ainsi que les compromis entre accessibilité et
équité.
Données
Les données prévues proviennent de OpenStreetMap / OSMnx, pour une
ville et quelques quartiers, et peuvent être complétées par des cartes
synthétiques contrôlées dont le statut doit être explicitement signalé.

Les cartes représentent notamment : - les bâtiments résidentiels ; - la
voirie ; - les espaces verts existants ; - les parcelles candidates ; -
les obstacles.

Le réseau piéton est représenté par un graphe métrique fixe. Les
origines résidentielles sont également fixées pour les évaluations
avant/après.
Parcelles candidates et données synthétiques
Les parcelles candidates ne sont pas directement disponibles dans
OpenStreetMap : elles doivent être identifiées et documentées. Les
parcelles hypothétiques ne doivent jamais être présentées comme des
terrains réellement aménageables.

Les paires avant/après sont produites par un algorithme procédural
stochastique. Elles constituent une supervision simulée, et non des
projets urbains réellement réalisés.
Partition et qualité des données
La partition est effectuée par quartier ou zone source avant la
création des variantes, afin d'éviter qu'une variante d'une carte de
test se retrouve dans l'entraînement.

Un tableau de couverture des annotations doit être établi avant
l'entraînement. Il faut notamment vérifier la présence des parcelles,
des origines, des entrées des espaces verts et du réseau piéton.
Architecture et méthodes
Trois configurations génératives sont prévues :
  Configuration           Description             Rôle
  A --- Référence     GAN conditionnel de     Point de comparaison
référence, de type
Pix2Pix, adapté aux
entrées M, R, c
et z.                 

  B --- Modulation    Configuration A avec    Contrôlabilité
modulation explicite
des caractéristiques
par (b, a), de type
FiLM.                   
C --- Complète      Configuration B avec    Validité et précision
attention spatiale
guidée par R et
projection de validité.
Deux références non génératives sont également utilisées : - Référence
aléatoire : placement aléatoire valide au même budget. - Référence
gloutonne : placement glouton maximisant A400 au même budget.

L'apprentissage prévoit une perte adversariale, une reconstruction des
masques, une pénalité pour les violations des zones protégées et un
conditionnement explicite par les objectifs b et a.

Un mécanisme de diversité (perte de recherche de modes ou régression du
code latent) doit encourager le générateur à utiliser le bruit z. Son
effet sera mesuré et étudié par ablation.
Métriques d'évaluation
Les indicateurs sont recalculés après génération par un module distinct
du générateur.
Couverture végétale --- V
\(V = `\frac{A_{\mathrm{vert}}}{A_{\mathrm{zone}}}`{=tex}\)

Les surfaces sont calculées dans une projection métrique et sur le même
périmètre avant/après. Le budget doit être respecté : ΔAvert ≤ b.
Seules les zones vertes publiques ou explicitement accessibles comptent
pour l'accès.
