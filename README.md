# A. Fiche d’identité du projet – Version Optimisée

## A.1. Titre
**SAPS – Surveillance Automatisée des Pages SharePoint**

## A.2. Objectifs

- Détecter et suivre les liens brisés, doublons et permissions trop larges.
- Assurer la gouvernance documentaire et la conformité des sites SharePoint et Teams.
- Fournir un tableau de bord centralisé pour le pilotage et la communication.

## A.3. Portée

- Sites SharePoint et Teams (catalogués dans `SitesCatalog.xlsx`).
- Liens, documents, pages et métadonnées.
- Gouvernance des accès et suivi des corrections.
- Archivage et gestion du cycle de vie des données.

## A.4. Livrables

- **SitesCatalog.xlsx** : catalogue des sites avec métadonnées (Owner, Priority, Frequency).
- **Tables de suivi** :
  - `BrokenLinkScan` (liens actifs)
  - `BrokenLinkArchive` (liens archivés)
  - `DuplicateFiles` (doublons détectés)
  - `PermissionAudit` (permissions trop larges)
- **Tableau de bord Power BI** :
  - Vue Active, Archive, Doublons, Gouvernance
  - Indicateurs clés (liens brisés, SLA, permissions, doublons, erreurs)
- **Rapports automatiques** :
  - Export mensuel (Excel/PDF)
  - Notifications ciblées (Outlook/Teams)

## A.5. Bénéfices
- Qualité documentaire : réduction des erreurs et doublons.
- Conformité : surveillance des permissions et respect des SLA.
- Efficacité : automatisation des scans et notifications.
- Clarté : tableau de bord unique pour la gouvernance.
- Évolutivité : architecture modulaire, extensible vers de nouvelles fonctionnalités.

# B. Timeline (avec buffer)

- Phase 0 : Préparation et buffer initial → 1-2 semaines
- Phase 1 : Fondations → 2-3 semaines
- Phase 2 : Validation et suivi → 2-3 semaines
- Phase 3 : Communication automatique (notifications, escalades, rapports consolidés) → 1 semaine
- Phase 4 : Archivistique (données + rollbacks système) → 2-2,5 semaines
- Phase 5 : Qualité documentaire → 1,5 semaine
- Phase 6 : Gouvernance avancée → 3-4 semaines
- Phase 7 : Visualisation et communication → 2-3 semaines
- Phase 8 : Escalade et conformité → 2-3 semaines
- Phase 9 : Optimisation continue → 1,5 semaine

**Durée totale estimée : 16-20 semaines (~4 à 5 mois, buffer inclus)**

# C. Estimation annuelle des gains de temps avec SAPS

## C.1. Hypothèse de base
- Temps sans SAPS : ~14 h/mois par site.
- Temps avec SAPS : ~4,5 h/mois par site.
- Gain moyen : ≈ 65–70 %.

## C.2. Calcul annuel (par site)

- **Sans SAPS** : 14 h × 12 mois = **168 h/an**.
- **Avec SAPS** : 4,5 h × 12 mois = **54 h/an**.
- **Temps gagné** : 168 – 54 = **114 h/an**.
- **Pourcentage de gain** : (114 ÷ 168) ≈ **68 %**.

## C.3. Estimation annuelle des gains de temps avec SAPS

| Nombre de sites | Temps sans SAPS (h/an) | Temps avec SAPS (h/an) | Temps gagné (h/an) | % Gain |
|-----------------|-------------------------|-------------------------|--------------------|--------|
| 1 site          | 168                     | 54                      | 114                | 68 %   |
| 5 sites         | 840                     | 270                     | 570                | 68 %   |
| 10 sites        | 1680                    | 540                     | 1140               | 68 %   |
| 15 sites        | 2520                    | 810                     | 1710               | 68 %   |
| 20 sites        | 3360                    | 1080                    | 2280               | 68 %   |
| 25 sites        | 4200                    | 1350                    | 2850               | 68 %   |

## C.4. Synthèse
- **Par site** : ≈ 114 h économisées par an.
- **Organisation multi‑sites** : les gains se multiplient proportionnellement.
- **Impact stratégique** : réduction significative de la charge manuelle, meilleure conformité et adoption facilitée.

## C.5. Notes
- Les chiffres sont indicatifs et doivent être ajustés selon la taille réelle des sites et la fréquence des corrections.
- Les gains sont maximisés si les rapports SAPS sont utilisés régulièrement et les Owners corrigent rapidement.

# D. Étapes détaillées

## D.1. Étape 1 : Préparer le catalogue des sites

### D.1.1. Objectif
Centraliser les URLs principales des sites à scanner dans un fichier unique (`SitesCatalog.xlsx`).

### D.1.2. Structure du fichier
| Colonne    | Description                                      | Exemple                                         |
|------------|--------------------------------------------------|-------------------------------------------------|
| SiteUrl    | URL principale du site                           | `https://exemple.com/sites/Finance`             |
| Owner      | Responsable du site                              | `prenom.nom@exemple.com`                        |
| Priority   | Niveau de priorité (P1, P2, P3)                  | `P1`                                            |
| Frequency  | Fréquence de scan (Daily, Weekly, Monthly)       | `Daily`                                         |
| Notes      | Commentaires ou précisions (optionnel)           | `Site sensible, vérifier liens externes`        |

### D.1.3. Instructions
1. Créer le fichier `SitesCatalog.xlsx` dans un espace partagé (OneDrive, Teams).
2. Remplir la colonne `SiteUrl` avec les URLs principales des sites.
3. Ajouter les métadonnées (Owner, Priority, Frequency).
4. Utiliser ce fichier comme source pour l’automatisation et Power BI.

## D.2. Étape 2 : Créer la liste de résultats

### D.2.1. Objectif
Centraliser les résultats des scans de liens dans une base de données exploitable par Power BI.

### D.2.2. Structure
| Colonne     | Description                                    | Exemple                                         |
|-------------|------------------------------------------------|-------------------------------------------------|
| SiteUrl     | URL principale du site                         | `https://exemple.com/sites/Finance`             |
| SourceUrl   | URL de la page ou du fichier                   | `https://exemple.com/sites/Finance/page.aspx`   |
| SourceType  | Type de source (Page, List, Doc)               | `Page`                                          |
| LinkUrl     | Lien détecté                                   | `https://exemple.com/document.pdf`              |
| LinkScope   | Portée du lien (Interne ou Externe)            | `Externe`                                       |
| Status      | Résultat de la validation (OK, Error, Timeout) | `Error`                                         |
| LastChecked | Date et heure de la dernière vérification      | `2025-12-10 23:00`                              |
| Owner       | Responsable du site ou de la page              | `prenom.nom@exemple.com`                        |

### D.2.3. Instructions
1. Lire le fichier `SitesCatalog.xlsx`.
2. Boucler sur chaque `SiteUrl`.
3. Extraire les liens des pages ou fichiers.
4. Valider chaque lien (HTTP ou équivalent).
5. Écrire les résultats dans `BrokenLinkScan`.

## D.3. Étape 3 : Construire le flux d’automatisation

### D.3.1. Objectif
Automatiser l’extraction des liens et leur validation, puis stocker les résultats dans `BrokenLinkScan`.

### D.3.2. Déclencheur
- Planifier une exécution quotidienne ou hebdomadaire.

### D.3.3. Étapes principales
1. Lire le fichier `SitesCatalog.xlsx`.
2. Boucler sur chaque `SiteUrl`.
3. Extraire les liens des pages ou fichiers.
4. Valider chaque lien.
5. Écrire les résultats dans `BrokenLinkScan`.

### D.3.4. Notifications
1. Définir une condition : si `Status = Error`.
2. Envoyer un mail ou un message Teams au `Owner`.
3. Définir un seuil d’alerte (ex. plus de 20 liens brisés).

## D.4. Étape 4 : Modèle de communication automatique SAPS

### D.4.1. Notification standard (Owner)
**Objet :** [SAPS] Lien brisé détecté sur votre site  
**Message :**  
Bonjour [Owner],  

Le système SAPS a détecté un problème sur votre site :  

- **Site :** https://exemple.com/sites/Finance  
- **Page :** /Docs/rapport.aspx  
- **Lien en erreur :** https://exemple.com/document.pdf  
- **Statut :** 404 – Not Found  
- **Date de détection :** 2025-12-11  

Merci de corriger ce lien dans un délai de 7 jours (SLA).  
En cas de difficulté, veuillez ajouter un commentaire dans SAPS (colonne Notes).  

Cordialement,  
SAPS – Gouvernance documentaire

### D.4.2. Escalade automatique (Gestionnaire)
**Objet :** [SAPS] Escalade – Lien non corrigé après SLA  
**Message :**  
Bonjour [Gestionnaire],  

Le lien suivant n’a pas été corrigé dans le délai de 7 jours :  

- **Site :** https://exemple.com/sites/Finance  
- **Lien en erreur :** https://exemple.com/document.pdf  
- **Ow ner :** prenom.nom@exemple.com  
- **Date de détection :** 2025-12-11  
- **SLA :** Dépassé  

Veuillez prendre les mesures nécessaires pour assurer la correction.  

Cordialement,  
SAPS – Gouvernance documentaire

### D.4.3. Rapport consolidé (Mensuel)
**Objet :** [SAPS] Rapport mensuel – Liens et doublons  
**Message :**  
Bonjour,  

Voici le rapport mensuel généré par SAPS :  

- Liens scannés : 12 450  
- Liens brisés : 320 (2,6 %)  
- Doublons détectés : 85 fichiers  
- Permissions trop larges : 12 cas  
- SLA respectés : 92 %  
- Rollbacks effectués : 2  

Le rapport détaillé est disponible dans Power BI.  

Cordialement,  
SAPS – Gouvernance documentaire

## D.5. Étape 5 : Construire le rapport Power BI

### D.5.1. Objectif
Créer un rapport moderne et clair qui présente les statistiques clés.

### D.5.2. Sources
- `BrokenLinkScan` pour les résultats.
- `SitesCatalog.xlsx` pour les métadonnées.

### D.5.3. Indicateurs clés
- Total de liens scannés.
- Nombre de liens brisés.
- Taux de liens brisés (%).
- Date du dernier scan.

### D.5.4. Mise en page recommandée
- Section 1 : Vue d’ensemble (cartes : TotalLinks, BrokenLinks, BrokenRate, LastCheckDate).
- Section 2 : Analyse par site (tableau + graphique en barres horizontales).
- Section 3 : Détails des liens (table + filtres).

## D.6. Étape 6 : Gouvernance et extension

### D.6.1. Objectif
Assurer la pérennité du système et définir des règles claires.

### D.6.2. Gouvernance
1. Centraliser la gestion des sites dans `SitesCatalog.xlsx`.
2. Définir un Owner pour chaque site.
3. Classer les sites par priorité.
4. Documenter la fréquence de scan.
5. Suivre les actions correctives.

### D.6.3. Notifications et escalade
1. Envoyer une notification au Owner lorsqu’un lien est en erreur.
2. Définir des seuils d’alerte (20 liens brisés, persistance > 7 jours).
3. Conserver un historique des notifications.
4. Escalader les problèmes critiques vers un canal dédié.

## D.7. Étape 7 : Archivistique et cycle de vie

### D.7.1. Objectif
Assurer que les données anciennes soient archivées et validées.

### D.7.2. Structure
Créer une table `BrokenLinkArchive` avec les colonnes suivantes :

| Colonne     | Description                                    | Exemple                                         |
|-------------|------------------------------------------------|-------------------------------------------------|
| SiteUrl     | URL principale du site                         | `https://exemple.com/sites/Finance`             |
| SourceUrl   | URL de la page ou du fichier                   | `https://exemple.com/sites/Finance/page.aspx`   |
| SourceType  | Type de source (Page, List, Doc)               | `Page`                                          |
| LinkUrl     | Lien détecté                                   | `https://exemple.com/document.pdf`              |
| LinkScope   | Portée du lien (Interne ou Externe)            | `Externe`                                       |
| Status      | Résultat de la validation (OK, Error, Timeout) | `Error`                                         |
| LastChecked | Date et heure de la dernière vérification      | `2024-11-30 23:00`                              |
| ArchiveDate | Date de transfert vers l’archive               | `2025-12-01 00:00`                              |
| Owner       | Responsable du site ou de la page              | `prenom.nom@exemple.com`                        |
| Notes       | Commentaires ou suivi                          | `Lien à vérifier manuellement`                  |

### D.7.3. Processus
1. Configurer un flux mensuel.
2. Déplacer les enregistrements plus anciens que 12 mois vers `BrokenLinkArchive`.
3. Ajouter la date de transfert dans `ArchiveDate`.
4. Notifier le Owner que des liens ont été archivés.
5. Conserver les archives pour une durée définie (ex. 3 ans).

### D.7.4. Power BI
1. Créer deux vues : Active (`BrokenLinkScan`) et Archive (`BrokenLinkArchive`).
2. Ajouter un filtre temporel.
3. Mettre en place un indicateur : nombre de liens en attente de validation dans l’archive.

## D.8. Étape 8 : Archivage du système SAPS et rollbacks

### D.8.1. Objectif
Assurer la possibilité de restaurer une version antérieure du système SAPS (flux, tables, rapports, configurations) en cas de problème technique ou organisationnel.

### D.8.2. Structure
Créer une table `RollbackLog` pour tracer les restaurations :

| Colonne       | Description                                     | Exemple                                         |
|---------------|-------------------------------------------------|-------------------------------------------------|
| Component     | Élément concerné (Flux, Table, Rapport, Config) | `Flux Power Automate`                           |
| Version       | Numéro ou identifiant de version                | `v3.2`                                          |
| ArchiveDate   | Date de sauvegarde                              | `2025-12-10 23:00`                              |
| RestoredDate  | Date de restauration                            | `2025-12-15 09:00`                              |
| Owner         | Responsable de l’action                         | `prenom.nom@exemple.com`                        |
| Action        | Type d’action (Sauvegarde, Rollback, Upgrade)   | `Rollback`                                      |
| Notes         | Commentaires ou suivi                           | `Retour à version stable v3.1`                  |

