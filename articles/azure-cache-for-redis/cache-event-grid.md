---
title: Vue d’ensemble d’Azure Cache pour Redis avec Event Grid
description: Utilisez Azure Event Grid pour publier des événements Azure Cache pour Redis.
author: curib
ms.author: cauribeg
ms.date: 12/21/2020
ms.topic: conceptual
ms.service: cache
ms.openlocfilehash: 0a0809076367356739dfeadcf8dd63f88866a987
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99056813"
---
# <a name="azure-cache-for-redis-event-grid-overview"></a>Vue d’ensemble d’Azure Cache pour Redis avec Event Grid 

Les événements Azure Cache pour Redis, tels que les événements de mise à jour corrective, mise à l’échelle, importation/exportation (RDB) sont envoyés à l’aide d’[Azure Event Grid](https://azure.microsoft.com/services/event-grid/) aux abonnés comme Azure Functions, Azure Logic Apps ou même à votre propre écouteur http personnalisé. Event Grid fournit des services de livraison d’événements fiables à vos applications par le biais de stratégies enrichies de nouvelle tentative et de livraison de lettres mortes.

Consultez l’article [Schéma des événements Azure Cache pour Redis](../event-grid/event-schema-azure-cache.md) pour voir la liste complète des événements pris en charge par Azure Cache pour Redis.

Si vous souhaitez essayer des événements Azure Cache pour Redis, consultez l’un des articles de démarrage rapide suivants :

|Si vous souhaitez utiliser cet outil :    |Consultez ce guide de démarrage rapide : |
|--|-|
|Portail Azure    |[Démarrage rapide : Router les événements Azure Cache pour Redis vers le point de terminaison web avec le portail Azure](cache-event-grid-quickstart-portal.md)|
|PowerShell    |[Démarrage rapide : Router les événements Azure Cache pour Redis vers le point de terminaison web avec PowerShell](cache-event-grid-quickstart-powershell.md)|
|Azure CLI    |[Démarrage rapide : Router les événements Azure Cache pour Redis vers le point de terminaison web avec Azure CLI](cache-event-grid-quickstart-cli.md)|

## <a name="the-event-model"></a>Le modèle d’événement

Event Grid utilise les [abonnements aux événements](../event-grid/concepts.md#event-subscriptions) pour acheminer les messages d’événements vers les abonnés. Cette image illustre la relation entre les éditeurs d’événements, les abonnements aux événements et les gestionnaires d’événements.

:::image type="content" source="media/cache-event-grid/event-grid-model.png" alt-text="Modèle Event Grid.":::

Tout d’abord, abonnez un point de terminaison à un événement. Ensuite, lorsqu’un événement est déclenché, le service Event Grid envoie les données relatives à cet événement au point de terminaison.

Consultez l’un des articles [Schéma des événements Azure Cache pour Redis](../event-grid/event-schema-azure-cache.md) :

> [!div class="checklist"]
> * Une liste complète des événements Azure Cache pour Redis et la manière dont chaque événement est déclenché.
> * Un exemple des données que Event Grid peut envoyer pour chacun de ces événements.
> * L’objectif de chaque paire de clé-valeur qui apparaît dans les données.


## <a name="best-practices-for-consuming-events"></a>Meilleures pratiques pour la consommation d’événements

Les applications qui gèrent des événements Azure Cache pour Redis doivent suivre certaines pratiques recommandées :
> [!div class="checklist"]
> * Comme plusieurs abonnements peuvent être configurés pour acheminer les événements vers le même gestionnaire d’événements, il est important de ne pas considérer que les événements proviennent d’une source particulière, mais de vérifier le sujet du message pour vous assurer qu’il provient de l’instance Azure Cache pour Redis que vous attendez.
> * De même, vérifiez que vous êtes prêt à traiter son eventType, et ne supposez pas que tous les événements reçus seront aux types que vous attendez.
> * Les événements Azure Cache pour Redis garantissent au moins une livraison aux abonnés, assurant ainsi la sortie de tous les messages. Toutefois, en raison de nouvelles tentatives ou de la disponibilité des abonnements, des messages dupliqués peuvent parfois se produire. Pour en savoir plus sur la livraison de messages et les nouvelles tentatives, consultez [Livraison et nouvelle tentative de livraison de messages avec Event Grid](../event-grid/delivery-and-retry.md).


## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur Event Grid et essayer les événements Azure Cache pour Redis :

- [À propos d’Event Grid](../event-grid/overview.md)
- [Schéma des événements Azure Cache pour Redis](../event-grid/event-schema-azure-cache.md)
- [Router les événements Azure Cache pour Redis vers le point de terminaison web avec Azure CLI](cache-event-grid-quickstart-cli.md)
- [Router les événements Azure Cache pour Redis vers le point de terminaison web avec le portail Azure](cache-event-grid-quickstart-portal.md)
- [Router les événements Azure Cache pour Redis vers le point de terminaison web avec PowerShell](cache-event-grid-quickstart-powershell.md)
