---
title: Compromis entre disponibilité et performance pour différents niveaux de cohérence dans Azure Cosmos DB
description: Compromis entre disponibilité et performance pour différents niveaux de cohérence dans Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: a76e277bf56861bcaefb5bf7f8b3b3bc03ad1164
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60894026"
---
# <a name="consistency-availability-and-performance-tradeoffs"></a>Cohérence, disponibilité et compromis sur les performances 

Les bases de données distribuées qui s’appuient sur la réplication pour une haute disponibilité, une faible latence, ou les deux, doivent faire des compromis. Ces compromis interviennent entre la cohérence de lecture et la disponibilité, la latence ou le débit.

Azure Cosmos DB aborde la cohérence des données en offrant un éventail de choix. Cette approche inclut plus d’options que les deux extrêmes de cohérence, forte et éventuelle. Vous pouvez choisir parmi cinq modèles bien définis sur l’échelle des cohérences. De la plus forte à la plus faible cohérence, les modèles sont les suivants :

- *Fort*
- *Obsolescence limitée*
- *Session*
- *Préfixe cohérent*
- *Éventuel*

Chaque modèle fournit la disponibilité et de performances et repose sur des contrats SLA complets.

## <a name="consistency-levels-and-latency"></a>Niveaux de cohérence et latence

La latence de lecture est toujours garantie inférieure à 10 millisecondes au 99e centile pour tous les niveaux de cohérence. La latence de lecture est garantie par le contrat SLA. La latence de lecture moyenne (au 50e centile) est généralement inférieure ou égale à 2 millisecondes. Les comptes Azure Cosmos qui s’étendent sur plusieurs régions et sont configurés avec une cohérence forte constituent une exception à cette garantie.

La latence d’écriture pour tous les niveaux de cohérence est systématiquement être inférieur à 10 millisecondes au 99e centile. La latence d’écriture est garantie par le contrat SLA. La latence d’écriture moyenne (au 50e centile) est généralement inférieure ou égale à 5 millisecondes.

Pour les comptes Azure Cosmos configurés avec une cohérence forte avec plusieurs régions, la latence d’écriture est garantie être temps inférieur à deux fois aller-retour (RTT) entre l’un des deux régions plus éloignées, plus 10 millisecondes au 99e centile.

La latence exacte de la durée des boucles s’exprime en fonction de la distance à la vitesse de la lumière et de la topologie de réseau Azure. Le réseau Azure ne propose pas de contrat SLA de latence pour la durée des boucles entre deux régions Azure. Pour votre compte Azure Cosmos, les latences de réplication sont affichées dans le portail Azure. Vous pouvez utiliser le portail Azure (accédez au panneau métriques) pour surveiller les latences de réplication entre les différentes régions associées à votre compte Azure Cosmos.

## <a name="consistency-levels-and-throughput"></a>Niveaux de cohérence et débit

- Pour la même quantité d’unités de requête, les niveaux de cohérence de type session, préfixe cohérent et éventuelle assurent un débit de lecture environ deux fois supérieur à celui des cohérences de type forte et obsolescence limitée.

- Pour un type donné d’opération d’écriture, tel qu’insert, replace, upsert et delete, le débit d’écriture des unités de requête est identique pour tous les niveaux de cohérence.

## <a id="rto"></a>Niveaux de cohérence et durabilité des données

Dans un environnement de base de données globalement distribuée, il existe une relation directe entre le niveau de cohérence et la durabilité des données en situation de panne à l'échelle d'une région. Au moment de l'élaboration de votre plan de continuité d'activité, vous devez identifier le délai maximal acceptable nécessaire à la récupération complète de l'application après un événement perturbateur. Le temps nécessaire pour une application de façon à restaurer est appelé **objectif de délai de récupération** (**RTO**). Vous devez également déterminer sur quelle période maximale l'application peut accepter de perdre les mises à jour de données récentes lors de la récupération après l'événement perturbateur. La période des mises à jour pourrez vous permettre de perdre est appelée **objectif de point de récupération** (**RPO**).

Le tableau ci-dessous définit la relation entre la durabilité de modèle et les données de cohérence en cas de panne large de région. Il est important de noter que dans un système distribué, même avec une cohérence forte, il est impossible d’avoir une base de données distribuée avec un RPO et le RTO de zéro en raison du théorème CAP. Pour en savoir plus sur la raison, consultez [niveaux de cohérence dans Azure Cosmos DB](consistency-levels.md).

|**Région(s)**|**Mode de réplication**|**Niveau de cohérence**|**RPO**|**RTO**|
|---------|---------|---------|---------|---------|
|1|Maître unique ou multimaître|Tous les niveaux de cohérence|< 240 minutes|< 1 semaine|
|>1|Maître unique|Session, Préfixe cohérent et Éventuel|< 15 minutes|< 15 minutes|
|>1|Maître unique|Obsolescence limitée|*K* & *T*|< 15 minutes|
|>1|Multimaître|Session, Préfixe cohérent et Éventuel|< 15 minutes|0|
|>1|Multimaître|Obsolescence limitée|*K* & *T*|0|
|>1|Maître unique ou multimaître|Remarque|0|< 15 minutes|

*K* = nombre de *« K »* versions (par exemple, les mises à jour) d’un élément.

*T* = l’intervalle de temps *« T »* depuis la dernière mise à jour.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les compromis en matière de distribution globale et de cohérence générale dans les systèmes distribués. Consultez les articles suivants :

- [Compromis en matière de cohérence dans la conception des systèmes de base de données distribuées modernes](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [Haute disponibilité](high-availability.md)
- [SLA pour Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
