---
title: Compromis entre disponibilité et performance pour différents niveaux de cohérence dans Azure Cosmos DB | Microsoft Docs
description: Compromis entre disponibilité et performance pour différents niveaux de cohérence dans Azure Cosmos DB.
keywords: Cohérence, performance, Azure Cosmos DB, Azure, Microsoft Azure
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 10/20/2018
ms.author: mjbrown
ms.openlocfilehash: 8f36026c7e5802994b8cf22d60c6ecea052e6382
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2018
ms.locfileid: "50963045"
---
# <a name="availability-and-performance-tradeoffs-for-various-consistency-levels-in-azure-cosmos-db"></a>Compromis entre disponibilité et performance pour différents niveaux de cohérence dans Azure Cosmos DB

Les bases de données distribuées qui reposent sur la réplication afin d’offrir une haute disponibilité, une faible latence ou les deux, constituent le compromis fondamental entre la cohérence de la lecture et la disponibilité, la latence et le débit. Azure Cosmos DB considère la cohérence des données comme un éventail de choix, plutôt que comme deux extrêmes (la cohérence forte et la cohérence à terme). Cosmos DB permet aux développeurs de choisir parmi les cinq modèles de cohérence bien définis couvrant tout l’éventail de cohérence (du plus fort au plus faible) : **fort**, **obsolescence limitée**, **session**, **préfixe cohérent** et **à terme**. Chacun des cinq modèles de cohérence propose des compromis clairs entre disponibilité et performances, et ces modèles sont appuyés par des contrats de niveau de service complets.

## <a name="consistency-levels-and-latency"></a>Niveaux de cohérence et latence

- La **latence de lecture** est garantie inférieure à 10 millisecondes au 99e centile par le contrat SLA pour tous les niveaux de cohérence. La latence de lecture moyenne (au 50e centile) est généralement inférieure ou égale à 2 millisecondes.

- À l’exception des comptes Cosmos couvrant plusieurs régions et configurés avec une cohérence forte, la **latence d’écriture** des niveaux de cohérence restants est garantie inférieure à 10 millisecondes au 99e centile. La latence d’écriture est garantie par le contrat SLA. La latence d’écriture moyenne (au 50e centile) est généralement inférieure ou égale à 5 millisecondes.

- Dans le cas des comptes Cosmos dont plusieurs régions sont configurées avec une cohérence forte (actuellement en préversion), la **latence d’écriture** est garantie inférieure à (2 *Round-trip time/RTT) + 10 millisecondes au 99e centile. (RTT entre l’une des deux régions les plus éloignées associées au compte Cosmos). La latence RTT exacte s’exprime en fonction de la distance à la vitesse de la lumière et de la topologie de mise en réseau Azure précise. La mise en réseau Azure ne propose pas de contrat SLA de latence RTT entre les deux régions Azure. Les latences de réplication Cosmos DB s’affichent sur le Portail Azure pour le compte Cosmos correspondant, ce qui permet de les surveiller entre les différentes régions associées à ce compte.

## <a name="consistency-levels-and-throughput"></a>Niveaux de cohérence et débit

- Pour la même quantité d’unités de requête, les niveaux de cohérence de type session, préfixe cohérent et à terme assurent un débit de lecture approximativement deux fois supérieur à celui des cohérences de type fort et obsolescence limitée.

- Pour un type donné d’opération d’écriture, tel qu’insert, replace, upsert, delete, etc., le débit d’écriture des unités de requête est identique pour tous les niveaux de cohérence.

## <a name="consistency-levels-and-durability"></a>Niveaux de cohérence et durabilité

Avant la reconnaissance d’une opération d’écriture auprès du client, les données sont validées durablement par un quorum de réplicas se trouvant la région qui accepte les opérations d’écriture. Par ailleurs, si le conteneur est configuré avec une stratégie d’indexation cohérente, l’index est également mis à jour de manière synchrone, répliqué et durablement validé par le quorum de réplicas avant cette même reconnaissance de l’opération d’écriture.

Le tableau suivant récapitule la fenêtre potentielle de perte de données en cas de sinistre régional pour les comptes Cosmos qui couvrent plusieurs régions.

| **Niveau de cohérence** | **Fenêtre de perte de données potentielle en cas de sinistre régional** |
| - | - |
| Remarque | Zéro |
| Obsolescence limitée | Limitée à la « fenêtre d’obsolescence » que vous configurez sur le compte Cosmos. |
| session | Jusqu’à 5 secondes |
| Préfixe cohérent | Jusqu’à 5 secondes |
| Eventual (Éventuel) | Jusqu’à 5 secondes |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les compromis en matière de distribution globale et de cohérence générale dans les systèmes distribués, voir les articles suivants :

* [Compromis en matière de cohérence dans la conception des systèmes de base de données distribuées modernes](https://www.computer.org/web/csdl/index/-/csdl/mags/co/2012/02/mco2012020037-abs.html)
* [Haute disponibilité](high-availability.md)
* [Contrat SLA Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
