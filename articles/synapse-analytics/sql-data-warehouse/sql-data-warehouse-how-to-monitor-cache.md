---
title: Optimiser le cache Gen2
description: Découvrez comment surveiller votre cache Gen2 à l’aide du portail Microsoft Azure.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 4d66a1174b1b4adc94b24c6aecd55b2b8679f2f7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85211882"
---
# <a name="how-to-monitor-the-gen2-cache"></a>Procédure de surveillance du cache Gen2

Cet article décrit la procédure de surveillance et de résolution des problèmes de lenteur des performances des requêtes en déterminant si votre charge de travail exploite lu cache Gen2 de façon optimale.

L’architecture de stockage Gen2 nivelle automatiquement vos segments columnstore les plus fréquemment interrogés dans un cache résidant sur des disques SSD basés sur NVMe conçus pour les entrepôts de données Gen2. Les performances sont optimisées lorsque vos requêtes récupèrent des segments qui résident dans le cache.
 
## <a name="troubleshoot-using-the-azure-portal"></a>Résolution des problèmes à l’aide du portail Azure

Vous pouvez utiliser Azure Monitor pour afficher les mesures du cache Gen2 afin de résoudre les problèmes de performances des requêtes. Commencez par accéder au portail Microsoft Azure et cliquez sur **Superviser**, **Métriques** et **+ Sélectionner une étendue** :

![Azure Monitor](./media/sql-data-warehouse-how-to-monitor-cache/cache-0.png)

Utilisez les barres de recherche et de liste déroulante pour rechercher votre entrepôt de données. Sélectionnez ensuite Appliquer.

![Azure Monitor](./media/sql-data-warehouse-how-to-monitor-cache/cache-1.png)

Les métriques clés pour la résolution des problèmes du cache Gen2 sont **Pourcentage d’accès au cache** et **Pourcentage de cache utilisé**. Sélectionnez **Pourcentage d’accès au cache**, puis utilisez le bouton **Ajouter une métrique** pour ajouter **Pourcentage de cache utilisé**. 

![Métriques du cache](./media/sql-data-warehouse-how-to-monitor-cache/cache-2.png)

## <a name="cache-hit-and-used-percentage"></a>Correspondance dans le cache et pourcentage utilisé

La matrice ci-dessous décrit les scénarios en fonction des valeurs des métriques de cache :

|                                | **Pourcentage d’accès au cache élevé** | **Pourcentage d’accès au cache faible** |
| :----------------------------: | :---------------------------: | :--------------------------: |
| **Pourcentage de cache utilisé élevé** |          Scénario 1           |          Scénario 2          |
| **Pourcentage de cache utilisé faible**  |          Scénario 3           |          Scénario 4          |

**Scénario 1 :** Vous utilisez votre cache de manière optimale. [Détecter les problèmes](sql-data-warehouse-manage-monitor.md) d’autres éléments qui peuvent ralentir vos requêtes.

**Scénario 2 :** Votre jeu de données de travail en cours ne peut pas tenir dans le cache, ce qui provoque un faible pourcentage d’accès au cache en raison de lectures physiques. Tenez compte de la montée en puissance de votre niveau de performance, puis réexécutez votre charge de travail pour remplir le cache.

**Scénario 3 :** Il est probable que votre requête s’exécute lentement pour des raisons non liées au cache. [Détecter les problèmes](sql-data-warehouse-manage-monitor.md) d’autres éléments qui peuvent ralentir vos requêtes. Vous pouvez également envisager une [descente en puissance de votre instance](sql-data-warehouse-manage-monitor.md) pour réduire la taille du cache afin de diminuer les coûts. 

**Scénario 4 :** Vous aviez un cache froid, ce qui pourrait être la raison pour laquelle votre requête a été lente. Envisagez de réexécuter votre requête, car votre jeu de données de travail doit maintenant être mis en cache. 

> [!IMPORTANT]
> Si le pourcentage de correspondance dans le cache ou le pourcentage de cache utilisé n’est pas mis à jour après une réexécution de votre charge de travail, votre plage de travail réside peut-être déjà en mémoire. Seules les tables columnstore du cluster sont mises en cache.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur le réglage des performances des requêtes générales, consultez [Surveillance de l’exécution des rêquêtes](sql-data-warehouse-manage-monitor.md#monitor-query-execution).