### D.8.3. Processus
1. Sauvegarder régulièrement les flux Power Automate (export `.zip` ou `.json`).
2. Versionner les rapports Power BI (`.pbix`) et conserver un historique.
3. Exporter les tables principales (`BrokenLinkScan`, `DuplicateFiles`, etc.) en CSV/Excel.
4. Documenter chaque changement dans un registre (changelog).
5. En cas de problème, restaurer la dernière version stable depuis les archives.
6. Notifier les Owners et gestionnaires lors d’un rollback.

### D.8.4. Power BI
- Vue dédiée aux rollbacks et versions archivées.
- Indicateurs :
  - Nombre de rollbacks effectués.
  - Composants les plus restaurés.
  - Temps moyen entre incident et rollback.
- Graphiques :
  - Évolution du nombre de versions par composant.
  - Répartition des actions (Sauvegarde, Rollback, Upgrade).

### D.8.5. Gouvernance
- Restreindre les droits de rollback aux administrateurs.
- Documenter chaque rollback dans `RollbackLog`.
- Intégrer le suivi des rollbacks dans les rapports mensuels.
- Conserver les archives pendant au moins 3 ans.

## D.9. Étape 9 : Gouvernance des rollbacks SAPS

### D.9.1. Objectif
Encadrer l’archivage et la restauration du système SAPS (flux, tables, rapports, configurations) afin de garantir la continuité de service et la traçabilité des actions.

### D.9.2 Règles de gouvernance
**Archivage systématique**

   - Sauvegarder chaque version des flux Power Automate (export `.zip` ou `.json`).
   - Versionner les rapports Power BI (`.pbix`) et conserver un historique.
   - Exporter régulièrement les tables principales (`BrokenLinkScan`, `BrokenLinkArchive`, `DuplicateFiles`, `PermissionAudit`).
**Registre des rollbacks**
   - Utiliser la table `RollbackLog` pour tracer toutes les opérations de sauvegarde et de restauration.
   - Champs obligatoires : Component, Version, ArchiveDate, RestoredDate, Owner, Action, Notes.

**Droits et responsabilités**
   - Restreindre les droits de rollback aux administrateurs ou gestionnaires désignés.
   - Les Owners sont responsables de la validation des données restaurées.
   - Chaque rollback doit être approuvé par un référent gouvernance.

**Processus de rollback**
   - Identifier la version stable précédente.
   - Restaurer les flux, rapports et tables depuis les archives.
   - Vérifier la cohérence des données restaurées.
   - Notifier les Owners et gestionnaires concernés.

**Durée de conservation**
   - Conserver les archives et versions pendant au moins 3 ans.
   - Supprimer uniquement après validation par l’équipe de gouvernance.

**Suivi et reporting**
   - Intégrer le suivi des rollbacks dans les rapports mensuels Power BI.
   - Indicateurs clés :
     - Nombre de rollbacks effectués.
     - Composants restaurés.
     - Temps moyen entre incident et rollback.
   - Graphiques :
     - Évolution du nombre de versions par composant.
      - Répartition des actions (Sauvegarde, Rollback, Upgrade).

  ### D.9.3. Résultat attendu
  - Traçabilité complète des sauvegardes et restaurations.
  - Réduction du risque lié aux mises à jour ou évolutions.
  - Pérennité du système SAPS grâce à une gouvernance claire et documentée.

  ## D.10. Étape 10 : Détection des fichiers doublons

  ### D.10.1. Objectif
  Identifier les fichiers en doublon, même lorsqu’ils portent des noms différents, et les centraliser dans une liste pour vérification manuelle.

  ### D.10.2. Structure de la liste
  Créer une table `DuplicateFiles` avec les colonnes suivantes :

  | Colonne       | Description                                                   | Exemple                                         |
  |---------------|---------------------------------------------------------------|-------------------------------------------------|
  | FileName      | Nom du fichier détecté                                        | `rapport_final_v2.xlsx`                         |
  | FilePath      | Chemin ou URL du fichier                                      | `https://exemple.com/sites/Finance/Docs/file`   |
  | FileType      | Type de fichier (Excel, Word, PowerPoint, PDF)                | `Excel`                                         |
  | FileHash      | Empreinte unique calculée sur le contenu du fichier           | `A1B2C3D4E5...`                                 |
  | DuplicateGroup| Identifiant du groupe de doublons                             | `Group_001`                                     |
  | Owner         | Responsable du fichier                                        | `prenom.nom@exemple.com`                        |
  | LastChecked   | Date de la dernière vérification                              | `2025-12-10 23:00`                              |
  | Notes         | Commentaires ou suivi                                         | `À fusionner avec version finale`               |

  ### D.10.3. Méthode de détection
  1. Extraction des fichiers : parcourir les bibliothèques définies dans `SitesCatalog.xlsx`.
  2. Calcul d’empreinte (hash MD5/SHA256).
  3. Comparaison : grouper les fichiers ayant le même hash.
  4. Écriture des résultats dans `DuplicateFiles`.

  ### D.10.4. Automatisation
  - Configurer un flux planifié (mensuel ou trimestriel).
  - Lire les fichiers des sites listés dans `SitesCatalog.xlsx`.
  - Calculer les empreintes et comparer.
  - Écrire les doublons dans `DuplicateFiles`.
  - Notifier les Owners concernés pour validation.

  ### D.10.5. Power BI
  1. Créer une vue dédiée aux doublons.
  2. Indicateurs clés :
    - Nombre total de doublons détectés.
    - Répartition par type de fichier.
    - Sites les plus concernés.
  3. Table détaillée : FileName, FilePath, DuplicateGroup, Owner.
  4. Graphiques : histogramme par site, répartition par type de fichier.

  ### D.10.6. Gouvernance
  1. Définir une règle : tout doublon doit être validé ou supprimé sous 30 jours.
  2. Documenter les doublons critiques (contrats, rapports financiers).
  3. Suivre les actions correctives dans la colonne `Notes`.

  ## D.11. Étape 11 : Nouvelles fonctionnalités de gouvernance et de suivi

  ### D.10.1 Suivi des permissions et accès
  - Table `PermissionAudit` : ResourceUrl, ResourceType, Owner, Permission, LastChecked, Notes.
  - Processus :
    1. Scanner les ressources.
    2. Identifier les permissions trop larges.
    3. Écrire les résultats dans `PermissionAudit`.
    4. Notifier les Owners concernés.

  ### D.10.2 Historique des corrections
  - Ajouter deux colonnes dans `BrokenLinkScan` et `DuplicateFiles` :
    - `CorrectionDate`
    - `CorrectionAction`
  - Processus :
    1. Lorsqu’un Owner corrige un problème, mettre à jour les colonnes.
    2. Conserver l’historique pour analyse.
  - Power BI : indicateur temps moyen de correction, graphique évolution mensuelle.

  ### D.10.3 SLA et délais de correction
  - Ajouter une colonne `SLAStatus` : Respecté / Dépassé.
  - Processus :
    1. Comparer `LastChecked` et `CorrectionDate`.
    2. Si délai > 7 jours, marquer `SLAStatus = Dépassé`.
    3. Envoyer notification d’escalade.
  - Power BI : % de corrections respectant le SLA, sites avec dépassements.

  ### D.10.4 Export automatique des rapports
  - Flux mensuel : exporter données (`BrokenLinkScan`, `BrokenLinkArchive`, `DuplicateFiles`, `PermissionAudit`).
  - Générer PDF ou Excel.
  - Envoyer par email aux gestionnaires.

  ### D.10.5 Validation des liens externes critiques
  - Ajouter colonne `CriticalLink` (Oui/Non) dans `BrokenLinkScan`.
  - Processus :
    1. Marquer les liens critiques dans `SitesCatalog.xlsx`.
    2. Si `CriticalLink = Oui` et `Status = Error`, alerte immédiate.
    3. Escalader si problème persiste > 24h.
  - Power BI : indicateur nombre de liens critiques en erreur.

  ### D.10.6 Catégorisation des erreurs
  - Enrichir `Status` avec codes précis : 404, 403, Timeout, Server Error, Permission Denied.
  - Processus :
    1. Capturer le code d’erreur HTTP ou système.
    2. Écrire le code exact dans `Status`.
  - Power BI : graphique en camembert (répartition des erreurs), tableau par site.

  ## D.12. Résultat attendu
  - Gouvernance renforcée (permissions, SLA, corrections).
  - Suivi complet des actions correctives.
  - Rapports automatiques pour les gestionnaires.
  - Surveillance proactive des liens critiques.
  - Analyse fine des erreurs pour améliorer la qualité documentaire.

# E. Architecture globale du projet

## E.1. Flux d’automatisation SAPS

- SitesCatalog.xlsx
  - Flux d’automatisation
    - Extraction des liens
      - Pages
      - Docs
      - Listes
    - Validation des liens
      - OK / Error / Timeout
      - BrokenLinkScan (données actives)
    - Communication automatique
      - Notifications standard (Owners)
      - Escalades automatiques (Gestionnaires)
      - Rapports consolidés (Mensuel)
    - Archivage des données anciennes (> 12 mois)
      - BrokenLinkArchive (données archivées)
    - Archivage du système SAPS et rollbacks
      - RollbackLog (versions, sauvegardes, restaurations)
    - Détection des doublons
      - Excel
      - Word
      - PowerPoint
      - PDF
      - DuplicateFiles (liste des fichiers en doublon)
    - Gouvernance et suivi
      - PermissionAudit (permissions trop larges)
      - Historique des corrections (CorrectionDate, CorrectionAction)
      - SLAStatus (Respecté / Dépassé)
      - Export mensuel (PDF/Excel)
      - Liens critiques (CriticalLink)
      - Catégorisation des erreurs (404, 403, Timeout, etc.)
    - Notifications (Outlook / Teams)
      - Owners et gestionnaires

## E.2. Power BI – Vues et indicateurs

- Vues principales
  - Vue Active (BrokenLinkScan)
  - Vue Communication (Notifications, Escalades, Rapports)
  - Vue Archive (BrokenLinkArchive)
  - Vue Rollbacks (RollbackLog)
  - Vue Doublons (DuplicateFiles)
  - Vue Gouvernance
    - PermissionAudit
    - SLA
    - Corrections
    - Liens critiques
    - Erreurs

- Indicateurs clés
  - Total de liens scannés
  - Liens brisés
  - Taux de liens brisés (%)
  - Nombre de doublons détectés
  - Permissions trop larges
  - % SLA respectés
  - Nombre de liens critiques en erreur
  - Répartition des types d’erreurs
  - Nombre de rollbacks effectués
  - Nombre de notifications envoyées / escalades

- Analyse par site
  - SiteUrl
  - Owner
  - Priority
  - Graphiques comparatifs
    - Liens brisés
    - Doublons
    - Permissions
    - SLA
    - Rollbacks
    - Notifications

- Détails
  - SourceUrl, LinkUrl, Status, LastChecked
  - FileName, FilePath, FileType, DuplicateGroup
  - Permission, CorrectionDate, CorrectionAction, SLAStatus
  - CriticalLink, ErrorType
  - Rollback version, ArchiveDate, RestoredDate, Action
  - NotificationType, EscalationLevel, ReportDate
  - Filtres par type, portée, priorité

# F. Timeline (avec buffer)

## F.1. Phase Préliminaire : Préparation et cadrage – 1 à 2 semaines
- [ ] Étape 0 : Mise en place du buffer initial (Poids : 3) – 3 à 4 jours
  - Définir marges de sécurité (20–30 % du temps).
  - Identifier les dépendances critiques (Owners, accès, gouvernance).
  - Mettre en place un plan de contingence (scripts externes, sauvegardes).
  - Documenter les risques connus et prévoir les points de contrôle.

## F.2. Phase 1 : Fondations – 2 à 3 semaines
- [ ] Étape 1 : Catalogage des sites (Poids : 2) – 2 à 3 jours
- [ ] Étape 2 : Extraction des liens (Poids : 3) – 3 à 4 jours

## F.3. Phase 2 : Validation et suivi – 2 à 3 semaines
- [ ] Étape 3 : Validation des liens (Poids : 4) – 3 à 4 jours
- [ ] Étape 4 : Notifications (Poids : 3) – 2 à 3 jours

## F.4. Phase 3 : Communication automatique – 1 semaine
- [ ] Étape 5 : Mise en place des notifications automatiques, escalades et rapports consolidés (Poids : 4) – 4 à 5 jours

## F.5. Phase 4 : Archivistique – 2 semaines
- [ ] Étape 6 : Archivage des données (Poids : 3) – 3 jours
- [ ] Étape 7 : Gouvernance de l’archive (Poids : 2) – 2 à 3 jours
- [ ] Étape 8 : Archivage du système SAPS et rollbacks (Poids : 4) – 3 à 4 jours

## F.6. Phase 5 : Qualité documentaire – 1,5 semaine
- [ ] Étape 9 : Détection des doublons (Poids : 5) – 5 jours

## F.7. Phase 6 : Gouvernance avancée – 3 à 4 semaines
- [ ] Étape 10.1 : Suivi des permissions (Poids : 5) – 4 à 5 jours
- [ ] Étape 10.2 : Historique des corrections (Poids : 4) – 3 jours
- [ ] Étape 10.3 : SLA et délais de correction (Poids : 5) – 4 à 5 jours
- [ ] Étape 10.4 : Export automatique des rapports (Poids : 4) – 3 jours
- [ ] Étape 10.5 : Validation des liens critiques (Poids : 4) – 3 jours
- [ ] Étape 10.6 : Catégorisation des erreurs (Poids : 3) – 3 jours

