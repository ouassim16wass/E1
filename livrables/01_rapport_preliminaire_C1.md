# 1. Rapport préliminaire — C1
## Projet MobilityPulse — Métropole de NovaVille

---

## 1.1 Contexte métier

La Métropole de NovaVille exploite un réseau de mobilité multimodal : bus, tramway, vélos en libre-service, parkings relais et zones piétonnes instrumentées *(pièce 00)*. Depuis deux ans, les réclamations des usagers augmentent : retards, ruptures de service, stations saturées, information voyageur incohérente entre application et panneaux *(pièces 00, 04)*.

Ces difficultés révèlent surtout les limites du système d'information : les données existent (capteurs, SAE bus/tram, incidents, météo, événements) mais sont éparpillées dans des outils cloisonnés, consolidées à la main et restituées avec plusieurs jours de retard *(pièces 01, 02)*.

La Direction Mobilité lance donc le programme **MobilityPulse** : concevoir une architecture de gestion de données qui consolide les flux de mobilité et produit des indicateurs fiables pour les métiers — premier lot cadré en moins de 3 mois, budget plafonné à 85 000 € *(pièce 01)*.

**Services concernés :** Direction Mobilité (commanditaire), centre de supervision, exploitation réseau et vélos, relation usagers, Direction événements, DSI, référents RGPD / accessibilité / RSE ; les élus sont destinataires des indicateurs *(pièces 01, 02, 04)*.

**Enjeux :** fiabiliser les indicateurs, réduire le délai de détection des anomalies, prioriser les interventions selon l'impact usager et préparer un futur composant IA — dans le respect du RGPD, du RGAA et de la sobriété numérique *(pièces 00, 01, 05)*.

---

## 1.2 Cartographie des flux existants

### Schéma des flux actuels

```mermaid
flowchart LR
    subgraph CH1["Chaîne 1 — Capteurs & véhicules"]
        A1["Capteurs stations<br/>+ SAE bus/tram"] --> A2["Outils<br/>fournisseurs"]
        A2 --> A3["Exports<br/>MANUELS"]
        A3 --> A4["Fichiers<br/>partagés"]
        A4 --> A5["Tableurs BI"]
    end

    subgraph CH2["Chaîne 2 — Incidents terrain"]
        B1["Agents<br/>terrain"] --> B2["Formulaire<br/>interne"]
        B2 --> B3["CSV<br/>hebdomadaire"]
        B3 --> B4["Consolidation<br/>MANUELLE"]
    end

    subgraph CH3["Chaîne 3 — Données de contexte"]
        D1["Météo /<br/>calendrier"] --> D2["Consultation<br/>SÉPARÉE"]
    end

    subgraph CH4["Chaîne 4 — Réclamations usagers"]
        E1["Usagers"] --> E2["Outil CRM<br/>(données personnelles)"]
    end

    A5 --> R["Rapport hebdomadaire<br/>assemblé À LA MAIN"]
    B4 --> R
    D2 -- "commentaires ajoutés<br/>À LA MAIN" --> R
    E2 -.->|"✗ aucun flux documenté<br/>vers les rapports"| R
    R --> F["Direction / élus<br/>(délai jusqu'à J+7, voire plus)"]

    classDef manual fill:#ffe3e3,stroke:#cc0000,color:#7a0000;
    class A3,B3,B4,D2,R manual;
    classDef silo fill:#eeeeee,stroke:#888888,color:#333333,stroke-dasharray: 5 5;
    class E2 silo;
```

*(En rouge : les étapes manuelles. En pointillés : le silo non intégré. Schéma également disponible en annexe : `annexes/schemas_flux.md`.)*

**Lecture du schéma :** aucune chaîne n'est automatisée de bout en bout ; chacune comporte au moins une étape manuelle (en rouge), source de retards et d'erreurs. Les trois premières chaînes ne se rejoignent qu'au rapport hebdomadaire, assemblé à la main *(pièces 01, 02)* : la corrélation entre incidents, saturation et météo est faite « de tête » par les analystes. Les réclamations usagers restent cloisonnées dans le CRM : aucun flux documenté ne les relie aux rapports *(pièce 02)*. Selon le moment de l'événement, la direction est informée avec un délai pouvant atteindre 7 jours, davantage en cas de retard de saisie des incidents.

*Note — hypothèse : le dossier atteste les rapports hebdomadaires manuels (pièce 01) sans préciser leur outil d'assemblage ; le lien « tableurs BI → rapport » est une interprétation à confirmer en phase de cadrage.*

### Table des sources de données

| ID | Source | Volumétrie / jour | Format | Sensibilité RGPD | Qualité | Problèmes connus |
|---|---|---|---|---|---|---|
| SRC01 | Capteurs stations vélos | 250 000 événements | JSON / API fournisseur | faible | moyenne | horodatages incohérents, doublons |
| SRC02 | SAE bus/tram | 900 000 événements | flux propriétaire | faible à moyenne | moyenne | champs variables selon les lignes |
| SRC03 | Incidents terrain | 300 lignes | CSV (saisie manuelle) | faible | **faible** | retards de saisie, typologies non normalisées |
| SRC04 | Météo horaire | 2 400 lignes | JSON API | nulle | bonne | coût API, disponibilité |
| SRC05 | Calendrier événements | 50 lignes | XLSX | nulle | moyenne | données non structurées |
| SRC06 | Réclamations usagers | 800 tickets | export CRM | **élevée** | moyenne | données personnelles en texte libre |

*Source : catalogue des sources (pièce 03) et cartographie des flux (pièce 02).*

