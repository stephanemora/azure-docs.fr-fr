---
title: Synchroniser des données à partir d’Azure SQL Edge (préversion) à l’aide de SQL Data Sync
description: En savoir plus sur la synchronisation de données à partir d’Azure SQL Edge (préversion) à l’aide d’Azure SQL Data Sync
keywords: SQL Edge, synchroniser des données à partir de SQL Edge, synchronisation de données SQL Edge
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 6bcdfc3eb09b6c5ed4f8dde4d48a34bee8746e1e
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83593443"
---
# <a name="tutorial-sync-data-from-sql-edge-to-azure-sql-database-by-using-sql-data-sync"></a>Tutoriel : Synchroniser des données de SQL Edge sur Azure SQL Database en utilisant SQL Data Sync

Ce tutoriel explique comment utiliser un *groupe de synchronisation* Azure SQL Data Sync pour synchroniser de façon incrémentielle des données d’Azure SQL Edge vers Azure SQL Database. SQL Data Sync est un service conçu sur Azure SQL Database qui vous permet de synchroniser les données choisies de manière bidirectionnelle sur plusieurs bases de données SQL et instances SQL Server. Pour plus d’informations sur SQL Data Sync, consultez [Azure SQL Data Sync](../sql-database/sql-database-sync-data.md).

Azure SQL Edge étant construit sur les dernières versions du [moteur de base de données SQL Server](/sql/sql-server/sql-server-technical-documentation/), tout mécanisme de synchronisation de données applicable à une instance SQL Server locale peut également être utilisé pour synchroniser des données avec une instance SQL Edge exécutée sur un périphérique.

## <a name="prerequisites"></a>Prérequis

Ce tutoriel nécessite un ordinateur Windows configuré avec [Data Sync Agent pour Azure SQL Data Sync](../sql-database/sql-database-data-sync-agent.md).

## <a name="before-you-begin"></a>Avant de commencer

* Créez une base de données Azure SQL. Pour plus d’informations sur la création d’une base de données Azure SQL avec le portail Azure, consultez [Créer une base de données unique dans Azure SQL Database](../sql-database/sql-database-single-database-get-started.md?tabs=azure-portal).

* Créez les tables et autres objets nécessaires dans votre déploiement Azure SQL Database.

* Créez les tables et objets nécessaires dans votre déploiement Azure SQL Edge. Pour plus d’informations, consultez [Utilisation de packages DAC SQL Database avec SQL Edge](stream-analytics.md).

* Inscrivez l’instance Azure SQL Edge auprès de Data Sync Agent pour Azure SQL Data Sync. Pour plus d’informations, consultez [Ajouter une base de données SQL Server locale](../sql-database/sql-database-get-started-sql-data-sync.md#add-on-prem).

## <a name="sync-data-between-an-azure-sql-database-and-sql-edge"></a>Synchroniser des données entre une base de données Azure SQL et SQL Edge

La configuration de la synchronisation entre une base de données Azure SQL et une instance SQL Edge à l’aide de SQL Data Sync implique trois étapes clés :  

1. Utilisez le portail Azure pour créer un groupe de synchronisation. Pour plus d’informations, consultez [Créer un groupe de synchronisation](../sql-database/sql-database-get-started-sql-data-sync.md#create-sync-group). Vous pouvez utiliser une seule base de données *Hub* pour créer plusieurs groupes de synchronisation afin de synchroniser les données de différentes instances SQL Edge avec une ou plusieurs bases de données SQL dans Azure.

2. Ajoutez des membres de synchronisation au groupe de synchronisation. Pour plus d’informations, consultez [Ajouter des membres de synchronisation](../sql-database/sql-database-get-started-sql-data-sync.md#add-sync-members).

3. Configurez le groupe de synchronisation pour sélectionner les tables à ajouter à cette synchronisation. Pour plus d'informations, consultez [Configurer un groupe de synchronisation](../sql-database/sql-database-get-started-sql-data-sync.md#add-sync-members).

Une fois les étapes ci-dessus effectuées, vous avez un groupe de synchronisation comprenant une base de données Azure SQL et une instance SQL Edge.

Pour plus d’informations sur SQL Data Sync, consultez ces articles :

* [Data Sync Agent pour Azure SQL Data Sync](../sql-database/sql-database-data-sync-agent.md)

* [Bonnes pratiques](../sql-database/sql-database-best-practices-data-sync.md) et [Comment résoudre les problèmes liés à Azure SQL Data Sync](../sql-database/sql-database-troubleshoot-data-sync.md)

* [Superviser SQL Data Sync avec des journaux Azure Monitor](../sql-database/sql-database-sync-monitor-oms.md)

* [Mettre à jour le schéma de synchronisation avec Transact-SQL](../sql-database/sql-database-update-sync-schema.md) ou [PowerShell](../sql-database/scripts/sql-database-sync-update-schema.md)

## <a name="next-steps"></a>Étapes suivantes

* [Utiliser PowerShell pour synchroniser Azure SQL Database avec Azure SQL Edge](../sql-database/scripts/sql-database-sync-data-between-azure-onprem.md). Dans ce tutoriel, remplacez les détails de la base de données `OnPremiseServer` par ceux d’Azure SQL Edge.
