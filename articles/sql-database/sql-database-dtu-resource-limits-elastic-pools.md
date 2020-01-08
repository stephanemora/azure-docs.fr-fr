---
title: Limites de ressources DTU - Pools élastiques
description: Cette page décrit certaines des limites de ressources DTU courantes pour des pools élastiques dans Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: carlrab
ms.date: 03/14/2019
ms.openlocfilehash: 1c9d3256f3898d57eb9f7fe5d758bcaffb37aca6
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/03/2020
ms.locfileid: "75637573"
---
# <a name="resources-limits-for-elastic-pools-using-the-dtu-purchasing-model"></a>Limites de ressources pour des pools élastiques suivant le modèle d’achat DTU

Cet article détaille les limites de ressources des pools élastiques Azure SQL Database et des bases de données mises en pool suivant le modèle d’achat DTU.

Pour connaître les limites de ressources des bases de données uniques du modèle d’achat DTU, consultez [Limites des ressources DTU : bases de données uniques](sql-database-vcore-resource-limits-elastic-pools.md). Pour connaître les limites de ressources vCore, consultez [Limites de ressources vCore : bases de données uniques](sql-database-vcore-resource-limits-single-databases.md) et [Limites de ressources vCore : pools élastiques](sql-database-vcore-resource-limits-elastic-pools.md).

## <a name="elastic-pool-storage-sizes-and-compute-sizes"></a>Pool élastique : tailles de stockage et tailles de calcul

