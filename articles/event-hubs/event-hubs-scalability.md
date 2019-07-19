---
title: Scalabilité - Azure Event Hubs | Microsoft Docs
description: Fournit des informations sur la façon de mettre à l’échelle Azure Event Hubs.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 06/18/2019
ms.author: shvija
ms.openlocfilehash: 3eb20013a6b3afaddce10f2e4652add0edf22a9a
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276526"
---
# <a name="scaling-with-event-hubs"></a>Mise à l’échelle avec Event Hubs

Deux facteurs influencent la mise à l’échelle avec Event Hubs.
*   Unités de débit
*   Partitions

## <a name="throughput-units"></a>Unités de débit

La capacité de débit des concentrateurs d’événements est contrôlée par les *unités de débit*. Les unités de débit sont des unités de capacité achetées préalablement. Un débit unique vous permet de disposer des capacités suivantes :

* Entrée : jusqu’à 1 Mo par seconde ou 1 000 événements par seconde, selon ce qui se produit en premier.
* Sortie : jusqu’à 2 Mo par seconde ou 4 096 événements par seconde.

En cas de dépassement de la capacité des unités de débit achetées, l’entrée est limitée et une exception [ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) est renvoyée. La sortie ne produit aucune exception de limitation, mais reste limitée à la capacité des unités de débit achetées. Si vous recevez des exceptions de vitesse de publication ou si vous attendez une sortie plus élevée, vérifiez le nombre d’unités de débit achetées pour l’espace de noms. Vous pouvez gérer des unités de débit sur le panneau **Mettre à l’échelle** des espaces de noms, dans le [portail Azure](https://portal.azure.com). Vous pouvez également gérer les unités de débit par programmation à l’aide des [API Event Hubs](event-hubs-api-overview.md).

Les unités de débit sont achetées à l’avance et facturées à l’heure. Une fois achetées, les unités de débit sont facturées au moins une heure. Vous pouvez acheter jusqu’à 20 unités de débit par espace de noms Azure Event Hubs. Ces unités sont partagées entre tous les hubs d’événements de l’espace de noms en question.

La fonctionnalité **Majoration automatique** d’Event Hubs augmente automatiquement la taille des instances en augmentant le nombre d’unités de débit pour répondre aux besoins d’utilisation. Cette opération permet d’éviter les situations de limitation, dans lesquelles :

- Les taux d’entrée de données sont supérieurs aux unités de débit définies.
- Les taux de demande de sortie de données sont supérieurs aux unités de débit définies.

Le service Event Hubs augmente le débit quand la charge dépasse le seuil minimal, sans qu’aucune demande échoue avec des erreurs ServerBusy. 

Pour plus d’informations sur la fonctionnalité d’augmentation automatique, consultez [Mettre à l’échelle automatiquement les unités de débit](event-hubs-auto-inflate.md).

## <a name="partitions"></a>Partitions

Les partitions vous permettent de mettre à l’échelle en fonction du traitement en aval. En raison du modèle de consommateur partitionné offert par Event Hubs avec les partitions, vous pouvez effectuer un scale-out pendant le traitement simultané de vos événements. Un hub d’événements peut avoir jusqu’à 32 partitions.

Nous vous recommandons d’équilibrer 1:1 les partitions et les unités de débit pour obtenir un dimensionnement optimal. Une partition unique offre une garantie d’entrée et de sortie pouvant aller jusqu’à 1 unité de débit. Bien que vous puissiez atteindre un débit plus élevé sur une partition, les performances ne sont pas garanties. C’est pourquoi nous vous recommandons vivement de faire en sorte que le nombre de partitions dans un hub d’événements soit supérieur ou égal au nombre d’unités de débit.

Étant donné le débit total dont vous prévoyez d’avoir besoin, vous connaissez le nombre d’unités de débit nécessaires et le nombre minimal de partitions, mais combien de partitions devez-vous avoir ? Choisissez le nombre de partitions en fonction du parallélisme en aval que vous souhaitez atteindre, ainsi que de vos besoins futurs en matière de débit. Il n’y a aucun frais lié au nombre de partitions que vous avez au sein d’un hub d’événements.

Pour obtenir des informations de tarification détaillées des concentrateurs d’événements, consultez [Tarification des concentrateurs d’événements](https://azure.microsoft.com/pricing/details/event-hubs/).

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez en apprendre plus sur Event Hubs en consultant les liens suivants :

- [Mettre à l'échelle automatiquement les unités de débit](event-hubs-auto-inflate.md)
- [Présentation du service Event Hubs](event-hubs-what-is-event-hubs.md)
