# C1 — Sections 1.4 et 1.5 (version vérifiée, prête à copier dans Word)

> ⚠️ Avant de coller : supprime les lignes de consigne du template (« Identifier les irritants… », « Expliquer pourquoi… »).
> Les références *(pièce XX)* sont à mettre en italique dans Word.

---

## 1.4 Synthèse de l'existant

Les irritants relevés dans le dossier documentaire se classent en trois familles.

**Irritants de gouvernance :**
- Absence de référentiel commun des stations et des lignes : une même station peut ne pas être reconnue d'un système à l'autre *(pièces 02, 07)*.
- Aucun dictionnaire de données partagé : les indicateurs n'ont pas de définition officielle unique *(pièce 02)*.
- Conséquence : les chiffres des tableaux de bord sont parfois contestés par les équipes terrain *(pièce 01)* — le risque de rejet métier des indicateurs est d'ailleurs évalué « probabilité forte, impact fort » *(pièce 07)*.
- Des données personnelles circulent en texte libre dans les réclamations (nom, téléphone), sans cadre de traitement formalisé *(pièces 02, 07)*.

**Irritants techniques :**
- Événements hétérogènes selon les fournisseurs *(pièce 01)* : horodatages incohérents et doublons sur les capteurs vélos, champs variables selon les lignes sur le SAE *(pièce 02)*.
- Contrôles qualité non automatisés : aucune vérification systématique des données reçues *(pièce 02)*.
- Faible traçabilité des transformations : difficile de justifier comment un chiffre a été obtenu *(pièce 02)*.
- Rétention des exports non formalisée *(pièce 02)*, avec un risque identifié de saturation du stockage par conservation indéfinie d'événements bruts *(pièce 07)*.

**Irritants organisationnels :**
- Incidents saisis manuellement dans plusieurs outils *(pièce 01)*, avec retards de saisie et typologies non normalisées *(pièce 02)*.
- Exports et consolidations manuels tout au long de la chaîne ; rapports de performance produits à la main chaque semaine *(pièces 01, 02)*.
- Météo et calendrier consultés séparément, sans intégration systématique : de simples commentaires ajoutés aux rapports *(pièces 01, 02)*.
- Information voyageur incohérente entre les canaux (application, panneaux) *(pièces 00, 04)*.
- Indicateurs pas toujours accessibles aux personnes en situation de handicap *(pièce 01)*.

**Opportunités (points d'appui de l'existant) :**
- Les données existent déjà et couvrent tout le périmètre : capteurs vélos, SAE bus/tram, incidents, météo, événements, réclamations — soit environ 1,15 million d'enregistrements par jour *(pièce 03)*.
- Les volumes restent maîtrisables pour une architecture moderne : de l'ordre du Go par jour (hypothèse : ~1 Ko par enregistrement, à confirmer en cadrage).
- Six KPI métier sont déjà esquissés par la direction, chacun associé à une décision *(pièce 08)*.
- Les parties prenantes sont identifiées et convergent toutes vers le même besoin de fiabilisation *(pièce 04)*.

**Limites de l'étude :**
- Les volumétries et coûts fournis sont déclaratifs et devront être confirmés en phase de cadrage.
- Aucun audit technique sur site n'a été mené : l'état réel des API fournisseurs reste une hypothèse à vérifier.

---

## 1.5 Opportunité du projet

Le diagnostic (1.4) montre que les difficultés de NovaVille ont une cause racine commune : l'absence d'un socle de données consolidé et gouverné. Traiter les symptômes un par un — recruter plus d'analystes, remplacer un outil isolé — reproduirait le problème. Un projet d'architecture de gestion de données traite la cause : collecter automatiquement, normaliser sur un référentiel commun, contrôler la qualité, puis exposer des indicateurs définis une seule fois et consommés partout.

**Bénéfices attendus par partie prenante** *(pièces 01, 04)* :

| Partie prenante | Bénéfice attendu |
|---|---|
| Direction Mobilité | Indicateurs fiables partageables avec les élus ; pilotage par l'impact usager |
| Exploitation / supervision | Détection des anomalies en minutes plutôt qu'en jours ; interventions priorisées |
| Relation usagers | Une seule vérité diffusée sur tous les canaux ; communication crédible |
| DSI | Intégration au SI, suppression des flux redondants |
| Référent RGPD | Minimisation par conception : le lot 1 fonctionne sans donnée personnelle |
| Référent accessibilité | Dashboards conformes RGAA dès la conception, sans rattrapage coûteux |
| Responsable RSE | Rétention maîtrisée, fréquences proportionnées, stockage piloté par un KPI de sobriété |

**Le coût de l'inaction :** sans projet, les réclamations continuent d'augmenter, les décisions restent fondées sur des chiffres contestés, les exports s'accumulent sans limite (risque de saturation identifié, *pièce 07*), et les données personnelles des réclamations demeurent traitées sans cadre (risque RGPD latent).

**Un prérequis pour l'ambition IA :** la direction envisage à moyen terme un composant d'IA de prédiction des anomalies *(pièce 00)*. Or un modèle prédictif n'est fiable que si les données qui l'alimentent le sont : le socle de données proposé ici est le prérequis indispensable à cette ambition. Le projet crée donc de la valeur immédiate (indicateurs fiables) tout en préparant la suite.
