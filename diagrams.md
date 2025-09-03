```mermaid
erDiagram
    POINT_DE_MESURE ||--o{ INTERVENTION : "a des interventions"
    INTERVENTION ||--o| INSTALLATION : "peut créer une installation"
    INTERVENTION ||--o{ INSTALLATION : "peut terminer des installations"
    INSTALLATION ||--o| REFERENCE_ALTIMETRIQUE : "peut avoir une référence"
    INTERVENTION }|--|| CORDISTE : "réalisée par"
    REFERENCE_ALTIMETRIQUE }|--|| RESULTAT_POSITIONNEMENT : "utilise"
    
    POINT_DE_MESURE {
        string id PK "Identifiant du point de mesure"
        string nom
        string description
    }
    
    INTERVENTION {
        string point_de_mesure_id "PK, FK"
        datetime date_intervention "PK"
        string cordiste_id "FK"
        string numero_boitier
        string type_installation
        float distance_antenne
        boolean boitier_deplace
        text description
    }
    
    INSTALLATION {
        string intervention_installation_id "PK, FK"
        string intervention_desinstallation_id "FK"
        datetime date_debut
        datetime date_fin
    }
    
    REFERENCE_ALTIMETRIQUE {
        string installation_id "PK, FK"
        string resultat_positionnement_id "FK"
        datetime date_debut
        datetime date_fin
    }
```

Voici une version beaucoup plus simple du diagramme :


## Modèle de données simplifié

```
POINT_DE_MESURE ──────(1:n)────── INTERVENTION
                                       │
                                    (1:1)
                                       │
                                  INSTALLATION
                                       │
                                    (1:1)
                                       │
                              REFERENCE_ALTIMETRIQUE
                                       │
                                    (n:1)
                                       │
                              RESULTAT_POSITIONNEMENT

         CORDISTE ──────(1:n)────── INTERVENTION
```

### Relations principales
- **1 Point de mesure** a **plusieurs Interventions**
- **1 Intervention** peut créer **1 Installation**  
- **1 Installation** a **1 Référence altimétrique**
- **1 Cordiste** réalise **plusieurs Interventions**

### Audit automatique sur tous les objets ✅

---
```

Cette version est beaucoup plus lisible et se concentre sur les relations essentielles sans détailler les champs. Ça vous convient mieux ?