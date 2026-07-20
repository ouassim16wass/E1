# Annexe — Schémas des flux de données
## Projet MobilityPulse — Métropole de NovaVille

---

## Schéma 1 — Flux existants (situation actuelle)

> Source : cartographie des flux (pièce 02). En rouge : les étapes manuelles.

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

**Constats :** aucune chaîne automatisée de bout en bout ; chaque chaîne comporte au moins une étape manuelle (en rouge). Les trois premières chaînes ne se rejoignent qu'au rapport hebdomadaire assemblé à la main ; les réclamations usagers (SRC06) restent cloisonnées dans le CRM, sans flux documenté vers les rapports. Délai d'information de la direction : jusqu'à 7 jours, davantage en cas de retard de saisie.

*Hypothèse signalée : les rapports hebdomadaires manuels sont attestés (pièce 01) mais leur outil d'assemblage n'est pas précisé ; le lien « tableurs BI → rapport » est une interprétation à confirmer.*

---

## Schéma 2 — Les 6 sources et leur poids relatif

> Source : catalogue des sources (pièce 03). Volumes par jour.

```mermaid
flowchart LR
    S2["SRC02 — SAE bus/tram<br/>900 000 évts/j (78 %)"] --> SI["Silos actuels<br/>(outils cloisonnés,<br/>consolidation manuelle)"]
    S1["SRC01 — Capteurs vélos<br/>250 000 évts/j (22 %)"] --> SI
    S4["SRC04 — Météo<br/>2 400 lignes/j"] --> SI
    S6["SRC06 — Réclamations CRM<br/>800 tickets/j — RGPD ÉLEVÉE"] --> SI
    S3["SRC03 — Incidents terrain<br/>300 lignes/j — qualité FAIBLE"] --> SI
    S5["SRC05 — Calendrier événements<br/>50 lignes/j"] --> SI

    classDef big fill:#dbe9ff,stroke:#1a5fb4,color:#0b3060;
    classDef rgpd fill:#ffe3e3,stroke:#cc0000,color:#7a0000;
    classDef quality fill:#fff3cd,stroke:#b8860b,color:#5c4300;
    class S1,S2 big;
    class S6 rgpd;
    class S3 quality;
```

**Constats :** deux sources concentrent **99,7 % du volume** (en bleu) → l'enjeu sobriété/rétention se joue sur elles. Une seule source est sensible RGPD (en rouge) et ne pèse que 0,07 % du volume → minimisation facile. La qualité la plus faible (en jaune) touche le flux le plus critique pour le métier.

---

## Schéma 3 — Architecture cible (version de travail)

> ⚠️ Version préliminaire — sera finalisée avec le cahier des charges (C4) et les recommandations (C5).

```mermaid
flowchart TB
    subgraph SRC["Sources"]
        X1["SRC01 Capteurs vélos"]
        X2["SRC02 SAE bus/tram"]
        X3["SRC03 Incidents"]
        X4["SRC04 Météo"]
        X5["SRC05 Événements"]
        X6["SRC06 Réclamations<br/>(lot 2 — pseudonymisées)"]
    end

    SRC --> ING["INGESTION automatisée<br/>connecteurs API + contrats d'interface"]
    ING --> BRZ["Zone BRUTE (bronze)<br/>rétention courte : 30 j"]
    BRZ --> SLV["Zone NETTOYÉE (argent)<br/>normalisation, dédoublonnage,<br/>mapping sur le référentiel"]
    SLV --> GLD["Zone MÉTIER (or)<br/>agrégats + KPI validés"]
    GLD --> EXP["EXPOSITION<br/>dashboards accessibles RGAA<br/>par rôle + exports + alertes"]
    EXP --> U["Direction / supervision /<br/>exploitation / élus"]

    REF["Référentiel maître<br/>stations · lignes · typologies"] -.-> SLV
    QUA["Contrôles QUALITÉ automatisés<br/>fraîcheur · complétude · conformité"] -.-> BRZ
    QUA -.-> SLV
    ORC["ORCHESTRATION<br/>planification · reprises · journalisation"] -.-> ING
    ORC -.-> SLV
    ORC -.-> GLD
    GOV["Gouvernance : dictionnaire données & KPI ·<br/>habilitations par rôle · rétention/purge"] -.-> GLD

    classDef zone fill:#dbe9ff,stroke:#1a5fb4,color:#0b3060;
    classDef transverse fill:#e2f7e2,stroke:#2d7a2d,color:#123f12;
    class BRZ,SLV,GLD zone;
    class REF,QUA,ORC,GOV transverse;
```

**Lecture :** chaque brique transverse (en vert) répond à un risque identifié dans la pièce 07 — référentiel maître ↔ données incohérentes, contrôles qualité ↔ rejet métier, rétention ↔ saturation stockage, contrats d'interface ↔ dépendance fournisseur.
