---
title: Compromis entre cohérence, disponibilité et niveau de performance d’Azure Cosmos DB
description: Compromis entre disponibilité et performance pour différents niveaux de cohérence dans Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/06/2020
ms.reviewer: sngun
ms.openlocfilehash: 7cdaa9699b15000359c438bcc410e300415b759a
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81379956"
---
# <a name="consistency-availability-and-performance-tradeoffs"></a>Cohérence, disponibilité et compromis sur les performances

Les bases de données distribuées qui s’appuient sur la réplication pour une haute disponibilité, une faible latence, ou les deux, doivent faire des compromis. Ces compromis interviennent entre la cohérence de lecture et la disponibilité, la latence ou le débit.

Azure Cosmos DB aborde la cohérence des données en offrant un éventail de choix. Cette approche inclut plus d’options que les deux extrêmes de cohérence, forte et éventuelle. Vous pouvez choisir parmi cinq niveaux bien définis sur l’échelle des cohérences. De la plus forte à la plus faible cohérence, les niveaux sont les suivants :

- *Fort*
- *Obsolescence limitée*
- *Session*
- *Préfixe cohérent*
- *Éventuel*

Chaque niveau offre des compromis entre disponibilité et performances, et ces modèles sont appuyés par des contrats de niveau de service complets.

## <a name="consistency-levels-and-latency"></a>Niveaux de cohérence et latence

La latence de lecture est toujours garantie inférieure à 10 millisecondes au 99e centile pour tous les niveaux de cohérence. La latence de lecture est garantie par le contrat SLA. La latence de lecture moyenne (au 50e centile) est généralement inférieure ou égale à 4 millisecondes.

La latence d’écriture est toujours garantie inférieure à 10 millisecondes au 99e centile pour tous les niveaux de cohérence. La latence d’écriture est garantie par le contrat SLA. La latence d’écriture moyenne (au 50e centile) est généralement inférieure ou égale à 5 millisecondes. Les comptes Azure Cosmos qui s’étendent sur plusieurs régions et sont configurés avec une cohérence forte constituent une exception à cette garantie.

### <a name="write-latency-and-strong-consistency"></a>Latence d’écriture et cohérence forte

Pour les comptes Azure Cosmos configurés avec une cohérence forte et plusieurs régions, la latence d’écriture est égale à deux allers-retours (RTT) entre deux des régions les plus éloignées, plus 10 millisecondes au 99e centile. Un temps d’aller-retour réseau élevé entre les régions se traduira par une latence plus élevée pour les requêtes Cosmos DB, car la cohérence forte effectue une opération seulement après avoir vérifié qu’elle a été validée dans toutes les régions d’un compte.

La latence exacte de la durée des boucles s’exprime en fonction de la distance à la vitesse de la lumière et de la topologie de réseau Azure. Le réseau Azure ne propose pas de contrat SLA de latence pour la durée des boucles entre deux régions Azure. Pour votre compte Azure Cosmos, les latences de réplication sont affichées dans le portail Azure. Vous pouvez utiliser le portail Azure (accédez au panneau Métriques, puis sélectionnez l’onglet Cohérence) pour superviser les latences de réplication entre les différentes régions associées à votre compte Azure Cosmos.

> [!IMPORTANT]
> Une cohérence forte pour les comptes dont les régions sont distantes de plus de 8 000 kilomètres est bloquée par défaut en raison d’une latence d’écriture élevée. Pour activer cette fonctionnalité, contactez le support technique.

## <a name="consistency-levels-and-throughput"></a>Niveaux de cohérence et débit

- Pour une obsolescence forte et limitée, les lectures sont effectuées sur deux réplicas dans un jeu de quatre réplicas (quorum minoritaire) pour fournir des garanties de cohérence. Les niveaux Session, Préfixe cohérent et À terme font des lectures sur un seul réplica. Le résultat est que, pour le même nombre d’unités de requête, le débit de lecture pour l’obsolescence forte et limitée est la moitié de celui des autres niveaux de cohérence.

- Pour un type donné d’opération d’écriture, tel qu’insert, replace, upsert et delete, le débit d’écriture des unités de requête est identique pour tous les niveaux de cohérence.

|**Niveau de cohérence**|**Lectures de quorum**|**Écritures de quorum**|
|--|--|--|
|**Fort**|Minorité locale|Majorité globale|
|**Obsolescence limitée**|Minorité locale|Majorité locale|
|**Session**|Réplica unique (avec un jeton de session)|Majorité locale|
|**Préfixe cohérent**|Réplica unique|Majorité locale|
|**Éventuel**|Réplica unique|Majorité locale|

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
