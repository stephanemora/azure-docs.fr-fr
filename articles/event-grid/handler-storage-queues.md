---
title: File d’attente de stockage en tant que gestionnaire d’événements pour des événements Azure Event Grid
description: Décrit comment utiliser des files d’attente de stockage Azure en tant que gestionnaires d’événements pour des événements Azure Event Grid.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: 9b767caa1041f865d8e15cd57796b186f7a4a6bb
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83596268"
---
# <a name="storage-queue-as-an-event-handler-for-azure-event-grid-events"></a>File d’attente de stockage en tant que gestionnaire d’événements pour des événements Azure Event Grid
Un gestionnaire d’événements désigne l’endroit où l’événement est envoyé. Le gestionnaire effectue des actions supplémentaires pour traiter l’événement. Plusieurs services Azure sont automatiquement configurés pour gérer des événements, et le **Stockage File d’attente Azure** est l’un d’eux. 

Utilisez le **Stockage File d’attente** pour recevoir des événements qui doivent être extraits. Vous pouvez éventuellement utiliser le Stockage File d’attente pour gérer un processus long qui met trop de temps à répondre. En envoyant des événements au Stockage File d’attente, l’application peut les extraire et les traiter à son propre rythme.

## <a name="tutorials"></a>Tutoriels
Consultez le tutoriel suivant pour obtenir un exemple d’utilisation d’un Stockage File d’attente en tant que gestionnaire d’événements. 

|Intitulé  |Description  |
|---------|---------|
| [Démarrage rapide : Acheminer des événements personnalisés vers le Stockage File d’attente Azure avec Azure CLI et Event Grid](custom-event-to-queue-storage.md) | Décrit comment envoyer des événements personnalisés à un stockage File d’attente. |

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des gestionnaires d’événements pris en charge, consultez l’article [Gestionnaires d’événements](event-handlers.md). 