## F.8. Phase 7 : Visualisation et communication – 2 à 3 semaines
- [ ] Étape 11 : Power BI avancé (Poids : 5) – 5 à 7 jours

## F.9. Phase 8 : Escalade et conformité – 2 à 3 semaines
- [ ] Étape 12 : Automatisation des escalades (Poids : 4) – 4 à 5 jours
- [ ] Étape 13 : Alignement avec gouvernance documentaire (Poids : 3) – 3 à 4 jours

## F.10. Phase 9 : Optimisation continue – 1,5 semaine
- [ ] Étape 14 : Suivi de volumétrie (Poids : 2) – 3 jours
- [ ] Étape 15 : Améliorations futures (Poids : 2) – 3 jours

## F.11. Totaux
- Nombre total de tâches : **21** (13 à 17 semaines avec buffer)
- Poids total (somme des difficultés) : **73**

## F.12. Notes
- L’étape 0 sécurise le projet avant le démarrage effectif.  
- La **Phase 3** introduit la communication automatique pour éviter la surcharge humaine.  
- L’archivage inclut désormais le système SAPS et les rollbacks.  
- La durée totale est ajustée à ~4 à 5 mois avec buffer inclus.

# G. Analyse des risques du projet SAPS

## G.1. Risques techniques
- Complexité des sites : surcharge possible lors des scans massifs.
- Automatisation : dépendance aux connecteurs Power Automate.
- Intégration Power BI : risque de latence ou données incomplètes.
- Qualité des métadonnées : erreurs dans `SitesCatalog.xlsx` réduisent la fiabilité.

## G.2. Risques organisationnels
- Disponibilité des Owners : corrections retardées si notifications ignorées.
- Adoption interne : faible utilisation du tableau de bord Power BI.
- Gouvernance documentaire : résistance au changement des équipes.
- Responsabilités floues : absence de processus clair pour corriger les erreurs.

## G.3. Risques sécurité et conformité
- Permissions trop larges : non-conformité prolongée si non corrigée.
- Archivage : risque de perte d’accès à des données importantes.
- Confidentialité : rapports exportés mal partagés → fuite d’information.

## G.4. Risques planning et ressources
- Délais : dépendance aux validations internes.
- Buffer insuffisant : imprévus dépassant les 20-30 % prévus.
- Maintenance : risque de non-suivi après livraison.

# H. Matrice des risques et solutions SAPS

| Catégorie        | Risque                                         | Probabilité | Impact | Mitigation proposée                                                      |
|------------------|------------------------------------------------|-------------|--------|--------------------------------------------------------------------------|
| Technique        | Évolutions Microsoft 365 cassant les flux      | Moyenne     | Élevé  | Veille technologique, versionner les flux et rapports, RollbackLog       |
| Technique        | Scalabilité (trop de liens/sites)              | Élevée      | Élevé  | Segmenter les scans, définir seuils, optimiser requêtes                  |
| Technique        | Performance Power BI lente                     | Moyenne     | Moyen  | Agrégations, Power Query, filtres par site/période                       |
| Technique        | Rollback incomplet (données externes)          | Faible      | Élevé  | Export parallèle des données, tests réguliers de restauration            |
| Organisationnel  | Adoption faible par les Owners                 | Élevée      | Élevé  | Formation courte, guide simple, mise en avant des bénéfices              |
| Organisationnel  | Surcharge des gestionnaires (trop d’escalades) | Moyenne     | Élevé  | Regrouper escalades, seuil critique, tableau de bord central             |
| Organisationnel  | Manque de discipline documentaire              |  Moyenne    | Élevé  | SLA clairs, escalades automatiques, intégration dans gouvernance         |
| Organisationnel  | Conflits de gouvernance                        | Faible      | Élevé  | Comité SAPS, rôles documentés, processus d’arbitrage                     |
| Humain           | Résistance au changement                       | Moyenne     | Moyen  | Positionner SAPS comme aide, valoriser corrections réussies              |
| Humain           | Manque de formation                            | Moyenne     | Élevé  | Tutoriels vidéo courts, FAQ, infobulles Power BI                         |
| Humain           | Fatigue des notifications                      | Élevée      | Moyen  | Consolidation hebdomadaire, seuils anti-spam, Teams centralisé           |
| Sécurité         | Permissions trop larges non corrigées          | Moyenne     | Élevé  | Escalades automatiques, suivi Power BI, audits trimestriels              |
| Sécurité         | Export sensible (PDF/Excel)                    | Moyenne     | Élevé  | Restreindre exports, filigrane “Confidentiel”, stockage sécurisé         |
| Sécurité         | Archivage long terme non conforme              | Faible      | Élevé  | Politique de conservation, conformité RGPD/Loi 25, purge validée         |
| Pilotage         | Sous-estimation du temps de maintenance        | Moyenne     | Élevé  | Allouer 10–15 % du temps, points mensuels, registre des ajustements      |
| Pilotage         | Manque de sponsor exécutif                     | Moyenne     | Élevé  | Appui officiel, présenter SAPS comme outil de conformité                 |
| Pilotage         | Dépendance à une seule personne                | Élevée      | Élevé  | Former un backup, documentation exhaustive, partage des responsabilités  |

## H.1. Stratégies globales de mitigation

- **Technique** : tests de charge, scripts de validation des URLs.  
- **Organisationnel** : processus clair de correction avec SLA et escalade.  
- **Sécurité** : limiter la diffusion des rapports aux personnes autorisées.  
- **Planning** : buffer de 30 % et points de contrôle réguliers.  
- **Maintenance** : documentation complète pour assurer la pérennité.

# I. Solutions du projet SAPS

## I.1. Solution complète – Gestion des évolutions Microsoft 365 et Rollbacks SAPS

### I.1.1. Veille technologique

#### I.1.1.a. Sources officielles
- **Microsoft 365 Roadmap** : filtrer SharePoint, Teams, Power Automate, Power BI.
- **Message Center** : vérifier hebdomadairement les annonces d’impact.
- **Blogs produit et release notes** : Power Platform, Power BI.

#### I.1.1.b. Canal d’alerte
- Créer un canal Teams **“SAPS – Veille”**.
- Configurer des alertes (courriel ou Teams) pour les mots‑clés : *API*, *connector*, *SharePoint*, *Power Automate*, *Power BI*.

#### I.1.1.c. Cadence
- **Hebdomadaire** : scan rapide des annonces, noter les changements à risque.
- **Mensuel** : revue d’impact, décider des tests de régression.

#### I.1.1.d. Journal de veille
Créer une liste **RoadmapWatch** avec colonnes :
- Title
- Category (Automate/BI/SharePoint)
- RiskLevel (Low/Med/High)
- AffectedComponent
- DueDate
- Status (Watch/Test/Fixed)
- Notes

### I.1.2. Versionnement des flux Power Automate

#### I.1.2.a. Structurer en Solutions
- Créer une solution **SAPS.Core**.
- Déplacer les flows existants dans la solution.
- Paramétrer via **variables d’environnement** (SiteCatalogUrl, ArchiveLibrary).

#### I.1.2.b. Export et stockage
- Exporter la solution en Managed (prod) et Unmanaged (dev/test).
- Nommage versionné : `SAPS.Core_v1.4.2_YYYYMMDD.zip`.
- Stocker dans SharePoint/OneDrive avec historique des versions.

#### I.1.2.c. Contrôles
- Checklist avant commit :
  - Connecteurs authentifiés
  - Erreurs gérées (try/catch)
  - Variables d’environnement non hardcodées
- Tests de régression sur un lot réduit (100 liens).

#### I.1.2.d. Restauration rapide
- Choisir la version stable dans l’historique.
- Importer la solution Managed en prod.
- Appliquer variables d’environnement.
- Exécuter test court.
- Journaliser dans **RollbackLog**.

### I.1.3. Versionnement des rapports Power BI

#### I.1.3.a. Basculer vers PBIP
- Convertir `.pbix` en **PBIP** (projet Power BI).
- Organiser dépôt : `/SAPS.BI/Model`, `/Reports`, `/Parameters`.

#### I.1.3.b. Gouvernance
- Stocker PBIP dans SharePoint/Git avec historique.
- Déploiement via **pipelines** : Dev → Test → Prod.
- Interdire publication directe en Prod.

#### I.1.3.c. Performance
- Déplacer calculs lourds en Power Query.
- Créer tables d’agrégation.
- Paramètres de requête pour filtrer par site/période.

#### I.1.3.d. Restauration rapide
- Sélectionner la release PBIP stable.
- Re‑publier via pipeline Prod.
- Vérifier data sources et KPIs.
- Journaliser dans **RollbackLog**.

### I.1.4. RollbackLog et procédures de restauration

#### I.1.4.a. Schéma RollbackLog
| Champ         | Description                                      |
|---------------|--------------------------------------------------|
| Component     | Flow, Solution, Report, Dataset, Paramètres      |
| Version       | vMajor.Minor.Patch (ex. v1.4.2)                  |
| Action        | Save, Deploy, Rollback, Hotfix                   |
| ArchiveDate   | Date de sauvegarde/versionnement                 |
| RestoredDate  | Date de restauration                             |
| Owner         | Responsable de l’opération                       |
| Approval      | Référent gouvernance qui approuve                |
| Reason        | Motif : bug, régression, perf, conformité        |
| AffectedScope | Dev/Test/Prod, ou site(s) ciblé(s)               |
| Result        | Résultat, tests post‑restauration, anomalies     |

#### I.1.4.b. Processus de restauration
1. **Déclenchement** : détecter incident, ouvrir entrée RollbackLog.
2. **Sélectionner version stable** : vérifier dépendances.
3. **Restaurer** :
   - Flows : importer solution Managed.
   - BI : déployer PBIP via pipeline.
4. **Tester** : exécution sur lot réduit, validation KPIs.
5. **Notifier** : communication automatique aux Owners/gestionnaires.
6. **Clôturer** : mise à jour RollbackLog, analyse cause racine.

#### I.1.4.c. Checklists
- **Avant rollback** : identifier version stable, capturer paramètres, geler déploiements.
- **Pendant rollback** : importer solution/rapport, appliquer variables, tests de régression.
- **Après rollback** : communication, journalisation, plan de hotfix.

### I.1.5. Intégration dans SAPS

- **Veille technologique** : Phase 0 (buffer initial).
- **Versionnement Power Automate** : Phase Archivistique (Étape Rollbacks).
- **Versionnement Power BI** : Phase Visualisation et communication.
- **RollbackLog** : Phase Gouvernance avancée, visible dans Power BI (Vue Rollbacks).

## I.2. Solution complète – Scalabilité SAPS

### I.2.1. Segmenter les scans par site ou par lot (batch)

#### I.2.1.a. Objectif
Éviter la surcharge en divisant les traitements en unités plus petites et contrôlées.

#### I.2.1.b. Étapes

1. **Créer une variable d’environnement** `BatchSize` (ex. 500 liens).
2. **Configurer Power Automate** pour :
   - Boucler sur les sites un par un.
   - Découper les liens en lots (500 par cycle).
3. **Stocker les résultats** par lot dans une table intermédiaire `ScanBatch`.
4. **Assembler les résultats** en fin de cycle dans `BrokenLinkScan`.

#### I.2.1.c. Contrôles
- Vérifier que chaque lot est complet (pas de liens manquants).
- Journaliser dans `BatchLog` : Site, LotID, NbLiens, Durée, Statut.

### I.2.2. Définir des seuils de traitement

#### I.2.2.a. Objectif
Limiter la taille des cycles pour éviter dépassements de temps ou quotas Microsoft 365.

#### I.2.2.b. Étapes

1. **Fixer un seuil global** : max 10 000 liens par cycle.
2. **Créer une règle de validation** :
   - Si `NbLiens > 10 000` → diviser automatiquement en sous‑cycles.
   - Si `NbSites > 50` → répartir sur plusieurs jours.
3. **Notifier automatiquement** :
   - Message Teams/Outlook si seuil dépassé.
   - Indiquer le nombre de cycles créés.

#### I.2.2.c. Contrôles
- Ajouter un indicateur dans Power BI : **Volume par cycle**.
- Colorer en rouge si seuil dépassé.

### I.2.3. Optimiser les requêtes et limiter les colonnes exportées

#### I.2.3.a. Objectif
Réduire la charge et accélérer les traitements.

#### I.2.3.b. Étapes
1. **Limiter les colonnes** :
   - Exporter uniquement : `SourceUrl`, `LinkUrl`, `Status`, `LastChecked`.
   - Éviter les colonnes inutiles (Owner, Metadata lourde).
2. **Optimiser les requêtes SharePoint** :
   - Utiliser `CAML Query` ou `OData` avec filtres (`IsDocument=Yes`, `LastModified<12mois`).
   - Paginer les résultats (500 par page).
3. **Optimiser Power BI** :
   - Charger uniquement les tables nécessaires.
   - Créer des vues agrégées pour les indicateurs globaux.

#### I.2.3.c. Contrôles
- Mesurer le temps moyen par cycle.
- Comparer avant/après optimisation.
- Journaliser dans `PerfLog` : NbLiens, Colonnes, Durée, Gain.

### I.2.4. Intégration dans SAPS
- **Segmentation par lot** : intégrée dès **Phase 1 – Extraction des liens**.
- **Seuils de traitement** : contrôles automatiques en **Phase 2 – Validation et suivi**.
- **Optimisation des requêtes** : appliquée en **Phase 4 – Archivistique** et **Phase 6 – Power BI**.
- **Logs associés** :
  - `BatchLog` pour suivi des lots.
  - `PerfLog` pour performance et optimisation.
  - Indicateurs visibles dans Power BI (Volume par cycle, Temps moyen, Colonnes exportées).

### I.2.5. Checklists opérationnelles

#### 1.2.5.a. Avant scan
- Vérifier `BatchSize` paramétré.
- Vérifier seuil global (10 000 liens).
- Vérifier filtres actifs (colonnes minimales).

