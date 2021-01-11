---
title: Données synchronisées de votre produit ITSM vers l’espace de travail Log Analytics
description: Cet article fournit une vue d’ensemble des données synchronisées entre votre produit ITSM et l’espace de travail Log Analytics.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/29/2020
ms.custom: references_regions
ms.openlocfilehash: fd570950190ceabac413aca2d68368e5e722a3da
ms.sourcegitcommit: 31d242b611a2887e0af1fc501a7d808c933a6bf6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/29/2020
ms.locfileid: "97811967"
---
# <a name="data-synced-from-your-itsm-product"></a>Données synchronisées à partir de votre produit ITSM

Les incidents et les demandes de changement sont synchronisés entre votre outil ITSM et votre espace de travail Log Analytics, en fonction de la configuration de la connexion (avec le champ « Synchroniser les données »).
* [ServiceNow](./itsmc-connections-servicenow.md)
* [System Center Service Manager](./itsmc-connections-scsm.md)
* [Cherwell](./itsmc-connections-cherwell.md)
* [Provance](./itsmc-connections-provance.md)

## <a name="synced-data"></a>Données synchronisées

Cette section présente des exemples de données collectées par ITSMC.

Les champs présents dans **ServiceDesk_CL** varient en fonction du type d’élément de travail que vous importez dans Log Analytics. Voici une liste de champs pour deux types d’éléments de travail :

**Élément de travail :** **Incidents**  
ServiceDeskWorkItemType_s="Incident"

**Fields**

- ServiceDeskConnectionName
- ID du service d’assistance
- State
- Urgence
- Impact
- Priority
- Escalade
- Créé par
- Résolu par
- Fermé par
- Source
- Affecté à
- Category
- Intitulé
- Description
- Date de création
- Date de fermeture
- Date de résolution
- Date de dernière modification
- Computer

**Élément de travail :** **Demandes de modification**

ServiceDeskWorkItemType_s="ChangeRequest"

**Fields**
- ServiceDeskConnectionName
- ID du service d’assistance
- Créé par
- Fermé par
- Source
- Affecté à
- Intitulé
- Type
- Category
- State
- Escalade
- État conflictuel
- Urgence
- Priority
- Risque
- Impact
- Affecté à
- Date de création
- Date de fermeture
- Date de dernière modification
- Date de la demande
- Date de début prévue
- Date de fin prévue
- Date de début du travail
- Date de fin du travail
- Description
- Computer

## <a name="servicenow-example"></a>Exemple ServiceNow 
### <a name="output-data-for-a-servicenow-incident"></a>Données de sortie pour un incident ServiceNow

| Champ Log Analytics | Champ ServiceNow |
|:--- |:--- |
| ServiceDeskId_s| Number |
| IncidentState_s | State |
| Urgency_s |Urgence |
| Impact_s |Impact|
| Priority_s | Priority |
| CreatedBy_s | Ouvert par |
| ResolvedBy_s | Résolu par|
| ClosedBy_s  | Fermé par |
| Source_s| Type de contact |
| AssignedTo_s | Affecté à  |
| Category_s | Category |
| Title_s|  Description courte |
| Description_s|  Notes |
| CreatedDate_t|  Ouvert |
| ClosedDate_t| Fermé|
| ResolvedDate_t|Résolu|
| Computer  | Élément de configuration |

### <a name="output-data-for-a-servicenow-change-request"></a>Données de sortie pour une demande de modification ServiceNow

| Log Analytics | Champ ServiceNow |
|:--- |:--- |
| ServiceDeskId_s| Number |
| CreatedBy_s | Demandé par |
| ClosedBy_s | Fermé par |
| AssignedTo_s | Affecté à  |
| Title_s|  Description courte |
| Type_s|  Type |
| Category_s|  Category |
| CRState_s|  State|
| Urgency_s|  Urgence |
| Priority_s| Priority|
| Risk_s| Risque|
| Impact_s| Impact|
| RequestedDate_t  | Date demandée |
| ClosedDate_t | Date de fermeture |
| PlannedStartDate_t  | Date de début prévue |
| PlannedEndDate_t  | Date de fin prévue |
| WorkStartDate_t  | Date de début réelle |
| WorkEndDate_t | Date de fin réelle|
| Description_s | Description |
| Computer  | Élément de configuration |

## <a name="next-steps"></a>Étapes suivantes

* [Résolution des problèmes liés au connecteur ITSM](./itsmc-resync-servicenow.md)
