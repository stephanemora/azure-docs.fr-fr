---
title: Scalabilité - Azure Event Hubs | Microsoft Docs
description: Cet article fournit des informations sur la mise à l'échelle d'Azure Event Hubs à l'aide de partitions et d'unités de débit.
ms.topic: article
ms.date: 03/16/2021
ms.openlocfilehash: f258ee2a3b4162dabf7a8e615db82b9b889d628b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103601274"
---
# <a name="scaling-with-event-hubs"></a>Mise à l’échelle avec Event Hubs

Deux facteurs influencent la mise à l’échelle avec Event Hubs.
*   Unités de débit
*   Partitions

## <a name="throughput-units"></a>Unités de débit

La capacité de débit des concentrateurs d’événements est contrôlée par les *unités de débit*. Les unités de débit sont des unités de capacité achetées préalablement. Un débit unique vous permet de disposer des capacités suivantes :

* Entrée : jusqu’à 1 Mo par seconde ou 1 000 événements par seconde, selon ce qui se produit en premier.
* Sortie : jusqu’à 2 Mo par seconde ou 4 096 événements par seconde.

En cas de dépassement de la capacité des unités de débit achetées, l’entrée est limitée et une exception [ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) est renvoyée. La sortie ne produit aucune exception de limitation, mais reste limitée à la capacité des unités de débit achetées. Si vous recevez des exceptions de vitesse de publication ou si vous attendez une sortie plus élevée, vérifiez le nombre d’unités de débit achetées pour l’espace de noms. Vous pouvez gérer des unités de débit sur le panneau **Mettre à l’échelle** des espaces de noms, dans le [portail Azure](https://portal.azure.com). Vous pouvez également gérer les unités de débit par programmation à l’aide des [API Event Hubs](./event-hubs-samples.md).

Les unités de débit sont achetées à l’avance et facturées à l’heure. Une fois achetées, les unités de débit sont facturées au moins une heure. Vous pouvez acheter jusqu’à 20 unités de débit par espace de noms Azure Event Hubs. Ces unités sont partagées entre tous les hubs d’événements de l’espace de noms en question.

La fonctionnalité **Majoration automatique** d’Event Hubs augmente automatiquement la taille des instances en augmentant le nombre d’unités de débit pour répondre aux besoins d’utilisation. Cette opération permet d’éviter les situations de limitation, dans lesquelles :

- Les taux d’entrée de données sont supérieurs aux unités de débit définies.
- Les taux de demande de sortie de données sont supérieurs aux unités de débit définies.

Le service Event Hubs augmente le débit quand la charge dépasse le seuil minimal, sans qu’aucune demande échoue avec des erreurs ServerBusy. 

Pour plus d’informations sur la fonctionnalité d’augmentation automatique, consultez [Mettre à l’échelle automatiquement les unités de débit](event-hubs-auto-inflate.md).

## <a name="partitions"></a>Partitions
[!INCLUDE [event-hubs-partitions](../../includes/event-hubs-partitions.md)]




## <a name="next-steps"></a>Étapes suivantes
Vous pouvez en apprendre plus sur Event Hubs en consultant les liens suivants :

- [Mettre à l’échelle automatiquement les unités de débit](event-hubs-auto-inflate.md)
- [Présentation du service Event Hubs](./event-hubs-about.md)