#### 1.2.5.b. Pendant scan
- Journaliser chaque lot dans `BatchLog`.
- Contrôler temps d’exécution par lot.
- Générer alertes si seuil dépassé.

#### 1.2.5.c. Après scan
- Consolider résultats dans `BrokenLinkScan`.
- Mettre à jour `PerfLog`.
- Vérifier indicateurs Power BI (temps, volume, colonnes).

## I.3. Solution complète – Performance Power BI

### I.3.1. Employer des agrégations et tables résumées

#### I.3.1.a. Objectif
Réduire la charge de calcul et accélérer l’affichage des rapports en Power BI.

#### I.3.1.b. Étapes
1. **Créer des tables d’agrégation** :
   - Exemple : `Agg_BrokenLinks` avec colonnes : SiteUrl, NbLiensBrisés, NbLiensTotal, %Erreur.
   - Exemple : `Agg_Doublons` avec colonnes : SiteUrl, NbDoublons, NbFichiers.
2. **Utiliser des mesures pré‑calculées** :
   - `NbLiensBrisés = COUNTROWS(FILTER(BrokenLinkScan, Status="Error"))`
   - `TauxErreur = DIVIDE(NbLiensBrisés, NbLiensTotal)`
3. **Stocker les agrégations** dans une table dédiée pour éviter recalculs à chaque visualisation.

#### I.3.1.c. Contrôles
- Vérifier que les agrégations couvrent 80 % des besoins.
- Comparer temps de rafraîchissement avant/après (objectif : gain > 30 %).

### I.3.2. Déplacer les calculs lourds dans Power Query

#### I.3.2.a. Objectif
Alléger le modèle DAX en déplaçant les transformations en amont.

#### 1.3.2.b. Étapes
1. **Nettoyer les données dans Power Query** :
   - Supprimer colonnes inutiles.
   - Normaliser formats (dates, URLs).
   - Fusionner tables si nécessaire.
2. **Appliquer les filtres en amont** :
   - Exclure liens archivés (> 12 mois).
   - Exclure doublons exacts déjà traités.
3. **Créer des colonnes calculées** directement dans Power Query :
   - Exemple : `ErrorType` (404, 403, Timeout).
   - Exemple : `CriticalFlag` (Oui/Non).

#### I.3.2.c. Contrôles
- Vérifier que le modèle Power BI contient uniquement les colonnes utiles.
- Mesurer la taille du dataset (objectif : < 1 Go).
- Journaliser dans `PerfLog` : NbColonnes, NbTables, DuréeRefresh.

### I.3.3. Mettre en place des filtres par site ou par période

#### I.3.3.a. Objectif
Limiter la volumétrie affichée et améliorer la lisibilité des rapports.

#### I.3.3.b. Étapes
1. **Créer des paramètres Power BI** :
   - `Param_Site` : liste déroulante des sites.
   - `Param_Période` : mois/année.
2. **Appliquer les filtres aux visuels** :
   - Graphiques comparatifs filtrés par site.
   - Courbes temporelles filtrées par période.
3. **Configurer des vues personnalisées** :
   - Vue “Site” : Owner, NbLiens, NbDoublons, SLA.
   - Vue “Période” : NbLiensBrisés, %Erreur, Rollbacks.

#### I.3.3.c. Contrôles
- Vérifier que les filtres réduisent le temps de rendu.
- Mesurer temps moyen d’affichage (objectif : < 3 secondes par visuel).
- Journaliser dans `PerfLog` : Site, Période, DuréeAffichage.

### I.3.4. Intégration dans SAPS

- **Agrégations** : intégrées dans **Phase 6 – Visualisation et communication**.
- **Power Query optimisé** : appliqué dès l’import des données (BrokenLinkScan, DuplicateFiles).
- **Filtres par site/période** : visibles dans Power BI (Vue Active, Vue Archive, Vue Gouvernance).
- **Logs associés** :
  - `PerfLog` pour suivi des performances.
  - Indicateurs Power BI : DuréeRefresh, DuréeAffichage, TailleDataset.

### I.3.5. Checklists opérationnelles

#### I.3.5.a. Avant publication
- Vérifier tables d’agrégation créées.
- Vérifier colonnes inutiles supprimées.
- Vérifier paramètres Site/Période configurés.

#### I.3.5.b. Pendant utilisation
- Contrôler temps de rafraîchissement.
- Contrôler temps d’affichage des visuels.
- Vérifier cohérence des filtres appliqués.

#### I.3.5.c. Après optimisation
- Mettre à jour `PerfLog`.
- Comparer indicateurs avant/après optimisation.
- Documenter les gains de performance.

## I.4. Solution complète – Rollback incomplet SAPS

### I.4.1. Documenter les dépendances externes (SharePoint, Teams)

#### I.4.1.a. Objectif
Assurer que les restaurations SAPS tiennent compte des systèmes externes qui évoluent indépendamment.

#### I.4.1.b. Étapes
1. **Cartographier les dépendances** :
   - Sites SharePoint utilisés (URL, bibliothèques, listes).
   - Canaux Teams liés (notifications, escalades).
   - Connecteurs Power Automate (Outlook, Graph API).
2. **Créer une table de dépendances** `DependencyMap` :
   - Colonnes : Composant, Type (SharePoint/Teams/API), Version, Dernière vérification, Impact potentiel.
3. **Mettre à jour régulièrement** :
   - Vérification mensuelle des URLs et permissions.
   - Journaliser les changements (ajout/suppression de sites, modification de connecteurs).

#### I.4.1.c. Contrôles
- Vérifier que chaque rollback inclut une revue des dépendances.
- Ajouter un champ `AffectedScope` dans RollbackLog pour noter les systèmes externes impactés.

### I.4.2. Conserver des exports parallèles des données

#### I.4.2.a Objectif
Garantir une copie indépendante des données pour restaurer même si les flux échouent.

#### I.4.2.b. Étapes
1. **Exporter régulièrement** :
   - `BrokenLinkScan` → CSV/Excel.
   - `DuplicateFiles` → CSV/Excel.
   - `PermissionAudit` → CSV/Excel.
2. **Stocker les exports** :
   - Dans une bibliothèque SharePoint sécurisée (`SAPS_Exports`).
   - Activer la conservation des versions.
3. **Automatiser l’export** :
   - Power Automate → export mensuel automatique.
   - Nommage : `SAPS_BrokenLinkScan_YYYYMMDD.xlsx`.

#### I.4.2.c Contrôles
- Vérifier que les exports sont complets et lisibles.
- Journaliser dans `ExportLog` : Table, Date, Format, Statut.

### I.4.3. Tester régulièrement les procédures de restauration

#### I.4.3.a. Objectif
Valider que les rollbacks fonctionnent réellement et ne sont pas seulement théoriques.

#### I.4.3.b. Étapes
1. **Planifier des tests trimestriels** :
   - Restaurer une version antérieure de SAPS (flows + Power BI).
   - Vérifier cohérence des données restaurées.
2. **Créer un environnement de test** :
   - Sandbox avec copies des datasets.
   - Restaurer depuis `RollbackLog` et `SAPS_Exports`.
3. **Valider les indicateurs clés** :
   - NbLiensBrisés, %Erreur, NbDoublons, Permissions.
   - Comparer avec les valeurs attendues.

#### I.4.3.c. Contrôles
- Documenter chaque test dans `TestLog` : Date, Version restaurée, Résultat, Anomalies.
- Escalader automatiquement si un rollback échoue.

### I.4.4. Intégration dans SAPS
- **Dépendances externes** : intégrées dans **Phase 0 – Buffer initial** et mises à jour en continu.
- **Exports parallèles** : intégrés dans **Phase 3 – Communication automatique** et **Phase 4 – Archivistique**.
- **Tests de restauration** : intégrés dans **Phase 7 – Escalade et conformité** (audit trimestriel).
- **Logs associés** :
  - `DependencyMap` pour suivi des dépendances.
  - `ExportLog` pour suivi des exports.
  - `TestLog` pour suivi des restaurations.

### I.4.5. Checklists opérationnelles

#### I.4.5.a. Avant rollback
- Vérifier dépendances externes documentées (`DependencyMap`).
- Vérifier exports disponibles (`SAPS_Exports`).
- Identifier version stable dans `RollbackLog`.

#### I.4.5.b. Pendant rollback
- Restaurer flows et rapports depuis version stable.
- Recharger données depuis exports parallèles.
- Valider cohérence des indicateurs.

#### I.4.5.c. Après rollback
- Mettre à jour `RollbackLog` et `TestLog`.
- Documenter anomalies rencontrées.
- Planifier correctifs si nécessaire.

## I.5. Solution complète – Adoption faible des Owners

### I.5.1. Former les Owners avec des sessions courtes et pratiques

#### I.5.1.a. Objectif
Assurer que les Owners comprennent rapidement SAPS et savent corriger les erreurs sans surcharge.

#### I.5.1.b. Étapes
1. **Planifier des sessions de 30 minutes maximum** :
   - Présentation des fonctionnalités clés (validation des liens, doublons, SLA).
   - Démonstration pratique sur un site réel.
2. **Créer un calendrier de formation** :
   - 2 sessions initiales (lancement).
   - 1 session de rappel après 3 mois.
3. **Utiliser des supports simples** :
   - Slides concises (5–6 pages).
   - Démo live dans Power BI et SharePoint.

#### I.5.1.c. Contrôles
- Évaluer la participation (nombre d’Owners formés).
- Mesurer la correction des erreurs après formation.
- Journaliser dans `TrainingLog` : Date, Participants, Sites, Résultats.

### I.5.2. Créer un guide utilisateur simple (1 page)

#### I.5.2.a. Objectif
Donner aux Owners un support rapide et accessible pour corriger les erreurs.

#### I.5.2.b. Étapes
1. **Rédiger un guide visuel** :
   - Étapes numérotées (1–2–3).
   - Captures d’écran Power BI et SharePoint.
   - Infographie des SLA et escalades.
2. **Format** :
   - PDF 1 page.
   - Disponible dans SharePoint et Teams.
3. **Mise à jour régulière** :
   - Révision tous les 6 mois.
   - Ajout des nouvelles fonctionnalités (ex. Rollbacks).

#### I.5.2.c. Contrôles
- Vérifier que le guide est téléchargé/utilisé.
- Ajouter un QR code vers la FAQ SAPS.
- Journaliser dans `DocLog` : Version, Date, Auteur, Changements.

### I.5.3. Mettre en avant les bénéfices (gain de temps, réduction des erreurs)

#### I.5.3.a. Objectif
Motiver les Owners à utiliser SAPS en montrant les avantages concrets.

#### I.5.3.b. Étapes
1. **Créer un tableau comparatif** :
   - Avant SAPS : corrections manuelles, temps élevé.
   - Avec SAPS : notifications automatiques, corrections rapides.
2. **Mettre en avant les indicateurs clés** :
   - % SLA respectés.
   - Réduction du nombre de doublons.
   - Gain de temps moyen par correction.
3. **Communiquer régulièrement** :
   - Rapport mensuel envoyé aux Owners.
   - Mise en avant des “success stories” (sites ayant corrigé >90 % des erreurs).

#### I.5.3.c. Contrôles
- Vérifier que les indicateurs sont visibles dans Power BI.
- Mesurer l’évolution de l’adoption (nombre de corrections effectuées).
- Journaliser dans `AdoptionLog` : Site, Owner, NbCorrections, SLA, Gains.

### I.5.4. Intégration dans SAPS

- **Formations** : intégrées dans **Phase 2 – Validation et suivi** (après notifications).
- **Guide utilisateur** : livré en **Phase 3 – Communication automatique**.
- **Mise en avant des bénéfices** : intégrée dans **Phase 6 – Visualisation et communication** (Power BI + rapports mensuels).
- **Logs associés** :
  - `TrainingLog` pour suivi des formations.
  - `DocLog` pour suivi des guides.
  - `AdoptionLog` pour suivi des bénéfices et corrections.

### I.5.5. Checklists opérationnelles

#### I.5.5.a. Avant lancement
- Planifier sessions de formation.
- Rédiger guide utilisateur 1 page.
- Configurer indicateurs adoption dans Power BI.

#### I.5.5.b. Pendant utilisation
- Former les nouveaux Owners.
- Diffuser le guide utilisateur.
- Communiquer les bénéfices via rapports mensuels.

#### I.5.5.c. Après 3 mois
- Vérifier taux de correction des erreurs.
- Mettre à jour guide utilisateur.
- Ajuster formation selon feedback.

## I.6. Solution complète – Surcharge des gestionnaires

### I.6.1. Regrouper les escalades par site ou par mois

#### I.6.1.a. Objectif
Éviter que les gestionnaires reçoivent trop de notifications isolées et rendre les escalades lisibles.

#### I.6.1.b. Étapes
1. **Configurer Power Automate** :
   - Collecter toutes les escalades d’un site sur une période donnée (ex. 1 semaine).
   - Générer un rapport consolidé (Excel/PDF).
2. **Envoyer un seul message** :
   - Notification mensuelle ou hebdomadaire par site.
   - Inclure : NbLiens non corrigés, SLA dépassés, doublons critiques.
3. **Stocker les escalades consolidées** :
   - Table `EscalationSummary` avec colonnes : Site, Période, NbEscalades, NbLiensNonCorrigés.

#### I.6.1.c. Contrôles
- Vérifier que chaque gestionnaire reçoit max 1 notification par site/période.
- Journaliser dans `EscalationLog` : Site, Période, NbEscalades, Statut.

### I.6.2. Limiter les escalades à un seuil critique

#### I.6.2.a. Objectif
Ne pas surcharger les gestionnaires avec des erreurs mineures ou isolées.

#### I.6.2.b. Étapes
1. **Définir seuil critique** :
   - Exemple : > 10 liens non corrigés par site.
   - Exemple : > 3 SLA dépassés consécutifs.
