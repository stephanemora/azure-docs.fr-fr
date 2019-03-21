---
title: Limites de ressources par vCore d’Azure SQL Database – base de données unique | Microsoft Docs
description: Cette page décrit certaines des limites de ressources courantes par vCore pour une base de données unique dans Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 990e6b4d869df030bfddfeb2d60f0ca0e23ea8ca
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57902261"
---
# <a name="azure-sql-database-vcore-based-purchasing-model-limits-for-a-single-database"></a>Limites du modèle d’achat par vCore d’Azure SQL Database pour une base de données unique

Cet article détaille les limites de ressources des bases de données uniques Azure SQL Database suivant le modèle d’achat par vCore.

Pour connaître les limites du modèle d’achat basé sur DTU pour les bases de données sur un serveur SQL Database, consultez [SQL Database resource limits for Azure SQL Database server](sql-database-resource-limits-database-server.md) (Limites de ressources SQL Database pour le serveur Azure SQL Database).

> [!IMPORTANT]
> Dans certaines circonstances, vous devrez peut-être réduire une base de données pour récupérer l’espace inutilisé. Pour plus d’informations, consultez l’article [Gérer l’espace du fichier de la base de données SQL Azure](sql-database-file-space-management.md).

Vous pouvez définir le niveau de service, la taille de calcul et la quantité de stockage pour une base de données unique à l’aide du [Portail Azure](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), de [Transact-SQL](sql-database-single-databases-manage.md#transact-sql-manage-sql-database-servers-and-single-databases), de [PowerShell](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases), [d’Azure CLI](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases) ou de [l’API REST](sql-database-single-databases-manage.md#rest-api-manage-sql-database-servers-and-single-databases).

> [!IMPORTANT]
> Mise à l’échelle les instructions et des considérations, consultez [mettre à l’échelle d’une base de données](sql-database-single-database-scale.md).

## <a name="general-purpose-service-tier-storage-sizes-and-compute-sizes"></a>Niveau de service Usage général : tailles de stockage et tailles de calcul

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-1"></a>Niveau de service Usage général : Plateforme de calcul de génération 4 (partie 1)

|Taille de calcul|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|Génération H/W|4|4|4|4|4|4|
|vCores|1|2|3|4|5|6.|
|Mémoire (Go)|7|14|21|28|35|42|
|Prise en charge de ColumnStore|OUI|OUI|OUI|OUI|OUI|OUI|
|Stockage In-Memory OLTP (Go)|N/A|N/A|N/A|N/A|N/A|S.O.|
|Taille maximale des données (Go)|1 024|1 024|1 024|1536|1536|1536|
|Taille maximale du journal (Go)|307|307|307|461|461|461|
|Taille de TEMPDB (Go)|32|64|96|128|160|192|
|Type de stockage|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|
|Latence d’E/S (approximative)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|
|IOPS cible (64 ko)|500|1 000|1 500|2000|2 500|3000|
|Limites de taux de journal (Mbits/s)|2.5|5.|7.5|10|12.5|15|
|Nombre maximal d’ouvriers simultanés (demandes)|200|400|600|800|1 000|1 200|
|Nombre maximal de sessions autorisé|30000|30000|30000|30000|30000|30000|
|Nombre de réplicas|1|1|1|1|1|1|
|Plusieurs zones de disponibilités|N/A|N/A|N/A|N/A|N/A|N/A|
|Lecture du Scale-out|N/A|N/A|N/A|N/A|N/A|N/A|
|Stockage de sauvegarde inclus|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-2"></a>Niveau de service Usage général : Plateforme de calcul de génération 4 (partie 2)

|Taille de calcul|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24
|:--- | --: |--: |--: |--: |--: |--: |
|Génération H/W|4|4|4|4|4|4|
|vCores|7|8|9|10|16|24|
|Mémoire (Go)|49|56|63|70|112|168|
|Prise en charge de ColumnStore|OUI|OUI|OUI|OUI|OUI|OUI|
|Stockage In-Memory OLTP (Go)|N/A|N/A|N/A|N/A|N/A|S.O.|
|Taille maximale des données (Go)|1536|3 072|3 072|3 072|4096|4096|
|Taille maximale du journal (Go)|461|922|922|922|1229|1229|
|Taille de TEMPDB (Go)|224|256|288|320|384|384|
|Type de stockage|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|
|Latence d’E/S (approximative)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)
|IOPS cible (64 ko)|3 500|4000|4500|5 000|7000|7000|
|Limites de taux de journal (Mbits/s)|17.5|20|20|20|20|20|
|Nombre maximal d’ouvriers simultanés (demandes)|1400|1 600|1 800|2000|3200|4 800|
|Nombre maximal de sessions autorisé|30000|30000|30000|30000|30000|30000|
|Nombre de réplicas|1|1|1|1|1|1|
|Plusieurs zones de disponibilités|N/A|N/A|N/A|N/A|N/A|N/A|
|Lecture du Scale-out|N/A|N/A|N/A|N/A|N/A|N/A|
|Stockage de sauvegarde inclus|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-1"></a>Niveau de service Usage général : Plateforme de calcul de génération 5 (partie 1)

