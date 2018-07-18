---
title: Utiliser des données Azure Blockchain Workbench avec SQL Server Management Studio
description: Découvrez comment vous connecter à la base de données SQL Azure Blockchain Workbench à partir de SQL Server Management Studio.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/3/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 5c5a2e2b42cd9c19810e6579159b228369ee8f7f
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38969297"
---
# <a name="using-azure-blockchain-workbench-data-with-sql-server-management-studio"></a>Utilisation des données Azure Blockchain Workbench avec SQL Server Management Studio

Microsoft SQL Server Management Studio permet d’écrire et de tester rapidement des requêtes sur la base de données SQL Azure Blockhain Workbench. Cette section explique pas à pas comment vous connecter à la base de données SQL Azure Blockchain Workbench à partir de SQL Server Management Studio.

## <a name="prerequisites"></a>Prérequis

* Téléchargez [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

## <a name="connecting-sql-server-management-studio-to-data-in-azure-blockchain-workbench"></a>Connexion de SQL Server Management Studio aux données dans Azure Blockchain Workbench

1. Ouvrez SQL Server Management Studio et sélectionnez **Se connecter**.
2. Sélectionnez **Moteur de base de données**.

    ![Moteur de base de données](media/blockchain-workbench-data-sql-management-studio/database-engine.png)

3. Dans la boîte de dialogue **Se connecter au serveur**, saisissez le nom du serveur et les informations d’identification de votre base de données.

    Si vous utilisez les informations d’identification créées par le processus de déploiement d’Azure Blockchain Workbench, le nom d’utilisateur sera **dbadmin** et le mot de passe, celui indiqué pendant le déploiement.

    ![Saisie des informations d’identification SQL](media/blockchain-workbench-data-sql-management-studio/sql-creds.png)

 4. SQL Server Management Studio affiche la liste des bases de données, des vues de base de données et des procédures stockées dans la base de données Azure Blockchain Workbench.

    ![Liste des bases de données](media/blockchain-workbench-data-sql-management-studio/db-list.png)

5. Pour afficher les données associées aux vues de la base de données, vous pouvez générer automatiquement une instruction select. Pour cela, procédez comme suit.
6. Cliquez avec le bouton droit sur l’une des vues de la base de données dans l’Explorateur d’objets.
7. Sélectionnez **Script View as** (Vue du script en tant que).
8. Choisissez **SELECT to** (SÉLECTIONNER vers).
9. Sélectionnez **Nouvelle fenêtre d’éditeur de requête**.
10. Vous pouvez créer une nouvelle requête en sélectionnant **Nouvelle requête**.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Vues de la base de données dans Azure Blockchain Workbench](blockchain-workbench-database-views.md)