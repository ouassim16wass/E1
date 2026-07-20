# 4. Rapport de recommandations — C5
## Projet MobilityPulse — Métropole de NovaVille

---

## 4.1 Synthèse exécutive

La Métropole de NovaVille perd aujourd'hui plusieurs jours entre un incident de mobilité et sa visibilité dans les indicateurs, faute d'un socle de données consolidé. Nous recommandons de bâtir en 3 mois, pour 85 000 €, un **socle de données open source** qui automatise la collecte des cinq sources principales, fiabilise les indicateurs grâce à un référentiel et un dictionnaire validés par les métiers, et publie des tableaux de bord accessibles à tous.

Résultats attendus dès la fin du lot 1 : détection des anomalies en moins de 15 minutes au lieu de plusieurs jours, chiffres incontestables car définis une seule fois, aucune donnée personnelle traitée, empreinte de stockage divisée par un facteur 8 à 10 par rapport à une conservation sans règle. L'architecture est conçue pour accueillir ensuite, sans refonte, les réclamations pseudonymisées, le temps réel si l'usage le justifie, puis le composant d'IA de prédiction souhaité par la direction.

## 4.2 Options étudiées

### Option A : socle micro-batch open source

Collecte automatisée toutes les 5 à 15 minutes, stockage en trois zones sur formats ouverts (Parquet/Iceberg), transformations tracées (dbt), contrôles qualité automatisés (GX Core), orchestration (Apache Airflow), tableaux de bord accessibles (Apache Superset). Toutes les briques sont sous licence Apache 2.0, versions vérifiées par la veille au 20/07/2026.

