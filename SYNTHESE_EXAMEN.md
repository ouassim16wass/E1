# 📋 Synthèse — Examen E1 · RNCP 38919 Data Engineer

> Synthèse d'analyse du dossier `Bloc 1 - E1 -RNCP 38919` — scénario **MobilityPulse_V1**
> Rédigée le 20/07/2026

---

## 1. C'est quoi cet examen ?

| | |
|---|---|
| **Titre** | RNCP 38919 — Data Engineer |
| **Bloc évalué** | Bloc 1 (E1) : *Concevoir un projet d'architecture technique de gestion de données* |
| **Compétences** | C1 à C5 |
| **Nature** | **Étude de cadrage uniquement — AUCUN code demandé** (pas de Python, Docker, API, ni modèle IA) |
| **Durée conseillée** | 4 à 6 h en autonomie |
| **Rendu** | Une archive unique : `E1_MobilityPulse_NOM_Prenom.zip` |
| **Travail** | Individuel |

⚠️ **Point clé** : tu es évalué comme **architecte/consultant data**, pas comme développeur. On juge ta capacité à analyser un besoin, faire de la veille, cadrer un projet et recommander une architecture — le tout **compréhensible par des non-techniques**.

---

## 2. Ce que contient le dossier (3 groupes de fichiers)

### 📁 `01_sujet_etudiant/` — les règles du jeu
Sujet, consignes de rendu, checklist d'auto-vérification avant dépôt.

### 📁 `dossier_documentaire/` — la matière première (à exploiter EXPLICITEMENT)

| Pièce | Contenu utile |
|---|---|
| 00 Contexte | L'organisation, objectifs métier, périmètre / hors périmètre |
| 01 Note direction | La commande officielle : problèmes, budget **85 k€**, cadrage **< 3 mois** |
| 02 Cartographie flux | Les flux actuels (tout est manuel) + 6 sources et leurs défauts |
| 03 Catalogue sources (CSV) | Volumétries, formats, sensibilité RGPD par source |
| 04 Entretiens | 6 parties prenantes, chacune avec son exigence |
| 05 Contraintes | RGPD, RGAA, RSE, sécurité — checklist réglementaire |
| 06 Budget (CSV) | 6 postes = 85 000 € pile (aucune réserve !) |
| 07 Risques | Matrice de 6 risques avec mesures attendues |
| 08 KPI | 6 KPI métier provisoires |
| 09 Sources de veille | Pistes à **vérifier, dater et qualifier** soi-même |

### 📁 `03_templates_candidat/` — les trames vides
Les 4 rapports + page de garde + trame du dossier final.

> ℹ️ Le pack mentionne aussi un examen **E4** (pilotage projet) — c'est un **autre bloc**, même scénario mais séparation stricte : en E1, ne fais **PAS** de RACI, sprints, reporting ou plan d'accompagnement.

---

## 3. Le contexte métier (l'histoire à retenir)

La **Métropole fictive de NovaVille** gère une mobilité urbaine multimodale : bus, tramway, vélos libre-service, parkings relais, zones piétonnes instrumentées.

### Le problème
Depuis 2 ans, réclamations en hausse (retards, ruptures de service, stations saturées, info voyageur incohérente entre applis). En cause : un SI data artisanal —

- incidents saisis **à la main** dans plusieurs outils,
- capteurs **hétérogènes** selon les fournisseurs (horodatages incohérents, doublons),
- rapports de performance **produits manuellement** chaque semaine,
- chiffres **contestés** par les équipes terrain,
- météo consultée à part, jamais intégrée,
- dashboards **non accessibles** aux personnes handicapées,
- pas de référentiel commun stations/lignes, pas de dictionnaire de données, pas de contrôles qualité, pas de politique de rétention.

### La commande
Le programme **MobilityPulse** = concevoir une architecture data qui consolide les flux et produit des indicateurs fiables. Avec :

- Budget lancement ≤ **85 000 €**, premier lot cadré en **< 3 mois**
- Données personnelles **exclues ou minimisées**
- Compatible avec un futur passage en production + composant IA de prédiction à moyen terme
- **Hors périmètre** : pilotage des feux, décision automatisée sans humain, tracking individuel, appli grand public

### Les 6 voix des entretiens (à citer — chacune porte une contrainte)

