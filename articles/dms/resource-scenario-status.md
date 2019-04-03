---
title: État du scénario de migration de base de données | Microsoft Docs
description: En savoir plus sur l’état des scénarios de migration pris en charge par Azure Database Migration Service.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 7017566092530dce2359d45314ac00dca63b8ad0
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2019
ms.locfileid: "58886231"
---
# <a name="status-of-migration-scenarios-supported-by-azure-database-migration-service"></a>État des scénarios de migration pris en charge par Azure Database Migration Service
Azure Database Migration Service est conçu pour prendre en charge différents scénarios de migration (paires source/cible) pour les deux en mode hors connexion (une fois) et les migrations en ligne (synchronisation continue). Azure Database Migration Service couvre de plus en plus de scénarios au fil du temps. De nouveaux scénarios sont régulièrement ajoutés. Cet article identifie les scénarios de migration pris en charge par le Service de Migration de base de données Azure et l’état (version préliminaire privée, version préliminaire publique ou à la disposition générale) pour chaque scénario.

## <a name="offline-versus-online-migrations"></a>Migrations hors connexion et en ligne
Avec Azure Database Migration Service, vous pouvez effectuer un hors connexion ou une migration en ligne. Avec les migrations *hors connexion*, les temps d’arrêt de l’application commencent en même temps que la migration. Pour limiter les temps d’arrêt sur le temps nécessaire au basculement vers le nouvel environnement une fois la migration terminée, utilisez un *online* migration. Il est recommandé de tester une migration hors connexion pour déterminer si le temps d’arrêt est acceptable ; Si ce n’est pas le cas, effectuer une migration en ligne.

## <a name="migration-scenario-status"></a>État du scénario de migration
L’état des scénarios de migration pris en charge par Azure Database Migration Service varie dans le temps. En règle générale, les scénarios sont apparus **Private Preview**. Participant à la version préliminaire privée exige que les clients de soumettre une candidature via la [site DMS Preview](https://aka.ms/dms-preview). Après la version préliminaire privée, l’état de scénario devient **version préliminaire publique**. Utilisateurs du Service de Migration de base de données Azure peuvent essayer de scénarios de migration en version préliminaire publique directement à partir de l’interface utilisateur. Pas d’inscription est obligatoire.  Toutefois, les scénarios de migration en version préliminaire publique ne peuvent pas être disponibles dans toutes les régions et sont susceptible de subir des modifications supplémentaires avant la version finale. Après la version préliminaire publique, l’état de scénario devient **à la disposition générale**. Généralement disponible (GA) est l’état de la version finale, et la fonctionnalité est terminée et accessible à tous les utilisateurs. 

## <a name="migration-scenario-support"></a>Prise en charge du scénario de migration
Les tableaux suivants indiquent les scénarios de migration sont prises en charge lors de l’utilisation d’Azure Database Migration Service.

> [!NOTE]
> Si un scénario apparaît dans la liste ci-dessous, mais qu’il ne s’affiche pas dans l’interface utilisateur, veuillez contacter l’[équipe de migration des données](mailto:datamigrationteam@microsoft.com) pour plus d’informations.

> [!IMPORTANT]
> Pour afficher les scénarios actuellement pris en charge par le Service de Migration de base de données Azure en version préliminaire privée, consultez le [site DMS Preview](https://aka.ms/dms-preview).

### <a name="offline-one-time-migration-support"></a>Prise en charge de la migration hors connexion (migration ponctuelle)
Le tableau suivant présente la prise en charge du Service de Migration de base de données Azure, pour les migrations hors connexion.

| Cible  | Source | Support | Statut |
| ------------- | ------------- | :-------------: | :-------------: |
| **Base de données SQL Azure** | SQL Server | ✔ | GA |
|   | RDS SQL |  |  |
|   | Oracle |  |  |
| **Azure SQL DB MI** | SQL Server | ✔ | GA |
|   | RDS SQL |  |  |
|   | Oracle |  |   |
| **Machine virtuelle Azure SQL** | SQL Server | ✔ | GA |
|   | Oracle |   |   |
| **Azure Cosmos DB** | MongoDB | ✔ | Version préliminaire publique |
| **Base de données Azure pour MySQL** | MySQL |   |   |
|   | RDS MySQL |   |   |
| **Base de données Azure pour PostgreSQL** | PostgreSQL |  |
|  | RDS PostgreSQL |   |   |

### <a name="online-continuous-sync-migration-support"></a>Prise en charge de la migration en ligne (synchronisation continue)
Le tableau suivant présente le Service de Migration de base de données Azure prise en charge, version préliminaire publique ou à la disposition générale, pour les migrations en ligne.

| Cible  | Source | Support | Statut |
| ------------- | ------------- | :-------------: | :-------------: |
| **Base de données SQL Azure** | SQL Server | ✔ | GA |
|   | RDS SQL | ✔ | GA |
|   | Oracle |  |  |
| **Azure SQL DB MI** | SQL Server | ✔ | GA |
|   | RDS SQL | ✔ | GA |
|   | Oracle | ✔ | Version préliminaire privée |
| **Machine virtuelle Azure SQL** | SQL Server |   |   |
|   | Oracle  |  |  |
| **Azure Cosmos DB** | MongoDB | ✔ | Version préliminaire publique |
| **Base de données Azure pour MySQL** | MySQL | ✔ | GA |
|   | RDS MySQL | ✔ | GA |
| **Base de données Azure pour PostgreSQL** | PostgreSQL | ✔ | GA |
|   | RDS PostgreSQL | ✔ | GA |
|   | Oracle | ✔ | Version préliminaire privée |

## <a name="next-steps"></a>Étapes suivantes
Pour une vue d’ensemble du Service de Migration de base de données Azure et disponibilité régionale, consultez l’article [quel est le Service de Migration de base de données Azure](dms-overview.md).
