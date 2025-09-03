---
marp: true
theme: default
paginate: true
backgroundColor: #f8f9fa
color: #2c3e50
style: |
  section {
    font-size: 24px;
  }
  h1 { font-size: 2em; }
  h2 { font-size: 1.5em; }
---

# [EPIC] Gestion V2 des points de mesures
## Interventions, installation, référencement alti et workflows associés

---

# Contexte

- **Difficultés des chefs de projets 🗂️ pour trouver l'historique des interventions** sur un point de mesure (déploiement, déplacement, SAV d'un boitier) et la **configuration d'installation** (ex: distance antenne-station)
- **Les références altimétriques 📍 ne peuvent pas être facilement reliées à une configuration installation** afin de garantir la qualité du positionnement.

---

# Objectifs

Moderniser la gestion des points de mesure avec **3 nouveaux objets métiers** :
- **🛠️ Interventions** - Actions terrain tracées
- **🏗️ Installations** - Caractéristiques physiques de l'installation
- **📍 Référencements altimétriques** - Positionnement GNSS précis lié à une installation

\+ un **📰 journal d'audit** des modifications.

Introduire des Workflows Automatisés:
1. **🟢 Validation d'installation** - Nouveaux déploiements/remplacements  
2. **🛠️ Maintenance** - Entretien, déplacements, désinstallations

---
# Bénéficiaires

- 👷 Coordinateurs techniques
- 🗂️ Chefs de projets  
- 🧑‍💼 Clients finaux

**Vision claire et historisée** des opérations terrain + **conformité** des processus internes (workflow + audit) + **liens** entre interventions <-> installations <-> référence altimétrique

---

# 🛠️ Objet Intervention

**Définition** : Action terrain tracée (installation, maintenance, désinstallation)

## Champs principaux
- **Point de mesure** + **Date intervention** (clés primaires)
- **Cordiste** (obligatoire)
- **N° boitier avant/après** intervention
- **Type installation** : `standard` | `custom` | `installation_retiree`
- **Distance antenne** (obligatoire si custom)
- **Boitier déplacé** (oui/non)
- **Description/rapport** d'intervention

---

# 🛠️ Objet Intervention

## Cas d'usage
- ✅ **Première installation** → n° boitier avant vide
- 🔄 **Remplacement** → changement n° boitier  
- ❌ **Désinstallation** → n° boitier après vide
- 🛠️ **Maintenance** → même boitier, déplacement possible

---

# 🏗️ Objet Installation

**Définition** : Caractéristiques physiques de l'installation d'un boitier sur son point de mesure

## Champs principaux
- **Intervention d'installation** (clé primaire)
- **Intervention de désinstallation** (facultatif)
- **Date début/fin de validité** (héritées des interventions)

## Contraintes importantes
- ⚠️ **Intervention liée** doit avoir un n° boitier après intervention
- 📅 **Pas de chevauchement** de périodes pour un même point de mesure
- 🔚 Seule l'installation la plus récente peut avoir une date de fin vide

---

# 🏗️ Objet Installation

## Cas d'usage par point de mesure
- **🚫 Aucune installation** → Point identifié sans boitier
- **📊 Une installation** → Boitier installé depuis le début d'existence du point de mesure
- **🔄 Plusieurs installations** → Changements de boitier ou déplacements d'antenne

## Période de validité
- **Date début** = Date intervention d'installation
- **Date fin** = Date intervention de désinstallation (si présente)
- **Installation active** = Date fin vide
 
 ---

 # 📍 Objet Référence Altimétrique

 **Définition** : Hauteur de référence GNSS liée à une installation pour convertir des mesures locales en un référentiel absolu.

 ## Champs principaux
- **Installation liée** (obligatoire) — lien vers l'objet `INSTALLATION`
- **Résultat de positionnement** (obligatoire) — référence vers `RESULTAT_POSITIONNEMENT`
- **Date début/fin** (héritées de l'installation)
- **Delta antenne** / correction à appliquer (si nécessaire)

 ## Contraintes et règles
- ⚠️ La référence s'applique uniquement pendant la période de validité de l'installation
- 🔁 Une modification d'installation (déplacement ou remplacement) doit créer une nouvelle référence
- ✅ Une référence nécessite un résultat de positionnement valide pour être considérée active

 ## Cas d'usage
- Création automatique après une intervention d'installation réussie (workflow validation)
- Mise à jour/création lors d'une maintenance avec déplacement (nouvelle installation)
- Suppression ou mise en attente si le résultat de positionnement est invalide

 ---

 # 📍 Objet Référence Altimétrique

 ## Cas d'usage
- Création automatique après une intervention d'installation réussie (workflow validation)
- Mise à jour/création lors d'une maintenance avec déplacement (nouvelle installation)
- Suppression ou mise en attente si le résultat de positionnement est invalide

 ---

 # 📍 Objet Référence Altimétrique

 | **Résultat de validation**  | **Nouveau point de mesure** | **Point de mesure existant** |
 | --- | --- | --- |
 | **Intervention d’installation validée** | - création d’une intervention avec les champs remplis sur la base des données de la validation
       - création d’une nouvelle installation liée
- création d’une requête de positionnement avec comme date de début d’intégration la date d’installation
- création d’une position de référence une fois la requête exécutée | - création d’une intervention avec les champs rempli sur la base des données de la validation
- mise à jour de l'installation avec intervention de désinstallation
- création d’une nouvelle installation
- création d’une requête de positionnement avec comme date de début d’intégration la date d’installation
- création d’une position de référence une fois la requête exécutée |
| **Installation invalidée** | - création d’une intervention avec boitier vide  | - création d’une intervention avec boitier vide et description adéquate
- mise à jour de l'installation  existante avec l’intervention de désinstallation si vide |