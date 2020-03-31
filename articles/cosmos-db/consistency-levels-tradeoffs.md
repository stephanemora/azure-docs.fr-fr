---
title: Compromis entre cohérence, disponibilité et niveau de performance d’Azure Cosmos DB
description: Compromis entre disponibilité et performance pour différents niveaux de cohérence dans Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.reviewer: sngun
ms.openlocfilehash: a16acfc8f9be820e9cc9b3bd59d6675b7f75d2ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75445561"
---
# <a name="consistency-availability-and-performance-tradeoffs"></a>Cohérence, disponibilité et compromis sur les performances 

Les bases de données distribuées qui s’appuient sur la réplication pour une haute disponibilité, une faible latence, ou les deux, doivent faire des compromis. Ces compromis interviennent entre la cohérence de lecture et la disponibilité, la latence ou le débit.

Azure Cosmos DB aborde la cohérence des données en offrant un éventail de choix. Cette approche inclut plus d’options que les deux extrêmes de cohérence, forte et éventuelle. Vous pouvez choisir parmi cinq modèles bien définis sur l’échelle des cohérences. De la plus forte à la plus faible cohérence, les modèles sont les suivants :

- *Fort*
- *Obsolescence limitée*
- *Session*
- *Préfixe cohérent*
- *Éventuel*

Chaque modèle propose des compromis clairs entre disponibilité et performances, et ces modèles sont appuyés par des contrats de niveau de service complets.

## <a name="consistency-levels-and-latency"></a>Niveaux de cohérence et latence

La latence de lecture est toujours garantie inférieure à 10 millisecondes au 99e centile pour tous les niveaux de cohérence. La latence de lecture est garantie par le contrat SLA. La latence de lecture moyenne (au 50e centile) est généralement inférieure ou égale à 2 millisecondes. Les comptes Azure Cosmos qui s’étendent sur plusieurs régions et sont configurés avec une cohérence forte constituent une exception à cette garantie.

La latence d’écriture est toujours garantie inférieure à 10 millisecondes au 99e centile pour tous les niveaux de cohérence. La latence d’écriture est garantie par le contrat SLA. La latence d’écriture moyenne (au 50e centile) est généralement inférieure ou égale à 5 millisecondes.

Pour les comptes Azure Cosmos configurés avec une cohérence forte et plusieurs régions, la latence d’écriture est garantie inférieure à deux allers-retours (RTT) entre l’une des deux régions les plus éloignées, plus 10 millisecondes au 99e centile.

La latence exacte de la durée des boucles s’exprime en fonction de la distance à la vitesse de la lumière et de la topologie de réseau Azure. Le réseau Azure ne propose pas de contrat SLA de latence pour la durée des boucles entre deux régions Azure. Pour votre compte Azure Cosmos, les latences de réplication sont affichées dans le portail Azure. Vous pouvez utiliser le portail Azure (accédez au panneau Métriques) pour superviser les latences de réplication entre les différentes régions associées à votre compte Azure Cosmos.

## <a name="consistency-levels-and-throughput"></a>Niveaux de cohérence et débit

- Pour la même quantité d’unités de requête, les niveaux de cohérence de type session, préfixe cohérent et éventuelle assurent un débit de lecture environ deux fois supérieur à celui des cohérences de type forte et obsolescence limitée.

- Pour un type donné d’opération d’écriture, tel qu’insert, replace, upsert et delete, le débit d’écriture des unités de requête est identique pour tous les niveaux de cohérence.

## <a name="consistency-levels-and-data-durability"></a><a id="rto"></a>Niveaux de cohérence et durabilité des données

Dans un environnement de base de données globalement distribuée, il existe une relation directe entre le niveau de cohérence et la durabilité des données en situation de panne à l'échelle d'une région. Au moment de l'élaboration de votre plan de continuité d'activité, vous devez identifier le délai maximal acceptable nécessaire à la récupération complète de l'application après un événement perturbateur. Ce délai s’appelle l’**objectif de délai de récupération** (**RTO**, recovery time objective). Vous devez également déterminer sur quelle période maximale l'application peut accepter de perdre les mises à jour de données récentes lors de la récupération après l'événement perturbateur. Il s’agit de l’**objectif de point de récupération** (**RPO**, recovery point objective).

Le tableau ci-dessous définit la relation entre le modèle de cohérence et la durabilité des données en situation de panne à l’échelle d’une région. Il est important de noter que dans un système distribué, même avec une cohérence forte, il s’avère impossible d’avoir une base de données distribuée avec un RPO de zéro en raison du théorème CAP. Pour en savoir plus, consultez [Niveaux de cohérence dans Azure Cosmos DB](consistency-levels.md).

|**Région(s)**|**Mode de réplication**|**Niveau de cohérence**|**RPO**|**RTO**|
|---------|---------|---------|---------|---------|
|1|Maître unique ou multimaître|Tous les niveaux de cohérence|< 240 minutes|< 1 semaine|
|>1|Maître unique|Session, Préfixe cohérent et Éventuel|< 15 minutes|< 15 minutes|
|>1|Maître unique|Obsolescence limitée|*K* & *T*|< 15 minutes|
|>1|Maître unique|Remarque|0|< 15 minutes|
|>1|Multimaître|Session, Préfixe cohérent et Éventuel|< 15 minutes|0|
|>1|Multimaître|Obsolescence limitée|*K* & *T*|0|

*K* = nombre de versions *« K »* (à savoir, mises à jour) d’un élément.

*T* = intervalle de temps *« T »* depuis la dernière mise à jour.

## <a name="strong-consistency-and-multi-master"></a>Cohérence forte et architecture multimaître

Les comptes Cosmos configurés pour une architecture multimaître ne peuvent pas être configurés pour une cohérence forte, car il n’est pas possible qu’un système distribué fournisse un objectif de point de récupération (RPO) et un objectif de délai de récupération (RTO) tous de valeur zéro. En outre, il n’y a pas d’avantage en termes de latence d’écriture à utiliser une cohérence forte avec une architecture multimaître, car toute écriture dans une région doit être répliquée et validée dans toutes les régions configurées au sein du compte. Cela aboutit à une latence d’écriture identique à celle d’un compte principal unique.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les compromis en matière de distribution globale et de cohérence générale dans les systèmes distribués. Voir les articles suivants :

- [Compromis en matière de cohérence dans la conception des systèmes de base de données distribuées modernes](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [Haute disponibilité](high-availability.md)
- [SLA pour Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
