---
title: Limites de ressources par vCore d’Azure SQL Database – pools élastiques | Microsoft Docs
description: Cette page décrit certaines des limites de ressources courantes par vCore pour des pools élastiques dans Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab
manager: craigg
ms.date: 10/15/2018
ms.openlocfilehash: f41974c6e2b2b0565f0a2703cfd638777a6bb9eb
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52878021"
---
# <a name="azure-sql-database-vcore-based-purchasing-model-limits-for-elastic-pools"></a>Limites du modèle d’achat par vCore d’Azure SQL Database pour les pools élastiques

Cet article détaille les limites de ressources des pools élastiques Azure SQL Database et des bases de données mises en pool suivant le modèle d’achat par vCore.

Pour connaître les limites du modèle d’achat par DTU, voir [Limites des ressources par DTU de SQL Database – pools élastiques](sql-database-dtu-resource-limits-elastic-pools.md).

> [!IMPORTANT]
> Dans certaines circonstances, vous devrez peut-être réduire une base de données pour récupérer l’espace inutilisé. Pour plus d’informations, consultez l’article [Gérer l’espace du fichier de la base de données SQL Azure](sql-database-file-space-management.md).

Vous pouvez définir le niveau de service, la taille de calcul et la quantité de stockage à l’aide du [Portail Azure](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), de [PowerShell](sql-database-elastic-pool-manage.md#powershell-manage-elastic-pools-and-pooled-databases), [d’Azure CLI](sql-database-elastic-pool-manage.md#azure-cli-manage-elastic-pools-and-pooled-databases) ou de [l’API REST](sql-database-elastic-pool-manage.md#rest-api-manage-elastic-pools-and-pooled-databases).

> [!NOTE]
> Les limites de ressources des bases de données individuelles dans les pools élastiques sont généralement identiques à celles des bases de données uniques situées hors des pools qui ont la même taille de calcul. Par exemple, le nombre maximal de workers simultanés dans une base de données GP_Gen4_1 est de 200. Par conséquent, le nombre maximal de workers simultanés pour une base de données dans un pool GP_Gen4_1 est aussi de 200. Notez que le nombre total de workers simultanés dans le pool GP_Gen4_1 est de 210.

## <a name="general-purpose-service-tier-storage-sizes-and-compute-sizes"></a>Niveau de service Usage général : tailles de stockage et tailles de calcul

### <a name="generation-4-compute-platform"></a>Plateforme de calcul de génération 4

|Taille de calcul|GP_Gen4_1|GP_Gen4_2|GP_Gen4_4|GP_Gen4_8|GP_Gen4_16|GP_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Génération H/W|4|4|4|4|4|4|
|vCores|1|2|4|8|16|24|
|Mémoire (Go)|7|14|28|56|112|168|
|Prise en charge de ColumnStore|OUI|OUI|OUI|OUI|OUI|OUI|
|Stockage In-Memory OLTP (Go)|N/A|N/A|N/A|N/A|N/A|N/A|
|Type de stockage|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|
|Taille maximale des données (Go)|512|756|1536|2 048|3584|4096|
|Taille maximale du journal|154|227|461|614|1075|1229|
|Taille de TempDB (DB)|32|64|128|256|384|384|
|IOPS cible (64 ko)|500|1 000|2000|4000|7000|7000|
|Latence d’E/S (approximative)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|
|Nombre maximal d’ouvriers simultanés (demandes)|210|420|840|1680|3360|5040|
|Nombre maximal de sessions autorisé|30000|30000|30000|30000|30000|30000|
|Nombre maximal de bases de données par pool|100|200|500|500|500|500|
|Choix du nombre minimal/maximal de cœurs virtuels de pool élastique par base de données|0 ; 0,25 ; 0,5 ; 1|0 ; 0,25 ; 0,5 ; 1 ; 2|0 ; 0,25 ; 0,5 ; 1 ; 2 ; 4|0 ; 0,25 ; 0,5 ; 1 ; 2 ; 4 ; 8|0 ; 0,25 ; 0,5 ; 1 ; 2 ; 4 ; 8 ; 16|0, 0.25, 0.5, 1, 2, 4, 8, 16, 24|
|Nombre de réplicas|1|1|1|1|1|1|
|Plusieurs zones de disponibilités|N/A|N/A|N/A|N/A|N/A|N/A|
|Lecture du Scale-out|N/A|N/A|N/A|N/A|N/A|N/A|
|Stockage de sauvegarde inclus|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|
|||

### <a name="generation-5-compute-platform"></a>Plateforme de calcul de génération 5

|Taille de calcul|GP_Gen5_2|GP_Gen5_4|GP_Gen5_8|GP_Gen5_16|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |--: |--: |--: |--: |
|Génération H/W|5.|5.|5.|5.|5.|5.|5.|5.|
|vCores|2|4|8|16|24|32|40|80|
|Mémoire (Go)|11|22|44|88|132|176|220|440|
|Prise en charge de ColumnStore|OUI|OUI|OUI|OUI|OUI|OUI|OUI|OUI|
|Stockage In-Memory OLTP (Go)|N/A|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Type de stockage|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|
|Taille maximale des données (Go)|512|756|1536|2 048|3 072|4096|4096|4096|
|Taille maximale du journal|154|227|461|614|922|1229|1229|1229|
|Taille de TempDB (DB)|64|128|256|384|384|384|384|384|
|IOPS cible (64 ko)|500|1 000|2000|4000|6000|7000|7000|7000|
|Latence d’E/S (approximative)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|
|Nombre maximal d’ouvriers simultanés (demandes)|210|420|840|1680|2520|3360|4200|8400
|Nombre maximal de sessions autorisé|30000|30000|30000|30000|30000|30000|30000|30000|
|Nombre maximal de bases de données par pool|100|200|500|500|500|500|500|500|
|Choix du nombre minimal/maximal de cœurs virtuels de pool élastique par base de données|0 ; 0,25 ; 0,5 ; 1 ; 2|0 ; 0,25 ; 0,5 ; 1 ; 2 ; 4|0 ; 0,25 ; 0,5 ; 1 ; 2 ; 4 ; 8|0 ; 0,25 ; 0,5 ; 1 ; 2 ; 4 ; 8 ; 16|0, 0.25, 0.5, 1, 2, 4, 8, 16, 24|0, 0.5, 1, 2, 4, 8, 16, 24, 32|0, 0.5, 1, 2, 4, 8, 16, 24, 32, 40|0, 0.5, 1, 2, 4, 8, 16, 24, 32, 40, 80|
|Nombre de réplicas|1|1|1|1|1|1|1|1|
|Plusieurs zones de disponibilités|N/A|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Lecture du Scale-out|N/A|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Stockage de sauvegarde inclus|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|
|||

## <a name="business-critical-service-tier-storage-sizes-and-compute-sizes"></a>Niveau de service Critique pour l’entreprise : tailles de stockage et tailles de calcul

### <a name="generation-4-compute-platform"></a>Plateforme de calcul de génération 4

|Taille de calcul|BC_Gen4_1|BC_Gen4_2|BC_Gen4_4|BC_Gen4_8|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Génération H/W|4|4|4|4|4|4|
|vCores|1|2|4|8|16|24|
|Mémoire (Go)|7|14|28|56|112|168|
|Prise en charge de ColumnStore|OUI|OUI|OUI|OUI|OUI|OUI|
|Stockage In-Memory OLTP (Go)|1|2|4|8|20|36|
|Type de stockage|SSD local|SSD local|SSD local|SSD local|SSD local|SSD local|
|Taille maximale des données (Go)|1 024|1 024|1 024|1 024|1 024|1 024|
|Taille maximale du journal|307|307|307|307|307|307|
|Taille de TempDB (DB)|32|64|128|256|384|384|
|IOPS cible (64 ko)|5 000|10000|20000|40000|80000|120 000|
|Latence d’E/S (approximative)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|
|Nombre maximal d’ouvriers simultanés (demandes)|210|420|840|1680|3360|5040|
|Nombre maximal de sessions autorisé|30000|30000|30000|30000|30000|30000|
|Nombre maximal de bases de données par pool|Seules les bases de données uniques sont prises en charge pour cette taille de calcul|50|100|100|100|100|
|Choix du nombre minimal/maximal de cœurs virtuels de pool élastique par base de données|N/A|0 ; 0,25 ; 0,5 ; 1 ; 2|0 ; 0,25 ; 0,5 ; 1 ; 2 ; 4|0 ; 0,25 ; 0,5 ; 1 ; 2 ; 4 ; 8|0 ; 0,25 ; 0,5 ; 1 ; 2 ; 4 ; 8 ; 16|0, 0.25, 0.5, 1, 2, 4, 8, 16, 24|
|Nombre de réplicas|3|3|3|3|3|3|
|Plusieurs zones de disponibilités|N/A|N/A|N/A|N/A|N/A|N/A|
|Lecture du Scale-out|OUI|OUI|OUI|OUI|OUI|OUI|
|Stockage de sauvegarde inclus|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|
|||

#### <a name="generation-5-compute-platform"></a>Plateforme de calcul de génération 5

|Taille de calcul|BC_Gen5_2|BC_Gen5_4|BC_Gen5_8|BC_Gen5_16|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |--: |--: |--: |--: |
|Génération H/W|5.|5.|5.|5.|5.|5.|5.|5.|
|vCores|2|4|8|16|24|32|40|80|
|Mémoire (Go)|11|22|44|88|132|176|220|440|
|Prise en charge de ColumnStore|OUI|OUI|OUI|OUI|OUI|OUI|OUI|OUI|
|Stockage In-Memory OLTP (Go)|1 571|3 142|6 284|15 768|25 252|37 936|52.22|131,64|
|Type de stockage|SSD local|SSD local|SSD local|SSD local|SSD local|SSD local|SSD local|SSD local|
|Latence d’E/S (approximative)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|
|Taille maximale des données (Go)|1 024|1 024|1 024|1 024|2 048|4096|4096|4096|
|Taille maximale du journal|307|307|307|307|614|1229|1229|1229|
|Taille de TempDB (DB)|64|128|256|384|384|384|384|384|
|IOPS cible (64 ko)|5 000|10000|20000|40000|60000|80000|100000|200000
|Nombre maximal d’ouvriers simultanés (demandes)|210|420|840|1680|2520|3360|5040|8400|
|Nombre maximal de sessions autorisé|30000|30000|30000|30000|30000|30000|30000|30000|
|Nombre maximal de bases de données par pool|N/A|50|100|100|100|100|100|100|
|Choix du nombre minimal/maximal de cœurs virtuels de pool élastique par base de données|N/A|0 ; 0,25 ; 0,5 ; 1 ; 2 ; 4|0 ; 0,25 ; 0,5 ; 1 ; 2 ; 4 ; 8|0 ; 0,25 ; 0,5 ; 1 ; 2 ; 4 ; 8 ; 16|0, 0.25, 0.5, 1, 2, 4, 8, 16, 24|0, 0.5, 1, 2, 4, 8, 16, 24, 32|0, 0.5, 1, 2, 4, 8, 16, 24, 32, 40|0, 0.5, 1, 2, 4, 8, 16, 24, 32, 40, 80|
|Nombre de réplicas|3|3|3|3|3|3|3|3|
|Plusieurs zones de disponibilités|N/A|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Lecture du Scale-out|OUI|OUI|OUI|OUI|OUI|OUI|OUI|OUI|
|Stockage de sauvegarde inclus|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|
|||

Si tous les vCore d’un pool élastique sont occupés, chaque base de données du pool reçoit une quantité égale de ressources de calcul pour traiter les requêtes. Le service de base de données SQL offre un partage équitable des ressources entre les bases de données, garantissant des tranches de temps de calcul égales. Le partage équitable des ressources du pool élastique s’ajoute à n’importe quelle quantité de ressources garantie pour chaque base de données lorsque le nombre minimal de vCore par base de données est défini sur une valeur différente de zéro.

## <a name="database-properties-for-pooled-databases"></a>Propriétés de base de données pour les bases de données mises en pool

Le tableau suivant décrit les propriétés des bases de données mises en pool.

| Propriété | Description |
|:--- |:--- |
| Nombre maximal de vCore par base de données |Nombre maximal de vCore pouvant être utilisés par une des bases de données du pool en fonction du nombre de vCore utilisés par les autres bases de données du pool. Le nombre maximal de vCore par base de données n’est pas une garantie concernant l’octroi des ressources pour une base de données. Il s’agit d’un paramètre global qui s’applique à toutes les bases de données du pool. Définissez un nombre maximal de vCore par base de données suffisamment élevé pour gérer les pics d’utilisation des bases de données. Une certaine allocation excessive est attendue dans la mesure où le pool prend généralement en compte des modèles de creux et de pics d’utilisation des bases de données, dans lesquels toutes les bases de données ne connaissent pas simultanément des pics d’utilisation.|
| Nombre minimal de vCore par base de données |Nombre minimal de vCore garanti pour chaque base de données du pool. Il s’agit d’un paramètre global qui s’applique à toutes les bases de données du pool. Le nombre minimal de vCore par base de données peut être défini sur 0, qui est également la valeur par défaut. Cette propriété est définie sur une valeur comprise entre 0 et le nombre moyen de vCore utilisés par base de données. Le produit du nombre de bases de données du pool et du nombre minimal de vCore par base de données ne peut pas dépasser le nombre de vCore par pool.|
| Espace de stockage maximal par base de données |La taille de base de données maximale définie par l’utilisateur pour une base de données dans un pool. Les bases de données regroupées se partagent l’espace de stockage du pool alloué. Par conséquent, la taille qu’une base de données peut atteindre est limitée au stockage de pool minimal restant et à la taille de base de données. La taille de base de données maximale fait référence à la taille maximale des fichiers de données et n’inclut pas l’espace utilisé par les fichiers journaux. |
|||

## <a name="next-steps"></a>Étapes suivantes

- Consultez [SQL Database FAQ](sql-database-faq.md) (FAQ de SQL Database) pour obtenir des réponses aux questions fréquemment posées.
- Pour plus d’informations sur les limites au niveau du serveur et de l’abonnement, consultez l’article de [vue d’ensemble des limites de ressources sur un serveur logique](sql-database-resource-limits-logical-server.md).
- Pour plus d’informations sur les limites générales d’Azure, consultez [Abonnement Azure et limites, quotas et contraintes du service](../azure-subscription-service-limits.md).
