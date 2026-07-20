# 2. Rapport de veille — C2 et C3
## Projet MobilityPulse — Métropole de NovaVille

---

## 2.1 Stratégie de veille

### Objectifs de la veille

La veille menée pour MobilityPulse poursuit quatre objectifs, directement liés aux sous-questions de la problématique (cf. rapport C1, §1.3) :

1. **Choisir les briques d'architecture** (ingestion, orchestration, qualité, visualisation) adaptées à une équipe réduite et un budget de 85 000 €.
2. **Sécuriser la conformité** : obligations RGPD, RGAA et sobriété numérique applicables à une collectivité territoriale.
3. **Assurer l'interopérabilité** : suivre les standards de données de mobilité (GTFS/GTFS-RT, NeTEx) pour ne pas créer un nouveau silo.
4. **Anticiper le cadre du futur composant IA** (règlement européen sur l'IA), prévu à moyen terme *(pièce 00)*.

### Questions de recherche

1. Quels outils open source d'orchestration et de contrôle qualité conviennent à un premier lot de 3 mois ?
2. Batch, micro-batch ou streaming : quel niveau de latence justifie quelle architecture, à quel coût ?
3. Quelles obligations RGPD s'appliquent aux données de mobilité et aux réclamations en texte libre ?
4. Quelle version du RGAA s'impose aux tableaux de bord d'une métropole, avec quels critères concrets ?
5. Comment estimer puis réduire l'empreinte du stockage (sobriété numérique) ?
6. Quels standards facilitent l'échange et la réutilisation des données transport ?

### Mots-clés de recherche

- **Français :** orchestration de données, qualité des données, entrepôt de données, architecture lakehouse, sobriété numérique, écoconception de services numériques, RGAA tableaux de bord, RGPD minimisation, données de mobilité, GTFS temps réel, données ouvertes transport.
- **Anglais :** data pipeline orchestration, data quality framework, data lakehouse, medallion architecture, open source BI, GTFS-RT, green IT.

### Canaux de veille

| Canal | Exemples | Usage |
|---|---|---|
| Documentation officielle des outils | Airflow, Kafka, dbt, Great Expectations, Superset, Metabase | Versions, capacités réelles, prérequis |
| Sites institutionnels | CNIL, DINUM (accessibilite.numerique.gouv.fr), ADEME, Commission européenne | Obligations réglementaires, référentiels |
| Standards et données ouvertes | gtfs.org (MobilityData), transport.data.gouv.fr | Interopérabilité mobilité |
| Newsletters et blogs d'ingénierie | Data Engineering Weekly, blogs techniques d'exploitants | Tendances, retours d'expérience |
| Communautés et code | Dépôts GitHub (releases, roadmaps), meetups data | Vitalité des projets open source |

### Fréquence et organisation

- **Hebdomadaire (~1 h)** : veille technologique — newsletters, releases des outils suivis.
- **Mensuelle (~1 h)** : veille réglementaire — CNIL, DINUM, ADEME, textes européens.
- **Trimestrielle** : revue de synthèse diffusée aux parties prenantes (cf. §2.5).

### Méthode de qualification des sources

Chaque source candidate est évaluée sur une grille de 4 critères notés de 0 à 3 :

| Critère | Question posée | 0 | 3 |
|---|---|---|---|
| **Autorité** | Qui publie ? | Auteur anonyme | Institution officielle / éditeur du produit |
| **Actualité** | Quand a-t-elle été mise à jour ? | Non datée / > 3 ans | Datée < 12 mois ou versionnée |
| **Fiabilité** | Source primaire ou reprise ? | Reprise sans référence | Source primaire recoupable |
| **Utilité projet** | Impact concret sur MobilityPulse ? | Généraliste | Répond à une question de recherche |

**Règle de rétention :** une source est retenue si elle obtient **≥ 8/12** et qu'elle a été **vérifiée en ligne à la date du 20/07/2026** (existence de l'URL, date ou version constatée). Les pistes fournies par le dossier documentaire *(pièce 09)* ont été soumises à cette même grille — conformément à la consigne qui impose de les vérifier, dater et qualifier.

---

## 2.2 Sources retenues