2. **Configurer règle d’escalade** :
   - Si seuil atteint → escalade automatique.
   - Sinon → rester au niveau Owner.
3. **Notifier uniquement les cas critiques** :
   - Message clair : “Seuil dépassé – Escalade déclenchée”.

#### I.6.2.c. Contrôles
- Vérifier que les escalades envoyées respectent le seuil.
- Ajouter indicateur Power BI : NbEscaladesCritiques vs NbEscaladesTotales.
- Journaliser dans `EscalationLog` : Seuil, Déclenchement, Date.

### I.6.3. Utiliser Power BI comme tableau de bord central

#### I.6.3.a. Objectif
Centraliser les escalades et éviter la dispersion des informations.

#### I.6.3.b. Étapes
1. **Créer une vue Power BI “Escalades”** :
   - Graphiques par site (NbLiens non corrigés, SLA dépassés).
   - Courbes temporelles (évolution mensuelle).
   - Indicateurs critiques (sites en surcharge).
2. **Configurer filtres** :
   - Par site, par période, par Owner.
   - Vue consolidée pour les gestionnaires.
3. **Automatiser actualisation** :
   - Rafraîchissement quotidien.
   - Export mensuel consolidé.

#### I.6.3.c. Contrôles
- Vérifier que les gestionnaires consultent Power BI au lieu de mails multiples.
- Mesurer temps de réaction aux escalades.
- Journaliser dans `DashboardLog` : NbConsultations, Sites, Périodes.

### I.6.4. Intégration dans SAPS

- **Regroupement des escalades** : intégré dans **Phase 7 – Escalade et conformité**.
- **Seuil critique** : appliqué dans **Phase 7 – Automatisation des escalades**.
- **Tableau de bord Power BI** : intégré dans **Phase 6 – Visualisation et communication**.
- **Logs associés** :
  - `EscalationSummary` pour regroupement.
  - `EscalationLog` pour suivi des seuils.
  - `DashboardLog` pour utilisation du tableau de bord.

### I.6.5. Checklists opérationnelles

#### I.6.5.a. Avant mise en place
- Définir seuil critique (ex. 10 liens).
- Configurer regroupement par site/période.
- Créer vue Power BI “Escalades”.

#### I.6.5.b. Pendant utilisation
- Vérifier consolidation des escalades.
- Contrôler respect du seuil critique.
- Suivre consultations du tableau de bord.

#### I.6.5.c. Après 3 mois
- Évaluer réduction du nombre de notifications.
- Ajuster seuil critique si nécessaire.
- Améliorer vue Power BI selon feedback.

## I.7. Solution complète – Manque de discipline documentaire

### I.7.1. Intégrer SAPS dans les processus officiels de gouvernance

#### I.7.1.a. Objectif
Faire en sorte que SAPS ne soit pas perçu comme un outil optionnel, mais comme une partie intégrante de la gouvernance documentaire.

#### I.7.1.b. Étapes
1. **Inclure SAPS dans les politiques internes** :
   - Ajouter SAPS dans les procédures officielles de gestion documentaire.
   - Définir SAPS comme outil de référence pour la détection des erreurs.
2. **Créer un comité de gouvernance SAPS** :
   - Membres : administrateurs, gestionnaires, Owners.
   - Rôle : suivi des corrections, arbitrage en cas de litige.
3. **Aligner SAPS avec les audits internes** :
   - Utiliser les rapports SAPS comme preuve de conformité.
   - Intégrer SAPS dans les cycles de revue documentaire.

#### I.7.1.c. Contrôles
- Vérifier que SAPS est mentionné dans les chartes de gouvernance.
- Journaliser dans `GovernanceLog` : Processus, Date d’intégration, Responsable.

### I.7.2. Définir des SLA clairs et visibles dans les rapports

#### I.7.2.a. Objectif
Donner aux Owners et gestionnaires des délais précis et mesurables pour corriger les erreurs.

#### I.7.2.b. Étapes
1. **Fixer des SLA standards** :
   - Liens brisés : correction sous 7 jours.
   - Doublons : correction sous 14 jours.
   - Permissions trop larges : correction sous 30 jours.
2. **Afficher les SLA dans Power BI** :
   - Colonne `SLAStatus` : Respecté / Dépassé.
   - Graphiques : % SLA respectés par site.
3. **Notifier automatiquement** :
   - Message Outlook/Teams si SLA dépassé.
   - Escalade au gestionnaire si non corrigé.

#### I.7.2.c. Contrôles
- Vérifier que les SLA sont visibles dans tous les rapports.
- Journaliser dans `SLAStatusLog` : Site, Owner, SLA, Statut.

### I.7.3. Escalader automatiquement en cas de non‑respect

#### I.7.3.a. Objectif
Assurer que les erreurs non corrigées ne restent pas bloquées au niveau Owner.

#### I.7.3.b. Étapes
1. **Configurer Power Automate** :
   - Vérifier SLA chaque jour.
   - Si SLA dépassé → escalade automatique au gestionnaire.
2. **Créer une table `EscalationLog`** :
   - Colonnes : Site, Owner, Erreur, SLA, DateEscalade, Statut.
3. **Notifier les gestionnaires** :
   - Message consolidé (hebdomadaire/mensuel).
   - Indiquer les sites en non‑conformité.

#### I.7.3.c. Contrôles
- Vérifier que toutes les escalades sont tracées.
- Mesurer temps moyen de correction après escalade.
- Journaliser dans `EscalationLog`.

### I.7.4. Intégration dans SAPS

- **Processus officiels** : intégrés dès **Phase 5 – Gouvernance avancée**.
- **SLA clairs** : visibles dans **Phase 6 – Visualisation et communication** (Power BI).
- **Escalades automatiques** : appliquées dans **Phase 7 – Escalade et conformité**.
- **Logs associés** :
  - `GovernanceLog` pour intégration dans les processus.
  - `SLAStatusLog` pour suivi des délais.
  - `EscalationLog` pour suivi des escalades.

### 1.7.5. Checklists opérationnelles

#### 1.7.5.a. Avant lancement
- Intégrer SAPS dans chartes de gouvernance.
- Définir SLA standards (7j, 14j, 30j).
- Configurer escalades automatiques.

#### 1.7.5.b. Pendant utilisation
- Vérifier SLA visibles dans Power BI.
- Contrôler escalades automatiques.
- Suivre corrections dans `GovernanceLog`.

#### 1.7.5.c. Après 3 mois
- Évaluer % SLA respectés.
- Ajuster délais si nécessaire.
- Améliorer processus d’escalade selon feedback.

## I.8. Solution complète – Conflits de gouvernance

### I.8.1. Créer un comité de gouvernance SAPS

#### I.8.1.a. Objectif
Établir une instance officielle pour superviser SAPS, arbitrer les litiges et garantir la cohérence des décisions.

#### I.8.1.b. Étapes
1. **Constituer le comité** :
   - Membres : Administrateurs SAPS, Gestionnaires de sites, Représentants métiers, éventuellement un sponsor exécutif.
   - Taille recommandée : 5 à 7 personnes.
2. **Définir la fréquence des réunions** :
   - Mensuelle pour suivi régulier.
   - Trimestrielle pour revue stratégique.
3. **Mandat du comité** :
   - Suivi des SLA et escalades.
   - Validation des rollbacks critiques.
   - Arbitrage des conflits entre Owners et Gestionnaires.

#### I.8.1.c. Contrôles
- Journaliser dans `GovernanceCommitteeLog` : Date, Participants, Décisions, Actions.
- Vérifier que les décisions sont appliquées dans SAPS.

### I.8.2. Documenter les rôles et responsabilités (Owner, Gestionnaire, Admin)

#### I.8.2.a. Objectif
Clarifier les responsabilités pour éviter les zones grises et les conflits.

#### I.8.2.b. Étapes
1. **Rédiger une matrice RACI** :
   - Owner : Responsable de la correction des erreurs.
   - Gestionnaire : Supervise les SLA et escalades.
   - Admin : Configure SAPS, valide rollbacks, gère Power BI.
2. **Publier la matrice** :
   - Disponible dans SharePoint (bibliothèque Gouvernance).
   - Accessible via Power BI (vue Gouvernance).
3. **Mettre à jour régulièrement** :
   - Révision annuelle ou lors de changements organisationnels.

#### I.8.2.c. Contrôles
- Vérifier que chaque rôle est attribué à une personne réelle.
- Journaliser dans `RoleResponsibilityLog` : Rôle, Nom, Date d’attribution.

### I.8.3. Prévoir un processus d’arbitrage en cas de désaccord

#### I.8.3.a. Objectif
Éviter les blocages lorsque Owners et Gestionnaires ne s’accordent pas sur une correction ou une escalade.

#### I.8.3.b. Étapes
1. **Définir un workflow d’arbitrage** :
   - Étape 1 : Owner et Gestionnaire discutent (délai max 7 jours).
   - Étape 2 : Si désaccord persiste → comité de gouvernance tranche.
   - Étape 3 : Si critique → sponsor exécutif valide.
2. **Automatiser la détection des conflits** :
   - Power Automate → si SLA dépassé + commentaire “désaccord” → déclenche arbitrage.
3. **Tracer les arbitrages** :
   - Table `ArbitrationLog` : Site, Problème, Parties impliquées, Décision, Date.

#### I.8.3.c. Contrôles
- Vérifier que tous les arbitrages sont documentés.
- Mesurer temps moyen de résolution.
- Journaliser dans `ArbitrationLog`.

### I.8.4. Intégration dans SAPS

- **Comité de gouvernance** : intégré dans **Phase 5 – Gouvernance avancée**.
- **Rôles et responsabilités** : visibles dans **Phase 6 – Visualisation et communication** (Power BI).
- **Processus d’arbitrage** : appliqué dans **Phase 7 – Escalade et conformité**.
- **Logs associés** :
  - `GovernanceCommitteeLog` pour suivi des réunions.
  - `RoleResponsibilityLog` pour attribution des rôles.
  - `ArbitrationLog` pour suivi des arbitrages.

### I.8.5. Checklists opérationnelles

#### I.8.5.a. Avant lancement
- Constituer comité de gouvernance.
- Rédiger matrice RACI.
- Définir workflow d’arbitrage.

#### I.8.5.b. Pendant utilisation
- Tenir réunions mensuelles du comité.
- Vérifier rôles attribués et mis à jour.
- Suivre arbitrages dans `ArbitrationLog`.

#### I.8.5.c. Après 6 mois
- Évaluer efficacité du comité.
- Ajuster matrice RACI si nécessaire.
- Améliorer workflow d’arbitrage selon feedback.

## I.9. Solution complète – Résistance au changement

### I.9.1. Positionner SAPS comme outil d’aide, pas de contrôle

#### I.9.1.a. Objectif
Faire percevoir SAPS comme un support à la productivité et à la qualité documentaire, plutôt qu’un outil de surveillance.

#### I.9.1.b. Étapes
1. **Communication initiale** :
   - Présenter SAPS comme un assistant qui réduit les erreurs et simplifie la gestion documentaire.
   - Mettre en avant la neutralité : SAPS détecte, mais ne sanctionne pas.
2. **Messages clés** :
   - “SAPS vous aide à gagner du temps.”
   - “SAPS réduit les doublons et les liens brisés.”
   - “SAPS est un outil de fiabilité, pas de contrôle.”
3. **Canaux de diffusion** :
   - Réunions de lancement.
   - Messages Teams/Outlook.
   - Documentation officielle.

#### I.9.1.c. Contrôles
- Vérifier que les communications utilisent un vocabulaire positif.
- Journaliser dans `ChangeManagementLog` : Date, Message, Canal, Feedback.

### I.9.2. Mettre en avant les gains (moins de doublons, moins de liens brisés)

#### I.9.2.a. Objectif
Montrer les bénéfices concrets pour convaincre les utilisateurs de l’utilité de SAPS.

#### I.9.2.b. Étapes
1. **Créer des indicateurs comparatifs** :
   - Avant SAPS : NbLiensBrisés élevé, NbDoublons élevé.
   - Après SAPS : réduction mesurable.
2. **Afficher les gains dans Power BI** :
   - Graphiques “Avant/Après”.
   - Indicateurs clés : % réduction des erreurs, temps gagné.
3. **Communiquer régulièrement** :
   - Rapport mensuel avec gains par site.
   - Mise en avant des sites exemplaires.

#### I.9.2.c. Contrôles
- Vérifier que les gains sont visibles dans Power BI.
- Journaliser dans `BenefitLog` : Site, Indicateur, Gain, Date.

### I.9.3. Valoriser les corrections réussies dans les rapports

#### I.9.3.a. Objectif
Encourager les utilisateurs en mettant en avant leurs réussites plutôt que leurs erreurs.

#### I.9.3.b. Étapes
1. **Créer une section “Succès” dans Power BI** :
   - Sites ayant corrigé >90 % des erreurs.
   - Owners ayant respecté tous les SLA.
2. **Envoyer des notifications positives** :
   - Message Outlook/Teams : “Bravo, votre site est à jour.”
   - Mentionner les corrections réussies dans les rapports mensuels.
3. **Mettre en place un tableau d’honneur** :
   - Liste des sites exemplaires.
   - Diffusion trimestrielle au comité de gouvernance.

#### I.9.3.c. Contrôles
- Vérifier que les rapports incluent une section positive.
- Journaliser dans `SuccessLog` : Site, Owner, NbCorrections, SLA respectés.

### I.9.4. Intégration dans SAPS

- **Positionnement positif** : intégré dès **Phase 0 – Buffer initial** (communication de lancement).
- **Mise en avant des gains** : intégrée dans **Phase 6 – Visualisation et communication** (Power BI).
- **Valorisation des corrections** : intégrée dans **Phase 7 – Escalade et conformité** (rapports mensuels).
- **Logs associés** :
  - `ChangeManagementLog` pour suivi des communications.
  - `BenefitLog` pour suivi des gains.
  - `SuccessLog` pour suivi des corrections réussies.