1. **Exploitation** → l'info terrain arrive trop tard dans les rapports (délai de détection)
2. **Relation usagers** → fiabiliser la donnée avant de communiquer (cohérence)
3. **DSI** → pas d'appli isolée, intégration au SI, pas de flux redondants
4. **RGPD** → réclamations uniquement agrégées/pseudonymisées
5. **Accessibilité** → RGAA dès le cadrage (contraste, clavier, alternatives textuelles)
6. **RSE** → agréger vite, supprimer ce qui ne sert plus, durées de vie claires

---

## 4. Ce qu'il faut produire (4 livrables + annexes)

| # | Livrable | Compétence | Barème | Contenu attendu |
|---|---|---|---|---|
| 1 | `rapport_preliminaire_C1.pdf` | C1 — Analyser le besoin | **4 pts** | Contexte, **schéma des flux existants**, table des sources, **problématique claire**, irritants, opportunité du projet |
| 2 | `rapport_veille_C2_C3.pdf` | C2 — Stratégie de veille / C3 — Synthèse de veille | **4 + 3 pts** | Mots-clés, canaux, fréquence, **méthode de qualification des sources** ; tableau source/date/fiabilité/impact ; tendances tech ; cadre réglementaire ; **plan de diffusion** de la veille |
| 3 | `cahier_des_charges_C4.pdf` | C4 — Formaliser le périmètre | **6 pts** ⭐ | **Objectifs SMART**, périmètre + exclusions, sources retenues, besoins fonctionnels/techniques, **architecture cible**, contraintes & risques, RGPD/RGAA/RSE/éthique, **cycle de vie des données**, indicateurs de réussite |
| 4 | `recommandations_C5.pdf` | C5 — Recommander | **3 pts** | Synthèse exécutive, **≥ 2 architectures comparées**, recommandation argumentée, plan en lots 1/2/3, risques résiduels, messages par partie prenante |
| 5 | `annexes/` | — | — | Schémas, matrices de risques, tableau de sources de veille, **matrice de décision**, glossaire |

**Total : 20 points.** Le C4 pèse 30 % à lui seul ; la veille C2+C3 pèse 35 %.

---

## 5. Les exigences transversales (là où on gagne ou perd des points)

D'après la checklist officielle, le jury vérifiera notamment :

- ✅ Une problématique métier **claire et argumentée**
- ✅ Sources de veille **vérifiées, datées, qualifiées** (le doc 09 s'appelle littéralement « sources À VÉRIFIER » — les recopier telles quelles = piège)
- ✅ RGPD + RGAA + RSE + inclusion intégrés **dans les choix**, pas en paragraphe alibi
- ✅ Une **estimation qualitative ou chiffrée de l'impact écologique**
- ✅ Le **cycle de vie complet** : création → usage → archivage → suppression
- ✅ Une **communication adaptée à une direction non technique**
- ✅ Distinguer clairement **faits du dossier / hypothèses / recommandations** (déclaration signée en page de garde)

---

## 6. Les pièges repérés dans les données

1. **Budget = 85 000 € pile**, plafond atteint au centime, marge risques de 5 000 € seulement (5,9 %) → à relever et à traiter par un arbitrage/lotissement dans C4-C5.
2. **Volume concentré à 99,7 % sur 2 sources** (SAE bus/tram 900 k + capteurs vélos 250 k événements/jour, sur ~1,15 M total) → tout l'enjeu sobriété/stockage/rétention se joue là.
3. **RGPD quasi gratuit à régler** : la seule source sensible (réclamations CRM, 800 tickets/jour = 0,07 % du volume) peut être exclue du lot 1 ou pseudonymisée sans perte métier.
4. **Consigne ambiguë** « `recommandations_C5.pdf` **ou** `annexes/` » → fournir les deux.
5. **Matrice de décision** exigée par le sujet mais absente des templates → ne pas l'oublier en annexe.

---

## 7. Plan de travail conseillé (ordre logique ≠ ordre des livrables)

1. **C1** — cartographie + problématique. Fondation : tout le reste s'y adosse.
2. **C2/C3** — la veille. Le plus long (7 pts), vérifier réellement les sources.
3. **C4** — cahier des charges. Le plus noté, il consomme C1 et C3.
4. **C5** — recommandations. Rapide si C4 est solide : matrice de décision + 2 options d'architecture comparées.
5. **Annexes + relecture** contre la checklist, point par point.

⏱️ Compter 4 à 6 h au total.

---

## 8. La mission en une phrase

> Jouer le consultant data qui remet à la Métropole de NovaVille un dossier de cadrage complet (analyse de l'existant → veille → cahier des charges → recommandation d'architecture), défendable devant un comité mixte métier/DSI/juridique/RSE/accessibilité, en 4 PDF + annexes zippés.
