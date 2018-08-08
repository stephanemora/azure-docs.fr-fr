---
title: Niveaux de service Azure SQL Database - DTU | Microsoft Docs
description: Découvrez les niveaux de service des bases de données uniques du pool qui permettent de fournir divers niveaux de performance et diverses tailles de stockage.
services: sql-database
author: sachinpMSFT
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 08/01/2018
manager: craigg
ms.author: carlrab
ms.openlocfilehash: 5d16763fc8f3331082b98216d25190b945d95b60
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39411818"
---
# <a name="choosing-a-dtu-based-service-tier-performance-level-and-storage-resources"></a>Choisir un niveau de service, un niveau de performance et des ressources de stockage basés sur des unités DTU 

Chaque niveau de service a son propre niveau de performance, ainsi qu’une quantité fixe de stockage inclus, une période de rétention fixe pour les sauvegardes et un prix fixe. Tous les niveaux de service permettent de changer de niveau de performance sans nécessiter de temps d’arrêt. Les bases de données et les pools élastiques sont facturés en fonction du niveau de service et du niveau de performance.

> [!IMPORTANT]
> SQL Database Managed Instance, qui est actuellement en préversion publique, ne prend pas en charge le modèle d’achat DTU. Pour plus d’informations, consultez [Azure SQL Database Managed Instance](sql-database-managed-instance.md). 

## <a name="choosing-a-dtu-based-service-tier"></a>Choisir un niveau de service basé sur des unités DTU

Le choix d’un niveau de service dépend principalement des exigences de continuité d’activité, de stockage et de performance.
||De base|standard|Premium|
| :-- | --: |--:| --:| --:| 
|Charge de travail cible|Développement et production|Développement et production|Développement et production||
|Contrat SLA de durée de fonctionnement|99,99 %|99,99 %|99,99 %|N/A pendant la version préliminaire|
|Rétention des sauvegardes|7 jours|35 jours|35 jours|
|UC|Faible|Faible, moyen, élevé|Faible, élevé|
|Débit d’E/S (approximatif) |2,5 IOPS par DTU| 2,5 IOPS par DTU | 48 IOPS par DTU|
|Latence d’E/S (approximative)|5 ms (lecture), 10 ms (écriture)|5 ms (lecture), 10 ms (écriture)|2 ms (lecture/écriture)|
|Indexation Columnstore |N/A|S3 et supérieur|Pris en charge|
|OLTP en mémoire|N/A|N/A|Pris en charge|
|||||

## <a name="single-database-dtu-and-storage-limits"></a>Limites de stockage et unités DTU d’une base de données unique

Les niveaux de performance en termes d’unités de transaction de base de données (DTU) pour des bases de données uniques et d’unités de transaction de base de données élastique (eDTU) pour les pools élastiques. Pour en savoir plus sur les DTU et les eDTU, consultez [Définition des DTU et des eDTU](sql-database-service-tiers.md#what-are-database-transaction-units-dtus).

||De base|standard|Premium|
| :-- | --: | --: | --: | --: |
| Taille de stockage maximale | 2 Go | 1 To | 4 To  | 
| DTU maximales | 5. | 3000 | 4000 | |
||||||

> [!IMPORTANT]
> Dans certaines circonstances, vous devrez peut-être réduire une base de données pour récupérer l’espace inutilisé. Pour plus d’informations, consultez l’article [Gérer l’espace du fichier de la base de données SQL Azure](sql-database-file-space-management.md).

## <a name="elastic-pool-edtu-storage-and-pooled-database-limits"></a>eDTU de pool élastique, stockage et limites de base de données mise en pool

| | **De base** | **Standard** | **Premium** | 
| :-- | --: | --: | --: | --: |
| Taille de stockage maximale par base de données  | 2 Go | 1 To | 1 To | 
| Taille de stockage maximale par pool | 156 Go | 4 To | 4 To | 
| Nombre maximal d’eDTU par base de données | 5. | 3000 | 4000 | 
| eDTU maximales par pool | 1 600 | 3000 | 4000 | 
| Nombre maximal de bases de données par pool | 500  | 500 | 100 | 
||||||

> [!IMPORTANT]
> Le niveau Premium offre plus de 1 To de stockage dans toutes les régions à l’exception des suivantes : USA Centre-Ouest, Chine Est, USDoDCentral, Allemagne - Centre, USDoDEast, US Gov Sud-Ouest, US Gov Iowa, Allemagne - Nord-Est, Chine Nord. Dans les autres régions, l’espace de stockage maximal au niveau Premium est limité à 1 To. Consultez [Limitations actuelles P11-P15](sql-database-dtu-resource-limits-single-databases.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  

> [!IMPORTANT]
> Dans certaines circonstances, vous devrez peut-être réduire une base de données pour récupérer l’espace inutilisé. Pour plus d’informations, consultez l’article [Gérer l’espace du fichier de la base de données SQL Azure](sql-database-file-space-management.md).

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les niveaux de performance et les options de taille de stockage disponibles pour les bases de données uniques, consultez [Limites des ressources DTU SQL Database pour les bases de données uniques](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-performance-levels).
- Pour plus d’informations sur les niveaux de performance et les options de taille de stockage disponibles pour les pools élastiques, consultez [Limites des ressources DTU SQL Database pour les pools élastiques](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-performance-levels).