- **Atouts** : zéro coût de licence, complexité maîtrisable par une équipe réduite, sobriété (une collecte toutes les 5 à 15 minutes au lieu d'un flux continu), réversibilité totale grâce aux formats ouverts, compatible avec le budget de 85 000 € et le délai de 3 mois.
- **Limites** : latence de quelques minutes, pas de traitement événementiel natif ; l'alerte la plus rapide arrive environ 15 minutes après l'événement.

### Option B : plateforme streaming temps réel

Bus d'événements Apache Kafka alimenté en continu par les capteurs et le SAE, traitement au fil de l'eau, alertes en quelques secondes, mêmes zones de stockage en aval.

- **Atouts** : latence de l'ordre de la seconde, socle événementiel très évolutif, adapté à de futurs cas d'usage temps réel (information voyageur dynamique).
- **Limites** : complexité opérationnelle nettement supérieure (cluster à exploiter, compétences spécifiques), coût d'infrastructure et d'expertise incompatible avec l'enveloppe de 85 000 €, empreinte énergétique plus élevée, et surdimensionnement avéré : la veille a mesuré un débit moyen d'environ 13 événements par seconde, très loin des volumes qui justifient une telle plateforme. Enfin, le besoin métier exprimé est de passer de plusieurs jours à quelques minutes, pas à quelques secondes.

### Option écartée en amont : suite SaaS propriétaire managée

Une suite cloud propriétaire clé en main a été écartée avant comparaison détaillée : coûts récurrents de licence incompatibles avec le budget, dépendance forte à un fournisseur unique (à rebours de la demande de réversibilité de la DSI), et maîtrise insuffisante de la localisation des données pour une collectivité.

### Tableau comparatif

| Critère | Option A : socle micro-batch | Option B : streaming temps réel |
|---|---|---|
| Coût lot 1 | Dans l'enveloppe de 85 000 €, zéro licence | Enveloppe dépassée (infrastructure et expertise) |
| Délai de mise en œuvre | 3 mois réalistes | 5 à 6 mois estimés |
| Latence de détection | 5 à 15 minutes | Quelques secondes |
| Adéquation au besoin exprimé | Répond au besoin (passer de jours à minutes) | Dépasse le besoin |
| Complexité d'exploitation | Faible à moyenne | Élevée (cluster, astreinte, compétences) |
| Sobriété numérique | Collecte proportionnée, agrégation précoce | Flux continu, empreinte supérieure |
| Réversibilité | Totale (formats ouverts, licences Apache 2.0) | Bonne mais opératoirement plus engageante |
| Évolutivité vers le temps réel et l'IA | Prévue par conception (zones découplées) | Native |

## 4.3 Recommandation prioritaire

**Nous recommandons l'option A pour le lot 1, conçue « prête pour le streaming ».** Les contrats d'interface et le découplage des zones de données permettent d'ajouter un bus d'événements au lot 2 sans refonte, si et seulement si l'usage du lot 1 démontre un besoin de latence inférieure à la minute. La métropole paie ainsi pour le besoin avéré d'aujourd'hui tout en préservant celui de demain.

### Matrice de décision

Notes de 1 à 5 par critère, pondérées selon les priorités du dossier (budget contraint et délai courts d'abord, conformité et sobriété ensuite).

| Critère | Poids | Option A | Option B |
|---|---|---|---|
| Coût (respect de l'enveloppe de 85 000 €) | 25 % | 5 | 2 |
| Délai (lot 1 en 3 mois) | 20 % | 5 | 2 |
| Conformité (RGPD, RGAA, sécurité) | 15 % | 4 | 4 |
| Sobriété numérique | 15 % | 5 | 2 |
| Simplicité d'exploitation et compétences | 15 % | 4 | 2 |
| Évolutivité (temps réel, IA) | 10 % | 4 | 5 |
| **Score pondéré (sur 5)** | | **4,6** | **2,6** |

La licence des outils (alerte Soda Core relevée par la veille) et la vérification des versions au 20/07/2026 font partie des critères d'entrée : seules des briques sous licence Apache 2.0 figurent dans le socle recommandé.

## 4.4 Plan de mise en œuvre

### Lot 1 : socle de confiance (M0 à M+3, 85 000 €)

- **M+1** : ateliers métier, référentiel maître (stations, lignes, typologies), dictionnaire des KPI validé, contrats d'interface fournisseurs, registre des traitements initialisé.
- **M+2** : ingestion automatisée des 5 sources, zones de stockage en place, contrôles qualité actifs, politique de rétention appliquée (purge à 30 jours sur le brut).
- **M+3** : tableaux de bord des 3 profils, alertes sur seuils, audit d'accessibilité RGAA 4.1.2, recette métier, documentation et transfert de compétences.

### Lot 2 : enrichissement (à cadrer à l'issue du lot 1)

- Intégration des réclamations (SRC06) pseudonymisées, avec avis du référent RGPD et analyse d'impact si nécessaire.
- Alerting avancé et corrélations multi-sources.
- Passage au temps réel sur les flux capteurs et SAE, uniquement si l'usage du lot 1 le justifie.
- Publication des données de mobilité au Point d'Accès National (obligation LOM).

### Lot 3 : intelligence prédictive (horizon 12 mois et plus)

- Composant d'IA de prédiction des anomalies, conditionné à la qualité de données mesurée sur le lot 1.
- Mise en conformité avec le calendrier de l'AI Act : obligations de transparence applicables depuis août 2026, exigences « haut risque » à décembre 2027 si la qualification s'appliquait.
- Validation humaine systématique des recommandations produites par le modèle, conformément au principe d'éthique du cahier des charges.

## 4.5 Risques résiduels et arbitrages

### Risques résiduels après mesures

- **Marge budgétaire de 5,9 %** : elle reste inférieure aux standards malgré le périmètre resserré. Levier : comité d'arbitrage mensuel, toute demande nouvelle bascule au lot 2.
- **Dépendance aux API fournisseurs** : les contrats d'interface et les tests de non-régression la réduisent sans l'éliminer ; un changement de schéma non annoncé reste possible.
- **Adoption par les métiers** : le dictionnaire validé en atelier crée les conditions de la confiance, mais celle-ci se reconstruit dans la durée ; l'indicateur d'adoption (70 % d'utilisateurs actifs à 3 mois de la mise en service) sert de signal d'alerte.
- **Compétences de l'équipe** : le choix d'outils standards et documentés limite le risque, qui resterait sensible en cas de départ simultané de plusieurs membres.

### Arbitrages assumés

1. Les réclamations usagers attendent le lot 2 : la conformité RGPD prime sur l'exhaustivité du lot 1.
2. Le temps réel est différé : le besoin exprimé (minutes, pas secondes) ne justifie pas son coût aujourd'hui.
3. L'historique des exports manuels n'est pas repris : sa qualité est invérifiable, l'historique fiable démarre à la mise en service.

## 4.6 Messages adaptés aux parties prenantes

| Partie prenante | Message clé |
|---|---|
| Direction Mobilité et élus | « En 3 mois et dans l'enveloppe de 85 000 €, vos indicateurs deviennent fiables et partageables ; les anomalies sont visibles en minutes, plus en jours. » |
| Exploitation et supervision | « Vos remontées terrain apparaissent dans les tableaux de bord en moins de 15 minutes, avec des typologies enfin normalisées : ce que vous savez, la plateforme le montre. » |
| Relation usagers | « Une seule vérité alimente l'application et les panneaux ; vous communiquez sur des chiffres que plus personne ne conteste. » |
| DSI | « Socle 100 % open source sous licence Apache 2.0, intégré à votre SI, formats ouverts réversibles, aucun flux redondant, secrets chiffrés et accès journalisés. » |
| Référent RGPD | « Le lot 1 ne traite aucune donnée personnelle ; les réclamations n'entreront qu'au lot 2, pseudonymisées, inscrites au registre et sous votre avis préalable. » |
| Référent accessibilité | « Les tableaux de bord sont conçus et audités en RGAA 4.1.2 avant mise en service, et la revue RGAA 5 est déjà planifiée. » |
| Responsable RSE | « Collecte proportionnée, brut purgé à 30 jours, volume conservé sous 50 Go à un an : la sobriété est un indicateur suivi, pas une intention. » |