|Taille de calcul|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Génération H/W|5.|5.|5.|5.|5.|5.|5.|
|vCores|2|4|6.|8|10|12|14|
|Mémoire (Go)|10.2|20,4|30,6|40,8|51|61,2|71,4|
|Prise en charge de ColumnStore|OUI|OUI|OUI|OUI|OUI|OUI|OUI|
|Stockage In-Memory OLTP (Go)|N/A|N/A|N/A|N/A|N/A|N/A|S.O.|
|Taille maximale des données (Go)|1 024|1 024|1536|1536|1536|3 072|3 072|
|Taille maximale du journal (Go)|307|307|307|461|461|461|461|
|Taille de TEMPDB (Go)|64|128|192|256|320|384|384|
|Type de stockage|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|
|Latence d’E/S (approximative)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|
|IOPS cible (64 ko)|500|1 000|1 500|2000|2 500|3000|3 500|
|Limites de taux de journal (Mbits/s)|2.5|56|7.5|10|12.5|15|17.5|
|Nombre maximal d’ouvriers simultanés (demandes)|200|400|600|800|1 000|1 200|1400|
|Nombre maximal de sessions autorisé|30000|30000|30000|30000|30000|30000|30000|
|Nombre de réplicas|1|1|1|1|1|1|1|
|Plusieurs zones de disponibilités|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Lecture du Scale-out|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Stockage de sauvegarde inclus|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-2"></a>Niveau de service Usage général : Plateforme de calcul de génération 5 (partie 2)

|Taille de calcul|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Génération H/W|5.|5.|5.|5.|5.|5.|5.|
|vCores|16|18|20|24|32|40|80|
|Mémoire (Go)|81,6|91,8|102|122,4|163,2|204|408|
|Prise en charge de ColumnStore|OUI|OUI|OUI|OUI|OUI|OUI|OUI|
|Stockage In-Memory OLTP (Go)|N/A|N/A|N/A|N/A|N/A|N/A|S.O.|
|Taille maximale des données (Go)|3 072|3 072|3 072|4096|4096|4096|4096|
|Taille maximale du journal (Go)|922|922|922|1229|1229|1229|1229|
|Taille de TEMPDB (Go)|384|384|384|384|384|384|384|
|Type de stockage|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|
|Latence d’E/S (approximative)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|
|IOPS cible (64 ko)|4000|4500|5 000|6000|7000|7000|7000|
|Limites de taux de journal (Mbits/s)|20|20|20|20|20|20|20|
|Nombre maximal d’ouvriers simultanés (demandes)|1 600|1 800|2000|2 400|3200|4000|8000|
|Nombre maximal de sessions autorisé|30000|30000|30000|30000|30000|30000|30000|
|Nombre de réplicas|1|1|1|1|1|1|1|
|Plusieurs zones de disponibilités|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Lecture du Scale-out|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Stockage de sauvegarde inclus|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|

## <a name="business-critical-service-tier-storage-sizes-and-compute-sizes"></a>Niveau de service Critique pour l’entreprise : tailles de stockage et tailles de calcul

