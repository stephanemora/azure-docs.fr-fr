---
title: Mettre à niveau vers la dernière génération d’Azure SQL Data Warehouse | Microsoft Docs
description: Étapes de la mise à niveau d’Azure SQL Data Warehouse vers la dernière génération de l’architecture matérielle et de stockage Azure.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.services: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/02/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 42b716274e655bf91f72c1b3ab207b8a5f1ccee0
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2018
---
# <a name="upgrade-to-latest-generation-of-azure-sql-data-warehouse-in-the-azure-portal"></a>Mettre à niveau vers la dernière génération d’Azure SQL Data Warehouse

Utilisez le portail Azure pour mettre à niveau votre Azure SQL Data Warehouse vers la dernière génération de l’architecture matérielle et de stockage Azure. La mise à niveau vous permet de tirer parti de meilleures performances, d’une évolutivité plus élevée et d’un stockage illimité pour les index columnstore.  

## <a name="applies-to"></a>S’applique à
Cette mise à niveau s’applique aux entrepôts de données du niveau de performances Optimisé pour l’élasticité.  Les instructions permettent de mettre à niveau un entrepôt de données du niveau de performances Optimisé pour l’élasticité vers le niveau de performances Optimisé pour le calcul. 

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [Portail Azure](https://portal.azure.com/).

## <a name="before-you-begin"></a>Avant de commencer

1. Si l’entrepôt de données Optimisé pour l’élasticité à mettre à niveau est mis en pause, [reprenez l’exécution de l’entrepôt de données](pause-and-resume-compute-portal.md).
2. Attendez-vous à quelques minutes de temps d’arrêt. 
3. Le processus de mise à niveau supprime toutes les sessions et met fin à toutes les connexions. Avant la mise à niveau, vérifiez que vos requêtes sont terminées. Si vous démarrez une mise à niveau alors que des transactions sont en cours, la durée de restauration peut être longue. 

## <a name="start-the-upgrade"></a>Lancer la mise à niveau

1. Ouvrez votre entrepôt de données dans le portail Azure, puis cliquez sur **Mettre à niveau vers Optimisé pour le calcul**.
2. Prenez note des choix du niveau de performances Optimisé pour le calcul. La sélection par défaut est comparable au niveau actuel avant la mise à niveau.
3. Choisissez un niveau de performances. Le prix du niveau de performance Optimisé pour le calcul est actuellement divisé de moitié pendant la période d’évaluation.
4. Cliquez sur **Mettre à niveau**.
5. Vérifiez l’état dans le portail Azure.
6. Attendez que l’entrepôt de données passe au mode En ligne.

## <a name="rebuild-columnstore-indexes"></a>Reconstruire les index columnstore

Une fois votre entrepôt de données en ligne, vous pouvez charger des données et exécuter des requêtes. Toutefois, les performances peuvent être lentes dans un premier temps, car un processus en arrière-plan migre les données sur le nouveau matériel. 

Pour forcer les données à migrer aussi vite que possible, nous vous recommandons de reconstruire les index columnstore. Pour ce faire, consultez les conseils dans [Reconstruction des index columnstore pour améliorer la qualité de segment](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality). 

## <a name="next-steps"></a>Étapes suivantes
Votre entrepôt de données est en ligne. Pour utiliser les nouvelles fonctionnalités dédiées aux performances, consultez [Classes de ressources pour la gestion des charges de travail](resource-classes-for-workload-management.md).
 