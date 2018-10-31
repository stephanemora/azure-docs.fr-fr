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
ms.date: 10/15/2018
ms.openlocfilehash: 12074ad28e27a249a6dc378986f014ede1cd2ab3
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49353577"
---
# <a name="azure-sql-database-vcore-based-purchasing-model-limits-for-a-single-database"></a>Limites du modèle d’achat par vCore d’Azure SQL Database pour une base de données unique

Cet article détaille les limites de ressources des bases de données uniques Azure SQL Database suivant le modèle d’achat par vCore.

Pour connaître les limites du modèle d’achat basé sur DTU pour les bases de données sur un serveur logique, consultez [Vue d’ensemble des limites de ressources sur un serveur logique](sql-database-resource-limits-logical-server.md).

> [!IMPORTANT]
> Dans certaines circonstances, vous devrez peut-être réduire une base de données pour récupérer l’espace inutilisé. Pour plus d’informations, consultez l’article [Gérer l’espace du fichier de la base de données SQL Azure](sql-database-file-space-management.md).

Vous pouvez définir le niveau de service, la taille de calcul et la quantité de stockage pour une base de données unique à l’aide du [Portail Azure](sql-database-single-databases-manage.md#azure-portal-manage-logical-servers-and-databases), de [Transact-SQL](sql-database-single-databases-manage.md#transact-sql-manage-logical-servers-and-databases), de [PowerShell](sql-database-single-databases-manage.md#powershell-manage-logical-servers-and-databases), [d’Azure CLI](sql-database-single-databases-manage.md#azure-cli-manage-logical-servers-and-databases) ou de [l’API REST](sql-database-single-databases-manage.md#rest-api-manage-logical-servers-and-databases).

## <a name="general-purpose-service-tier-storage-sizes-and-compute-sizes"></a>Niveau de service Usage général : tailles de stockage et tailles de capacité de calcul

### <a name="generation-4-compute-platform"></a>Plateforme de calcul de génération 4

|Taille de calcul|GP_Gen4_1|GP_Gen4_2|GP_Gen4_4|GP_Gen4_8|GP_Gen4_16|GP_Gen4_24
|:--- | --: |--: |--: |--: |--: |--: |
|Génération H/W|4|4|4|4|4|4|
|vCores|1|2|4|8|16|24|
|Mémoire (Go)|7|14|28|56|112|168|
|Prise en charge de ColumnStore|OUI|OUI|OUI|OUI|OUI|OUI|
|Stockage In-Memory OLTP (Go)|N/A|N/A|N/A|N/A|N/A|N/A|
|Type de stockage|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|
|Latence d’E/S (approximative)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|
|Taille maximale des données (Go)|1 024|1 024|1536|3 072|4096|4096|
|Taille maximale du journal (Go)|307|307|461|922|1229|1229|
|Taille de TEMPDB (Go)|32|64|128|256|384|384|
|IOPS cible (64 ko)|500|1 000|2000|4000|7000|7000|
|Nombre maximal d’ouvriers simultanés (demandes)|200|400|800|1 600|3200|4 800|
|Nombre maximal de sessions autorisé|30000|30000|30000|30000|30000|30000|
|Nombre de réplicas|1|1|1|1|1|1|
|Plusieurs zones de disponibilités|N/A|N/A|N/A|N/A|N/A|N/A|000
|Lecture du Scale-out|N/A|N/A|N/A|N/A|N/A|N/A|
|Stockage de sauvegarde inclus|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|
|||

### <a name="generation-5-compute-platform"></a>Plateforme de calcul de génération 5

|Taille de calcul|GP_Gen5_2|GP_Gen5_4|GP_Gen5_8|GP_Gen5_16|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40| GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |--: |
|Génération H/W|5.|5.|5.|5.|5.|5.|5.|
|vCores|2|4|8|16|24|32|40|80|
|Mémoire (Go)|11|22|44|88|132|176|220|440|
|Prise en charge de ColumnStore|OUI|OUI|OUI|OUI|OUI|OUI|OUI|OUI|
|Stockage In-Memory OLTP (Go)|N/A|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Type de stockage|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|Stockage (distant) Premium|
|Latence d’E/S (approximative)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|5-7 ms (écriture)<br>5-10 ms (lecture)|
|Taille maximale des données (Go)|1 024|1 024|1536|3 072|4096|4096|4096|4096|
|Taille maximale du journal (Go)|307|307|461|614|1229|1229|1229|1229|
|Taille de TEMPDB (Go)|64|128|256|384|384|384|384|384|
|IOPS cible (64 ko)|500|1 000|2000|4000|6000|7000|7000|7000|
|Nombre maximal d’ouvriers simultanés (demandes)|200|400|800|1 600|2 400|3200|4000|8000|
|Nombre maximal de sessions autorisé|30000|30000|30000|30000|30000|30000|30000|30000|
|Nombre de réplicas|1|1|1|1|1|1|1|1|
|Plusieurs zones de disponibilités|N/A|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Lecture du Scale-out|N/A|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Stockage de sauvegarde inclus|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|
|||

## <a name="business-critical-service-tier-storage-sizes-and-compute-sizes"></a>Niveau de service Critique pour l’entreprise : tailles de stockage et tailles de capacité de calcul

### <a name="generation-4-compute-platform"></a>Plateforme de calcul de génération 4

|Taille de calcul|BC_Gen4_1|BC_Gen4_2|BC_Gen4_4|BC_Gen4_8|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Génération H/W|4|4|4|4|4|4|
|vCores|1|2|4|8|16|24|
|Mémoire (Go)|7|14|28|56|112|168|
|Prise en charge de ColumnStore|N/A|N/A|N/A|N/A|N/A|N/A|
|Stockage In-Memory OLTP (Go)|1|2|4|8|20|36|
|Type de stockage|SSD local|SSD local|SSD local|SSD local|SSD local|SSD local|
|Taille maximale des données (Go)|1 024|1 024|1 024|1 024|1 024|1 024|
|Taille maximale du journal (Go)|307|307|307|307|307|307|
|Taille de TEMPDB (Go)|32|64|128|256|384|384|
|IOPS cible (64 ko)|5 000|10000|20000|40000|80000|120 000|
|Latence d’E/S (approximative)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|
|Nombre maximal d’ouvriers simultanés (demandes)|200|400|800|1 600|3200|4 800|
|Nombre maximal de sessions autorisé|30000|30000|30000|30000|30000|30000|
|Nombre de réplicas|3|3|3|3|3|3|
|Plusieurs zones de disponibilités|N/A|N/A|N/A|N/A|N/A|N/A|
|Lecture du Scale-out|OUI|OUI|OUI|OUI|OUI|OUI|
|Stockage de sauvegarde inclus|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|
|||

### <a name="generation-5-compute-platform"></a>Plateforme de calcul de génération 5

|Taille de calcul|BC_Gen5_2|BC_Gen5_4|BC_Gen5_8|BC_Gen5_16|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |--: |--: |--: |--: |--: |
|Génération H/W|5.|5.|5.|5.|5.|5.|5.|5.|
|vCores|2|4|8|16|24|32|40|80|
|Mémoire (Go)|11|22|44|88|132|176|220|440|
|Prise en charge de ColumnStore|OUI|OUI|OUI|OUI|OUI|OUI|OUI|OUI|
|Stockage In-Memory OLTP (Go)|1 571|3 142|6 284|15 768|25 252|37 936|52.22|131,64|
|Type de stockage|SSD local|SSD local|SSD local|SSD local|SSD local|SSD local|SSD local|SSD local|
|Latence d’E/S (approximative)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|1-2 ms (écriture)<br>1-2 ms (lecture)|
|Taille maximale des données (Go)|1 024|1 024|1 024|1 024|2 048|4096|4096|4096|
|Taille maximale du journal (Go)|307|307|307|307|614|1229|1229|1229|
|Taille de TEMPDB (Go)|64|128|256|384|384|384|384|384|
|IOPS cible (64 ko)|5 000|10000|20000|40000|60000|80000|100000|200000
|Nombre maximal d’ouvriers simultanés (demandes)|200|400|800|1 600|2 400|3200|4000|8000|
|Nombre maximal de sessions autorisé|30000|30000|30000|30000|30000|30000|30000|30000|
|Nombre de réplicas|3|3|3|3|3|3|3|3|
|Plusieurs zones de disponibilités|N/A|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Lecture du Scale-out|N/A|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Stockage de sauvegarde inclus|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|1X taille de la base de données|
|||

## <a name="hyperscale-service-tier-preview"></a>Niveau de service Hyperscale (préversion)

### <a name="generation-4-compute-platform-storage-sizes-and-compute-sizes"></a>Plateforme de calcul de génération 4 : tailles de stockage et tailles de calcul

|Niveau de performance|HS_Gen4_1|HS_Gen4_2|HS_Gen4_4|HS_Gen4_8|HS_Gen4_16|HS_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |--: |
|Génération H/W|4|4|4|4|4|4|
|vCores|1|2|4|8|16|24|
|Mémoire (Go)|7|14|28|56|112|168|
|Prise en charge de ColumnStore|OUI|OUI|OUI|OUI|OUI|OUI|
|Stockage In-Memory OLTP (Go)|N/A|N/A|N/A|N/A|N/A|N/A|
|Type de stockage|SSD local|SSD local|SSD local|SSD local|SSD local|SSD local|
|Taille maximale des données (To)|100 |100 |100 |100 |100 |100 |
|Taille maximale du journal (To)|1 |1 |1 |1 |1 |1 |
|Taille de TEMPDB (Go)|32|64|128|256|384|384|
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
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |--: |--: |--: |--: |--: |
|Génération H/W|5.|5.|5.|5.|5.|5.|5.|5.|
|vCores|2|4|8|16|24|32|40|80|
|Mémoire (Go)|11|22|44|88|132|176|220|440|
|Prise en charge de ColumnStore|OUI|OUI|OUI|OUI|OUI|OUI|OUI|OUI|
|Stockage In-Memory OLTP (Go)|N/A|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Type de stockage|SSD local|SSD local|SSD local|SSD local|SSD local|SSD local|SSD local|SSD local|
|Taille maximale des données (To)|100 |100 |100 |100 |100 |100 |100 |100 |
|Taille maximale du journal (To)|1 |1 |1 |1 |1 |1 |1 |1 |
|Taille de TEMPDB (Go)|64|128|256|384|384|384|384|384|
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

- Consultez [SQL Database FAQ](sql-database-faq.md) (FAQ de SQL Database) pour obtenir des réponses aux questions fréquemment posées.
- Pour plus d’informations sur les limites générales d’Azure, consultez [Abonnement Azure et limites, quotas et contraintes du service](../azure-subscription-service-limits.md).
