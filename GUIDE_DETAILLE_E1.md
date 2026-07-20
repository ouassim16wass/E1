# 📖 Guide détaillé — Examen E1 · RNCP 38919 Data Engineer

> Complément approfondi de `SYNTHESE_EXAMEN.md`. Ici : les explications, le contexte, le vocabulaire et les pistes concrètes.
> ⚠️ Ce guide t'aide à **comprendre** — les livrables restent à rédiger par toi, avec tes mots et tes choix (travail individuel, défendable devant jury).

---

## Partie 1 — Comprendre la certification et l'épreuve

### 1.1 C'est quoi un titre RNCP et un « bloc » ?

- Le **RNCP** (Répertoire National des Certifications Professionnelles) est le registre officiel français des diplômes et titres professionnels, géré par **France Compétences**.
- Un titre RNCP est découpé en **blocs de compétences** : des ensembles cohérents de compétences évaluables séparément. Tu peux valider les blocs un par un.
- Le **RNCP 38919** est un titre de **Data Engineer** (niveau ingénierie des données). Son **Bloc 1** s'intitule : *« Concevoir un projet d'architecture technique de gestion de données »*.
- L'épreuve **E1** évalue ce bloc 1 à travers **5 compétences (C1 à C5)**.

👉 Réflexe utile : consulter la **fiche officielle** sur francecompetences.fr (chercher « RNCP38919 ») pour lire le libellé exact des compétences et des critères d'évaluation. C'est la référence que suit le jury.

### 1.2 Pourquoi un bloc « cadrage » pour un Data Engineer ?

Le métier de Data Engineer ne se résume pas à écrire des pipelines. Avant toute ligne de code, un projet data réussi exige :

1. **Comprendre le besoin métier** — sinon on construit la mauvaise chose.
2. **Faire de la veille** — les technologies data évoluent vite (nouveaux outils, nouvelles réglementations) ; un ingénieur qui ne veille pas propose des solutions obsolètes ou illégales.
3. **Cadrer** — périmètre, contraintes, risques, budget : c'est ce qui évite les projets qui explosent en coût et en délai.
4. **Recommander** — savoir comparer des architectures et défendre un choix devant des décideurs non techniques.

E1 évalue exactement cette phase **amont**. C'est pour ça qu'**aucun code n'est demandé** : coder ici serait hors sujet.

### 1.3 La posture attendue : consultant, pas développeur

Le jury lit ton dossier comme une **direction métier** lirait le rapport d'un consultant :

| Ce qui impressionne | Ce qui pénalise |
|---|---|
| Citer précisément les pièces du dossier (« la note de direction indique… ») | Rester générique, copier des généralités sur la data |
| Distinguer **faits / hypothèses / recommandations** | Mélanger tout, affirmer sans source |
| Chiffrer (volumes, coûts, délais, impact CO₂) | Rester vague (« beaucoup de données ») |
| Vulgariser pour les non-techniciens, schématiser | Jargonner sans expliquer |
| Assumer des arbitrages (« je propose X plutôt que Y parce que… ») | Lister des options sans trancher |

### 1.4 E1 vs E4 : ne pas confondre

Le pack contient une notice d'indépendance car le même scénario sert à deux blocs :

- **E1 (ce dossier)** = *Strategy & Architecture* → analyse, veille, cahier des charges, recommandations.
- **E4 (autre examen)** = *Governance & Adoption* → pilotage projet, sprints, RACI, reporting, accompagnement utilisateurs.