### <a name="business-critical-service-tier-generation-4-compute-platform-part-1"></a>Niveau de service Critique pour l’entreprise : Plateforme de calcul de génération 4 (partie 1)

|Taille de calcul|BC_Gen4_1|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|Génération H/W|4|4|4|4|4|4|
|vCores|1|2|3|4|5|6.|
|Mémoire (Go)|7|14|21|28|35|42|
|Prise en charge de ColumnStore|OUI|OUI|OUI|OUI|OUI|OUI|
|Stockage In-Memory OLTP (Go)|1|2|3|4|5|6.|
|Type de stockage|SSD local|SSD local|SSD local|SSD local|SSD local|SSD local|
|Taille maximale des données (Go)|650|650|650|650|650|650|
|Taille maximale du journal (Go)|195|195|195|195|195|195|
|Taille de TEMPDB (Go)|32|64|96|128|160|192|
|Latence d’E/S (approximative)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|
|IOPS cible (64 ko)|5 000|10000|15000|20000|25000|30000|
|Limites de taux de journal (Mbits/s)|6.|12|18|24|30|36|
|Nombre maximal d’ouvriers simultanés (demandes)|200|400|600|800|1 000|1 200|
|Nombre maximal de connexions simultanées|200|400|600|800|1 000|1 200|
|Nombre maximal de sessions autorisé|30000|30000|30000|30000|30000|30000|
|Nombre de réplicas|4|4|4|4|4|4|
|Plusieurs zones de disponibilités|N/A|N/A|N/A|N/A|N/A|N/A|
|Lecture du Scale-out|OUI|OUI|OUI|OUI|OUI|OUI|
|Stockage de sauvegarde inclus|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|

### <a name="business-critical-service-tier-generation-4-compute-platform-part-2"></a>Niveau de service Critique pour l’entreprise : Plateforme de calcul de génération 4 (partie 2)

|Taille de calcul|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Génération H/W|4|4|4|4|4|4|
|vCores|7|8|9|10|16|24|
|Mémoire (Go)|49|56|63|70|112|168|
|Prise en charge de ColumnStore|OUI|OUI|OUI|OUI|OUI|OUI|
|Stockage In-Memory OLTP (Go)|7|8|9,5|11|20|36|
|Type de stockage|SSD local|SSD local|SSD local|SSD local|SSD local|SSD local|
|Taille maximale des données (Go)|650|650|650|650|1 024|1 024|
|Taille maximale du journal (Go)|195|195|195|195|307|307|
|Taille de TEMPDB (Go)|224|256|288|320|384|384|
|Latence d’E/S (approximative)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|
|IOPS cible (64 ko)|35000|40000|45000|50000|80000|120 000|
|Limites de taux de journal (Mbits/s)|42|48|54|60|64|64|
|Nombre maximal d’ouvriers simultanés (demandes)|1400|1 600|1 800|2000|3200|4 800|
|Nombre maximal de connexions simultanées (demandes)|1400|1 600|1 800|2000|3200|4 800|
|Nombre maximal de sessions autorisé|30000|30000|30000|30000|30000|30000|
|Nombre de réplicas|4|4|4|4|4|4|
|Plusieurs zones de disponibilités|N/A|N/A|N/A|N/A|N/A|N/A|
|Lecture du Scale-out|OUI|OUI|OUI|OUI|OUI|OUI|
|Stockage de sauvegarde inclus|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|

### <a name="business-critical-service-tier-generation-5-compute-platform-part-1"></a>Niveau de service Critique pour l’entreprise : Plateforme de calcul de génération 5 (partie 1)

