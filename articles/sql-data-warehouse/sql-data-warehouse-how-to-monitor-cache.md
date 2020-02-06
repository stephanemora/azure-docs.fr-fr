---
title: Optimiser le cache Gen2
description: Découvrez comment surveiller votre cache Gen2 à l’aide du portail Microsoft Azure.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: performance
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 924705b7ce1d324583077797714491bdf3fc5cc9
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721215"
---
# <a name="how-to-monitor-the-gen2-cache"></a>Procédure de surveillance du cache Gen2
L’architecture de stockage Gen2 nivelle automatiquement vos segments columnstore les plus fréquemment interrogés dans un cache résidant sur des disques SSD basés sur NVMe conçus pour les entrepôts de données Gen2. Les performances sont optimisées lorsque vos requêtes récupèrent des segments qui résident dans le cache. Cet article décrit la procédure de surveillance et de résolution des problèmes de lenteur des performances des requêtes en déterminant si votre charge de travail exploite lu cache Gen2 de façon optimale.  
## <a name="troubleshoot-using-the-azure-portal"></a>Résolution des problèmes à l’aide du portail Azure
Vous pouvez utiliser Azure Monitor pour afficher les mesures du cache Gen2 afin de résoudre les problèmes de performances des requêtes. Commencez par accéder au portail Microsoft Azure et cliquez sur Monitor :

![Azure Monitor](./media/sql-data-warehouse-cache-portal/cache_0.png)

Sélectionnez le bouton Indicateurs de performance et renseignez **Abonnement**, **Groupe de** **ressources**, **Type de ressource** et **Nom de la ressource** pour votre entrepôt de données.

Les métriques clés pour la résolution des problèmes du cache Gen2 sont **Pourcentage d’accès au cache** et **Pourcentage de cache utilisé**. Configurez le graphique de métrique Azure pour afficher ces deux métriques.

![Métriques du cache](./media/sql-data-warehouse-cache-portal/cache_1.png)


## <a name="cache-hit-and-used-percentage"></a>Correspondance dans le cache et pourcentage utilisé
La matrice ci-dessous décrit les scénarios en fonction des valeurs des métriques de cache :

|                                | **Pourcentage d’accès au cache élevé** | **Pourcentage d’accès au cache faible** |
| :----------------------------: | :---------------------------: | :--------------------------: |
| **Pourcentage de cache utilisé élevé** |          Scénario 1           |          Scénario 2          |
| **Pourcentage de cache utilisé faible**  |          Scénario 3           |          Scénario 4          |

**Scénario 1 :** Vous utilisez votre cache de manière optimale. [Détecter les problèmes](sql-data-warehouse-manage-monitor.md) d’autres éléments qui peuvent ralentir vos requêtes.

**Scénario 2 :** Votre jeu de données de travail actuel ne peut pas tenir dans le cache, ce qui provoque un faible pourcentage d’accès au cache en raison des lectures physiques. Tenez compte de la montée en puissance de votre niveau de performance, puis réexécutez votre charge de travail pour remplir le cache.

**Scénario 3 :** Il est probable que votre requête s’exécute lentement pour des raisons non liées au cache. [Détecter les problèmes](sql-data-warehouse-manage-monitor.md) d’autres éléments qui peuvent ralentir vos requêtes. Vous pouvez également envisager une [descente en puissance de votre instance](sql-data-warehouse-manage-monitor.md) pour réduire la taille du cache afin de diminuer les coûts. 

**Scénario 4 :** Vous aviez un cache froid, ce qui est peut-être la raison pour laquelle votre requête a été lente. Envisagez de réexécuter votre requête, car votre jeu de données de travail doit maintenant être mis en cache. 

> [!IMPORTANT]
> Si le pourcentage de correspondance dans le cache ou le pourcentage de cache utilisé n’est pas mis à jour après une réexécution de votre charge de travail, votre plage de travail réside peut-être déjà en mémoire. Seules les tables columnstore du cluster sont mises en cache.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur le réglage des performances des requêtes générales, consultez [Surveillance de l’exécution des rêquêtes](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md#monitor-query-execution).