### I.9.5. Checklists opérationnelles

#### I.9.5.a. Avant lancement
- Préparer messages positifs (outil d’aide, gains).
- Configurer indicateurs comparatifs dans Power BI.
- Créer section “Succès” dans rapports.

#### I.9.5.b. Pendant utilisation
- Diffuser communications positives.
- Mettre en avant gains dans rapports mensuels.
- Envoyer notifications de réussite aux Owners.

#### I.9.5.c. Après 6 mois
- Évaluer perception des utilisateurs (feedback).
- Ajuster communication si SAPS est perçu comme outil de contrôle.
- Renforcer valorisation des corrections réussies.

## I.10. Solution complète – Manque de formation

### I.10.1. Créer des tutoriels vidéo courts (2‑3 min)

#### I.10.1.a. Objectif
Permettre aux utilisateurs de comprendre rapidement SAPS sans investir trop de temps.

#### I.10.1.b. Étapes
1. **Identifier les cas d’usage clés** :
   - Validation des liens.
   - Correction des doublons.
   - Lecture des rapports Power BI.
2. **Produire des vidéos courtes (2‑3 min)** :
   - Format simple : démonstration écran + voix off.
   - Scénario clair : problème → solution → bénéfice.
3. **Diffuser les vidéos** :
   - Hébergement dans SharePoint ou Stream.
   - Lien intégré dans Teams et Power BI.

#### I.10.1.c. Contrôles
- Vérifier que les vidéos sont accessibles à tous les Owners.
- Journaliser dans `TrainingLog` : Titre, Durée, Date, NbVues.

### I.10.2. Ajouter des infobulles ou guides intégrés dans Power BI

#### I.10.2.a. Objectif
Offrir une aide contextuelle directement dans l’outil, sans quitter le rapport.

#### I.10.2.b. Étapes
1. **Créer des infobulles personnalisées** :
   - Exemple : survol d’un indicateur → explication du SLA.
   - Exemple : survol d’un graphique → définition des doublons.
2. **Ajouter des guides intégrés** :
   - Bouton “Aide” dans Power BI.
   - Lien vers tutoriels vidéo et FAQ.
3. **Mettre à jour régulièrement** :
   - Ajouter les nouvelles fonctionnalités.
   - Adapter selon feedback des utilisateurs.

#### I.10.2.c. Contrôles
- Vérifier que les infobulles apparaissent correctement.
- Journaliser dans `GuideLog` : Rapport, Infobulle, Date, Auteur.

### I.10.3. Prévoir une FAQ accessible

#### I.10.3.a. Objectif
Donner aux utilisateurs une référence rapide pour les questions fréquentes.

#### I.10.3.b. Étapes
1. **Rédiger une FAQ** :
   - Questions courantes : “Comment corriger un lien brisé ?”, “Que faire en cas de doublon ?”.
   - Réponses simples et illustrées.
2. **Publier la FAQ** :
   - Hébergement dans SharePoint.
   - Lien intégré dans Power BI et Teams.
3. **Mettre à jour régulièrement** :
   - Ajouter nouvelles questions selon feedback.
   - Révision trimestrielle.

#### I.10.3.c. Contrôles
- Vérifier que la FAQ est consultée.
- Journaliser dans `FAQLog` : Question, Réponse, Date, NbConsultations.

### 1.10.4. Intégration dans SAPS

- **Tutoriels vidéo** : intégrés dans **Phase 2 – Validation et suivi** (après notifications).
- **Infobulles Power BI** : intégrées dans **Phase 6 – Visualisation et communication**.
- **FAQ** : intégrée dans **Phase 7 – Escalade et conformité** (support continu).
- **Logs associés** :
  - `TrainingLog` pour suivi des vidéos.
  - `GuideLog` pour suivi des guides intégrés.
  - `FAQLog` pour suivi des consultations FAQ.

### I.10.5. Checklists opérationnelles

#### I.10.5.a. Avant lancement
- Produire tutoriels vidéo (2‑3 min).
- Configurer infobulles dans Power BI.
- Rédiger FAQ initiale.

#### I.10.5.b. Pendant utilisation
- Diffuser vidéos et guides intégrés.
- Vérifier accessibilité de la FAQ.
- Collecter feedback utilisateurs.

#### I.10.5.c. Après 3 mois
- Évaluer taux de consultation des vidéos et FAQ.
- Mettre à jour guides et FAQ.
- Ajouter tutoriels selon nouvelles fonctionnalités.

## I.11. Solution complète – Fatigue des notifications

### I.11.1. Consolider les notifications en un rapport hebdomadaire

#### I.11.1.a. Objectif
Réduire la surcharge d’alertes quotidiennes et fournir une vue synthétique aux Owners et gestionnaires.

#### I.11.1.b. Étapes

1. **Configurer Power Automate** :
   - Collecter toutes les notifications générées dans la semaine.
   - Générer un rapport consolidé (Excel ou PDF).
2. **Envoyer un seul message hebdomadaire** :
   - Contenu : NbLiens brisés, NbDoublons, SLA dépassés, Permissions critiques.
   - Format : tableau synthétique + graphiques.
3. **Stocker les rapports consolidés** :
   - Bibliothèque SharePoint `SAPS_WeeklyReports`.
   - Activer la conservation des versions.

#### I.11.1.c. Contrôles
- Vérifier que les Owners reçoivent un seul rapport par semaine.
- Journaliser dans `NotificationLog` : Site, Période, NbNotifications, ConsolidationOK.

### I.11.2. Utiliser Teams pour centraliser les alertes

#### I.11.2.a. Objectif
Éviter la dispersion des notifications par courriel et créer un canal unique de suivi.

#### I.11.2.b. Étapes
1. **Créer un canal Teams “SAPS – Alertes”** :
   - Accessible aux Owners et gestionnaires.
   - Notifications regroupées par site.
2. **Configurer Power Automate** :
   - Envoyer les alertes critiques directement dans le canal.
   - Ajouter un tag @Owner ou @Gestionnaire.
3. **Mettre en place des onglets Teams** :
   - Lien vers Power BI (vue escalades).
   - Lien vers FAQ et guides.

#### I.11.2.c. Contrôles
- Vérifier que toutes les alertes passent par Teams.
- Journaliser dans `TeamsLog` : Date, Site, NbAlertes, NbConsultations.

### I.11.3. Paramétrer des seuils pour éviter le “spam”

#### I.11.3.a. Objectif
Limiter les notifications aux cas réellement critiques.

#### I.11.3.b. Étapes
1. **Définir seuils de déclenchement** :
   - Liens brisés : > 10 non corrigés.
   - SLA dépassés : > 3 consécutifs.
   - Permissions trop larges : > 5 utilisateurs affectés.
2. **Configurer Power Automate** :
   - Si seuil atteint → notification envoyée.
   - Sinon → inclure dans rapport hebdomadaire uniquement.
3. **Notifier uniquement les cas critiques** :
   - Message clair : “Seuil dépassé – Action requise”.

#### I.11.3.c. Contrôles
- Vérifier que les notifications respectent les seuils.
- Ajouter indicateur Power BI : NbAlertesCritiques vs NbAlertesTotales.
- Journaliser dans `ThresholdLog` : Site, Seuil, Déclenchement, Date.

### 11.11.4. Intégration dans SAPS

- **Rapports hebdomadaires** : intégrés dans **Phase 3 – Communication automatique**.
- **Canal Teams** : intégré dans **Phase 7 – Escalade et conformité**.
- **Seuils anti‑spam** : appliqués dans **Phase 7 – Automatisation des escalades**.
- **Logs associés** :
  - `NotificationLog` pour consolidation.
  - `TeamsLog` pour suivi des alertes centralisées.
  - `ThresholdLog` pour suivi des seuils.

### 11.11.5. Checklists opérationnelles

#### 11.11.5.a. Avant lancement
- Configurer rapport hebdomadaire consolidé.
- Créer canal Teams “SAPS – Alertes”.
- Définir seuils critiques.

#### 11.11.5.b. Pendant utilisation
- Vérifier consolidation des notifications.
- Contrôler passage par Teams.
- Suivre déclenchements de seuils.

#### 11.11.5.c. Après 3 mois
- Évaluer réduction du nombre de notifications.
- Ajuster seuils si nécessaire.
- Améliorer format du rapport hebdomadaire selon feedback.

## I.12. Solution complète – Permissions trop larges non corrigées

### I.12.1. Escalader automatiquement aux gestionnaires

#### I.12.1.a. Objectif
S’assurer que les permissions trop larges sont corrigées rapidement et ne restent pas ignorées par les Owners.

#### I.12.1.b. Étapes

1. **Configurer Power Automate** :
   - Vérifier régulièrement les permissions via `PermissionAudit`.
   - Détecter les cas “trop larges” (ex. accès en lecture à *Everyone*).
   - Déclencher une escalade automatique si non corrigé sous 30 jours.
2. **Notifier les gestionnaires** :
   - Message Teams/Outlook : “Permissions trop larges détectées – Action requise”.
   - Inclure : Site, Ressource, Type de permission, Date de détection.
3. **Tracer les escalades** :
   - Table `PermissionEscalationLog` : Site, Ressource, Owner, Gestionnaire, DateEscalade, Statut.

#### I.12.1.c. Contrôles
- Vérifier que toutes les escalades sont envoyées.
- Journaliser dans `PermissionEscalationLog`.

### I.12.2. Intégrer un suivi dans Power BI avec indicateurs rouges

#### I.12.2.a. Objectif
Donner une visibilité claire et immédiate sur les permissions critiques.

#### I.12.2.b. Étapes

1. **Créer une vue Power BI “Permissions”** :
   - Tableau des ressources avec colonnes : Ressource, Owner, TypePermission, DateDétection.
   - Indicateur visuel : rouge = permission trop large, vert = corrigée.
2. **Ajouter des KPI** :
   - NbPermissionsTropLarges.
   - % Permissions corrigées.
   - Temps moyen de correction.
3. **Configurer filtres** :
   - Par site, par Owner, par période.
   - Vue consolidée pour les gestionnaires.

#### I.12.2.c. Contrôles
- Vérifier que les indicateurs rouges apparaissent correctement.
- Journaliser dans `PermissionDashboardLog` : NbPermissions, NbCorrigées, %Corrigées.

### I.12.3. Auditer trimestriellement les permissions

#### I.12.3.a. Objectif

Assurer un contrôle régulier et documenté des permissions pour éviter les dérives.

#### I.12.3.b. Étapes

1. **Planifier audit trimestriel** :
   - Vérifier toutes les permissions via `PermissionAudit`.
   - Identifier les cas non corrigés malgré escalades.
2. **Documenter audit** :
   - Rapport PDF/Excel : NbPermissionsTropLarges, Sites concernés, Actions prises.
   - Stocker dans bibliothèque SharePoint `SAPS_AuditReports`.
3. **Communiquer résultats** :
   - Envoyer rapport au comité de gouvernance.
   - Inclure recommandations et actions correctives.

#### I.12.3.c. Contrôles
- Vérifier que l’audit est réalisé chaque trimestre.
- Journaliser dans `PermissionAuditLog` : DateAudit, NbPermissions, NbCorrigées, Actions.

### I.12.4. Intégration dans SAPS

- **Escalades automatiques** : intégrées dans **Phase 7 – Escalade et conformité**.
- **Indicateurs Power BI** : intégrés dans **Phase 6 – Visualisation et communication**.
- **Audits trimestriels** : intégrés dans **Phase 5 – Gouvernance avancée**.
- **Logs associés** :
  - `PermissionEscalationLog` pour suivi des escalades.
  - `PermissionDashboardLog` pour suivi Power BI.
  - `PermissionAuditLog` pour suivi des audits.

### I.12.5. Checklists opérationnelles

#### I.12.5.a. Avant lancement
- Configurer escalades automatiques.
- Créer vue Power BI “Permissions”.
- Planifier audits trimestriels.

#### I.12.5.b. Pendant utilisation
- Vérifier escalades envoyées.
- Contrôler indicateurs rouges dans Power BI.
- Réaliser audits trimestriels.

#### I.12.5.c. Après 6 mois
- Évaluer % permissions corrigées.
- Ajuster seuils d’escalade si nécessaire.
- Améliorer processus d’audit selon feedback.

## I.13. Solution complète – Export sensible

### I.13.1. Restreindre les exports PDF/Excel aux administrateurs

#### I.13.1.a. Objectif
Limiter la diffusion des données sensibles en contrôlant qui peut exporter les rapports.

#### I.13.1.b. Étapes
1. **Configurer les permissions Power BI** :
   - Autoriser uniquement les administrateurs SAPS à exporter en PDF/Excel.
   - Désactiver l’option “Exporter les données” pour les autres rôles.
2. **Mettre en place un rôle spécifique** :
   - Rôle “SAPS_AdminExport” avec droits d’export.
   - Associer uniquement les administrateurs validés.
3. **Tracer les exports** :
   - Table `ExportLog` : Utilisateur, Rapport, Format, Date, Statut.

#### I.13.1.c. Contrôles
- Vérifier que seuls les administrateurs apparaissent dans `ExportLog`.
- Auditer mensuellement les exports réalisés.

### I.13.2. Ajouter un filigrane “Confidentiel” aux rapports

#### I.13.2.a. Objectif
Marquer visuellement les exports pour rappeler leur caractère sensible.

#### I.13.2.b. Étapes
1. **Configurer Power BI** :
   - Ajouter un visuel texte “Confidentiel” en arrière‑plan.
   - Paramétrer pour qu’il apparaisse sur toutes les pages exportées.
2. **Automatiser le filigrane** :
   - Utiliser un modèle standard pour tous les rapports SAPS.
   - Vérifier que le filigrane reste visible en PDF/Excel.
3. **Mettre à jour régulièrement** :
   - Adapter le filigrane selon les politiques internes (ex. “Usage interne uniquement”).

