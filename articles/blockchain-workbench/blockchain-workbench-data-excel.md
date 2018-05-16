---
title: Utiliser les données Azure Blockchain Workbench dans Microsoft Excel
description: Découvrez comment charger et afficher les données de la base de données SQL Azure Blockchain Workbench dans Microsoft Excel.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/3/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: e8c20f4b8e39615e2a8c486130d7c8bec655a936
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2018
---
# <a name="view-azure-blockchain-workbench-data-with-microsoft-excel"></a>Afficher des données Azure Blockchain Workbench dans Microsoft Excel

Vous pouvez utiliser Microsoft Excel pour afficher des données dans la base de données SQL Azure Blockchain Workbench. Cet article fournit les étapes à suivre pour :

* Vous connecter à la base de données Blockchain Workbench à partir de Microsoft Excel
* Consulter les tables et vues de la base de données Blockchain Workbench
* Charger les données des vues Blockchain Workbench dans Excel

## <a name="connect-to-the-blockchain-workbench-database"></a>Vous connecter à la base de données Blockchain Workbench

Pour vous connecter à une base de données Blockchain Workbench :

1. Ouvrez Microsoft Excel.
2. Dans l’onglet **Données**, sélectionnez **Obtenir des données**.
3. Sélectionnez **À partir d’Azure**, puis **À partir d’Azure SQL Database**.

   ![Connexion à la base de données SQL Azure](media/blockchain-workbench-data-excel/connect-sql-db.png)

4. Dans la boîte de dialogue **Base de données SQL Server** :

    * Pour **Serveur**, saisissez le nom du serveur Blockchain Workbench.
    * Pour **Base de données (facultatif)**, saisissez le nom de la base de données.

   ![Serveur de base de données et base de données](media/blockchain-workbench-data-excel/provide-server-db.png)

5. Dans la barre de navigation de la boîte de dialogue **Base de données SQL Server**, sélectionnez **Base de données**. Entrez vos **nom d’utilisateur** et **mot de passe**, puis cliquez sur **Se connecter**.

    > [!NOTE]
    > Si vous utilisez les informations d’identification créées pendant le processus de déploiement d’Azure Blockchain Workbench, le **nom d’utilisateur** est `dbadmin`. Le **mot de passe** est celui que vous avez créé au moment du déploiement de Blockchain Workbench.
    
   ![Informations d'identification permettant d’accéder à la base de données](media/blockchain-workbench-data-excel/provide-credentials.png)

## <a name="look-at-database-tables-and-views"></a>Examiner les tables et vues de la base de données

La boîte de dialogue du navigateur d’Excel s’ouvre après votre connexion à la base de données. Vous pouvez utiliser ce navigateur pour examiner les tables et les vues dans la base de données. Les vues sont destinées aux rapports et leurs noms sont précédés de **vw**.

   ![Aperçu du navigateur Excel d’une vue](media/blockchain-workbench-data-excel/excel-navigator.png)

## <a name="load-view-data-into-an-excel-workbook"></a>Charger les données des vues dans un classeur Excel

L’exemple suivant montre comment charger des données à partir d’une vue dans un classeur Excel.

1. Dans la barre de défilement du **navigateur**, sélectionnez la vue **vwContractAction**. L’aperçu **vwContractAction** montre toutes les actions associées à un contrat dans la base de données Blockchain Workbench.
2. Sélectionnez **Charger** pour récupérer toutes les données de la vue et les placer dans votre classeur Excel.

   ![Données chargées à partir d’une vue](media/blockchain-workbench-data-excel/view-data.png)

Maintenant que vous avez chargé les données, vous pouvez utiliser les fonctionnalités d’Excel pour créer vos propres rapports en utilisant les données de transaction et les métadonnées à partir de la base de données Azure Blockchain Workbench.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Vues de la base de données dans Azure Blockchain Workbench](blockchain-workbench-database-views.md)