> **Toutes les sources ci-dessous ont été vérifiées en ligne le 20/07/2026** (existence de l'URL, autorité, date ou version constatée), puis notées avec la grille du §2.1. Seules les sources ≥ 8/12 sont retenues.

### Sources réglementaires et accessibilité

| Source (autorité) | Date / version constatée | Score | Apport pour MobilityPulse |
|---|---|---|---|
| CNIL — Durées de conservation (cnil.fr) | Page mise à jour 02/04/2026 | 12/12 | Cycle de vie en 3 phases (base active / archivage / suppression) → fonde le C4 §3.8 |
| CNIL — Guide sécurité des données personnelles | Édition 2024, 25 fiches (API, cloud, IA) | 12/12 | Check-list sécurité pour le cahier des charges |
| CNIL — Anonymisation vs pseudonymisation | Publiée 19/05/2020 | 11/12 | 3 critères (individualisation, corrélation, inférence) → statut RGPD des réclamations |
| CNIL — Registre des traitements (art. 30 RGPD) | Page 2018, article en vigueur | 11/12 | Chaque traitement MobilityPulse doit y figurer |
| RGPD — règlement (UE) 2016/679 | Texte consolidé (EUR-Lex/CNIL) | 12/12 | Base légale : minimisation (art. 5), registre (art. 30), sécurité (art. 32) |
| RGAA — accessibilite.numerique.gouv.fr (DINUM) | **Version en vigueur : 4.1.2** (18/04/2023) | 12/12 | Référentiel de conformité des tableaux de bord du lot 1 |
| DesignGouv — annonce RGAA 5 (DINUM) | Article du 02/03/2026, publication prévue fin 2026 | 12/12 | Anticipation : WCAG 2.2, apps mobiles, contrôle Arcom ; déclarations 4.1.2 valables 18 mois |
| AI Act — règlement (UE) 2024/1689 (Commission/EUR-Lex) | Page vérifiée 07/07/2026 ; application générale 02/08/2026, haut risque reporté 02/12/2027 | 12/12 | Calendrier réglementaire du futur composant IA (lot 3) |
| Data Governance Act — règlement (UE) 2022/868 | Applicable depuis 09/2023 (page vérifiée 03/07/2026) | 10/12 | Cadre de réutilisation si ouverture des données de mobilité |

### Sources technologiques

| Source (autorité) | Date / version constatée | Score | Apport pour MobilityPulse |
|---|---|---|---|
| Doc officielle Apache Airflow | v3.3.0 (06/07/2026), Apache 2.0 | 12/12 | Orchestration candidate lot 1 |
| Doc officielle dbt-core (GitHub releases) | v1.12.0 (16/07/2026), Apache 2.0 | 12/12 | Transformations SQL testées et documentées |
| Doc GX Core (Great Expectations) | v1.19.0 (07/2026), Apache 2.0 | 12/12 | Qualité de données candidate lot 1 |
| Doc officielle Apache Superset | v6.1.0 (13/05/2026), Apache 2.0 | 12/12 | Dashboards candidats lot 1 |
| Doc officielle Apache Kafka | v4.3.1 (25/06/2026), Apache 2.0, KRaft | 11/12 | Streaming en option lot 2 |
| Doc officielle Metabase + licence | v0.62.5 OSS (16/07/2026), AGPL / éditions payantes | 11/12 | Alternative dashboards, self-service |
| Annonce DuckDB 1.5.0 (duckdb.org) | 09/03/2026 | 11/12 | Moteur léger mono-machine : dimensionnement au juste besoin |
| Doc officielle Apache Spark | v4.2.0 (14/07/2026), Apache 2.0 | 10/12 | Retenu en veille, probablement surdimensionné pour le lot 1 |
| Dépôt Soda Core + fichier LICENSE | v4.17.1 (15/07/2026), **ELv2** | 9/12 | Alerte licence : v4 sortie de l'open source strict |

### Sources sobriété numérique et mobilité

| Source (autorité) | Date / version constatée | Score | Apport pour MobilityPulse |
|---|---|---|---|
| RGESN v2 — écoconception (Arcep/Arcom, loi REEN) | Version 2, mai 2024 (en vigueur au 20/07/2026) | 12/12 | 78 critères opposables : durées de conservation, minimisation, hébergement |
| ADEME — Numérique responsable pour collectivités | Page mise à jour 16/01/2026 | 12/12 | Diagnostic et accompagnement dédiés aux collectivités ; chiffres d'empreinte de référence |
| gtfs.org — standard GTFS/GTFS-RT (MobilityData) | Consulté 20/07/2026 ; révisions continues 2025-2026 | 12/12 | Standard d'échange des données transport à consommer et suivre |
| transport.data.gouv.fr — Point d'Accès National | Consulté 20/07/2026 : 777 jeux de données, 352 AOM | 12/12 | Guichet des GTFS/GTFS-RT ; obligation LOM de publication pour la métropole |
| Green IT — 115 bonnes pratiques RWEB, 5e éd. | Juin 2025, libre accès, mappé RGESN | 11/12 | Check-list opérationnelle d'écoconception des dashboards et API |
| NeTEx / SIRI — standards CEN (Transmodel) | Consulté 20/07/2026 (SIRI TS 2024) | 10/12 | Compatibilité européenne à prévoir (profils EPIP) |
| Observatoire politiques locales de mobilité (Cerema/GART) | Millésime 2025 (22/07/2025) | 10/12 | Référentiel territorial des AOM pour adosser les indicateurs |

**Bilan de la qualification :** 25 sources évaluées, 25 retenues (score ≥ 8/12), dont 3 avec réserves documentées (Soda Core pour sa licence, Spark pour son dimensionnement, NeTEx/SIRI en compatibilité différée). Les pistes de la pièce 09 ont toutes été vérifiées : aucune n'était obsolète, mais deux évolutions majeures non mentionnées ont été détectées (RGAA 5 annoncé, AI Act applicable au 02/08/2026) — c'est précisément l'apport d'une veille datée.

---

## 2.3 Analyse des tendances

> Versions et licences vérifiées en ligne le 20/07/2026 sur les sites et dépôts officiels de chaque outil.

### Panorama des briques candidates (open source)

| Famille | Outil vérifié | Version (07/2026) | Licence | Enseignement pour MobilityPulse |
|---|---|---|---|---|
| Orchestration | Apache Airflow | 3.3.0 (06/07/2026) | Apache 2.0 | Orchestre collectes et traitements batch avec supervision centralisée, sans licence |
| Streaming | Apache Kafka | 4.3.1 (25/06/2026) | Apache 2.0 | Depuis la série 4.x, plus de ZooKeeper (KRaft) : un composant de moins à exploiter |
| Traitement distribué | Apache Spark | 4.2.0 (14/07/2026) | Apache 2.0 | À ~13 évts/seconde en moyenne, probablement **surdimensionné** — à ne retenir que si le besoin le justifie |
| Transformations SQL | dbt-core | 1.12.0 (16/07/2026) | Apache 2.0 | Transformations versionnées, testées et documentées → répond à l'irritant « faible traçabilité » |
| Qualité de données | Great Expectations (**GX Core**) | 1.19.0 (07/2026) | Apache 2.0 | Validations déclaratives à l'ingestion (nulls, bornes, fraîcheur), rapports lisibles par des non-développeurs |
| Qualité (alternative) | Soda Core | 4.17.1 (15/07/2026) | ⚠️ **ELv2** (source-available) | La v4 a quitté l'open source strict → moins aligné avec la préférence du projet |
| Dashboards | Apache Superset | 6.1.0 (13/05/2026) | Apache 2.0 | BI complète 100 % open source, sans édition payante |
| Dashboards (alternative) | Metabase | 0.62.5 OSS (16/07/2026) | AGPL (OSS) / éditions payantes | Prise en main très rapide ; l'OSS suffit tant que SSO avancé et permissions fines ne sont pas requis |

### Tendances 2025-2026 et implications pour le projet

1. **Le lakehouse sur formats ouverts est devenu le standard** : Apache Iceberg s'impose (spec V4 présentée en avril 2026, catalogue Apache Polaris promu projet top-level en février 2026). → **Pour MobilityPulse : un stockage objet + Parquet/Iceberg évite le verrouillage fournisseur — les données restent requêtables par plusieurs moteurs.**
2. **Montée des moteurs analytiques légers** : DuckDB (1.5.x, mars 2026) traite confortablement le million d'événements/jour **sur une seule machine**. → **Pour MobilityPulse : un cluster distribué n'est plus un prérequis à ce volume — impact direct sur le budget de 85 k€ et la sobriété.**
3. **Simplification opérationnelle des briques historiques** : Kafka sans ZooKeeper (KRaft), Airflow 3.x modernisé (versioning des DAGs). Dagster et Prefect restent des alternatives d'orchestration plus légères si Airflow s'avérait surdimensionné. → **Moins de composants à maintenir pour une équipe réduite.**
4. **Vigilance sur les licences de l'outillage** : mouvement contrasté en 2026 — dbt Fusion publié sous Apache 2.0, GX Core repris par Fivetran en restant Apache 2.0, mais **Soda Core v4 passé sous ELv2**. → **Pour MobilityPulse : vérifier la licence de chaque brique avant de la retenir (fait dans le tableau ci-dessus) ; ce critère entre dans la matrice de décision (C5).**

### Synthèse exploitable

La veille technologique confirme qu'un **socle 100 % open source sous licence Apache 2.0** (Airflow + dbt + GX Core + Superset, stockage Parquet/Iceberg) couvre l'ensemble du besoin du lot 1 sans coût de licence, avec une complexité maîtrisable par une équipe réduite. Le streaming (Kafka) reste pertinent **en option** pour le lot 2 si le besoin de latence inférieure à quelques minutes est confirmé par l'usage — la volumétrie seule (~13 évts/s en moyenne) ne l'impose pas.

---

## 2.4 Cadre réglementaire

> Toutes les sources ci-dessous ont été vérifiées en ligne le 20/07/2026 (URL et dates constatées — cf. tableau §2.2).

### RGPD — protection des données personnelles

- **Base légale** : règlement (UE) 2016/679 — minimisation (art. 5), registre des traitements (art. 30), sécurité (art. 32).
- **Durées de conservation** : la CNIL impose de définir un cycle de vie par traitement — base active / archivage intermédiaire / archivage définitif (page CNIL mise à jour le 02/04/2026). → **Pour MobilityPulse : fonde directement le tableau de cycle de vie du cahier des charges (C4 §3.8).**
- **Anonymisation vs pseudonymisation** : la CNIL distingue trois critères (individualisation, corrélation, inférence). → **Pour MobilityPulse : les réclamations (SRC06) pseudonymisées restent des données personnelles soumises au RGPD ; seule une agrégation anonymisante les en sort.**
- **Sécurité** : guide CNIL de la sécurité des données personnelles, édition 2024 (25 fiches, dont API, cloud et IA). → **Check-list de référence pour les exigences de sécurité du C4.**
- **Registre** : chaque traitement (collecte capteurs, tableaux de bord…) devra figurer au registre de la métropole avec finalité, durées et mesures de sécurité.

### RGAA — accessibilité numérique

- **Version en vigueur : RGAA 4.1.2** (18/04/2023), publiée par la DINUM sur accessibilite.numerique.gouv.fr. Déclaration d'accessibilité obligatoire pour les services publics en ligne.
- **⚠️ Évolution repérée par la veille : le RGAA 5 est officiellement annoncé** (article DesignGouv du 02/03/2026, publication prévue fin 2026) — intégration des WCAG 2.2, extension aux applications mobiles, contrôle confié à l'Arcom. Les déclarations établies avant sa publication resteront valables 18 mois.
- → **Pour MobilityPulse : concevoir et auditer les tableaux de bord en RGAA 4.1.2 dès le lot 1, sans attendre le RGAA 5 ; prévoir une revue de conformité à la publication du nouveau référentiel.**

### Règlement européen sur l'IA (AI Act)

- Règlement (UE) 2024/1689. Calendrier vérifié au 20/07/2026 : interdictions applicables depuis février 2025, obligations des modèles génératifs depuis août 2025, **application générale au 2 août 2026** ; obligations « haut risque » (annexe III) reportées au 2 décembre 2027 (paquet « Digital Omnibus »).
- → **Pour MobilityPulse : le futur composant IA de prédiction (lot 3) devra a minima respecter les obligations de transparence ; s'il était qualifié de haut risque (infrastructure critique), l'échéance de conformité serait décembre 2027. À anticiper dès la conception du socle de données.**

### Données publiques et partage

- **Data Governance Act** (règlement (UE) 2022/868, applicable depuis septembre 2023) : encadre la réutilisation des données protégées du secteur public et les intermédiaires de données. → **Pertinent si la métropole ouvre ou partage ses données de mobilité (open data).**

### Sobriété numérique et écoconception

- **RGESN v2** (mai 2024) — Référentiel général d'écoconception des services numériques, porté par l'Arcep et l'Arcom dans le cadre de la **loi REEN**, avec l'ADEME, la DINUM, la CNIL et Inria. Ses 78 critères imposent notamment de **définir des durées de conservation, limiter les données collectées et stockées**, et justifier les choix d'hébergement. → **Pour MobilityPulse : référentiel officiel à appliquer dès la conception ; converge avec les exigences RGPD sur la rétention.**
- **ADEME** — parcours « numérique responsable » dédié aux collectivités (page mise à jour 16/01/2026) : diagnostic, écoconception, achats durables ; chiffres de référence issus de l'étude ADEME-Arcep (le numérique = 10 % de la consommation électrique française, 2,5 % de l'empreinte carbone). → **Source des ordres de grandeur pour l'estimation d'impact du C4.**
- **Green IT — 115 bonnes pratiques d'écoconception web** (5ᵉ édition, juin 2025, libre accès, mappée sur le RGESN) : check-list opérationnelle — agrégation à la source, cache, pagination des API. → **Directement applicable aux dashboards et API de MobilityPulse.**

