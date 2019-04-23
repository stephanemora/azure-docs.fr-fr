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
ms.author: ninarn
ms.reviewer: carlrab
manager: craigg
ms.date: 04/11/2019
ms.openlocfilehash: b8395b5e67660f2b6fb1b671a7be6a20b4fceddd
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2019
ms.locfileid: "60004973"
---
# <a name="quickstart-create-a-single-database-in-azure-sql-database-using-the-azure-portal"></a>Démarrage rapide : créer et interroger une base de données unique dans Azure SQL Database à l’aide du portail Azure

La création d’une [base de données unique](sql-database-single-database.md) est l’option de déploiement la plus rapide et la plus simple pour créer une base de données dans Azure SQL Database. Ce guide de démarrage rapide vous montre comment créer et interroger une base de données unique à l’aide du portail Azure.

Si vous n’avez pas d’abonnement Azure, [créez un compte gratuit](https://azure.microsoft.com/free/).

Pour toutes les étapes de ce guide de démarrage rapide, connectez-vous au [Portail Azure](https://portal.azure.com/).

## <a name="create-a-single-database"></a>Créer une base de données unique

Une base de données comprend un ensemble défini de ressources (capacité de calcul, mémoire, E/S et stockage) qui varient selon l’un des deux [modèles d’achat](sql-database-purchase-models.md) choisi. Quand vous créez une base de données unique, vous définissez également un [serveur SQL Database](sql-database-servers.md) pour la gérer et la placer dans le [groupe de ressources Azure](../azure-resource-manager/resource-group-overview.md) d’une région spécifiée.

Pour créer une base de données unique contenant les exemples de données AdventureWorksLT :

1. Sélectionnez **Créer une ressource** dans le coin supérieur gauche du portail Azure.
2. Sélectionnez **Bases de données**, puis sélectionnez **Base de données SQL** pour ouvrir la page **Créer une base de données SQL**. 

   ![Créer une base de données unique](./media/sql-database-get-started-portal/create-database-1.png)

1. Sous l’onglet **Bases**, dans la section **Détails du projet**, tapez ou sélectionnez les valeurs suivantes :

   - **Abonnement**: Faites défiler la liste et sélectionnez l’abonnement approprié, s’il n’apparaît pas.
   - **Groupe de ressources** : sélectionnez **Créer**, tapez `myResourceGroup`, puis sélectionnez **OK**.

   ![Nouvelle base de données SQL - Onglet des informations de base](media/sql-database-get-started-portal/new-sql-database-basics.png)


1. Dans la section formulaire **Détails de la base de données**, tapez ou sélectionnez les valeurs suivantes : 

   - **Nom de la base de données** : Entrez `mySampleDatabase`.
   - **Server** (Serveur) : sélectionnez **Créer**, entrez les valeurs suivantes, puis sélectionnez **Sélectionner**. 
       - **Nom du serveur** : tapez `mysqlserver` ainsi que des chiffres à des fins d’unicité. 
       - **Connexion administrateur au serveur** : Saisissez `azureuser`.
       - **Mot de passe** : tapez un mot de passe complexe qui répond aux exigences de mot de passe. 
       - **Emplacement** : choisissez un emplacement dans la liste déroulante, tel que `West US 2`. 

       ![Nouveau serveur](media/sql-database-get-started-portal/new-server.png)

        > [!IMPORTANT]
        > Mémorisez votre nom d’utilisateur et mot de passe de connexion d’administrateur au serveur, car vous en aurez besoin pour vous connecter au serveur et aux bases de données dans le cadre de ce guide ou d’autres guides de démarrage rapide. Si vous oubliez votre mot de passe ou vos identifiants de connexion, vous pouvez obtenir le nom de connexion ou réinitialiser le mot de passe sur la page **SQL Server**. Pour ouvrir la page **SQL Server** , sélectionnez le nom du serveur sur la page **Vue d’ensemble** de la base de données une fois cette dernière créée.

      ![Détails de la base de données SQL](media/sql-database-get-started-portal/sql-db-basic-db-details.png)

   - **Vous souhaitez utiliser un pool élastique SQL ?**  : sélectionnez l’option **Non**. 
   - **Calcul + stockage** : sélectionnez **Configurer la base de données** pour ce guide de démarrage rapide, sélectionnez le niveau de service **Standard** et utilisez le curseur pour sélectionner **10 DTUs (S0)** et **1** Go de stockage. Sélectionnez **Appliquer**. 

    ![Configurer le niveau](media/sql-database-get-started-portal/create-database-s1.png) 


      > [!NOTE]
      > Ce gudie de démarrage rapide utilise le [modèle d’achat DTU](sql-database-service-tiers-dtu.md), mais le [modèle d’achat vCore](sql-database-service-tiers-vcore.md) est également disponible.
      > [!IMPORTANT]
      > Un espace de stockage supérieur à 1 To au niveau Premium est actuellement disponible dans les toutes régions sauf les suivantes : Chine Est, Chine Nord, Allemagne Centre, Allemagne Nord-Est, USA Centre-Ouest, US DoD et Gouvernement US Centre. Dans ces régions, l’espace de stockage maximal au niveau Premium est limité à 1 To.  Pour plus d’informations, voir les [limitations actuelles P11-P15](sql-database-single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb).  

    



1. Sélectionnez l’onglet **Paramètres supplémentaires**. 
1. Dans la section **Source de données**, sous **Utiliser des données existantes**, sélectionnez `Sample`. 

   ![Paramètres supplémentaires de la base de données SQL](media/sql-database-get-started-portal/create-sql-database-additional-settings.png)

   > [!IMPORTANT]
   > Veillez à sélectionner les données **Exemple (AdventureWorksLT)** pour pouvoir facilement suivre le présent guide ainsi que d’autres articles dédiés à Azure SQL Database qui utilisent ces données.

1. Laissez le reste des valeurs par défaut, puis sélectionnez **Vérifier + créer** en bas du formulaire. 
1. Passez en revue les paramètres finaux et sélectionnez **Créer**. 

8. Sur le formulaire **SQL Database**, sélectionnez **Créer** pour déployer et configurer le groupe de ressources, le serveur et la base de données.


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
- Pour créer une base de données unique à l’aide d’Azure CLI, consultez [Exemples d’interface de ligne de commande Azure](sql-database-cli-samples.md).
- Pour créer une base de données unique à l’aide d’Azure PowerShell, consultez [Exemples Azure PowerShell](sql-database-powershell-samples.md).