⚠️ En E1, **n'inclus pas** de planning de sprints, de matrice RACI ni de plan d'accompagnement : c'est hors bloc et ça n'apporte aucun point. (L'analyse financière détaillée relève, elle, du bloc E5.)

---

## Partie 2 — Le scénario MobilityPulse expliqué en profondeur

### 2.1 L'organisation : la Métropole de NovaVille

Une **métropole** est une collectivité territoriale qui gère les services urbains d'une grande agglomération — dont les **transports publics**. NovaVille (fictive) exploite un réseau **multimodal** :

| Mode | Ce que ça génère comme données |
|---|---|
| **Bus & tramway** | Positions GPS des véhicules, retards, interruptions — via le **SAE** |
| **Vélos en libre-service** | Occupation des stations, disponibilité des bornes, états techniques — via capteurs |
| **Parkings relais** | Taux de remplissage |
| **Zones piétonnes instrumentées** | Comptages de flux piétons |

**Vocabulaire clé :**
- **SAE (Système d'Aide à l'Exploitation)** : le système embarqué + central qui suit les véhicules de transport en temps réel. C'est LA source majeure de données d'un réseau de transport.
- **Information voyageur** : tout ce qui est affiché aux usagers (appli, panneaux en station). À NovaVille, elle est **incohérente** entre canaux — un des irritants majeurs.
- **Multimodal** : plusieurs modes de transport combinés ; l'enjeu data est de les **consolider** dans une vue unique.

### 2.2 Le problème raconté (l'« avant »)

Depuis 2 ans, les **réclamations augmentent** : retards, ruptures de service, stations saturées, informations contradictoires. En creusant, on découvre que le problème n'est pas (que) le transport — c'est le **système d'information** :

**La chaîne actuelle est artisanale :**
```
Capteurs/SAE ──► outils fournisseurs ──► exports MANUELS ──► fichiers partagés ──► tableurs BI
Incidents terrain ──► formulaire interne ──► CSV hebdomadaire ──► consolidation MANUELLE
Météo/calendrier ──► consultation séparée ──► commentaires ajoutés À LA MAIN aux rapports
```

**Conséquences concrètes :**
- L'info terrain arrive **trop tard** : les agents savent qu'une ligne est perturbée avant les indicateurs officiels.
- Les chiffres sont **contestés** par les équipes (pas de définition partagée des KPI).
- Chaque fournisseur de capteurs a **son propre format** → horodatages incohérents, doublons, champs variables.
- **Aucune gouvernance** : pas de référentiel commun des stations/lignes, pas de dictionnaire de données, pas de contrôle qualité automatisé, pas de règle de rétention.

👉 **C'est un cas d'école de dette data.** Ta problématique C1 doit capturer ça : *comment passer d'une chaîne manuelle et hétérogène à une architecture consolidée, fiable et gouvernée, sous contraintes réglementaires et budgétaires ?*

### 2.3 La demande (l'« après » espéré)

La Direction Mobilité commande le programme **MobilityPulse** avec 5 objectifs métier :

1. Vision **consolidée** des incidents et niveaux de service
2. Réduire le **délai de détection** des anomalies
3. **Prioriser** les interventions terrain selon l'impact usager
4. Préparer (moyen terme) un composant **IA de prédiction** d'anomalies
5. **Tableaux de bord accessibles** aux directions métier

**Contraintes fermes de la direction :**
- Premier lot cadré en **< 3 mois**
- Budget de lancement ≤ **85 000 €**
- Données personnelles **exclues ou fortement minimisées**
- Choix **compatibles avec un futur passage en production** (pas un POC jetable)
- **Limiter la duplication** des données (exigence DSI + RSE)

**Hors périmètre explicite** (à recopier dans ton C4 — les exclusions sont notées !) :
- Pilotage automatique des feux de circulation
- Décision automatisée sans validation humaine
- Tracking individuel des usagers
- Application mobile grand public complète

### 2.4 Les parties prenantes : qui veut quoi, et l'impact sur l'architecture

Chaque entretien du document 04 est une **exigence déguisée**. Voici comment les traduire :

| Qui | Ce qu'iel dit | Traduction architecture |
|---|---|---|
| **Responsable exploitation** | « On sait qu'une ligne est perturbée avant les indicateurs officiels » | Réduire la **latence** de bout en bout → collecte automatisée, fréquence de rafraîchissement adaptée (quasi temps réel ou micro-batch) |
| **Relation usagers** | « L'appli et les panneaux affichent des infos différentes » | **Source unique de vérité** : un référentiel + des KPI définis une fois, consommés partout |
| **DSI** | « Pas de nouvelle appli isolée, respecter les standards, limiter les flux redondants » | S'**intégrer au SI existant**, éviter le shadow IT, architecture mutualisée, sécurité by design |
| **Référent RGPD** | « Pas de données individuelles au lot 1 ; réclamations agrégées ou pseudonymisées » | **Minimisation** : exclure/pseudonymiser SRC06, pas de tracking, registre des traitements |
| **Référent accessibilité** | « Contraste, clavier, alternatives textuelles, exports accessibles, dès le cadrage » | **RGAA dès la conception** des dashboards (pas en rattrapage) |
| **Responsable RSE** | « Ne pas tout stocker sans durée de vie ; agréger dès que possible, supprimer l'inutile » | **Politique de rétention** + agrégation progressive (données fines → agrégats) |

👉 En C5, la section « messages adaptés aux parties prenantes » consiste à répondre **à chacune de ces six voix** en une ou deux phrases dans son langage.

### 2.5 Les 6 sources de données décortiquées

Le catalogue (CSV 03) croisé avec la cartographie (doc 02) :

| ID | Source | Volume/jour | Format | RGPD | Qualité | Problèmes connus | Lecture stratégique |
|---|---|---|---|---|---|---|---|
| SRC01 | Capteurs stations vélos | **250 000 évts** | JSON/API | faible | moyenne | horodatages incohérents, doublons | Gros volume → normalisation + dédoublonnage nécessaires |
| SRC02 | SAE bus/tram | **900 000 évts** | flux propriétaire | faible-moyenne | moyenne | champs variables selon lignes | **78 % du volume total** ; schéma hétérogène → contrat d'interface |
| SRC03 | Incidents terrain | 300 lignes | CSV manuel | faible | **faible** | retards de saisie, typologies non normalisées | Petit volume mais **critique métier** → normaliser la typologie |
| SRC04 | Météo horaire | 2 400 lignes | JSON API | nulle | bonne | coût API, disponibilité | Facile à intégrer, forte valeur de corrélation |
| SRC05 | Calendrier événements | 50 lignes | XLSX | nulle | moyenne | non structurées | Faible volume, structurer un référentiel événements |
| SRC06 | Réclamations usagers | 800 tickets | export CRM | **élevée** | moyenne | données personnelles en texte libre | **Seule source sensible** → agrégée/pseudonymisée ou lot 2 |

**Trois enseignements chiffrés à réutiliser :**
1. **Concentration** : SRC01+SRC02 = 1 150 000 évts/jour ≈ **99,7 %** du volume. La sobriété numérique (stockage, rétention, fréquence) se joue sur ces deux flux.
2. **Risque RGPD marginal en volume** : SRC06 = 0,07 % du volume. L'exclure du lot 1 ou la pseudonymiser coûte très peu de valeur métier → arbitrage facile à défendre.
3. **La qualité faible est sur le flux critique métier** (SRC03 incidents) : c'est là que les contrôles qualité et la normalisation des typologies ont le plus d'impact.

**Ordre de grandeur stockage (hypothèse à afficher comme telle)** : si 1 événement ≈ 1 Ko, alors ~1,15 Go/jour brut ≈ **420 Go/an** si on garde tout en brut indéfiniment. Avec une rétention brute de 30 jours + agrégats horaires/journaliers ensuite : on tombe à quelques dizaines de Go. → C'est ça, une « estimation chiffrée de l'impact » : une hypothèse posée + un calcul simple + une conséquence (politique de rétention).

### 2.6 Le budget décortiqué

| Poste | € | Commentaire du dossier |
|---|---|---|
| Ateliers métier et cadrage | 12 000 | entretiens, cartographie, validation du besoin |
| Architecture et sécurité | 18 000 | schémas, choix techniques, risques, RGPD |
| Socle données initial | 25 000 | référentiels, ingestion pilote, qualité |
| Tableaux de bord pilote | 16 000 | KPI exploitation, maquettes, accessibilité |
| Tests et documentation | 9 000 | recette, guides, transfert |
| Marge risques | 5 000 | aléas fournisseurs et disponibilité métier |
| **Total** | **85 000** | = plafond exact fixé par la direction |

**Ce qu'il faut en dire (C4) :**
- Le total atteint le **plafond au centime près** → aucune marge de dérive.
- La marge risques est de **5,9 %** alors que les standards projet recommandent plutôt 10-15 % — surtout avec des risques « forts » identifiés (dépendance fournisseurs, rejet métier).
- Deux leviers à proposer : **réduire le périmètre du lot 1** (ex. reporter SRC06 et SRC05) pour recréer de la marge, et/ou **prioriser l'open source** pour limiter les coûts de licence.

### 2.7 Les risques déjà identifiés (doc 07) — et comment les exploiter

Le dossier te donne une matrice de 6 risques **avec les mesures attendues** — c'est un cadeau : reprends-la, enrichis-la et référence-la.

| Risque | Prob. | Impact | Mesure attendue (fournie !) |
|---|---|---|---|
| Données incohérentes entre fournisseurs | Forte | Fort | **Référentiel maître** + règles de mapping |
| Données personnelles dans réclamations | Moyenne | Fort | Minimisation, anonymisation/pseudonymisation |
| Saturation stockage flux temps réel | Moyenne | Moyen | **Politique de rétention** + agrégation |
| Rejet métier des indicateurs | Forte | Fort | Validation métier + **dictionnaire KPI** |
| Dashboards inaccessibles | Moyenne | Moyen | **Critères RGAA dès la conception** |
| Dépendance fournisseur API | Moyenne | Moyen | **Contrat d'interface** + tests de non-régression |

👉 Remarque la cohérence : chaque mesure attendue correspond à une brique de l'architecture cible. Ta recommandation C5 doit « répondre » à cette matrice.

### 2.8 Les KPI métier (doc 08) — la matière du dictionnaire

6 KPI provisoires sont fournis, chacun lié à une **décision métier** (c'est la bonne pratique : un KPI sans décision associée ne sert à rien) :

1. **Taux d'anomalies critiques** → prioriser les interventions
2. **Délai moyen de détection** → améliorer la supervision *(répond directement au Responsable exploitation)*
3. **Taux de stations saturées** → rééquilibrage des vélos
4. **Fiabilité des flux** (événements conformes/reçus) → piloter la qualité fournisseurs
5. **Indice d'impact usager** → arbitrage opérationnel
6. **Sobriété stockage** → politique de rétention *(un KPI RSE — original et à valoriser)*

👉 Dans C4 « indicateurs de réussite », distingue bien : les **KPI métier** (ci-dessus, produits PAR la plateforme) et les **KPI de réussite du projet** (ex. % de sources automatisées, taux d'adoption des dashboards, conformité RGAA mesurée).

---

## Partie 3 — Les 5 compétences expliquées une par une

### C1 — Rapport préliminaire (4 pts) : « prouver que tu as compris le besoin »

**Ce que le correcteur cherche :** est-ce que le candidat a lu et digéré le dossier ? Sait-il reformuler un besoin métier et le problématiser ?

Structure (template fourni) et conseils :

| Section | Quoi mettre | Conseil |
|---|---|---|
| 1.1 Contexte métier | NovaVille, les 4-5 modes de transport, la hausse des réclamations, le programme MobilityPulse | 10-15 lignes, factuel, cite le doc 00 |
| 1.2 Cartographie des flux | **Un schéma** des 3 chaînes actuelles (capteurs→tableurs, incidents→CSV, météo→manuel) + le tableau des 6 sources | Le schéma est explicitement attendu ; un schéma en blocs simple suffit |
| 1.3 Problématique | UNE question centrale argumentée | Ex. de forme : « Comment consolider des flux de mobilité hétérogènes en une plateforme de données fiable et gouvernée, permettant de détecter plus vite les anomalies, dans le respect du RGPD, du RGAA et de la sobriété numérique, avec 85 k€ et un lot 1 en 3 mois ? » — reformule-la avec tes mots |
| 1.4 Synthèse de l'existant | Les irritants (liste du doc 02 : pas de référentiel, pas de traçabilité, pas de dictionnaire, pas de contrôles qualité, pas de rétention) + les limites | Classe-les : gouvernance / technique / organisationnel |
| 1.5 Opportunité | Pourquoi une architecture data répond au problème ; bénéfices attendus par partie prenante | Relie chaque bénéfice à un objectif de la direction |

**Erreurs classiques :** paraphraser le dossier sans analyse ; problématique trop vague (« comment mieux gérer les données ? ») ; oublier le schéma.

### C2 — Stratégie de veille (4 pts) : « montrer une MÉTHODE de veille »

**Ce que le correcteur cherche :** pas une liste de liens, mais une **démarche organisée et reproductible**. C'est une compétence de méthode.

Contenu attendu :
- **Objectifs de veille** : quoi surveiller et pourquoi (ex. « choisir les briques d'ingestion et d'orchestration », « rester conforme RGPD/RGAA », « suivre les standards mobilité »)
- **Questions de recherche** : 4-6 questions précises (ex. « quel outil d'orchestration open source pour des flux multi-sources ? », « quelles obligations d'accessibilité pour un dashboard public ? »)
- **Mots-clés** : FR + EN (ex. *data pipeline orchestration, data quality framework, GTFS-RT, sobriété numérique, RGAA dashboard*)
- **Canaux** : documentation officielle, blogs d'ingénierie, newsletters (ex. Data Engineering Weekly), agrégateurs, sites institutionnels (CNIL, DINUM, ADEME), conférences/meetups, dépôts GitHub
- **Fréquence** : ex. hebdomadaire pour la techno, mensuelle pour le réglementaire, avec un temps dédié
- **Méthode de qualification des sources** : une grille explicite. Par exemple 4 critères notés : **Autorité** (qui publie ?), **Actualité** (quelle date ?), **Fiabilité** (source primaire ou reprise ?), **Utilité projet** (impact sur MobilityPulse ?). C'est LE point différenciant.

⚠️ Le document 09 s'appelle « sources **à vérifier** » : le piège est de recopier la liste. Il faut la **passer dans ta grille** — vérifier que chaque source existe, la dater, la qualifier, dire ce qu'elle apporte.

### C3 — Synthèse de veille (3 pts) : « exploiter la veille POUR le projet »

**Ce que le correcteur cherche :** la veille débouche-t-elle sur des enseignements actionnables ?

Contenu attendu :
- **Tableau des sources retenues** : source | date | type | fiabilité | information utile | impact sur MobilityPulse
- **Tendances technologiques** : synthèse honnête des familles d'outils (voir lexique partie 5) — orchestration (Airflow), streaming (Kafka), transformation (dbt, Spark), qualité (Great Expectations/Soda), visualisation (Superset, Metabase), architectures (entrepôt, lake, lakehouse, médaillon)
- **Cadre réglementaire** : RGPD (minimisation, registre, durées), RGAA (version en vigueur — vérifier sur accessibilite.numerique.gouv.fr), RSE (référentiels ADEME/GreenIT, écoconception), et en ouverture les textes européens data/IA (Data Governance Act, AI Act — pertinent vu le composant IA prévu à moyen terme)
- **Diffusion de la veille** : à qui (DSI, équipe data, direction), sous quel format (note mensuelle, canal partagé, revue trimestrielle), avec quels messages clés

**Erreur classique :** une veille « catalogue » sans lien avec le projet. Chaque enseignement doit se terminer par « → pour MobilityPulse, cela implique… ».

### C4 — Cahier des charges (6 pts ⭐) : « le cœur du dossier »

**Ce que le correcteur cherche :** un document de cadrage professionnel, complet, cohérent avec C1 et C3, qui intègre TOUTES les contraintes.

Section par section :

- **3.1 Objectifs SMART** — Spécifique, Mesurable, Atteignable, Réaliste, Temporel. Transforme les objectifs de la direction en objectifs datés et chiffrés. Ex. de forme : « D'ici fin du lot 1 (M+3), automatiser l'ingestion des sources SRC01-SRC04 avec un taux de fiabilité des flux ≥ 95 % mesuré par le KPI dédié. » Fais-en 4-6.
- **3.2 Périmètre / hors périmètre** — reprends le périmètre fonctionnel du doc 00 ET les 4 exclusions explicites. Ajoute tes propres exclusions de lot 1 argumentées (ex. SRC06 reportée).
- **3.3 Sources retenues** — le tableau des 6 sources avec ta décision par source : lot 1 / lot 2 / exclue, et pourquoi.
- **3.4 Besoins fonctionnels** — ce que les utilisateurs doivent pouvoir faire : consulter des dashboards par rôle, être alertés, tracer un incident de bout en bout, consulter les définitions des KPI…
- **3.5 Besoins techniques & architecture cible** — les briques logiques (voir partie 4) + un **schéma cible**. Reste au niveau logique (couches, flux), pas besoin de citer 15 produits.
- **3.6 Contraintes & risques** — budget (85 k€ pile, marge faible → dis-le), délais (3 mois), dépendances fournisseurs, disponibilité métier ; enrichis la matrice de risques du doc 07 (probabilité × impact, mesures, porteur).
- **3.7 RGPD, RGAA, RSE, éthique** — pas un paragraphe alibi : des **décisions**. RGPD : SRC06 pseudonymisée/agrégée ou reportée, pas de tracking, registre, durées. RGAA : exigences intégrées aux specs des dashboards. RSE : fréquences proportionnées, agrégation, rétention. Éthique : validation humaine des décisions, transparence des indicateurs.
- **3.8 Cycle de vie des ressources numériques** — le tableau attendu : par type de donnée → création (ingestion), usage (dashboards), archivage (agrégats), suppression (échéance). Ex. : événements bruts SAE conservés 30 j, agrégats horaires 24 mois, agrégats journaliers 5 ans, purge automatisée.
- **3.9 Indicateurs de réussite** — KPI métier (doc 08) + KPI projet (adoption, conformité, automatisation).

### C5 — Recommandations (3 pts) : « trancher et convaincre »

**Ce que le correcteur cherche :** une comparaison honnête d'au moins **2 options** et un choix assumé, communiqué simplement.

Structure efficace :
- **4.1 Synthèse exécutive** — 10 lignes max, lisibles par un élu : le problème, la solution recommandée, le coût, le calendrier, les bénéfices.
- **4.2 Options étudiées** — par exemple :
  - **Option A « Socle batch/micro-batch open source »** : ingestion planifiée (rafraîchissement 5-15 min), orchestrateur, stockage mutualisé, transformations SQL, qualité automatisée, dashboards open source. Coût maîtrisé, sobre, tient dans 85 k€, latence de quelques minutes.
  - **Option B « Plateforme streaming temps réel »** : bus d'événements (type Kafka) + traitement en continu. Latence en secondes, mais coût, complexité et empreinte plus élevés — dépasse probablement le budget du lot 1.
  - (Option C possible : SaaS/cloud managé — rapide mais dépendance fournisseur et questions de localisation des données.)
- **4.3 Recommandation** — typiquement : **Option A pour le lot 1**, conçue « streaming-ready » (on pourra brancher du temps réel au lot 2 si le besoin de latence < 5 min est confirmé par l'usage). Argumente avec la **matrice de décision** (annexe) : critères pondérés — coût, délai, latence, conformité, sobriété, réversibilité, compétences requises — notés par option.
- **4.4 Plan en lots** — Lot 1 (3 mois, 85 k€) : référentiel + ingestion SRC01-SRC04 + qualité + dashboards pilotes accessibles. Lot 2 : réclamations pseudonymisées, alerting avancé. Lot 3 : composant IA de prédiction (conditionné à la qualité des données du lot 1).
- **4.5 Risques résiduels** — ce qui reste même après tes mesures (ex. dépendance API fournisseurs, adoption métier) et les arbitrages faits.
- **4.6 Messages par partie prenante** — une phrase par voix (les 6 de la partie 2.4), dans le langage de chacune.

---

## Partie 4 — L'architecture cible : les briques logiques à connaître

Pour C4/C5, raisonne en **couches logiques** (c'est le niveau attendu en cadrage) :

```
[SOURCES]        SRC01 capteurs · SRC02 SAE · SRC03 incidents · SRC04 météo · SRC05 événements · (SRC06 lot 2)
     │
[INGESTION]      connecteurs API / dépôt de fichiers normalisé — automatisée, planifiée, contrats d'interface
     │
[STOCKAGE]       zone brute (rétention courte) ──► zone nettoyée ──► zone métier (agrégats, KPI)
     │             (= approche « médaillon » : bronze / argent / or)
[TRANSFORMATION] normalisation des schémas, dédoublonnage, mapping sur le RÉFÉRENTIEL stations/lignes
     │
[QUALITÉ]        contrôles automatisés (fraîcheur, complétude, conformité) + KPI « fiabilité des flux »
     │
[ORCHESTRATION]  planification, dépendances, reprise sur erreur, journalisation
     │
[EXPOSITION]     dashboards accessibles RGAA par rôle + exports + dictionnaire de données/KPI
     │
[GOUVERNANCE transverse]  référentiel maître · dictionnaire · habilitations par rôle · journalisation · rétention/purge
```

**Briques transverses à ne pas oublier** (elles répondent aux risques du doc 07) :
- **Référentiel maître** (stations, lignes, typologies d'incident) → répond au risque « données incohérentes »
- **Dictionnaire de données + dictionnaire KPI** validé métier → répond au « rejet métier des indicateurs »
- **Contrats d'interface** avec les fournisseurs + tests de non-régression → répond à la « dépendance fournisseur »
- **Politique de rétention** avec purge automatisée → répond à la « saturation stockage » + exigence RSE
- **Sécurité** : accès par rôle, journalisation, secrets chiffrés (doc 05)

---

## Partie 5 — Lexique technique (pour l'écrit ET l'oral)

| Terme | Définition simple | Lien MobilityPulse |
|---|---|---|
| **ETL / ELT** | Extraire, Transformer, Charger (ou charger puis transformer dans l'entrepôt) | Le doc 00 parle d'« architecture évolutive vers un pipeline ETL/ML » |
| **Batch / micro-batch / streaming** | Traitement périodique (heures) / fréquent (minutes) / continu (secondes) | Cœur de l'arbitrage option A vs B — le besoin réel est « détecter plus vite qu'aujourd'hui » (aujourd'hui = hebdomadaire !) |
| **Entrepôt de données (data warehouse)** | Base structurée optimisée pour l'analyse et les dashboards | La zone « métier » où vivent les KPI |
| **Data lake** | Stockage de données brutes hétérogènes à faible coût | La zone d'atterrissage des événements JSON |
| **Lakehouse** | Combinaison des deux (lake + transactions/qualité de l'entrepôt) | Tendance de veille à mentionner en C3 |
| **Architecture médaillon** | Zones bronze (brut) / argent (nettoyé) / or (métier) | Structure lisible pour ton schéma cible + support de la rétention différenciée |
| **Orchestration** (ex. **Apache Airflow**) | Planifier et enchaîner les traitements, gérer erreurs et reprises | Remplace les « exports manuels » actuels |
| **Bus d'événements** (ex. **Apache Kafka**) | File de messages pour flux temps réel massifs | Option B ; pertinent si latence < 1 min exigée |
| **Apache Spark** | Moteur de calcul distribué (gros volumes, batch ou streaming) | Sans doute surdimensionné pour 1,15 M évts/jour — le dire est un signe de maturité |
| **dbt** | Transformations SQL versionnées, testées, documentées | Bonne pratique de traçabilité (irritant n°2 du doc 02) |
| **Great Expectations / Soda** | Frameworks de tests de qualité de données | Automatise les « contrôles qualité non automatisés » |
| **Superset / Metabase** | Outils de dashboards open source | Candidats pour les tableaux de bord pilotes (à confronter au RGAA) |
| **Référentiel maître / MDM** | La liste unique et officielle des entités (stations, lignes) | Mesure attendue n°1 du doc 07 |
| **Dictionnaire de données** | Description de chaque champ : définition, source, propriétaire, sensibilité | Irritant explicite : « aucun dictionnaire partagé » |
| **GTFS / GTFS-RT** | Standard mondial de description des réseaux de transport (statique / temps réel) | Standard à citer en veille mobilité ; normalise les échanges |
| **Pseudonymisation vs anonymisation** | Réversible avec une clé conservée à part / irréversible | Exigence du référent RGPD sur SRC06 |
| **Minimisation (RGPD)** | Ne collecter que le nécessaire à la finalité | Principe directeur du lot 1 |
| **RGAA** | Référentiel Général d'Amélioration de l'Accessibilité (obligatoire pour les organismes publics) | Contraste suffisant, navigation clavier, alternatives textuelles, pas d'info portée uniquement par la couleur |
| **Sobriété numérique / écoconception** | Limiter l'empreinte : stockage, calcul, fréquence proportionnée au besoin | Fréquence de collecte « proportionnée », agrégation, purge (doc 05) |
| **Rétention** | Durée de conservation par type de donnée avant archivage/suppression | Le tableau cycle de vie du C4 §3.8 |
| **Contrat d'interface** | Engagement formalisé sur le schéma des données échangées | Mesure attendue face au risque « changement de schéma API sans préavis » |

---

## Partie 6 — Les contraintes réglementaires expliquées

### RGPD — pourquoi ça concerne un projet de mobilité
Les données de mobilité peuvent révéler les **déplacements d'individus** (donnée sensible par recoupement). Le projet s'en protège par conception :
- **Finalité** : améliorer le service, jamais suivre des personnes (le tracking individuel est hors périmètre — le rappeler)
- **Minimisation** : le lot 1 fonctionne avec des données **techniques et agrégées** ; seule SRC06 contient du personnel → pseudonymisation ou report
- **Durées de conservation** définies par type de donnée (= ton tableau cycle de vie)
- **Documentation** : registre des traitements, responsabilités
- **Sécurité** : accès par rôle, journalisation des accès sensibles
- Sources de veille : **CNIL** (guides minimisation, registre), textes européens

### RGAA — pourquoi dès le cadrage
NovaVille est un organisme public : l'accessibilité numérique est une **obligation légale**, pas une option. Rattraper l'accessibilité après coup coûte cher ; l'intégrer aux spécifications des dashboards coûte peu :
- contrastes suffisants, navigation clavier complète
- jamais d'information portée **uniquement par la couleur** (ex. rouge/vert → ajouter formes ou libellés)
- libellés clairs, alternatives textuelles, **exports accessibles** (tableaux structurés)
- Sources : **DINUM** / accessibilite.numerique.gouv.fr (vérifier la version RGAA en vigueur)

### RSE / sobriété — la matérialiser, pas la déclarer
La checklist exige une **estimation** d'impact. Méthode simple en 3 pas :
1. Poser une hypothèse (taille moyenne d'un événement, ex. ~1 Ko)
2. Calculer (1,15 M évts/j ≈ ~1 Go/j brut ≈ 420 Go/an si conservation illimitée)
3. Décider (rétention brute 30 j + agrégats → division par ~10 du stockage ; fréquence de collecte alignée sur le besoin réel : les dashboards hebdo actuels ne justifient pas de la seconde)
- Sources : **ADEME** (numérique responsable), guides **GreenIT**/écoconception

### Éthique
- Aucune décision automatisée sans validation humaine (déjà hors périmètre — le transformer en principe de conception)
- Transparence des KPI (définitions publiées dans le dictionnaire) pour éviter la défiance métier
- Le futur composant IA (lot 3) devra être préparé : qualité des données d'abord, biais, explicabilité — mentionner l'**AI Act** européen en veille

---

## Partie 7 — Stratégie de points et checklist finale

### Où se gagnent les points
| Bloc | Pts | Effort conseillé | Pourquoi |
|---|---|---|---|
| C4 | 6 | ~35 % du temps | Barème max, consomme C1+C3 |
| C2+C3 | 7 | ~30 % | Souvent bâclé par les candidats alors que c'est 35 % de la note |
| C1 | 4 | ~20 % | Fondation ; le schéma des flux est vite fait et très rentable |
| C5 | 3 | ~15 % | Rapide si C4 solide ; la matrice de décision fait la différence |

### Checklist de dépôt (celle du jury, reformulée)
- [ ] Analyse du contexte + flux existants (avec schéma)
- [ ] Problématique claire et argumentée
- [ ] Synthèse de l'existant et des irritants
- [ ] Stratégie de veille : mots-clés, canaux, sources, **méthode de qualification**
- [ ] Synthèse de veille **exploitable** (chaque enseignement → implication projet)
- [ ] RGPD, RGAA, RSE, inclusion intégrés aux choix
- [ ] Cahier des charges : objectifs, périmètre, **exclusions**, contraintes, risques, données
- [ ] Recommandations **hiérarchisées** (≥ 2 options comparées + matrice de décision)
- [ ] Estimation qualitative **ou chiffrée** de l'impact écologique
- [ ] Cycle de vie : création → usage → archivage → suppression
- [ ] Communication adaptée à une direction **non technique**
- [ ] Archive `E1_MobilityPulse_NOM_Prenom.zip` : 4 PDF + `annexes/` (+ page de garde signée)
- [ ] Faits / hypothèses / recommandations clairement distingués partout

### Les 5 réflexes qui font « pro »
1. **Cite tes sources internes** : « (cf. note de direction, doc 01) » — le jury vérifie l'exploitation du dossier documentaire.
2. **Chiffre tout ce qui peut l'être** : volumes, %, €, délais, Go.
3. **Un schéma vaut mille mots** : flux existants (C1) + architecture cible (C4) minimum.
4. **Chaque contrainte → une décision** : ne jamais dire « on respectera le RGPD » mais « SRC06 est pseudonymisée dès l'ingestion, clé conservée par le DPO ».
5. **Écris pour un élu pressé** : synthèse exécutive courte, tableaux lisibles, jargon expliqué.
