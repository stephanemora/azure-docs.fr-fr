---
title: Suspendre et reprendre le calcul dans le pool SQL Synapse avec le portail Azure
description: Utilisez le portail Azure afin de mettre en pause le calcul pour le pool SQL en vue de réduire les coûts. Reprenez le calcul dès que vous pourrez à nouveau utiliser l’entrepôt de données.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/18/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 55e3d5bf4fb63c35d484e4a764c7eeb2e2484fcf
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350957"
---
# <a name="quickstart-pause-and-resume-compute-in-synapse-sql-pool-via-the-azure-portal"></a>Démarrage rapide : Suspendre et reprendre le calcul dans le pool SQL Synapse avec le portail Azure

Vous pouvez utiliser le portail Azure pour suspendre et reprendre les ressources de calcul du pool SQL Synapse (entrepôt de données). Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [portail Azure](https://portal.azure.com/).

## <a name="before-you-begin"></a>Avant de commencer

Utilisez [Créer et connecter - Portail](create-data-warehouse-portal.md) pour créer un pool SQL nommé **mySampleDataWarehouse**. 

## <a name="pause-compute"></a>Suspension du calcul

Pour réduire les coûts, vous pouvez suspendre et reprendre des ressources de calcul à la demande. Par exemple, si vous n’utilisez pas la base de données pendant la nuit et les week-ends, vous pouvez la suspendre à ces moments et la reprendre pendant la journée. 
>[!NOTE]
>Aucune ressource de calcul ne vous sera facturée tant que la base de données restera suspendue. Le stockage, en revanche, continuera à occasionner des frais. 

Suivez ces étapes pour suspendre un pool SQL :

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Cliquez sur **Azure Synapse Analytics (anciennement SQL DW)** dans la page de navigation de gauche du portail Azure.
2. Sélectionnez **mySampleDataWarehouse** dans la page **Azure Synapse Analytics (anciennement SQL DW)** pour ouvrir le pool SQL. 
3. Sur la page **mySampleDataWarehouse**, **l’État** est **En ligne**.

    ![Calcul en ligne](././media/pause-and-resume-compute-portal/compute-online.png)

4. Pour mettre en pause un pool SQL, cliquez sur le bouton **Suspendre**. 
5. Une question de confirmation apparaît, vous demandant si vous souhaitez continuer. Cliquez sur **Oui**.
6. Après quelques instants, **l’État** passe à **Suspension en cours**.

    ![Suspension en cours](./media/pause-and-resume-compute-portal/pausing.png)

7. Une fois l’opération de mise en pause terminée, l’état est **Suspendu** et la case d’option est **Reprendre**.
8. Les ressources de calcul du pool SQL sont maintenant hors ligne. Le calcul ne vous sera pas facturé tant que le service n’aura pas repris.

    ![Calcul hors ligne](././media/pause-and-resume-compute-portal/compute-offline.png)


## <a name="resume-compute"></a>Reprise du calcul

Suivez ces étapes pour la reprise du pool SQL.

1. Cliquez sur **Azure Synapse Analytics (anciennement SQL DW)** dans la page de gauche du portail Azure.
2. Sélectionnez **mySampleDataWarehouse** dans la page **Azure Synapse Analytics (anciennement SQL DW)** pour ouvrir la page du pool SQL. 
3. Sur la page **mySampleDataWarehouse**, **l’État** est **Suspendu**.

    ![Calcul hors ligne](././media/pause-and-resume-compute-portal/compute-offline.png)

4. Pour la reprise du pool SQL, cliquez sur **Reprendre**. 
5. Une question de confirmation apparaît, vous demandant si vous souhaitez le démarrer. Cliquez sur **Oui**.
6. **L’État** passe à **Reprise en cours**.

    ![Reprise](./media/pause-and-resume-compute-portal/resuming.png)

7. Une fois le pool SQL de nouveau en ligne, l’état est **En ligne** et la case d’option est **Suspendre**.
8. Les ressources de calcul du pool SQL sont à présent en ligne. Vous pouvez utiliser le service. Les frais de calcul ont repris.

    ![Calcul en ligne](././media/pause-and-resume-compute-portal/compute-online.png)

## <a name="clean-up-resources"></a>Nettoyer les ressources

Les unités Data Warehouse Unit et les données stockées dans votre pool SQL vous sont facturées. Ces ressources de calcul et de stockage sont facturées séparément. 

- Si vous souhaitez conserver les données dans le stockage, suspendez le calcul.
- Si vous voulez éviter des frais à venir, vous pouvez supprimer le pool SQL. 

Suivez ces étapes pour nettoyer les ressources selon vos besoins.

1. Connectez-vous au [portail Azure](https://portal.azure.com), puis cliquez sur votre pool SQL.

    ![Nettoyer les ressources](./media/pause-and-resume-compute-portal/clean-up-resources.png)

1. Pour suspendre le calcul, cliquez sur le bouton **Suspendre**. 

2. Pour supprimer le pool SQL afin de ne pas être facturé pour le calcul ou le stockage, cliquez sur **Supprimer**.

3. Pour supprimer le serveur SQL que vous avez créé, cliquez sur **sqlpoolservername.database.windows.net**, puis sur **Supprimer**.  

   > [!CAUTION]
   > N’oubliez pas que la suppression du serveur supprime également toutes les bases de données qui lui sont attribuées.

5. Pour supprimer le groupe de ressources, cliquez sur **myResourceGroup**, puis sur **Supprimer le groupe de ressources**.


## <a name="next-steps"></a>Étapes suivantes

Vous venez de mettre en pause et de reprendre le calcul pour votre pool SQL. Passez à l’article suivant pour en savoir plus sur la façon de [charger des données dans un pool SQL](load-data-from-azure-blob-storage-using-polybase.md). Pour plus d’informations sur la gestion des capacités de calcul, consultez l’article [Vue d’ensemble de la gestion du calcul](sql-data-warehouse-manage-compute-overview.md). 

