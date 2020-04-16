---
title: Rubriques système dans Azure Event Grid
description: Décrit les rubriques système d’Azure Event Grid.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: spelluru
ms.openlocfilehash: 46bceeb31fa38068c6c4f9f3a86ed556ad39effb
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393148"
---
# <a name="system-topics-in-azure-event-grid"></a>Rubriques système dans Azure Event Grid
Le service Azure Event Grid crée des rubriques système lorsque vous créez un premier abonnement d’événement pour une source d’événements Azure. Actuellement, Event Grid ne crée pas de rubriques système pour les sources de rubrique créées avant le 15 mars 2020. Pour toutes les sources de rubriques que vous avez créées à cette date ou après, Event Grid crée automatiquement des rubriques système. Cet article décrit les principales **rubriques système** utilisées dans Azure Event Grid.

> [!NOTE]
> Cette fonctionnalité n’est actuellement pas activée pour Azure Government Cloud. 

## <a name="overview"></a>Vue d’ensemble
Lorsque vous créez un premier abonnement d’événement pour une source d’événements Azure, comme un compte de stockage Azure, le processus d’approvisionnement de l’abonnement crée une ressource supplémentaire de type **Microsoft.EventGrid/systemTopics**. Lorsque le dernier abonnement aux événements de la source d’événements Azure est supprimé, la rubrique système est automatiquement supprimée.

La rubrique système n’est pas applicable aux scénarios de rubriques personnalisées, autrement dit, les rubriques Event Grid et les domaines Event Grid. 

## <a name="location"></a>Emplacement
Pour les sources d’événements Azure qui se trouvent dans une région/un emplacement spécifique, la rubrique système est créée au même endroit que la source d’événements Azure. Par exemple, si vous créez un abonnement aux événements pour un stockage Blob dans USA Est, la rubrique système est créée dans la région USA Est. Pour les sources d’événements Azure globales, comme les abonnements Azure, les groupes de ressources ou les Azure Maps, Event Grid crée la rubrique système dans l’emplacement **global**. 

## <a name="resource-group"></a>Resource group 
En général, la rubrique système est créée dans le même groupe de ressources dans lequel se trouve la source d’événements Azure. Pour les abonnements aux événements créés dans l’étendue de l’abonnement Azure, la rubrique système est créée sous le groupe de ressources **Default-EventGrid**. Si le groupe de ressources n’existe pas, Azure Event Grid le crée avant de créer la rubrique système. 

Lorsque vous essayez de supprimer le groupe de ressources avec le compte de stockage, la rubrique système apparaît dans la liste des ressources affectées.  

![Supprimer un groupe de ressources](./media/system-topics/delete-resource-group.png)

## <a name="next-steps"></a>Étapes suivantes
Voir les articles suivants : 

- [Rubriques personnalisées](custom-topics.md)
- [Domaines](event-domains.md)