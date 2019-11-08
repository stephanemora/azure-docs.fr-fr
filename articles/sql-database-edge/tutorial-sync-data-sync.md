---
title: Synchroniser des données à partir d’Azure SQL Database Edge avec SQL Data Sync | Microsoft Docs
description: En savoir plus sur la synchronisation des données entre Azure SQL Database Edge et Azure SQL Data Sync
keywords: sql database edge, synchroniser des données à partir de sql database edge, synchronisation de données sql database edge
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 857cee30ac4c1002fb7ca57d6be5fa461a14e9ee
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73509193"
---
# <a name="tutorial-sync-data-from-sql-database-edge-to-azure-sql-database-using-sql-data-sync"></a>Didacticiel : Synchroniser des données entre SQL Database Edge et Azure SQL Database avec SQL Data Sync

Dans ce tutoriel, vous allez apprendre à utiliser le *groupe de synchronisation* SQL Data Sync pour synchroniser de façon incrémentielle des données entre Azure SQL Database Edge et Azure SQL Database. SQL Data Sync est un service conçu sur Azure SQL Database qui vous permet de synchroniser les données choisies de manière bidirectionnelle sur plusieurs bases de données SQL et instances SQL Server. Pour plus d’informations sur Azure SQL Data Sync, consultez [Azure SQL Data Sync](../sql-database/sql-database-sync-data.md).

Étant donné qu’Azure SQL Database Edge est basé sur les dernières versions du [Moteur de base de données Microsoft SQL Server](/sql/sql-server/sql-server-technical-documentation/), tout mécanisme de synchronisation de données applicable à une instance SQL Server locale peut également être utilisé pour synchroniser des données avec ou à partir d’une instance SQL Database Edge exécutée sur un appareil de périphérie.

## <a name="prerequisites"></a>Prérequis

Ce tutoriel nécessite un ordinateur Windows configuré avec l’[agent Azure SQL Data Sync](../sql-database/sql-database-data-sync-agent.md).

## <a name="before-you-begin"></a>Avant de commencer

* Créez une instance d’Azure SQL Database. Pour plus d’informations sur la création d’une instance Azure SQL Database avec le portail Azure, consultez [Créer une base de données dans Azure SQL Database](../sql-database/sql-database-single-database-get-started.md?tabs=azure-portal).

* Créez les tables et autres objets nécessaires dans votre déploiement Azure SQL Database.

* Créez les tables et objets nécessaires dans votre déploiement Azure SQL Database Edge. Pour plus d’informations, consultez [Utilisation de packages DAC SQL Database avec SQL Database Edge](stream-analytics.md).

* Inscrivez l’instance Azure SQL Database Edge auprès de l’agent Azure SQL Data Sync. Pour plus d’informations, consultez [Ajouter une base de données SQL Server locale](../sql-database/sql-database-get-started-sql-data-sync.md#add-on-prem).

## <a name="sync-data-between-an-azure-sql-database-and-sql-database-edge"></a>Synchroniser des données entre Azure SQL Database et SQL Database Edge

La configuration de la synchronisation entre Azure SQL Database et une instance SQL Database Edge à l’aide de SQL Data Sync implique trois étapes clés.  

1. Utilisez le portail Azure pour créer un groupe de synchronisation. Pour créer le groupe de synchronisation, consultez [Créer un groupe de synchronisation à l’aide du portail Azure](../sql-database/sql-database-get-started-sql-data-sync.md#create-sync-group). La même base de données *Hub* peut être utilisée pour créer plusieurs groupes de synchronisation afin de synchroniser les données de différentes instances SQL Database Edge avec une ou plusieurs bases de données SQL dans Azure.

2. Ajoutez des membres de synchronisation au groupe de synchronisation. Pour ajouter des membres au groupe de synchronisation, consultez [Ajouter des membres au groupe SQL Data Sync](../sql-database/sql-database-get-started-sql-data-sync.md#add-sync-members).

3. Configurez le groupe de synchronisation pour sélectionner les tables qui feront partie de cette synchronisation. Pour configurer le groupe de synchronisation, consultez [Configurer le groupe de synchronisation](../sql-database/sql-database-get-started-sql-data-sync.md#add-sync-members).

Une fois les étapes ci-dessus terminées, vous disposerez d’un groupe de synchronisation comprenant une instance Azure SQL Database et une instance SQL Database Edge.

Pour plus d’informations sur SQL Data Sync, consultez les articles suivants :

* [Data Sync Agent pour Azure SQL Data Sync](../sql-database/sql-database-data-sync-agent.md)

* [Bonnes pratiques](../sql-database/sql-database-best-practices-data-sync.md) et [Comment résoudre les problèmes liés à Azure SQL Data Sync](../sql-database/sql-database-troubleshoot-data-sync.md)

* [Superviser SQL Data Sync avec des journaux Azure Monitor](../sql-database/sql-database-sync-monitor-oms.md)

* [Mettre à jour le schéma de synchronisation avec Transact-SQL](../sql-database/sql-database-update-sync-schema.md) ou [PowerShell](../sql-database/scripts/sql-database-sync-update-schema.md)

## <a name="next-steps"></a>Étapes suivantes

* [Utilisez PowerShell pour effectuer une synchronisation entre Azure SQL Database et Azure SQL Database Edge](../sql-database/scripts/sql-database-sync-data-between-azure-onprem.md). Dans le tutoriel, remplacez les détails de la base de données *OnPremiseServer* par les détails d’Azure SQL Database Edge.
