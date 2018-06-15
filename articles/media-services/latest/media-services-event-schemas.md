---
title: Schémas Azure Event Grid pour les événements Media Services
description: Décrit les propriétés qui sont fournies pour les événements Media Services avec Azure Event Grid
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: c443ea755c9e7e88b4685682d5e43f675d42efa4
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
ms.locfileid: "33782658"
---
# <a name="azure-event-grid-schemas-for-media-services-events"></a>Schémas Azure Event Grid pour les événements Media Services

Cet article fournit les schémas et les propriétés pour les événements Media Services.

## <a name="media-services-job-state-change-event"></a>Événement de changement d’état du travail Media Services

Cette section fournit les propriétés et le schéma pour l’événement de changement d’état du travail Media Services.  

### <a name="available-event-types"></a>Types d’événement disponibles

Un **travail** Media Services émet les types d’événements suivants :

| Type d'événement | Description |
| ---------- | ----------- |
| Microsoft.Media.JobStateChange| Déclenché lorsque le travail change d’état. |

### <a name="example-event"></a>Exemple d’événement

L’exemple suivant montre le schéma d’un événement de travail à l’état terminé : 

```json
[
  {
    "topic": "/subscriptions/{subscription id}/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount",
    "subject": "transforms/VideoAnalyzerTransform/jobs/{job id}",
    "eventType": "Microsoft.Media.JobStateChange",
    "eventTime": "2018-04-20T21:26:13.8978772",
    "id": "<id>",
    "data": {
      "previousState": "Processing",
      "state": "Finished"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

### <a name="event-properties"></a>Propriétés d’événement

Un événement contient les données générales suivantes :

| Propriété | type | Description |
| -------- | ---- | ----------- |
| rubrique | chaîne | Event Grid fournit cette valeur. |
| subject | chaîne | Le chemin d’accès aux ressource sous la ressource du compte Media Services. |
| eventType | chaîne | Un des types d’événements inscrits pour cette source d’événement. Par exemple, « Microsoft.Media.JobStateChange ». |
| eventTime | chaîne | L’heure à quelle l’événement est généré selon l’heure UTC du fournisseur. |
| id | chaîne | Identificateur unique de l’événement. |
| données | objet | Données d’événement Media Services. |
| dataVersion | chaîne | Version du schéma de l’objet de données. Le serveur de publication définit la version du schéma. |
| metadataVersion | chaîne | Version du schéma des métadonnées d’événement. Event Grid définit le schéma des propriétés de niveau supérieur. Event Grid fournit cette valeur. |

L’objet de données comporte les propriétés suivantes :

| Propriété | type | Description |
| -------- | ---- | ----------- |
| previousState | chaîne | L’état du travail avant l’événement. |
| state | chaîne | Le nouvel état de la tâche notifié dans cet événement. Par exemple, « en file d’attente : la tâche est en attente de ressources » ou « planifiée : le travail est prêt à démarrer ».|

Où l’état du travail peut prendre l’une des valeurs suivantes : *En file d’attente*, *Planifié*, *Traitement*, *Terminé*, *Erreur*, *Annulé*, *Annulation en cours*

## <a name="next-steps"></a>Étapes suivantes

[S’inscrire à des événements de changement d’état d’un travail](job-state-events-cli-how-to.md)
