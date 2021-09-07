---
title: Fichier include
description: Fichier include
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 08/30/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: b5406fa20d0ca01f75e23e2bf1c7836943c4833e
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123226311"
---
Event Hubs organise les séquences d’événements envoyés à un hub d’événements dans une ou plusieurs partitions. Les événements les plus récents sont ajoutés à la fin de cette séquence. 

![Event Hubs](./media/event-hubs-partitions/multiple-partitions.png)

Une partition peut être considérée comme un « journal de validation ». Les partitions contiennent des données d’événement contenant le corps de l’événement, un jeu de propriétés défini par l’utilisateur qui décrit l’événement, et des métadonnées telles que son décalage dans la partition, son numéro dans la séquence de flux et l’horodatage côté service auquel il a été accepté.

![Diagramme qui affiche les séquences des événements des plus anciennes aux plus récentes.](./media/event-hubs-partitions/partition.png)

### <a name="advantages-of-using-partitions"></a>Avantages de l’utilisation des partitions
Event Hubs est conçu pour faciliter le traitement de gros volumes d’événements, et le partitionnement aide à cela de deux manières :

- Bien qu’Event Hubs soit un service PaaS, il y a une réalité physique sous-jacente, et la gestion d’un journal qui conserve l’ordre des événements exige que ces événements soient conservés ensemble dans le stockage sous-jacent et ses réplicas, ce qui entraîne un plafond de débit pour ce type de journal. Le partitionnement permet d’utiliser plusieurs journaux parallèles pour le même hub d’événements et, par conséquent, la multiplication de la capacité de débit d’E/S brute disponible.
- Vos propres applications doivent être en mesure de traiter le volume d’événements envoyé dans un hub d’événements. Cela peut être complexe et nécessiter une capacité de traitement parallèle importante ayant fait l’objet d’un scale-out. La capacité d’un processus unique à gérer des événements étant limitée, vous avez besoin de plusieurs processus. C’est grâce aux partitions que votre solution alimente ces processus tout en garantissant que chaque événement a un propriétaire de traitement clair. 

### <a name="number-of-partitions"></a>Nombre de partitions
Le nombre de partitions est spécifié au moment de la création d’un hub d’événements. Il doit être compris entre 1 et le nombre maximal de partitions autorisé pour chaque niveau tarifaire. Pour connaître la limite du nombre de partitions de chaque niveau, consultez [cet article](../event-hubs-quotas.md#basic-vs-standard-vs-premium-vs-dedicated-tiers). 

Nous vous recommandons de choisir au moins autant de partitions que nécessaire pendant le pic de charge de votre application pour ce hub d’événements particulier. Vous ne pouvez pas changer le nombre de partitions d’un hub d’événements après sa création, sauf dans le cas du hub d’événements d’un cluster dédié. Dans un [cluster Event Hubs dédié](../event-hubs-dedicated-overview.md), le nombre de partitions d’un hub d’événements peut être [augmenté](../dynamically-add-partitions.md) après sa création, mais dans ce cas, la distribution des flux entre les partitions changera à mesure que le mappage des clés de partition aux partitions changera. Vous devez donc tenter d’éviter de telles modifications si l’ordre relatif des événements est important dans votre application.

Il est tentant de définir le nombre de partitions sur la valeur maximale autorisée, mais gardez toujours à l’esprit que vos flux d’événements doivent être structurés de sorte que vous puissiez tirer parti de plusieurs partitions. Si vous avez impérativement besoin de préserver l’ordre parmi tous les événements ou seulement quelques sous-flux, vous ne pourrez peut-être pas tirer parti de nombreuses partitions. En outre, de nombreuses partitions rendent le côté traitement plus complexe. 

Les tarifs ne tiennent pas compte du nombre de partitions présentes dans un hub d’événements. Cela dépend du nombre d’unités tarifaires ([unités de débit](../event-hubs-scalability.md#throughput-units) pour le niveau Standard, [unités de traitement](../event-hubs-scalability.md#processing-units) pour le niveau Premium et [unités de capacité](../event-hubs-dedicated-overview.md) pour le niveau dédié) de l’espace de noms ou du cluster dédié. Par exemple, un hub d’événements de niveau Standard avec 32 partitions ou 1 partition entraîne exactement le même coût quand l’espace de noms a une capacité de 1 unité de débit. De plus, vous pouvez mettre à l’échelle les unités de débit ou les unités de traitement de votre espace de noms, ou les unités de capacité de votre cluster dédié, indépendamment du nombre de partitions. 

### <a name="mapping-of-events-to-partitions"></a>Mappage d’événements à des partitions
Vous pouvez utiliser une clé de partition pour mapper des données d’événement entrant dans des partitions spécifiques dans le cadre de l’organisation des données. La clé de partition est une valeur fournie par l’expéditeur transmise dans un concentrateur d’événements. Elle est traitée par le biais d’une fonction de hachage statique, qui crée l’affectation de la partition. Si vous ne spécifiez aucune clé de partition lors de la publication d’un événement, une affectation de type tourniquet (round robin) est utilisée.

L’éditeur d’événements est uniquement informé de sa clé de partition, et non de la partition sur laquelle les événements sont publiés. Grâce à cette dissociation de la clé et de la partition, l’expéditeur n’a pas besoin de connaître trop d’informations sur le traitement en aval. Une identité par appareil ou unique à l'utilisateur constitue une bonne clé de partition, mais d'autres attributs tels que la géographie, peuvent également être utilisés pour regrouper des événements liés dans une seule partition.

Le fait de spécifier une clé de partition permet de conserver les événements associés dans la même partition et dans l’ordre exact de leur arrivée. La clé de partition est une chaîne qui est dérivée de votre contexte d’application et qui identifie les relations entre les événements. Une séquence d’événements identifiée par une clé de partition est un *flux*. Une partition est un magasin de journaux multiplexé pour de nombreux flux de ce type. 

> [!NOTE]
> Bien que vous puissiez envoyer des événements directement à des partitions, ceci n’est pas recommandé, surtout si vous accordez de l’importance à la haute disponibilité. Cela rétrograde la disponibilité d’un hub d’événements au niveau de la partition. Pour plus d’informations, consultez [Disponibilité et cohérence](../event-hubs-availability-and-consistency.md).

