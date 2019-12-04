---
title: Synchroniser des données Azure SQL Database Edge à l’aide de SQL Data Sync | Microsoft Docs
description: En savoir plus sur la synchronisation des données Azure SQL Database Edge à l’aide d’Azure SQL Data Sync
keywords: sql database edge, synchroniser des données à partir de sql database edge, synchronisation de données sql database edge
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 7c38ba6dbabef4affd8672295a93d46fd4b0e494
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74384180"
---
# <a name="tutorial-sync-data-from-sql-database-edge-to-azure-sql-database-by-using-sql-data-sync"></a>Didacticiel : Synchroniser des données entre SQL Database Edge et Azure SQL Database à l’aide de SQL Data Sync

Dans ce tutoriel, vous apprenez à utiliser un *groupe de synchronisation* Azure SQL Data Sync pour synchroniser de façon incrémentielle des données entre Azure SQL Database Edge et Azure SQL Database. SQL Data Sync est un service conçu sur Azure SQL Database qui vous permet de synchroniser les données choisies de manière bidirectionnelle sur plusieurs bases de données SQL et instances SQL Server. Pour plus d’informations sur SQL Data Sync, consultez [Azure SQL Data Sync](../sql-database/sql-database-sync-data.md).

Comme Azure SQL Database Edge est construit sur les dernières versions du [moteur de base de données SQL Server](/sql/sql-server/sql-server-technical-documentation/), tout mécanisme de synchronisation de données applicable à une instance SQL Server locale peut également être utilisé pour synchroniser des données avec une instance SQL Database Edge exécutée sur un appareil de périphérie.

## <a name="prerequisites"></a>Prérequis

Ce tutoriel nécessite un ordinateur Windows configuré avec [Data Sync Agent pour Azure SQL Data Sync](../sql-database/sql-database-data-sync-agent.md).

## <a name="before-you-begin"></a>Avant de commencer

* Créez une base de données Azure SQL. Pour plus d’informations sur la création d’une base de données Azure SQL avec le portail Azure, consultez [Créer une base de données unique dans Azure SQL Database](../sql-database/sql-database-single-database-get-started.md?tabs=azure-portal).

* Créez les tables et autres objets nécessaires dans votre déploiement Azure SQL Database.

* Créez les tables et objets nécessaires dans votre déploiement Azure SQL Database Edge. Pour plus d’informations, consultez [Utilisation de packages DAC SQL Database avec SQL Database Edge](stream-analytics.md).

* Inscrivez l’instance Azure SQL Database Edge à Data Sync Agent pour Azure SQL Data Sync. Pour plus d’informations, consultez [Ajouter une base de données SQL Server locale](../sql-database/sql-database-get-started-sql-data-sync.md#add-on-prem).

## <a name="sync-data-between-an-azure-sql-database-and-sql-database-edge"></a>Synchroniser des données entre une base de données Azure SQL et SQL Database Edge

La configuration de la synchronisation entre une base de données Azure SQL et une instance SQL Database Edge à l’aide de SQL Data Sync implique trois étapes clés :  

1. Utilisez le portail Azure pour créer un groupe de synchronisation. Pour plus d’informations, consultez [Créer un groupe de synchronisation](../sql-database/sql-database-get-started-sql-data-sync.md#create-sync-group). Vous pouvez utiliser une seule base de données *Hub* pour créer plusieurs groupes de synchronisation afin de synchroniser les données de différentes instances SQL Database Edge avec une ou plusieurs bases de données SQL dans Azure.

2. Ajoutez des membres de synchronisation au groupe de synchronisation. Pour plus d’informations, consultez [Ajouter des membres de synchronisation](../sql-database/sql-database-get-started-sql-data-sync.md#add-sync-members).

3. Configurez le groupe de synchronisation pour sélectionner les tables à ajouter à cette synchronisation. Pour plus d'informations, consultez [Configurer un groupe de synchronisation](../sql-database/sql-database-get-started-sql-data-sync.md#add-sync-members).

Une fois les étapes ci-dessus effectuées, vous avez un groupe de synchronisation comprenant une base de données Azure SQL et une instance SQL Database Edge.

Pour plus d’informations sur SQL Data Sync, consultez ces articles :

* [Data Sync Agent pour Azure SQL Data Sync](../sql-database/sql-database-data-sync-agent.md)

* [Bonnes pratiques](../sql-database/sql-database-best-practices-data-sync.md) et [Comment résoudre les problèmes liés à Azure SQL Data Sync](../sql-database/sql-database-troubleshoot-data-sync.md)

* [Superviser SQL Data Sync avec des journaux Azure Monitor](../sql-database/sql-database-sync-monitor-oms.md)

* [Mettre à jour le schéma de synchronisation avec Transact-SQL](../sql-database/sql-database-update-sync-schema.md) ou [PowerShell](../sql-database/scripts/sql-database-sync-update-schema.md)

## <a name="next-steps"></a>Étapes suivantes

* [Utilisez PowerShell pour synchroniser Azure SQL Database avec Azure SQL Database Edge](../sql-database/scripts/sql-database-sync-data-between-azure-onprem.md). Dans ce tutoriel, remplacez les détails de la base de données `OnPremiseServer` par les ceux d’Azure SQL Database Edge.