|Taille de calcul|BC_Gen5_2|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Génération H/W|5.|5.|5.|5.|5.|5.|5.|
|vCores|2|4|6.|8|10|12|14|
|Mémoire (Go)|10.2|20,4|30,6|40,8|51|61,2|71,4|
|Prise en charge de ColumnStore|OUI|OUI|OUI|OUI|OUI|OUI|OUI|
|Stockage In-Memory OLTP (Go)|1 571|3 142|4 713|6 284|8 655|11 026|13 397|
|Taille maximale des données (Go)|1 024|1 024|1536|1536|1536|3 072|3 072|
|Taille maximale du journal (Go)|307|307|307|461|461|922|922|
|Taille de TEMPDB (Go)|64|128|192|256|320|384|384|
|Type de stockage|SSD local|SSD local|SSD local|SSD local|SSD local|SSD local|SSD local|
|Latence d’E/S (approximative)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|
|IOPS cible (64 ko)|5 000|10000|15000|20000|25000|30000|35000|
|Limites de taux de journal (Mbits/s)|6.|12|18|24|30|36|42|
|Nombre maximal d’ouvriers simultanés (demandes)|200|400|600|800|1 000|1 200|1400|
|Nombre maximal de connexions simultanées|200|400|600|800|1 000|1 200|1400|
|Nombre maximal de sessions autorisé|30000|30000|30000|30000|30000|30000|30000|
|Nombre de réplicas|4|4|4|4|4|4|4|
|Plusieurs zones de disponibilités|OUI|OUI|OUI|OUI|OUI|OUI|OUI|
|Lecture du Scale-out|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Stockage de sauvegarde inclus|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|

### <a name="business-critical-service-tier-generation-5-compute-platform-part-2"></a>Niveau de service Critique pour l’entreprise : Plateforme de calcul de génération 5 (partie 2)

|Taille de calcul|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Génération H/W|5.|5.|5.|5.|5.|5.|5.|
|vCores|16|18|20|24|32|40|80|
|Mémoire (Go)|81,6|91,8|102|122,4|163,2|204|408|
|Prise en charge de ColumnStore|OUI|OUI|OUI|OUI|OUI|OUI|OUI|
|Stockage In-Memory OLTP (Go)|15 768|18 139|20,51|25 252|37 936|52.22|131,64|
|Taille maximale des données (Go)|3 072|3 072|3 072|4096|4096|4096|4096|
|Taille maximale du journal (Go)|922|922|922|1229|1229|1229|1229|
|Taille de TEMPDB (Go)|384|384|384|384|384|384|384|
|Type de stockage|SSD local|SSD local|SSD local|SSD local|SSD local|SSD local|SSD local|
|Latence d’E/S (approximative)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|
|IOPS cible (64 ko)|40000|45000|50000|60000|80000|100000|200000|
|Limites de taux de journal (Mbits/s)|48|54|60|72|96|96|96|
|Nombre maximal d’ouvriers simultanés (demandes)|1 600|1 800|2000|2 400|3200|4000|8000|
|Nombre maximal de connexions simultanées|1 600|1 800|2000|2 400|3200|4000|8000|
|Nombre maximal de sessions autorisé|30000|30000|30000|30000|30000|30000|30000|
|Nombre de réplicas|4|4|4|4|4|4|4|
|Plusieurs zones de disponibilités|OUI|OUI|OUI|OUI|OUI|OUI|OUI|
|Lecture du Scale-out|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Stockage de sauvegarde inclus|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|

## <a name="hyperscale-service-tier-preview"></a>Niveau de service Hyperscale (préversion)

### <a name="generation-4-compute-platform-storage-sizes-and-compute-sizes"></a>Plateforme de calcul de génération 4 : tailles de stockage et tailles de calcul

|Niveau de performance|HS_Gen4_1|HS_Gen4_2|HS_Gen4_4|HS_Gen4_8|HS_Gen4_16|HS_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Génération H/W|4|4|4|4|4|4|
|vCores|1|2|4|8|16|24|
|Mémoire (Go)|7|14|28|56|112|168|
|Prise en charge de ColumnStore|OUI|OUI|OUI|OUI|OUI|OUI|
|Stockage In-Memory OLTP (Go)|N/A|N/A|N/A|N/A|N/A|S.O.|
|Taille maximale des données (To)|100 |100 |100 |100 |100 |100 |
|Taille maximale du journal (To)|1 |1 |1 |1 |1 |1 |
|Taille de TEMPDB (Go)|32|64|128|256|384|384|
|Type de stockage|SSD local|SSD local|SSD local|SSD local|SSD local|SSD local|
|IOPS cible (64 ko)|À déterminer|À déterminer|À déterminer|À déterminer|À déterminer|À déterminer|
|Latence d’E/S (approximative)|À déterminer|À déterminer|À déterminer|À déterminer|À déterminer|À déterminer|
|Nombre maximal d’ouvriers simultanés (demandes)|200|400|800|1 600|3200|4 800|
|Nombre maximal de sessions autorisé|30000|30000|30000|30000|30000|30000|
|Nombre de réplicas|2|2|2|2|2|2|
|Plusieurs zones de disponibilités|N/A|N/A|N/A|N/A|N/A|N/A|
|Lecture du Scale-out|OUI|OUI|OUI|OUI|OUI|OUI|
|Stockage de sauvegarde inclus|7|7|7|7|7|7|
|||

