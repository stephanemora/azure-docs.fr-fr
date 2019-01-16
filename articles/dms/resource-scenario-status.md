---
title: État du scénario de migration de base de données | Microsoft Docs
description: Obtenez davantage d’informations sur l’état des scénarios de migration pris en charge par Azure Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: douglasl
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 01/08/2019
ms.openlocfilehash: 9e153cca321e94233cfda2a03cf52ba85a0f6b02
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2019
ms.locfileid: "54102803"
---
# <a name="status-of-migration-scenarios-supported-by-the-azure-database-migration-service"></a>État des scénarios de migration pris en charge par Azure Database Migration Service
Azure Database Migration Service est conçu pour prendre en charge plusieurs scénarios de migration (paires source/cible), aussi bien pour le mode hors connexion (migration ponctuelle) que le mode en ligne (synchronisation continue). Azure Database Migration Service couvre de plus en plus de scénarios au fil du temps. De nouveaux scénarios sont régulièrement ajoutés. Cet article identifie les scénarios de migration qui sont actuellement pris en charge par Azure Database Migration Service et l’état de chaque scénario (préversion privée [ou limitée], préversion publique ou disposition générale).

## <a name="offline-versus-online-migrations"></a>Migrations hors connexion et en ligne
Lorsque vous migrez des bases de données vers Azure à l’aide d’Azure Database Migration Service, vous pouvez opter pour une migration hors connexion ou en ligne. Avec les migrations *hors connexion*, les temps d’arrêt de l’application commencent en même temps que la migration. Avec les migrations *en ligne*, les interruptions sont limitées au temps qui est nécessaire pour basculer vers le nouvel environnement une fois la migration terminée. Nous vous recommandons de tester une migration hors connexion pour déterminer si le temps d’arrêt est acceptable ; dans le cas contraire, privilégiez une migration en ligne.

## <a name="migration-scenario-status"></a>État du scénario de migration
L’état de chaque scénario de migration pris en charge par Azure Database Migration Service varie dans le temps. En général, les scénarios sont d’abord mis en production sous forme de **préversion privée**. Afin de tirer parti de la fonctionnalité, le client doit envoyer une proposition par le biais du [site DMS Preview](https://aka.ms/dms-preview). Au terme de la préversion privée, le scénario passe à l’état de **préversion publique**. Tous les utilisateurs Azure Database Migration Service peuvent bénéficier des scénarios de migration disponibles en préversion publique. Toutefois, il se peut que le scénario de migration ne soit pas disponible dans toutes les régions et que la fonctionnalité subisse des modifications supplémentaires avant sa version finale. Lorsqu’un scénario de migration est mis **à disposition générale** (c’est-à-dire qu’il atteint son état final et qu’il est publié), la fonctionnalité est terminée et elle est mise à disposition de tous les utilisateurs Azure Database Migration Service. 

## <a name="migration-scenario-support"></a>Prise en charge du scénario de migration

Les tableaux suivants indiquent les scénarios de migration qui sont pris en charge lorsqu’Azure Database Migration Service est utilisé.

> [!NOTE]
> Si un scénario apparaît dans la liste ci-dessous, mais qu’il ne s’affiche pas dans l’interface utilisateur, veuillez contacter l’[équipe de migration des données](mailto:datamigrationteam@microsoft.com) pour plus d’informations.

### <a name="offline-one-time-migration-support"></a>Prise en charge de la migration hors connexion (migration ponctuelle)
Le tableau suivant présente la prise en charge d’Azure Database Migration Service pour les migrations hors connexion.

| Cible  | Source | Support |
| ------------- | ------------- | :-------------: |
| **Base de données Azure SQL**  | SQL Server | ✔ |
|   | RDS SQL  |  ✔ |
|   | Oracle  |   |
| **Base de données Azure SQL MI**  | SQL Server  | ✔ |
|   | RDS SQL  | ✔ |
|   | Oracle  | ✔  |
| **Machine virtuelle Azure SQL**  | SQL Server  | ✔ |
|   | Oracle  |   |
| **Cosmos DB**  | MongoDB  | ✔ |
| **Base de données Azure pour MySQL**  | MySLQ  |  |
|   | RDS MySQL  |  |
| **Base de données Azure pour PostgresSQL**  | PostgreSQL |  |
|  | RDS PostgreSQL  |  |

### <a name="online-continuous-sync-migration-support"></a>Prise en charge de la migration en ligne (synchronisation continue)
Le tableau suivant présente la prise en charge d’Azure Database Migration Service pour les migrations en ligne.

| Cible  | Source | Support |
| ------------- | ------------- | :-------------: |
| **Base de données Azure SQL**  | SQL Server | ✔ |
|   | RDS SQL  |   |
|   | Oracle  |  ✔ |
| **Base de données Azure SQL MI**  | SQL Server  | ✔ |
|   | RDS SQL  |  |
|   | Oracle  | ✔  |
| **Machine virtuelle Azure SQL**  | SQL Server  |   |
|   | Oracle  | ✔  |
| **Cosmos DB**  | MongoDB  | ✔ |
| **Base de données Azure pour MySQL**  | MySLQ  | ✔ |
|   | RDS MySQL  | ✔ |
| **Base de données Azure pour PostgresSQL**  | PostgreSQL | ✔ |
|  | RDS PostgreSQL  | ✔ |

## <a name="next-steps"></a>Étapes suivantes
Pour une présentation d’Azure Database Migration Service et de la mise à disponibilité régionale, consultez l’article [Qu’est-ce qu’Azure Database Migration Service ?](dms-overview.md). 