**Constat clé :** deux sources (SRC01 et SRC02) concentrent environ 1 150 000 événements par jour, soit **99,7 % du volume total**. À l'inverse, la seule source sensible au titre du RGPD (SRC06) ne représente que 0,07 % du volume. Ce déséquilibre orientera les choix d'architecture (sobriété, rétention) et de conformité (minimisation).

---

## 1.3 Problématique métier

L'analyse du contexte et des flux existants conduit à formuler la problématique suivante :

> **« Comment consolider des flux de mobilité hétérogènes en une plateforme de données fiable et gouvernée, permettant de détecter plus rapidement les anomalies, dans le respect du RGPD, de l'accessibilité (RGAA) et de la sobriété numérique, avec un budget de 85 000 € et un premier lot cadré en 3 mois ? »**

Cette problématique se décompose en quatre sous-questions, qui structurent la suite du dossier :

1. **Quelles données consolider, avec quel niveau de qualité et à quelle fréquence ?** → traité dans le cahier des charges (C4)
2. **Quelles solutions techniques et quelles obligations réglementaires faut-il connaître pour choisir ?** → traité par la veille (C2-C3)
3. **Quel périmètre est réaliste avec 85 000 € et 3 mois, et qu'est-ce qui doit être reporté ?** → traité dans le cahier des charges (C4)
4. **Quelle architecture recommander et selon quels critères de décision ?** → traité dans les recommandations (C5)

---

## 1.4 Synthèse de l'existant

Les irritants relevés dans le dossier documentaire *(pièces 01, 02, 04)* peuvent être classés en trois familles :

### Irritants de gouvernance
- **Aucun référentiel commun** des stations et des lignes : une même station peut ne pas être reconnue d'un système à l'autre.
- **Aucun dictionnaire de données partagé** : les indicateurs n'ont pas de définition officielle unique.
- Conséquence directe : **les chiffres sont contestés** par les équipes terrain — la donnée existe mais ne fait pas autorité.

### Irritants techniques
- **Formats hétérogènes** selon les fournisseurs de capteurs : horodatages incohérents, doublons (SRC01), champs variables selon les lignes (SRC02).
- **Contrôles qualité non automatisés** : aucune vérification systématique des données reçues.
- **Faible traçabilité des transformations** : impossible de savoir comment un chiffre a été obtenu.
- **Rétention non formalisée** : les exports s'accumulent sans durée de vie définie (risque de saturation et non-conformité RSE).

### Irritants organisationnels
- **Consolidations manuelles** à chaque étape : exports, copier-coller, rapports hebdomadaires faits à la main.
- **Retards de saisie** des incidents terrain : l'information arrive après coup dans les rapports.
- **Données de contexte (météo, événements) jamais intégrées** : la corrélation se fait « de tête ».
- **Information voyageur incohérente** entre l'application et les panneaux.
- **Tableaux de bord inaccessibles** aux personnes en situation de handicap.

### Points d'appui (l'existant n'a pas que des faiblesses)
- Les **données existent déjà** et sont riches : ~1,15 million d'événements par jour couvrant vélos, bus/tram, incidents, météo et événements.
- Les **volumes restent maîtrisables** pour une architecture moderne (ordre du Go/jour, pas du To/jour).
- Des **KPI métier sont déjà esquissés** par la direction (pièce 08), chacun associé à une décision.
- Les **parties prenantes sont identifiées et demandeuses** : les six entretiens (pièce 04) convergent vers le même besoin de fiabilisation.

### Limites de l'étude
- Les volumétries et coûts fournis sont **déclaratifs** (dossier documentaire) et devront être confirmés en phase de cadrage.
- L'étude ne comporte pas d'audit technique sur site : l'état réel des API fournisseurs est une **hypothèse à vérifier**.

---

## 1.5 Opportunité du projet

### Pourquoi une architecture data est la bonne réponse

Le diagnostic (1.4) montre que les difficultés de NovaVille ont une **cause racine commune** : l'absence d'un socle de données consolidé et gouverné. Traiter les symptômes un par un (recruter plus d'analystes, changer un outil isolé) reproduirait le problème. Un projet d'architecture de gestion de données traite la cause : **collecter automatiquement, normaliser sur un référentiel commun, contrôler la qualité, exposer des indicateurs définis une seule fois et consommés partout**.

### Bénéfices attendus par partie prenante

| Partie prenante | Bénéfice attendu |
|---|---|
| Direction Mobilité | Indicateurs fiables partageables avec les élus ; pilotage par l'impact usager |
| Exploitation / supervision | Détection des anomalies en minutes plutôt qu'en jours ; priorisation des interventions |
| Relation usagers | Une seule vérité diffusée sur tous les canaux ; communication crédible |
| DSI | Intégration au SI, suppression des flux redondants et du shadow IT |
| Référent RGPD | Minimisation par conception : le lot 1 fonctionne sans donnée personnelle |
| Référent accessibilité | Dashboards conformes RGAA dès la conception, sans rattrapage coûteux |
| Responsable RSE | Rétention maîtrisée, fréquences proportionnées, stockage piloté par un KPI de sobriété |

### Le coût de l'inaction

Sans projet : les réclamations continuent d'augmenter, les décisions restent fondées sur des chiffres contestés, les exports s'accumulent sans limite (risque de saturation), et les données personnelles présentes dans les réclamations demeurent traitées sans cadre (risque RGPD latent).

### Un prérequis pour l'ambition IA

La direction envisage à moyen terme un composant d'IA de prédiction des anomalies *(pièce 00)*. Or un modèle prédictif n'est fiable que si les données qui l'alimentent le sont : **le socle de données proposé ici est le prérequis indispensable** à cette ambition. Le projet crée donc de la valeur immédiate (indicateurs fiables) tout en préparant la suite.
