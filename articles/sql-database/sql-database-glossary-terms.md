---
title: Glossaire des termes
description: Lexique Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/22/2020
ms.openlocfilehash: 733901d38703e02ab7dbe811b0f80a1dfedf03d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76705783"
---
# <a name="azure-sql-database-glossary-of-terms"></a>Lexique Azure SQL Database

|Context|Terme|Informations complémentaires|
|:---|:---|:---|
|Service Azure|Azure SQL Database ou SQL Database|[Le service Azure SQL Database](sql-database-technical-overview.md)|
|Modèle d'achat|Modèle d’achat DTU|[Modèle d’achat DTU](sql-database-service-tiers-dtu.md)|
||Modèle d’achat vCore|[Modèle d’achat vCore](sql-database-service-tiers-vcore.md)|
|Option de déploiement |Base de données unique|[Bases de données uniques](sql-database-single-database.md)|
||Pool élastique|[Pool élastique](sql-database-elastic-pool.md)|
||Instance gérée|[instance gérée](sql-database-managed-instance.md)|
|Niveau de service|De base, Standard, Premium, Usage général, Hyperscale, Critique pour l'entreprise|Pour les niveaux de service du modèle vCore, consultez [Base de données unique et pool élastique](sql-database-service-tiers-vcore.md#service-tiers) et [Instance gérée](sql-database-managed-instance.md#managed-instance-service-tiers). Pour les niveaux de service du modèle DTU, consultez [Modèle DTU](sql-database-service-tiers-dtu.md#compare-the-dtu-based-service-tiers).|
|Niveau de calcul|Calcul serverless|[Calcul serverless](sql-database-service-tiers-vcore.md#compute-tiers)
||Calcul provisionné|[Calcul provisionné](sql-database-service-tiers-vcore.md#compute-tiers)
|Génération de calcul|Gen5, Série M, Série Fsv2|[Génération du matériel](sql-database-service-tiers-vcore.md#hardware-generations)
|Entité serveur|Serveur Azure SQL Database ou serveur de base de données|[Serveur de base de données](sql-database-servers.md)|
||Serveur SQL Database Managed Instance, serveur Managed Instance ou instance de serveur|[instance gérée](sql-database-managed-instance.md)|
|Type de ressource|vCore|Cœur de processeur fourni à la ressource de calcul pour une base de données unique, un pool élastique ou une instance gérée.|
||Taille de calcul et quantité de stockage|La taille de calcul correspond à la quantité maximale d'UC, de mémoire et d'autres ressources non liées au stockage disponibles pour une base de données unique, un pool élastique ou une instance gérée.  La taille de stockage est la quantité maximale de stockage disponible pour une base de données unique, un pool élastique ou une instance gérée.  Pour plus d'informations sur les options de dimensionnement du modèle vCore, consultez [Bases de données uniques vCore](sql-database-vcore-resource-limits-single-databases.md), [Pools élastiques vCore](sql-database-vcore-resource-limits-elastic-pools.md) et [Instances gérées](sql-database-managed-instance-resource-limits.md).  Pour plus d'informations sur les options de dimensionnement du modèle DTU, consultez [Bases de données uniques DTU](sql-database-dtu-resource-limits-single-databases.md) et [Pools élastiques DTU](sql-database-dtu-resource-limits-elastic-pools.md).
