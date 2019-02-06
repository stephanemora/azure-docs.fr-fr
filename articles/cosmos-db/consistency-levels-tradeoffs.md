---
title: Compromis entre disponibilité et performance pour différents niveaux de cohérence dans Azure Cosmos DB
description: Compromis entre disponibilité et performance pour différents niveaux de cohérence dans Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/20/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: f56b1b06e8be46c1a111e920cfb98bc58d4f6636
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55466861"
---
# <a name="consistency-availability-and-performance-tradeoffs"></a>Cohérence, disponibilité et compromis sur les performances 

Les bases de données distribuées qui s’appuient sur la réplication pour une haute disponibilité, une faible latence, ou les deux, doivent faire des compromis. Ces compromis interviennent entre la cohérence de lecture et la disponibilité, la latence ou le débit. 

Azure Cosmos DB aborde la cohérence des données en offrant un éventail de choix. Cette approche inclut plus d’options que les deux extrêmes de cohérence, forte et éventuelle. Vous pouvez choisir parmi cinq modèles bien définis sur l’échelle des cohérences. De la plus forte à la plus faible cohérence, les modèles sont les suivants :

- Remarque 
- Bounded staleness (En fonction de l'obsolescence) 
- session 
- Préfixe cohérent 
- Eventual (Éventuel) 

Chaque modèle propose des compromis entre disponibilité et performances, et repose sur un contrat de niveau de service (SLA) complet.

## <a name="consistency-levels-and-latency"></a>Niveaux de cohérence et latence

- La latence de lecture est toujours garantie inférieure à 10 millisecondes au 99e centile pour tous les niveaux de cohérence. La latence de lecture est garantie par le contrat SLA. La latence de lecture moyenne (au 50e centile) est généralement inférieure ou égale à 2 millisecondes. Les comptes Azure Cosmos qui s’étendent sur plusieurs régions et sont configurés avec une cohérence forte constituent une exception à cette garantie.

-  La latence d’écriture est toujours garantie inférieure à 10 millisecondes au 99e centile pour les niveaux de cohérence restants. La latence d’écriture est garantie par le contrat SLA. La latence d’écriture moyenne (au 50e centile) est généralement inférieure ou égale à 5 millisecondes.

Certains comptes Azure Cosmos peuvent comporter plusieurs régions configurées avec une cohérence forte. Dans ce cas, la latence d’écriture est garantie inférieure à deux fois la durée des boucles (RTT), plus 10 millisecondes au 99e centile. La durée des boucles entre deux des régions les plus éloignées est associée à votre compte Azure Cosmos. Elle est égale à la durée des boucles entre deux des régions les plus éloignées qui sont associées au compte Azure Cosmos. Cette option est actuellement en préversion. 

La latence exacte de la durée des boucles s’exprime en fonction de la distance à la vitesse de la lumière et de la topologie de réseau Azure. Le réseau Azure ne propose pas de contrat SLA de latence pour la durée des boucles entre deux régions Azure. Pour votre compte Azure Cosmos, les latences de réplication sont affichées dans le portail Azure. Vous pouvez utiliser le portail Azure pour superviser les latences de réplication entre les différentes régions associées à votre compte.

## <a name="consistency-levels-and-throughput"></a>Niveaux de cohérence et débit

- Pour la même quantité d’unités de requête, les niveaux de cohérence de type session, préfixe cohérent et éventuelle assurent un débit de lecture environ deux fois supérieur à celui des cohérences de type forte et obsolescence limitée.

- Pour un type donné d’opération d’écriture, tel qu’insert, replace, upsert et delete, le débit d’écriture des unités de requête est identique pour tous les niveaux de cohérence.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les compromis en matière de distribution globale et de cohérence générale dans les systèmes distribués. Consultez les articles suivants :

* [Compromis en matière de cohérence dans la conception des systèmes de base de données distribuées modernes](https://www.computer.org/web/csdl/index/-/csdl/mags/co/2012/02/mco2012020037-abs.html)
* [Haute disponibilité](high-availability.md)
* [SLA pour Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
