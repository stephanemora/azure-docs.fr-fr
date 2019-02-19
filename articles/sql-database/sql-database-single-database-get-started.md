---
title: 'Portail Azure : Créer une base de données unique - Azure SQL Database | Microsoft Docs'
description: Créez et interrogez une base de données unique dans Azure SQL Database à l’aide du portail Azure.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: carlrab
manager: craigg
ms.date: 02/04/2019
ms.openlocfilehash: 66640bd60f6ba8c2f612a6bc9b88a07b13012997
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2019
ms.locfileid: "55990032"
---
# <a name="quickstart-create-a-single-database-in-azure-sql-database-using-the-azure-portal"></a>Démarrage rapide : créer et interroger une base de données unique dans Azure SQL Database à l’aide du portail Azure

La création d’une [base de données unique](sql-database-single-database.md) est l’option de déploiement la plus rapide et la plus simple pour créer une base de données dans Azure SQL Database. Ce guide de démarrage rapide vous montre comment créer et interroger une base de données unique à l’aide du portail Azure.

Si vous n’avez pas d’abonnement Azure, [créez un compte gratuit](https://azure.microsoft.com/free/).

Pour toutes les étapes de ce guide de démarrage rapide, connectez-vous au [Portail Azure](https://portal.azure.com/).

## <a name="create-a-single-database"></a>Créer une base de données unique

Une base de données unique comprend un ensemble défini de ressources (capacité de calcul, mémoire, E/S et stockage) qui varient selon le modèle d’achat choisi. Quand vous créez une base de données unique, vous définissez également un [serveur SQL Database](sql-database-servers.md) pour la gérer et la placer dans le [groupe de ressources Azure](../azure-resource-manager/resource-group-overview.md) d’une région spécifiée.

Pour créer une base de données unique contenant les exemples de données AdventureWorksLT :

1. Sélectionnez **Créer une ressource** dans le coin supérieur gauche du portail Azure.
2. Sélectionnez **Bases de données**, puis **Base de données SQL**.
3. Dans le formulaire **Créer une base de données SQL**, tapez ou sélectionnez les valeurs suivantes :

   - **Nom de la base de données** : Entrez *mySampleDatabase*.
   - **Abonnement**: Faites défiler la liste et sélectionnez l’abonnement approprié, s’il n’apparaît pas.
   - **Groupe de ressources** : Sélectionnez **Créer**, entrez *myResourceGroup* et sélectionnez **OK**.
   - **Sélectionner la source** : Sélectionnez **Sample (AdventureWorksLT)** dans la liste.

    >[!IMPORTANT]
    >Veillez à sélectionner les données **Exemple (AdventureWorksLT)** pour pouvoir facilement suivre le présent guide ainsi que d’autres articles dédiés à Azure SQL Database qui utilisent ces données.
  
   ![Créer une base de données unique](./media/sql-database-get-started-portal/create-database-1.png)

4. Sous **Serveur**, sélectionnez **Créer**.
5. Dans le formulaire **Nouveau serveur**, saisissez ou sélectionnez les valeurs suivantes :

   - **Nom du serveur** : Entrez *mysqlserver*.
   - **Connexion administrateur au serveur** : Tapez *azureuser*.
   - **Mot de passe** : Entrez *Azure1234567*.
   - **Confirmer le mot de passe** : Retapez le mot de passe.
   - **Emplacement** : Sélectionnez n’importe quel emplacement valide dans la liste.  

   >[!IMPORTANT]
   >Mémorisez votre nom d’utilisateur et mot de passe de connexion d’administrateur au serveur, car vous en aurez besoin pour vous connecter au serveur et aux bases de données dans le cadre de ce guide ou d’autres guides de démarrage rapide. Si vous oubliez votre mot de passe ou vos identifiants de connexion, vous pouvez obtenir le nom de connexion ou réinitialiser le mot de passe sur la page **SQL Server**. Pour ouvrir la page **SQL Server** , sélectionnez le nom du serveur sur la page **Vue d’ensemble** de la base de données une fois cette dernière créée.

    ![Créer un serveur](./media/sql-database-get-started-portal/create-database-server.png)

6. Choisissez **Select**.
7. Sur le formulaire **SQL Database**, sélectionnez **Niveau tarifaire**. Explorez la quantité de DTU et de stockage disponible pour chaque niveau de service.

   >[!NOTE]
   >Ce gudie de démarrage rapide utilise le [modèle d’achat DTU](sql-database-service-tiers-dtu.md), mais le [modèle d’achat vCore](sql-database-service-tiers-vcore.md) est également disponible.
   >[!IMPORTANT]
   >Un espace de stockage supérieur à 1 To au niveau Premium est actuellement disponible dans les toutes régions sauf les suivantes : Royaume-Uni Nord, USA Centre-Ouest, Royaume-Uni Sud 2, Chine Est, USDoDCentral, Allemagne Centre, USDoDEast, US Gov Sud-Ouest, US Gov Centre-Sud, Allemagne Nord-Est, Chine Nord et US Gov Est. Dans ces régions, l’espace de stockage maximal au niveau Premium est limité à 1 To. Pour plus d’informations, voir les [limitations actuelles P11-P15](sql-database-dtu-resource-limits-single-databases.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  

8. Pour ce démarrage rapide, sélectionnez le niveau de service **Standard** et utilisez le curseur pour sélectionner **10 DTU (S0)** et **1** Go de stockage.
9. Sélectionnez **Appliquer**.  

   ![Sélectionner une tarification](./media/sql-database-get-started-portal/create-database-s1.png)

10. Sur le formulaire **SQL Database**, sélectionnez **Créer** pour déployer et configurer le groupe de ressources, le serveur et la base de données.

   Le déploiement prend quelques minutes. Pour surveiller la progression du déploiement, sélectionnez **Notifications** dans la barre d’outils.

   ![Notification](./media/sql-database-get-started-portal/notification.png)

## <a name="query-the-database"></a>Interroger la base de données

Maintenant que vous avez créé la base de données, utilisez l’outil de requête intégré au portail Azure pour vous connecter à la base de données et interroger les données.

1. Sur la page **SQL Database** de votre base de données, sélectionnez **Éditeur de requête (préversion)** dans le menu de gauche.

   ![Se connecter à l’Éditeur de requêtes](./media/sql-database-get-started-portal/query-editor-login.png)

2. Entrez vos informations de connexion, puis sélectionnez **OK**.
3. Entrez la requête suivante dans le volet **Éditeur de requêtes**.

   ```sql
   SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

4. Sélectionnez **Exécuter**, puis passez en revue les résultats de la requête dans le volet **Résultats**.

   ![Interroger les résultats de l’Éditeur](./media/sql-database-get-started-portal/query-editor-results.png)

5. Fermez la page **Éditeur de requêtes**, puis sélectionnez **OK** à l’invite pour ignorer les modifications que vous n’avez pas enregistrées.

## <a name="clean-up-resources"></a>Supprimer des ressources

Conservez ce groupe de ressources, ce serveur SQL et cette base de données unique pour accéder aux [étapes suivantes](#next-steps). Les étapes suivantes vous montrent comment vous connecter à votre base de données et comment l’interroger via différentes méthodes.

Une fois que vous avez fini d’utiliser ces ressources, vous pouvez les supprimer comme suit :

1. Dans le menu de gauche du Portail Azure, cliquez sur **Groupes de ressources**, puis sur **myResourceGroup**.
2. Dans la page de votre groupe de ressources, sélectionnez **Supprimer le groupe de ressources**.
3. Entrez *myResourceGroup* dans le champ, puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

- Créez une règle de pare-feu au niveau du serveur pour vous connecter à la base de données unique à l’aide d’outils locaux ou à distance. Pour plus d’informations, consultez [Créer une règle de pare-feu au niveau du serveur](sql-database-server-level-firewall-rule.md).
- Après avoir créé une règle de pare-feu au niveau du serveur, [connectez et interrogez](sql-database-connect-query.md) votre base de données à l’aide de plusieurs outils et langues différents.
  - [Se connecter et effectuer des requêtes à l’aide de SQL Server Management Studio](sql-database-connect-query-ssms.md)
  - [Se connecter et effectuer des requêtes à l’aide d’Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Pour créer des bases de données uniques à l’aide d’Azure CLI, consultez [Exemples d’interface de ligne de commande Azure](sql-database-cli-samples.md).
- Pour créer des bases de données uniques à l’aide d’Azure PowerShell, consultez [Exemples Azure PowerShell](sql-database-powershell-samples.md).