### Standards mobilité et obligations de publication

- **GTFS / GTFS-RT** (MobilityData) : standard de fait pour l'échange de données transport, en évolution continue (6 révisions constatées sur 2025-2026). **NeTEx/SIRI** (CEN) restent les normes européennes exigées pour les points d'accès nationaux → architecture « bi-format » à prévoir à terme.
- **transport.data.gouv.fr** (Point d'Accès National) : guichet où récupérer les GTFS/GTFS-RT des réseaux — et où la métropole a **l'obligation (loi LOM) de publier ses propres données** de mobilité. → **Pour MobilityPulse : la publication au PAN devient un livrable de sortie du projet, pas une option.**

---

## 2.5 Diffusion de la veille

La veille n'a de valeur que partagée. Le dispositif proposé adapte le format à chaque cible :

| Cible | Format | Fréquence | Contenu type |
|---|---|---|---|
| Équipe data / DSI | Canal partagé (fil de discussion dédié) + revue des releases | Hebdomadaire | Versions d'outils, correctifs de sécurité, évolutions GTFS |
| Comité projet MobilityPulse | Note de synthèse (1 page) | Mensuelle | Enseignements actionnables : « ce qui change pour le projet » |
| Direction Mobilité et élus | Encadré « veille » dans le reporting existant | Trimestrielle | 3 messages maximum, sans jargon, avec impacts budget/délai |
| Référents RGPD, accessibilité, RSE | Alerte ciblée (courriel) | À l'événement | Ex. : annonce du RGAA 5 (03/2026), échéance AI Act du 02/08/2026 |

**Exemples de messages clés issus de la présente veille :**
1. « Le RGAA 5 arrive fin 2026 : nous concevons en 4.1.2 dès maintenant, nos déclarations resteront valables 18 mois. »
2. « L'AI Act s'applique généralement au 2 août 2026 : le composant IA prévu en lot 3 a désormais un calendrier réglementaire à respecter. »
3. « Un socle 100 % open source Apache 2.0 couvre le lot 1 sans coût de licence — la vigilance porte sur les changements de licence (cas Soda Core). »

**Capitalisation :** chaque source retenue est consignée dans le tableau du §2.2 (URL, date de vérification, score) ; le tableau est re-vérifié à chaque revue trimestrielle — une source qui n'est plus à jour est requalifiée ou retirée.