### <a name="generation-5-compute-platform"></a>Plateforme de calcul de génération 5

|Niveau de performance|HS_Gen5_2|HS_Gen5_4|HS_Gen5_8|HS_Gen5_16|HS_Gen5_24|HS_Gen5_32|HS_Gen5_40|HS_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |
|Génération H/W|5.|5.|5.|5.|5.|5.|5.|5.|
|vCores|2|4|8|16|24|32|40|80|
|Mémoire (Go)|10.2|20,4|40,8|81,6|122,4|163,2|204|408|
|Prise en charge de ColumnStore|OUI|OUI|OUI|OUI|OUI|OUI|OUI|OUI|
|Stockage In-Memory OLTP (Go)|N/A|N/A|N/A|N/A|N/A|N/A|N/A|S.O.|
|Taille maximale des données (To)|100 |100 |100 |100 |100 |100 |100 |100 |
|Taille maximale du journal (To)|1 |1 |1 |1 |1 |1 |1 |1 |
|Taille de TEMPDB (Go)|64|128|256|384|384|384|384|384|
|Type de stockage|SSD local|SSD local|SSD local|SSD local|SSD local|SSD local|SSD local|SSD local|
|IOPS cible (64 ko)|À déterminer|À déterminer|À déterminer|À déterminer|À déterminer|À déterminer|À déterminer|À déterminer|
|Latence d’E/S (approximative)|À déterminer|À déterminer|À déterminer|À déterminer|À déterminer|À déterminer|À déterminer|À déterminer|
|Nombre maximal d’ouvriers simultanés (demandes)|200|400|800|1 600|2 400|3200|4000|8000|
|Nombre maximal de sessions autorisé|30000|30000|30000|30000|30000|30000|30000|30000|
|Nombre de réplicas|2|2|2|2|2|2|2|2|
|Plusieurs zones de disponibilités|N/A|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Lecture du Scale-out|OUI|OUI|OUI|OUI|OUI|OUI|OUI|Oui|
|Stockage de sauvegarde inclus (limité pour la préversion)|7|7|7|7|7|7|7|7|
|||

## <a name="next-steps"></a>Étapes suivantes

- Pour connaître les limites de ressources DTU des bases de données uniques, consultez l’article sur les [limites de ressources pour les bases de données uniques suivant le modèle d’achat DTU](sql-database-dtu-resource-limits-single-databases.md)
- Pour connaître les limites de ressources vCore des pools élastiques, consultez l'article consacré aux [limites de ressources pour les pools élastiques suivant le modèle d'achat vCore](sql-database-vcore-resource-limits-elastic-pools.md)
- Pour connaître les limites de ressources DTU des pools élastiques, consultez l’article sur les [limites de ressources pour les pools élastiques suivant le modèle d’achat DTU](sql-database-dtu-resource-limits-elastic-pools.md)
- Pour connaître les limites de ressources des instances gérées, consultez l’article sur les [limites de ressources des instances gérées](sql-database-managed-instance-resource-limits.md).
- Pour plus d’informations sur les limites générales d’Azure, consultez [Abonnement Azure et limites, quotas et contraintes du service](../azure-subscription-service-limits.md).
- Pour plus d'informations sur les limites de ressources au niveau du serveur de base de données et de l'abonnement, consultez l'article [Vue d'ensemble des limites de ressources sur un serveur SQL Database](sql-database-resource-limits-database-server.md).
