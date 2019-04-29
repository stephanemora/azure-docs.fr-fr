---
title: Limites de ressources DTU de bases de données uniques dans Azure SQL Database | Microsoft Docs
description: Cette page décrit certaines limites de ressources DTU courantes pour des bases de données uniques dans Azure SQL Database.
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
ms.date: 03/20/2019
ms.openlocfilehash: b3514eca8db5d5b68b3e5784ee95e8583813945c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61075234"
---
# <a name="resource-limits-for-single-databases-using-the-dtu-based-purchasing-model"></a>Limites de ressources pour des bases de données uniques suivant le modèle d’achat DTU

Cet article détaille les limites de ressources des bases de données uniques Azure SQL Database suivant le modèle d’achat DTU.

Pour connaître les limites de ressources des pools élastiques du modèle d’achat DTU, consultez [Limites des ressources DTU : pools élastiques](sql-database-dtu-resource-limits-elastic-pools.md). Pour voir les limites de ressources par vCore, consultez [Limites de ressources par vCore : bases de données uniques](sql-database-vcore-resource-limits-single-databases.md) et [Limites de ressources par vCore : pools élastiques](sql-database-vcore-resource-limits-elastic-pools.md). Pour plus d’informations concernant les différents modèles d’achat, consultez l’article décrivant les [modèles d’achat et niveaux de service](sql-database-purchase-models.md).

## <a name="single-database-storage-sizes-and-compute-sizes"></a>Base de données unique : tailles de stockage et tailles de calcul

Les tableaux suivants indiquent les ressources disponibles pour une base de données unique à chaque niveau de service et taille de calcul. Vous pouvez définir le niveau de service, la taille de calcul et la quantité de stockage pour une base de données unique à l’aide du [Portail Azure](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), de [Transact-SQL](sql-database-single-databases-manage.md#transact-sql-manage-sql-database-servers-and-single-databases), de [PowerShell](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases), [d’Azure CLI](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases) ou de [l’API REST](sql-database-single-databases-manage.md#rest-api-manage-sql-database-servers-and-single-databases).

> [!IMPORTANT]
> Mise à l’échelle les instructions et des considérations, consultez [mettre à l’échelle d’une base de données](sql-database-single-database-scale.md)

### <a name="basic-service-tier"></a>Niveau de service De base

| **Taille de calcul** | **De base** |
| :--- | --: |
| DTU max | 5. |
| Espace de stockage inclus (Go) | 2 |
| Choix de stockage maximum (Go) | 2 |
| Stockage In-Memory OLTP maximal (Go) |N/A |
| Nombre maximal d’ouvriers simultanés (demandes) | 30 |
| Nombre maximal de sessions simultanées | 300 |
|||

### <a name="standard-service-tier"></a>Niveau de service Standard

| **Taille de calcul** | **S0** | **S1** | **S2** | **S3** |
| :--- |---:| ---:|---:|---:|
| DTU max | 10 | 20 | 50 | 100 |
| Espace de stockage inclus (Go) | 250 | 250 | 250 | 250 |
| Choix de stockage maximum (Go) | 250 | 250 | 250 | 250, 500, 750, 1 024 |
| Stockage In-Memory OLTP maximal (Go) | N/A | N/A | N/A | N/A |
| Nombre maximal d’ouvriers simultanés (demandes)| 60 | 90 | 120 | 200 |
| Nombre maximal de sessions simultanées |600 | 900 | 1 200 | 2 400 |
||||||

### <a name="standard-service-tier-continued"></a>Niveau de service Standard (suite)

| **Taille de calcul** | **S4** | **S6** | **S7** | **S9** | **S12** |
| :--- |---:| ---:|---:|---:|---:|
| DTU max | 200 | 400 | 800 | 1 600 | 3000 |
| Espace de stockage inclus (Go) | 250 | 250 | 250 | 250 | 250 |
| Choix de stockage maximum (Go) | 250, 500, 750, 1 024 | 250, 500, 750, 1 024 | 250, 500, 750, 1 024 | 250, 500, 750, 1 024 | 250, 500, 750, 1 024 |
| Stockage In-Memory OLTP maximal (Go) | N/A | N/A | N/A | N/A |N/A |
| Nombre maximal d’ouvriers simultanés (demandes)| 400 | 800 | 1 600 | 3200 |6000 |
| Nombre maximal de sessions simultanées |4 800 | 9 600 | 19 200 | 30000 |30000 |
|||||||

### <a name="premium-service-tier"></a>Niveau de service Premium

| **Taille de calcul** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** |
| :--- |---:|---:|---:|---:|---:|---:|
| DTU max | 125 | 250 | 500 | 1 000 | 1 750 | 4000 |
| Espace de stockage inclus (Go) | 500 | 500 | 500 | 500 | 4096* | 4096* |
| Choix de stockage maximum (Go) | 500, 750, 1 024 | 500, 750, 1 024 | 500, 750, 1 024 | 500, 750, 1 024 | 4096* | 4096* |
| Stockage In-Memory OLTP maximal (Go) | 1 | 2 | 4 | 8 | 14 | 32 |
| Nombre maximal d’ouvriers simultanés (demandes)| 200 | 400 | 800 | 1 600 | 2 400 | 6 400 |
| Nombre maximal de sessions simultanées | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
|||||||

\* À partir de 1 024 Go jusqu'à 4 096 Go par incréments de 256 Go

> [!IMPORTANT]
> Un espace de stockage supérieur à 1 To au niveau Premium est actuellement disponible dans les toutes régions sauf les suivantes : Chine Est, Chine Nord, Allemagne Centre, Allemagne Nord-Est, USA Centre-Ouest, US DoD et Gouvernement US Centre. Dans ces régions, l’espace de stockage maximal au niveau Premium est limité à 1 To.  Pour plus d’informations, voir les [limitations actuelles P11-P15](sql-database-single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb).  
> [!NOTE]
> Pour `tempdb` les limites, consultez [limites tempdb](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database?view=sql-server-2017#tempdb-database-in-sql-database).

## <a name="next-steps"></a>Étapes suivantes

- Pour connaître les limites de ressources vCore d'une base de données unique, consultez l'article consacré aux [limites de ressources pour les bases de données uniques suivant le modèle d'achat vCore](sql-database-vcore-resource-limits-single-databases.md)
- Pour connaître les limites de ressources vCore des pools élastiques, consultez l'article consacré aux [limites de ressources pour les pools élastiques suivant le modèle d'achat vCore](sql-database-vcore-resource-limits-elastic-pools.md)
- Pour connaître les limites de ressources DTU des pools élastiques, consultez l’article sur les [limites de ressources pour les pools élastiques suivant le modèle d’achat DTU](sql-database-dtu-resource-limits-elastic-pools.md)
- Pour connaître les limites de ressources des instances gérées, consultez l’article sur les [limites de ressources des instances gérées](sql-database-managed-instance-resource-limits.md).
- Pour plus d’informations sur les limites générales d’Azure, consultez [Abonnement Azure et limites, quotas et contraintes du service](../azure-subscription-service-limits.md).
- Pour plus d'informations sur les limites de ressources au niveau du serveur de base de données et de l'abonnement, consultez l'article [Vue d'ensemble des limites de ressources sur un serveur SQL Database](sql-database-resource-limits-database-server.md).