#### I.13.2.c. Contrôles
- Vérifier que chaque export contient le filigrane.
- Journaliser dans `WatermarkLog` : Rapport, Date, Filigrane appliqué.

### I.13.3. Stocker les exports dans un espace sécurisé

#### I.13.3.a. Objectif
Empêcher la diffusion non contrôlée des fichiers exportés.

#### I.13.3.b. Étapes
1. **Créer une bibliothèque SharePoint sécurisée** :
   - Nom : `SAPS_ExportsSecure`.
   - Permissions : accès limité aux administrateurs.
2. **Configurer la conservation des versions** :
   - Activer versioning pour tracer les modifications.
   - Limiter la durée de conservation (ex. 12 mois).
3. **Automatiser le stockage** :
   - Power Automate → déplacer automatiquement les exports vers `SAPS_ExportsSecure`.
   - Interdire le stockage local non contrôlé.

#### I.13.3.c. Contrôles
- Vérifier que tous les exports sont stockés dans l’espace sécurisé.
- Journaliser dans `SecureStorageLog` : Fichier, Date, Utilisateur, Emplacement.

### I.13.4. Intégration dans SAPS

- **Restrictions d’export** : intégrées dans **Phase 6 – Visualisation et communication**.
- **Filigrane “Confidentiel”** : appliqué dans **Phase 6 – Power BI avancé**.
- **Stockage sécurisé** : intégré dans **Phase 5 – Gouvernance avancée**.
- **Logs associés** :
  - `ExportLog` pour suivi des exports.
  - `WatermarkLog` pour suivi des filigranes.
  - `SecureStorageLog` pour suivi du stockage sécurisé.

### I.13.5. Checklists opérationnelles

#### I.13.5.a. Avant lancement
- Configurer restrictions d’export dans Power BI.
- Ajouter filigrane “Confidentiel” aux rapports.
- Créer bibliothèque sécurisée `SAPS_ExportsSecure`.

#### I.13.5.b. Pendant utilisation
- Vérifier que seuls les administrateurs exportent.
- Contrôler présence du filigrane.
- Suivre stockage dans `SAPS_ExportsSecure`.

#### I.13.5.c. Après 6 mois
- Auditer les exports réalisés.
- Évaluer conformité du stockage sécurisé.
- Ajuster filigrane et politiques selon feedback.

## I.14. Solution complète – Archivage long terme

### I.14.1. Définir une politique de conservation (ex. 3 ans max)

#### I.14.1.a. Objectif
Éviter la conservation excessive des données et réduire les risques de non‑conformité.

#### I.14.1.b. Étapes
1. **Fixer une durée maximale de conservation** :
   - Exemple : 3 ans pour les données actives.
   - 12 mois pour les données sensibles ou critiques.
2. **Documenter la politique** :
   - Rédiger un document officiel validé par la gouvernance.
   - Inclure exceptions (projets réglementés, audits).
3. **Communiquer la politique** :
   - Diffuser aux Owners et gestionnaires.
   - Intégrer dans les chartes de gouvernance documentaire.

#### I.14.1.c. Contrôles
- Vérifier que toutes les données archivées respectent la durée fixée.
- Journaliser dans `RetentionPolicyLog` : Type de données, Durée, Date de mise en archive.

### I.14.2. Vérifier conformité RGPD / Loi 25

#### I.14.2.a. Objectif
S’assurer que l’archivage respecte les réglementations en vigueur (Europe et Québec).

#### I.14.2.b. Étapes
1. **Identifier les données personnelles** :
   - Liens contenant noms, adresses, identifiants.
   - Permissions associées à des utilisateurs.
2. **Appliquer les principes RGPD / Loi 25** :
   - Minimisation : conserver uniquement ce qui est nécessaire.
   - Limitation : durée de conservation définie.
   - Sécurité : accès restreint aux archives.
3. **Valider avec le comité de gouvernance** :
   - Vérification annuelle de conformité.
   - Documentation des exceptions.

#### I.14.2.c. Contrôles
- Vérifier que les archives ne contiennent pas de données personnelles non nécessaires.
- Journaliser dans `ComplianceLog` : Donnée, Règlement applicable, Statut.

### I.14.3. Mettre en place un processus de purge validé par gouvernance

#### I.14.3.a. Objectif
Supprimer automatiquement les données arrivées à échéance, avec validation par le comité.

#### I.14.3.b. Étapes
1. **Configurer Power Automate** :
   - Vérifier la date d’archivage.
   - Si > durée définie → déclencher purge.
2. **Créer une table `PurgeLog`** :
   - Colonnes : Donnée, DateArchivage, DatePurge, Responsable, Validation.
3. **Valider purge par gouvernance** :
   - Notification envoyée au comité.
   - Purge exécutée uniquement après approbation.

#### I.14.3.c. Contrôles
- Vérifier que toutes les purges sont tracées.
- Mesurer volume de données supprimées chaque trimestre.
- Journaliser dans `PurgeLog`.

### 1.14.4. Intégration dans SAPS

- **Politique de conservation** : intégrée dans **Phase 5 – Gouvernance avancée**.
- **Conformité RGPD / Loi 25** : intégrée dans **Phase 7 – Escalade et conformité**.
- **Processus de purge** : appliqué dans **Phase 9 – Optimisation continue**.
- **Logs associés** :
  - `RetentionPolicyLog` pour suivi des durées.
  - `ComplianceLog` pour suivi réglementaire.
  - `PurgeLog` pour suivi des suppressions.

### 1.14.5. Checklists opérationnelles

#### 1.14.5.a. Avant lancement
- Définir politique de conservation (3 ans max).
- Identifier données personnelles sensibles.
- Configurer processus de purge.

#### 1.14.5.b. Pendant utilisation
- Vérifier conformité RGPD / Loi 25.
- Contrôler respect des durées de conservation.
- Suivre purges dans `PurgeLog`.

#### 1.14.5.c. Après 12 mois
- Auditer conformité des archives.
- Ajuster politique de conservation si nécessaire.
- Améliorer processus de purge selon feedback.

## I.15. Solution complète – Sous‑estimation du temps de maintenance

### 1.15.1. Allouer 10–15 % du temps projet à la maintenance continue

#### 1.15.1.a. Objectif
Prévenir les dérives en réservant systématiquement une part du temps projet pour la maintenance.

#### 1.15.1.b. Étapes
1. **Définir une règle de gestion** :
   - Chaque projet SAPS doit inclure 10–15 % du temps total pour maintenance.
   - Exemple : projet de 200 h → 20 à 30 h réservées.
2. **Intégrer dans le planning** :
   - Ajouter une ligne “Maintenance continue” dans le plan projet.
   - Répartir ce temps sur toute la durée du projet.
3. **Communiquer la règle** :
   - Informer les gestionnaires et Owners.
   - Mentionner dans la charte de gouvernance.

#### 1.15.1.c. Contrôles
- Vérifier que le temps de maintenance est bien réservé.
- Journaliser dans `MaintenanceAllocationLog` : Projet, TempsTotal, TempsMaintenance, %.

### 1.15.2. Planifier des points de contrôle mensuels

#### 1.15.2.a. Objectif
Assurer un suivi régulier et anticiper les ajustements nécessaires.

#### 1.15.2.b. Étapes
1. **Créer un calendrier de contrôle** :
   - Réunion mensuelle avec gestionnaires et Owners.
   - Durée : 30–45 min.
2. **Agenda type** :
   - Revue des incidents du mois.
   - Vérification des SLA.
   - Ajustements nécessaires (flows, rapports, gouvernance).
3. **Tracer les décisions** :
   - Table `ControlMeetingLog` : Date, Participants, Décisions, Actions.

#### 1.15.2.c. Contrôles
- Vérifier que les réunions sont tenues chaque mois.
- Suivre le taux de réalisation des actions décidées.

### 1.15.3. Documenter les ajustements dans un registre

#### 1.15.3.a. Objectif
Assurer la traçabilité des modifications et faciliter les audits.

#### 1.15.3.b. Étapes
1. **Créer un registre d’ajustements** :
   - Table `AdjustmentLog` : Date, Composant, Ajustement, Motif, Responsable.
2. **Mettre à jour après chaque contrôle** :
   - Ajouter les ajustements décidés en réunion.
   - Indiquer si l’ajustement est appliqué ou en attente.
3. **Utiliser le registre pour les audits** :
   - Vérifier cohérence entre incidents et ajustements.
   - Identifier tendances (ajustements récurrents).

#### 1.15.3.c. Contrôles
- Vérifier que chaque ajustement est documenté.
- Auditer trimestriellement le registre.

### 1.15.4. Intégration dans SAPS

- **Allocation de temps** : intégrée dans **Phase 0 – Buffer initial** et planification projet.
- **Points de contrôle mensuels** : intégrés dans **Phase 5 – Gouvernance avancée**.
- **Registre des ajustements** : intégré dans **Phase 9 – Optimisation continue**.
- **Logs associés** :
  - `MaintenanceAllocationLog` pour suivi du temps réservé.
  - `ControlMeetingLog` pour suivi des réunions.
  - `AdjustmentLog` pour suivi des ajustements.

### 1.15.5. Checklists opérationnelles

#### 1.15.5.a. Avant lancement
- Réserver 10–15 % du temps projet pour maintenance.
- Planifier calendrier de réunions mensuelles.
- Créer registre des ajustements.

#### 1.15.5.b. Pendant utilisation
- Tenir réunions mensuelles de contrôle.
- Documenter ajustements dans `AdjustmentLog`.
- Vérifier allocation du temps de maintenance.

#### 1.15.5.c Après 6 mois
- Auditer registre des ajustements.
- Évaluer pertinence du % de maintenance réservé.
- Ajuster processus selon feedback.

## I.16. Solution complète – Manque de sponsor exécutif

### I.16.1. Obtenir un appui officiel de la direction

#### I.16.1.a. Objectif
Garantir que SAPS bénéficie d’un soutien visible et durable au niveau exécutif.

#### I.16.1.b. Étapes
1. **Préparer un dossier de présentation** :
   - Objectifs du projet SAPS.
   - Bénéfices attendus (réduction des erreurs, gain de temps, conformité).
   - Risques si absence de sponsor.
2. **Organiser une rencontre avec la direction** :
   - Présenter SAPS comme initiative stratégique.
   - Obtenir une validation écrite (note interne, courriel officiel).
3. **Communiquer le soutien exécutif** :
   - Diffuser aux gestionnaires et Owners.
   - Mentionner dans la charte de gouvernance.

#### I.16.1.c. Contrôles
- Vérifier existence d’un document officiel de soutien.
- Journaliser dans `SponsorLog` : Date, Direction impliquée, Type d’appui.

### I.16.2. Présenter SAPS comme outil de conformité et réduction de risques

#### I.16.2.a. Objectif
Positionner SAPS comme indispensable pour répondre aux obligations réglementaires et limiter les risques organisationnels.

#### I.16.2.b. Étapes
1. **Aligner SAPS avec les normes** :
   - RGPD, Loi 25, politiques internes.
   - Mettre en avant la traçabilité et les logs.
2. **Mettre en avant les bénéfices** :
   - Réduction des doublons.
   - Correction rapide des liens brisés.
   - Contrôle des permissions trop larges.
3. **Créer un rapport de conformité** :
   - Indicateurs : % SLA respectés, NbPermissions corrigées, NbRollbacks réussis.
   - Diffusion trimestrielle au comité exécutif.

#### I.16.2.c. Contrôles
- Vérifier que les rapports de conformité sont produits et diffusés.
- Journaliser dans `ComplianceLog` : Date, Indicateurs, Résultats.

### I.16.3. Inclure SAPS dans les objectifs stratégiques

#### I.16.3.a. bjectif
Faire de SAPS un projet reconnu dans les plans stratégiques de l’organisation.

#### I.16.3.b. Étapes
1. **Intégrer SAPS dans le plan annuel** :
   - Objectif : “Améliorer la gouvernance documentaire”.
   - KPI : % réduction des erreurs documentaires.
2. **Associer SAPS aux initiatives de transformation numérique** :
   - Positionner SAPS comme levier de modernisation.
   - Lier SAPS aux projets SharePoint/Teams/Power BI.
3. **Suivi stratégique** :
   - Inclure SAPS dans les tableaux de bord exécutifs.
   - Présenter résultats lors des comités de direction.

#### I.16.3.c. Contrôles
- Vérifier que SAPS apparaît dans les objectifs stratégiques.
- Journaliser dans `StrategyLog` : Objectif, KPI, Date d’intégration.

### I.16.4. Intégration dans SAPS

- **Appui officiel** : intégré dans **Phase 0 – Buffer initial** (lancement).
- **Conformité et réduction de risques** : intégrée dans **Phase 5 – Gouvernance avancée**.
- **Objectifs stratégiques** : intégrés dans **Phase 9 – Optimisation continue**.
- **Logs associés** :
  - `SponsorLog` pour suivi du soutien exécutif.
  - `ComplianceLog` pour suivi conformité.
  - `StrategyLog` pour suivi stratégique.

### I.16.5. Checklists opérationnelles

#### I.16.5.a. Avant lancement
- Préparer dossier de présentation SAPS.
- Obtenir appui officiel de la direction.
- Intégrer SAPS dans plan stratégique.

#### I.16.5.b. Pendant utilisation
- Produire rapports de conformité trimestriels.
- Communiquer soutien exécutif aux gestionnaires.
- Suivre intégration dans objectifs stratégiques.

#### I.16.5.c. Après 12 mois
- Auditer impact de SAPS sur conformité et gouvernance.
- Évaluer maintien du soutien exécutif.
- Ajuster positionnement stratégique si nécessaire.

## I.17. Solution complète – Dépendance à une seule personne

### I.17.1. Former au moins un backup technique

#### I.17.1.a. Objectif
Assurer la continuité opérationnelle si la personne clé est absente ou indisponible.