Pour les pools élastiques SQL Database, les tableaux suivants indiquent les ressources disponibles pour chaque niveau de service et chaque taille de calcul. Vous pouvez définir le niveau de service, la taille de calcul et la quantité de stockage à l’aide du [Portail Azure](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), de [PowerShell](sql-database-elastic-pool-manage.md#powershell-manage-elastic-pools-and-pooled-databases), [d’Azure CLI](sql-database-elastic-pool-manage.md#azure-cli-manage-elastic-pools-and-pooled-databases) ou de [l’API REST](sql-database-elastic-pool-manage.md#rest-api-manage-elastic-pools-and-pooled-databases).

> [!IMPORTANT]
> Pour obtenir des instructions et informations sur la mise à l’échelle, consultez [Mettre à l’échelle un pool élastique](sql-database-elastic-pool-scale.md).
> [!NOTE]
> Les limites de ressources des bases de données individuelles dans les pools élastiques sont généralement identiques à celles des bases de données uniques situées hors des pools. Elles dépendent du nombre de DTU et du niveau de service. Par exemple, le nombre maximal d’ouvriers simultanés dans une base de données S2 est de 120. Par conséquent, le nombre maximal d’ouvriers simultanés d’une base de données dans un pool Standard est également de 120 si le nombre maximal de DTU par base de données dans le pool est de 50 (soit l’équivalent de S2).

### <a name="basic-elastic-pool-limits"></a>Limites du pool élastique de base

| Nombre d’eDTU par pool | **50** | **100** | **200** | **300** | **400** | **800** | **1 200** | **1 600** |
|:---|---:|---:|---:| ---: | ---: | ---: | ---: | ---: |
| Espace de stockage inclus par pool (Go) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Choix de l’espace de stockage maximal par pool (Go) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Stockage OLTP en mémoire maximal par pool (Go) | N/A | N/A | N/A | N/A | N/A | N/A | N/A | N/A |
| Nombre maximal de bases de données par pool | 100 | 200 | 500 | 500 | 500 | 500 | 500 | 500 |
| Nombre maximal d’ouvriers simultanés (demandes) par pool | 100 | 200 | 400 | 600 | 800 | 1 600 | 2 400 | 3200 |
| Nombre maximal de sessions simultanées par pool | 30000 | 30000 | 30000 | 30000 |30000 | 30000 | 30000 | 30000 |
| Choix du nombre minimal d’eDTU par base de données | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 |
| Choix du nombre maximal d’eDTU par base de données | 5 | 5 | 5 | 5 | 5 | 5 | 5 | 5 |
| Espace de stockage maximal par base de données (Go) | 2 | 2 | 2 | 2 | 2 | 2 | 2 | 2 |
||||||||

### <a name="standard-elastic-pool-limits"></a>Limites du pool élastique standard

| Nombre d’eDTU par pool | **50** | **100** | **200** | **300** | **400** | **800**|
|:---|---:|---:|---:| ---: | ---: | ---: |
| Espace de stockage inclus par pool (Go) | 50 | 100 | 200 | 300 | 400 | 800 |
| Choix de l’espace de stockage maximal par pool (Go) | 50, 250, 500 | 100, 250, 500, 750 | 200, 250, 500, 750, 1 024 | 300, 500, 750, 1 204, 1 280 | 400, 500, 750, 1 024, 1 280, 1 536 | 800, 1 024, 1 280, 1 536, 1 792, 2 048 |
| Stockage OLTP en mémoire maximal par pool (Go) | N/A | N/A | N/A | N/A | N/A | N/A |
| Nombre maximal de bases de données par pool | 100 | 200 | 500 | 500 | 500 | 500 |
| Nombre maximal d’ouvriers simultanés (demandes) par pool | 100 | 200 | 400 | 600 | 800 | 1 600 |
| Nombre maximal de sessions simultanées par pool | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
| Choix du nombre minimal d’eDTU par base de données | 0, 10, 20, 50 | 0, 10, 20, 50, 100 | 0, 10, 20, 50, 100, 200 | 0, 10, 20, 50, 100, 200, 300 | 0, 10, 20, 50, 100, 200, 300, 400 | 0, 10, 20, 50, 100, 200, 300, 400, 800 |
| Choix du nombre maximal d’eDTU par base de données | 10, 20, 50 | 10, 20, 50, 100 | 10, 20, 50, 100, 200 | 10, 20, 50, 100, 200, 300 | 10, 20, 50, 100, 200, 300, 400 | 10, 20, 50, 100, 200, 300, 400, 800 |
| Espace de stockage maximal par base de données (Go) | 500 | 750 | 1 024 | 1 024 | 1 024 | 1 024 |
||||||||

### <a name="standard-elastic-pool-limits-continued"></a>Limites du pool élastique standard (suite)

| Nombre d’eDTU par pool | **1 200** | **1 600** | **2 000** | **2 500** | **3000** |
|:---|---:|---:|---:| ---: | ---: |
| Espace de stockage inclus par pool (Go) | 1200 | 1 600 | 2000 | 2 500 | 3000 |
| Choix de l’espace de stockage maximal par pool (Go) | 1 200, 1 280, 1 536, 1792, 2 048, 2 304, 2 560 | 1 600, 1 792, 2 048, 2 304, 2 560, 2 816, 3 072 | 2 000, 2 048, 2 304, 2 560, 2 816, 3 072, 3 328, 3 584 | 2 500, 2 560, 2 816, 3 072, 3 328, 3 584, 3 840, 4 096 | 3 000, 3 072, 3 328, 3 584, 3 840, 4 096 |
| Stockage OLTP en mémoire maximal par pool (Go) | N/A | N/A | N/A | N/A | N/A |
| Nombre maximal de bases de données par pool | 500 | 500 | 500 | 500 | 500 |
| Nombre maximal d’ouvriers simultanés (demandes) par pool | 2 400 | 3200 | 4000 | 5 000 | 6000 |
| Nombre maximal de sessions simultanées par pool | 30000 | 30000 | 30000 | 30000 | 30000 |
| Choix du nombre minimal d’eDTU par base de données | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 |
| Choix du nombre maximal d’eDTU par base de données | 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 |
| Choix de l’espace de stockage maximal par base de données (Go) | 1 024 | 1 024 | 1 024 | 1 024 | 1 024 |
|||||||

### <a name="premium-elastic-pool-limits"></a>Limites du pool élastique Premium

| Nombre d’eDTU par pool | **125** | **250** | **500** | **1 000** | **1 500**|
|:---|---:|---:|---:| ---: | ---: |
| Espace de stockage inclus par pool (Go) | 250 | 500 | 750 | 1 024 | 1536 |
| Choix de l’espace de stockage maximal par pool (Go) | 250, 500, 750, 1 024 | 500, 750, 1 024 | 750, 1 024 | 1 024 | 1536 |
| Stockage OLTP en mémoire maximal par pool (Go) | 1 | 2 | 4 | 10 | 12 |
| Nombre maximal de bases de données par pool | 50 | 100 | 100 | 100 | 100 |
| Nombre maximal d’ouvriers simultanés par pool (demandes) | 200 | 400 | 800 | 1 600 | 2 400 |
| Nombre maximal de sessions simultanées par pool | 30000 | 30000 | 30000 | 30000 | 30000 |
| Nombre minimal d’eDTU par base de données | 0, 25, 50, 75, 125 | 0, 25, 50, 75, 125, 250 | 0, 25, 50, 75, 125, 250, 500 | 0, 25, 50, 75, 125, 250, 500, 1000 | 0, 25, 50, 75, 125, 250, 500, 1000, 1500 |
| Nombre maximal d’eDTU par base de données | 25, 50, 75, 125 | 25, 50, 75, 125, 250 | 25, 50, 75, 125, 250, 500 | 25, 50, 75, 125, 250, 500, 1000 | 25, 50, 75, 125, 250, 500, 1000|
| Espace de stockage maximal par base de données (Go) | 1 024 | 1 024 | 1 024 | 1 024 | 1 024 |
|||||||

### <a name="premium-elastic-pool-limits-continued"></a>Limites du pool élastique Premium (suite)

| Nombre d’eDTU par pool | **2 000** | **2 500** | **3000** | **3 500** | **4000**|
|:---|---:|---:|---:| ---: | ---: |
| Espace de stockage inclus par pool (Go) | 2 048 | 2560 | 3 072 | 3 548 | 4096 |
| Choix de l’espace de stockage maximal par pool (Go) | 2 048 | 2560 | 3 072 | 3 548 | 4096|
| Stockage OLTP en mémoire maximal par pool (Go) | 16 | 20 | 24 | 28 | 32 |
| Nombre maximal de bases de données par pool | 100 | 100 | 100 | 100 | 100 |
| Nombre maximal d’ouvriers simultanés (demandes) par pool | 3200 | 4000 | 4 800 | 5600 | 6 400 |
| Nombre maximal de sessions simultanées par pool | 30000 | 30000 | 30000 | 30000 | 30000 |
| Choix du nombre minimal d’eDTU par base de données | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 |
| Choix du nombre maximal d’eDTU par base de données | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 |
| Espace de stockage maximal par base de données (Go) | 1 024 | 1 024 | 1 024 | 1 024 | 1 024 |
|||||||

> [!IMPORTANT]
> Un espace de stockage supérieur à 1 To au niveau Premium est actuellement disponible dans les toutes régions sauf les suivantes : Chine Est, Chine Nord, Allemagne Centre, Allemagne Nord-Est, USA Centre-Ouest, US DoD et Gouvernement US Centre. Dans ces régions, l’espace de stockage maximal au niveau Premium est limité à 1 To.  Pour plus d’informations, voir les [limitations actuelles P11-P15](sql-database-single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb).

Si toutes les DTU d’un pool élastique sont utilisées, chaque base de données du pool reçoit une quantité égale de ressources pour traiter les requêtes. Le service de base de données SQL offre un partage équitable des ressources entre les bases de données, garantissant des tranches de temps de calcul égales. Le partage équitable des ressources du pool élastique s’ajoute à n’importe quelle quantité de ressources garantie pour chaque base de données lorsque le nombre minimal de DTU par base de données est défini sur une valeur différente de zéro.

> [!NOTE]
> Pour les limites `tempdb`, consultez [Limites tempdb](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database?view=sql-server-2017#tempdb-database-in-sql-database).

### <a name="database-properties-for-pooled-databases"></a>Propriétés de base de données pour les bases de données mises en pool

Le tableau suivant décrit les propriétés des bases de données mises en pool.

| Propriété | Description |
|:--- |:--- |
| Nombre maximal d’eDTU par base de données |Nombre maximal d’eDTU pouvant être utilisées par une des bases de données du pool en fonction du nombre d’eDTU utilisées par les autres bases de données du pool. Le nombre maximal d’eDTU par base de données n’est pas une garantie concernant l’octroi des ressources pour une base de données. Il s’agit d’un paramètre global qui s’applique à toutes les bases de données du pool. Définissez un nombre maximal d’eDTU par base de données suffisamment élevé pour gérer les pics d’utilisation des bases de données. Une certaine allocation excessive est attendue dans la mesure où le pool prend généralement en compte des modèles de creux et de pics d’utilisation des bases de données dans lesquels toutes les bases de données ne connaissent pas simultanément des pics d’utilisation. Par exemple, supposons que le pic d’utilisation par base de données est de 20 eDTU et que seules 20 % des 100 bases de données du pool connaissent simultanément un pic d’utilisation. Si le nombre maximal d’eDTU par base de données est défini sur 20 eDTU, vous pouvez envisager une allocation 5 fois plus élevée du pool et définir le nombre d’eDTU par pool sur 400. |
| Nombre minimal d’eDTU par base de données |Nombre minimal d’eDTU garanti pour chaque base de données du pool. Il s’agit d’un paramètre global qui s’applique à toutes les bases de données du pool. Le nombre minimal d’eDTU par base de données peut être défini sur 0, qui est également la valeur par défaut. Cette propriété est définie sur une valeur comprise entre 0 et le nombre moyen d’eDTU utilisées par base de données. Le produit du nombre de bases de données du pool et du nombre minimal d’eDTU par base de données ne peut pas dépasser le nombre d’eDTU par pool. Par exemple, si un pool comporte 20 bases de données et que le nombre minimal d’eDTU par base de données est défini sur 10 eDTU, le nombre d’eDTU par pool doit être d’au moins 200 eDTU. |
| Espace de stockage maximal par base de données |La taille de base de données maximale définie par l’utilisateur pour une base de données dans un pool. Toutefois, les bases de données mises en pool partagent le stockage du pool alloué. Même si le stockage max total *par base de données* est supérieur au stockage total disponible *espace du pool*, l’espace total réellement utilisé par toutes les bases de données ne pourra pas dépasser la limite du pool disponible. La taille de base de données maximale fait référence à la taille maximale des fichiers de données et n’inclut pas l’espace utilisé par les fichiers journaux. |
|||

## <a name="next-steps"></a>Étapes suivantes

- Pour connaître les limites de ressources vCore d’une base de données unique, consultez l’article consacré aux [limites de ressources pour les bases de données uniques suivant le modèle d’achat vCore](sql-database-vcore-resource-limits-single-databases.md)
- Pour connaître les limites de ressources DTU d’une base de données unique, consultez l’article consacré aux [limites de ressources pour les bases de données uniques suivant le modèle d’achat DTU](sql-database-dtu-resource-limits-single-databases.md)
- Pour connaître les limites de ressources vCore des pools élastiques, consultez l’article consacré aux [limites de ressources pour les pools élastiques suivant le modèle d’achat vCore](sql-database-vcore-resource-limits-elastic-pools.md)
- Pour connaître les limites de ressources des instances gérées, consultez l'article consacré aux [limites de ressources des instances gérées](sql-database-managed-instance-resource-limits.md).
- Pour plus d’informations sur les limites générales d’Azure, consultez [Abonnement Azure et limites, quotas et contraintes du service](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Pour plus d'informations sur les limites de ressources au niveau du serveur de base de données et de l'abonnement, consultez l'article [Vue d'ensemble des limites de ressources sur un serveur SQL Database](sql-database-resource-limits-database-server.md).