#### I.17.1.b. Étapes
1. **Sélectionner 1–2 backups** :
   - Critères : disponibilité, compétences Power Platform/SharePoint, fiabilité.
2. **Plan de formation en 3 phases** :
   - Shadowing (2–3 sessions) : observer les opérations réelles (flows, déploiements, rollbacks).
   - Co‑pilotage (2–3 sessions) : exécuter avec supervision (tests, hotfix, publication BI).
   - Autonomie contrôlée : réaliser une opération bout‑à‑bout validée par l’Admin.
3. **Rotation opérationnelle** :
   - Planning mensuel : le backup pilote une opération clé (extraction, publication, rollback).
   - Bilan court post‑opération (10 min).

#### I.17.1.c. Contrôles
- **KPI formation** : % du parcours complété par backup.
- **Journal** : `BackupTrainingLog` (Personne, Module, Date, Statut, Observations).

### I.17.2. Documenter exhaustivement chaque étape

#### I.17.2.a. Objectif
Rendre le système opérable par un backup sans dépendre d’informations tacites.

#### I.17.2.b. Étapes
1. **Créer un Playbook SAPS** (SOPs) :
   - Dossiers : /Flows (ALM + variables), /BI (PBIP + pipelines), /Governance (SLA, escalades), /Rollback (procédures + Checklists).
2. **Standardiser les formats** :
   - Guides en Markdown (impératif, pas d’images lourdes), captures légères si critique.
   - Nommage versionné : `SAPS.Guide_<Composant>_vX.Y.Z.md`.
3. **Assurer la traçabilité** :
   - `ChangeLog.md` par composant (Date, Auteur, Motif, Impact).
   - Revues documentaires mensuelles (pair review).

#### I.17.2.c. Contrôles
- **KPI couverture** : % modules documentés vs liste de référence.
- **Journal** : `DocCoverageLog` (Composant, Version, Auteur, Revue, Statut).

### I.17.3. Partager les responsabilités avec l’équipe de gouvernance

#### I.17.3.a. Objectif
Éviter le blocage en distribuant la charge et les décisions.

#### I.17.3.b. Étapes
1. **Matrice RACI** :
   - Owner : **R** (corrections).
   - Gestionnaire : **A** (SLA/escalades).
   - Admin : **C** (config, rollbacks critiques).
   - Comité : **I/A** (arbitrages, audits).
2. **Dual‑control sur opérations sensibles** :
   - Rollback en Prod : approbation Comité + Admin.
   - Changements de permissions : validation Gestionnaire.
3. **Rotation de garde** (runbook) :
   - Calendrier : qui répond aux incidents chaque semaine.
   - Point hebdo de 15 min (risques, décisions).

#### I.17.3.c. Contrôles
- **KPI** : temps moyen de résolution post‑escalade.
- **Journal** : `ResponsibilityMatrixLog` (Rôle, Nom, Période, Changements).

### I.17.4. Réduction du bus factor et redondances d’accès

#### I.17.4.a. Objectif
Diminuer le risque systémique lié aux accès et aux secrets détenus par une seule personne.

#### I.17.4.b. Étapes
1. **Miroir d’accès** :
   - Comptes de service/Connectors : ajouter backup dans les groupes d’administration.
   - Vault des secrets (SharePoint sécurisé/Key Vault) avec accès Admin + backup.
2. **Procédure d’accès d’urgence** :
   - Scellé numérique (double approbation Comité + Admin).
   - Journaliser toute ouverture d’accès.
3. **Tests de continuité** (trimestriels) :
   - Simulation “absence personne clé” : le backup publie, corrige, restaure.

#### I.17.4.c. Contrôles
- **KPI bus factor** : nombre de personnes capables de restaurer en Prod.
- **Journal** : `AccessRedundancyLog` (Secret/Accès, Titulaires, Test, Résultat).

### I.17.5. Intégration dans SAPS

- **Backups & formation** : Phase 5 – Gouvernance avancée (calendrier, dual‑control).
- **Playbook & traçabilité** : Phase 9 – Optimisation continue (SOPs + ChangeLog).
- **Redondances d’accès** : Phase 6 – Visualisation et communication (contrôles + vérifications), et Phase 7 – Escalade et conformité (tests).
- **Logs associés** :
  - `BackupTrainingLog` pour la montée en compétence.
  - `DocCoverageLog` pour la couverture documentaire.
  - `ResponsibilityMatrixLog` pour l’attribution des rôles.
  - `AccessRedundancyLog` pour la continuité des accès.

### I.17.6. Checklists opérationnelles

#### I.17.6.a. Avant mise en place
- **Backups désignés** et calendrier de formation.
- **Playbook créé** (SOPs par composant, Checklists).
- **Matrice RACI publiée** et dual‑control activé.
- **Vault des secrets** partagé avec backup.

#### I.17.6.b. Pendant utilisation
- **Sessions de rotation** exécutées (shadowing, co‑pilotage, autonomie).
- **Revues documentaires mensuelles** tenues.
- **Point de gouvernance** hebdo (incidents, décisions).
- **Tests de continuité** trimestriels réalisés.

#### I.17.6.c. Après 6 mois
- **Audit bus factor** et redondances d’accès.
- **Mise à jour Playbook** et RACI selon feedback.
- **Ajustement du calendrier** de rotation et des approbations.

## I.18. Solution complète – Tableau des visites enrichi

### I.18.1. Structure du tableau

Chaque ligne = un fichier ou un lien.  
Colonnes recommandées :

- **Fichier/Lien** : nom du document ou URL.  
- **Site** : emplacement (SharePoint, Teams, etc.).  
- **Owner** : responsable du contenu.  
- **NbVisitesTotal** : nombre total de visites sur la période.  
- **NbUtilisateursUniques** : nombre d’utilisateurs distincts.  
- **HeuresUtilisées** : cumul des heures de consultation.  
- **Heures/Utilisateur** : moyenne par utilisateur.  
- **NbVisitesJour** : visites par jour (moyenne).  
- **NbVisitesSemaine** : visites par semaine.  
- **NbVisitesMois** : visites par mois.  
- **NbVisitesAnnée** : visites par année.  
- **RatioVisites** : NbVisites fichier ÷ NbVisitesTotal du site (%).  

### I.18.2. Exemple de tableau enrichi

| Fichier/Lien   | Site   | Owner   | NbVisitesTotal | NbUtilisateursUniques | HeuresUtilisées | Heures/Utilisateur | NbVisitesJour | NbVisitesSemaine | NbVisitesMois | NbVisitesAnnée | RatioVisites |
|----------------|--------|---------|----------------|-----------------------|-----------------|--------------------|---------------|------------------|---------------|----------------|--------------|
| Doc1.pdf       | Site A | Alice   | 120            | 35                    | 8,3 h           | 0,24 h             | 5             | 25               | 110           | 1200           | 12%          |
| Procédure.docx | Site B | Bob     | 85             | 20                    | 6,1 h           | 0,30 h             | 3             | 18               | 80            | 950            | 8%           |
| LienCritique1  | Site A | Claire  | 200            | 50                    | 15,7 h          | 0,31 h             | 8             | 40               | 180           | 2100           | 20%          |

---

### I.18.3. Intégration dans SAPS

- **Visites par période** : intégrées dans **Phase 2 – Validation et suivi**.  
- **Ratio par rapport au total** : intégré dans **Phase 6 – Visualisation et communication** (Power BI).  
- **Logs associés** :  
  - `VisitLog` → NbVisites par période.  
  - `UsageLog` → Heures utilisées.  
  - `UserLog` → NbUtilisateursUniques.  

---

### I.18.4. Checklists opérationnelles

#### I.18.4.a. Avant lancement
- Configurer table `VisitLog` avec colonnes jour/semaine/mois/année.  
- Définir calcul du ratio visites ÷ total site.  
- Créer vue Power BI “Usage par période”.

#### I.18.4.b. Pendant utilisation
- Vérifier cohérence des agrégations (jour/semaine/mois/année).  
- Contrôler ratio visites par site.  
- Suivre consultations du tableau.

#### I.18.4.c. Après 3 mois
- Évaluer pertinence des indicateurs.  
- Ajuster granularité (jour vs semaine).  
- Intégrer feedback des gestionnaires.

# J. Exemple de registres SAPS – Dépendance à une seule personne

## J.1. BackupTrainingLog
| Personne   | Module                  | Date       | Statut       | Observations                          |
|------------|-------------------------|------------|--------------|---------------------------------------|
| Alice B.   | Power Automate – Flows  | 2025-09-15 | En cours     | Shadowing sur extraction liens        |
| Marc D.    | Power BI – PBIP         | 2025-09-22 | Terminé      | Publication test validée              |
| Alice B.   | Rollback – Procédures   | 2025-10-05 | En attente   | Prévu pour prochaine rotation         |

## J.2. DocCoverageLog
| Composant        | Version | Auteur   | Revue       | Statut       |
|------------------|---------|----------|-------------|--------------|
| SAPS.Core Flows  | v1.4.2  | Tomjor   | 2025-09-20  | Validé       |
| SAPS BI Reports  | v2.1.0  | Marc D.  | 2025-09-25  | En cours     |
| Governance SOP   | v1.0.0  | Alice B. | 2025-10-01  | À compléter  |

## J.3. ResponsibilityMatrixLog
| Rôle        | Nom       | Période         | Changements                            |
|-------------|-----------|-----------------|----------------------------------------|
| Owner       | Sophie L. | Sept–Oct 2025   | Ajout responsabilité doublons          |
| Gestionnaire| Marc D.   | Sept–Oct 2025   | Supervision SLA permissions            |
| Admin       | Tomjor    | Sept–Oct 2025   | Rollback critique validé               |
| Comité      | Comité SAPS | Sept–Oct 2025 | Arbitrage conflit Owner/Gestionnaire   |

## J.4. AccessRedundancyLog
| Secret/Accès         | Titulaires        | Test       | Résultat                                |
|----------------------|-------------------|------------|-----------------------------------------|
| Compte Service Flow  | Tomjor, Alice B.  | 2025-09-28 | OK – accès miroir fonctionnel           |
| Power BI Pipeline    | Tomjor, Marc D.   | 2025-10-02 | OK – publication backup validée         |
| SharePoint AdminKey  | Tomjor seul       | 2025-10-05 | KO – accès non partagé, action requise  |

# K. Matrice RACI – Gouvernance SAPS
| Activité / Processus          | Owner        | Gestionnaire | Admin        | Comité de gouvernance |
|-------------------------------|--------------|--------------|--------------|-----------------------|
| Correction des liens brisés   | R            | A            | C            | I                     |
| Gestion des doublons          | R            | A            | C            | I                     |
| Suivi des SLA                 | I            | R/A          | C            | I                     |
| Escalades critiques           | I            | R            | C            | A                     |
| Rollbacks en production       | I            | C            | R            | A                     |
| Mise à jour des rapports BI   | I            | C            | R            | I                     |
| Documentation des procédures  | R            | I            | C            | A                     |
| Audits trimestriels           | I            | C            | R            | A                     |
| Arbitrage en cas de conflit   | I            | I            | C            | R/A                   |

## K.1. Légende RACI
- **R (Responsible)** : réalise l’action.
- **A (Accountable)** : responsable final, valide le résultat. 
- **C (Consulted)** : consulté pour avis ou expertise.
- **I (Informed)** : informé du résultat ou de la décision.

## K.2. Notes pratiques
- Les **Owners** sont responsables des corrections quotidiennes (liens, doublons).
- Les **Gestionnaires** supervisent les SLA et escalades.
- Les **Admins** assurent la configuration, les rollbacks et la maintenance technique.
- Le **Comité de gouvernance** arbitre les conflits, valide les audits et approuve les décisions critiques.

# L. Nouvelle feature – Vision stratégique SAPS
## L.1. Objectif
Permettre aux gestionnaires et au comité de gouvernance de suivre non seulement les erreurs et corrections, mais aussi les **tendances globales** (adoption, conformité, gains) directement dans SharePoint.

## L.2. Étapes d’intégration
1. **Définir la feature dans SAPS**
   - Nom : “Vision stratégique”
   - Description : Tableau de bord consolidé dans SharePoint, connecté à Power BI.
   - Portée : Adoption, conformité, gains, benchmarks externes.

2. **Créer une page SharePoint dédiée**
   - Section 1 : Indicateurs internes (SLA, corrections, escalades).
   - Section 2 : Tendances temporelles (évolution mensuelle).
   - Section 3 : Benchmarks externes (RGPD/Loi 25, adoption M365).
   - Section 4 : Rapports du comité de gouvernance.

3. **Connecter Power BI**
   - Publier un rapport “Vision stratégique SAPS”.
   - Ajouter filtres (site, période, Owner).
   - Intégrer directement dans la page SharePoint via webpart Power BI.

4. **Gouvernance et maintenance**
   - Responsable : Admin SAPS + Comité de gouvernance.
   - Mise à jour : mensuelle pour indicateurs internes, trimestrielle pour benchmarks externes.
   - Journaliser dans `StrategyVisionLog` : Date, Indicateurs, Résultats, Actions.

## L.3. Contrôles
- Vérifier que la page est consultée régulièrement (NbConsultations).
- Mesurer l’impact sur la prise de décision (feedback comité).
- Auditer la cohérence des données internes vs externes.

## L.4. Checklists opérationnelles

### L.4.1. Avant lancement
- Définir périmètre de la feature.
- Créer page SharePoint “Vision stratégique”.
- Publier rapport Power BI associé.

### L.4.2. Pendant utilisation
- Mettre à jour indicateurs mensuels.
- Ajouter benchmarks externes trimestriels.
- Suivre consultations via `StrategyVisionLog`.

### L.4.3. Après 6 mois
- Évaluer pertinence de la feature.
- Ajuster indicateurs selon feedback.
- Décider si extension à d’autres domaines (sécurité, adoption globale).
