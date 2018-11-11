---
title: Provisionner le runtime d’intégration Azure-SSIS | Microsoft Docs
description: Découvrez comment provisionner le runtime d’intégration Azure-SSIS dans Azure Data Factory afin de pouvoir déployer et exécuter des packages SSIS dans Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: tutorial
ms.date: 10/28/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: ed14dc45af3f47032e54c946486c4de70aeae11a
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2018
ms.locfileid: "50214951"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory"></a>Provisionner le runtime d’intégration Azure-SSIS dans Azure Data Factory
Ce tutoriel décrit les différentes étapes d’utilisation du portail Azure pour approvisionner un runtime d’intégration (IR) Azure-SSIS dans Azure Data Factory. Vous pouvez ensuite utiliser SQL Server Data Tools (SSDT) ou SQL Server Management Studio (SSMS) pour déployer des et exécuter des packages SQL Server Integration Services (SSIS) sur ce runtime dans Azure. Pour obtenir des informations conceptuelles sur les runtimes d’intégration (IR) Azure-SSIS, consultez [Vue d’ensemble du runtime d’intégration Azure-SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime).

Dans ce tutoriel, vous allez effectuer les étapes suivantes :

> [!div class="checklist"]
> * Créer une fabrique de données.
> * Approvisionner un runtime d’intégration Azure-SSIS.

## <a name="prerequisites"></a>Prérequis
- **Abonnement Azure**. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer. 
- **Serveur de base de données SQL Azure**. Si vous n’avez pas encore de serveur de base de données, créez-en un dans le portail Azure avant de commencer. Azure Data Factory crée le catalogue SSIS (base de données SSISDB) sur ce serveur de base de données. Nous vous recommandons de créer le serveur de base de données dans la même région Azure que le runtime d’intégration. Cette configuration permet au runtime d’intégration d’écrire des journaux d’exécution dans la base de données SSISDB sans dépasser les régions Azure. 
- En fonction du serveur de base de données sélectionné, SSISDB peut être créée à votre place en tant que base de données unique, faisant partie d’un pool élastique ou dans une instance gérée de type Managed Instance, et accessible sur un réseau public ou en rejoignant un réseau virtuel. Si vous utilisez Azure SQL Database avec des points de terminaison de service de réseau virtuel/Managed Instance pour héberger SSISDB, ou si vous avez besoin d’accéder à des données locales, vous devez joindre votre runtime d’intégration Azure-SSIS IR à un réseau virtuel. Consultez [Créer un runtime d’intégration Azure-SSIS IR dans un réseau virtuel](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 
- Vérifiez que le paramètre **Autoriser l’accès aux services Azure** est activé pour votre serveur de base de données. Ceci ne s’applique pas lors de l’utilisation d’Azure SQL Database avec des points de terminaison de service de réseau virtuel/Managed Instance pour héberger SSISDB. Pour en savoir plus, consultez [Sécuriser votre base de données SQL Azure](../sql-database/sql-database-security-tutorial.md#create-a-server-level-firewall-rule-in-the-azure-portal). Pour activer ce paramètre à l’aide de PowerShell, consultez [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule?view=azurermps-4.4.1). 
- Ajoutez l’adresse IP de l’ordinateur client ou une plage d’adresses IP qui inclut l’adresse IP de l’ordinateur client à la liste d’adresses IP client dans les paramètres de pare-feu du serveur de base de données. Pour plus d’informations, consultez [Règles de pare-feu au niveau du serveur et de la base de données d’Azure SQL Database](../sql-database/sql-database-firewall-configure.md). 
- Vous pouvez vous connecter au serveur de base de données à l’aide de l’authentification SQL avec vos identifiants administrateur du serveur ou l’authentification Azure Active Directory (AAD) avec l’identité managée de votre Azure Data Factory (ADF).  Pour cette dernière, vous devez ajouter l’identité managée de votre ADF dans un groupe AAD avec autorisations d’accès au serveur de base de données. Consultez [Créer un runtime d’intégration Azure-SSIS avec l’authentification AAD](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 
- Vérifiez que votre serveur Azure SQL Database ne dispose pas d’un catalogue SSIS (base de données SSISDB). L’approvisionnement d’un runtime d’intégration SSIS Azure ne prend pas en charge l’utilisation d’un catalogue SSIS existant. 

> [!NOTE]
> - Pour obtenir la liste des régions Azure dans lesquelles Data Factory et Azure-SSIS Integration Runtime sont actuellement disponibles, consultez [Disponibilité d’Azure Data Factory et de SSIS IR par région](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all). 

## <a name="create-a-data-factory"></a>Créer une fabrique de données

1. Lancez le navigateur web **Microsoft Edge** ou **Google Chrome**. L’interface utilisateur de Data Factory n’est actuellement prise en charge que par les navigateurs web Microsoft Edge et Google Chrome. 
1. Connectez-vous au [Portail Azure](https://portal.azure.com/). 
1. Sélectionnez **Nouveau** dans le menu de gauche, sélectionnez **Données + Analytique**, puis **Data Factory**. 

   ![Sélection Data Factory dans le volet « Nouveau »](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)

1. Sur la page **Nouvelle fabrique de données**, saisissez **MyAzureSsisDataFactory** sous **Nom**. 

   ![Page « Nouvelle fabrique de données »](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)

   Le nom de la fabrique de données Azure doit être un nom *global unique*. Si l’erreur suivante s’affiche, changez le nom de la fabrique de données (par exemple, **&lt;votrenom&gt;MyAzureSsisDataFactory**), puis tentez de la recréer. Consultez l’article [Data Factory - Règles d’affectation des noms](naming-rules.md) pour en savoir plus sur les règles d’affectation des noms d’artefacts Data Factory. 

   `Data factory name “MyAzureSsisDataFactory” is not available`

1. Pour **Abonnement**, sélectionnez l’abonnement Azure dans lequel vous voulez créer la fabrique de données. 
1. Pour **Groupe de ressources**, effectuez l’une des opérations suivantes : 

   - Sélectionnez **Utiliser l’existant**, puis sélectionnez un groupe de ressources existant dans la liste. 
   - Sélectionnez **Créer**, puis entrez le nom d’un groupe de ressources. 

   Pour plus d’informations sur les groupes de ressources, consultez [Utilisation des groupes de ressources pour gérer vos ressources Azure](../azure-resource-manager/resource-group-overview.md). 
1. Pour **Version**, sélectionnez **V2 (préversion)**. 
1. Pour **Emplacement**, sélectionnez l’emplacement de la fabrique de données. Seuls les emplacements pris en charge pour la création de fabriques de données sont affichés dans la liste. 
1. Sélectionnez **Épingler au tableau de bord**. 
1. Sélectionnez **Créer**. 
1. Sur le tableau de bord, vous voyez la vignette suivante avec l’état **Déploiement de Data Factory** : 

   ![Vignette « Déploiement de Data Factory »](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)

1. Une fois la création terminée, la page **Data Factory** s’affiche. 

   ![Page d’accueil de la fabrique de données](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)

1. Sélectionnez **Créer et surveiller** pour ouvrir l’interface utilisateur de Data Factory dans un onglet séparé. 

## <a name="provision-an-azure-ssis-integration-runtime"></a>Approvisionner un runtime d’intégration Azure-SSIS

1. Dans la page de **prise en main**, cliquez sur la vignette **Configurer un runtime d’intégration SSIS**. 

   ![Vignette Configurer un runtime d’intégration SSIS](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

1. Sur la page **Paramètres généraux** de **Configuration du runtime d’intégration**, procédez comme suit : 

   ![Paramètres généraux :](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   a. Dans **Nom**, saisissez le nom de votre runtime d’intégration. 

   b. Dans **Description**, saisissez la description de votre runtime d’intégration. 

   c. Pour **Emplacement**, sélectionnez l’emplacement de votre runtime d’intégration. Seuls les emplacements pris en charge sont affichés. Nous vous conseillons de sélectionner le même emplacement que votre serveur de base de données pour héberger SSISDB. 

   d. Pour **Taille du nœud**, sélectionnez la taille du nœud du cluster de votre runtime d’intégration. Seules les tailles de nœuds prises en charge sont affichées. Sélectionnez une taille de nœud importante si vous voulez exécuter de nombreux packages nécessitant beaucoup de calcul/mémoire. 

   e. Pour **Nombre de nœud**, sélectionnez le nombre de nœuds du cluster de votre runtime d’intégration. Seuls les nombres de nœuds pris en charge sont affichés. Sélectionnez un grand cluster avec plusieurs nœuds, si vous voulez exécuter de nombreux packages en parallèle. 

   f. Pour **Édition/Licence**, sélectionnez l’édition/licence de SQL Server pour votre runtime d’intégration : Standard ou Entreprise. Sélectionnez Entreprise, si vous voulez utiliser les fonctionnalités avancées/premium de votre runtime d’intégration. 

   g. Pour **Économiser de l’argent**, sélectionnez l’option Azure Hybrid Benefit (AHB) pour votre runtime d’intégration : Oui ou Non. Sélectionnez Oui si vous souhaitez mettre votre propre licence SQL Server avec Software Assurance pour faire des économies grâce à une utilisation hybride. 

   h. Cliquez sur **Suivant**. 

1. Sur la page **Paramètres SQL**, procédez comme suit : 

   ![Paramètres SQL](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   a. Pour **Abonnement**, sélectionnez l’abonnement Azure comprenant votre serveur de base de données pour héberger la base de données SSISDB. 

   b. Pour **Emplacement**, sélectionnez l’emplacement de votre serveur de base de données pour héberger la base de données SSISDB. Nous vous recommandons de sélectionner le même emplacement que celui de runtime d’intégration. 

   c. Pour **Point de terminaison du serveur de base de données du catalogue**, sélectionnez le point de terminaison de votre serveur de base de données pour héberger SSISDB. Basée sur le serveur de base de données sélectionné, SSISDB peut être créée pour vous en tant que base de données autonome, partie d’un pool élastique ou dans une instance managée, et accessible dans un réseau public ou en rejoignant un réseau virtuel. Pour obtenir des conseils sur le choix du type de serveur de base de données pour héberger SSISDB, consultez [Comparer un serveur logique SQL Database et Managed Instance](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-logical-server-and-sql-database-managed-instance). Si vous sélectionnez Azure SQL Database avec des points de terminaison de réseau virtuel/Managed Instance pour héberger SSISDB, ou si vous avez besoin d’accéder à des données locales, vous devez joindre votre runtime d’intégration Azure-SSIS à un réseau virtuel. Consultez [Créer un runtime d’intégration Azure-SSIS dans un réseau virtuel](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 

   d. Au niveau de la case à cocher **Utiliser l’authentification AAD...**, sélectionnez la méthode d’authentification pour que votre serveur de base de données accueille la base de données SSISDB : SQL ou Azure Active Directory (AAD) avec l’identité managée de votre Azure Data Factory (ADF). Si vous la vérifiez, vous devez ajouter l’identité managée de votre ADF dans un groupe AAD avec autorisations d’accès au serveur de base de données. Consultez [Créer un runtime d’intégration Azure-SSIS avec l’authentification AAD](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 

   e. Pour **Nom d’utilisateur administrateur**, entrez le nom d’utilisateur d’authentification SQL pour votre serveur de base de données accueillant la base de données SSISDB. 

   f. Pour **Mot de passe administrateur**, entrez le mot de passe d’authentification SQL pour votre serveur de base de données accueillant la base de données SSISDB. 

   g. Pour **Niveau de service de la base de données du catalogue**, sélectionnez le niveau de service pour votre serveur de base de données hébergeant la base de données SSISDB : Niveau De base/Standard/Premium ou nom du pool élastique. 

   h. Cliquez sur **Tester la connexion** et en cas de réussite, cliquez sur **Suivant**. 

1. Sur la page **Paramètres avancés**, procédez comme suit : 

   ![Paramètres avancés](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   a. Pour **Exécutions parallèles maximales par nœud**, sélectionnez le nombre maximal de packages exécutables simultanément par nœud au sein de votre cluster de runtime d’intégration. Seuls les numéros des packages pris en charge sont affichés. Sélectionnez un petit nombre, si vous souhaitez utiliser plusieurs cœurs pour exécuter un package volumineux/lourd intensif sur le calcul/la mémoire. Sélectionnez un nombre plus élevé, si vous souhaitez exécuter un ou plusieurs packages petits/légers dans un seul cœur. 

   b. Pour une **URI SAS de conteneur d’installation personnalisée**, vous pouvez également entrer l’URI (Uniform Resource Identifier) de la signature d’accès partagé (SAS) de votre conteneur Azure Storage Blob dans lequel votre script d’installation et les fichiers associés sont stockés, consultez [Installation personnalisée du runtime d’intégration SSIS Azure](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup). 

   c. Dans la case à cocher **Sélectionner un réseau virtuel...** , indiquez si vous voulez joindre votre runtime d’intégration à un réseau virtuel. Cochez-la si vous utilisez Azure SQL Database avec des points de terminaison de réseau virtuel/Managed Instance pour héberger SSISDB, ou si vous avez besoin d’accéder à des données locales. Consultez [Créer un runtime d’intégration Azure-SSIS dans un réseau virtuel](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 

1. Cliquez sur **Terminer** pour démarrer la création du runtime d’intégration. 

   > [!IMPORTANT]
   > Ce processus prend environ 20 à 30 minutes.
   >
   > Le service Data Factory se connecte à votre serveur Azure SQL Database pour préparer le catalogue SSIS (base de données SSISDB). 
   > 
   > Si vous approvisionnez l’instance d’un runtime d’intégration Azure-SSIS, les composants Azure Feature Pack pour SSIS et Access Redistributable sont également installés. Ces composants fournissent la connectivité aux fichiers Excel et Access et à diverses sources de données Azure, en plus des sources de données prises en charge par les composants intégrés. Vous pouvez également installer des composants supplémentaires. Pour plus d’informations, consultez [Custom setup for the Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md) (Configuration personnalisée du runtime d’intégration Azure-SSIS). 

1. Dans l’onglet **Connexions**, basculez vers **Runtimes d’intégration** si nécessaire. Sélectionnez **Actualiser** pour actualiser l’état. 

   ![État de la création avec bouton Actualiser](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

1. Utilisez les liens sous la colonne **Actions** pour arrêter/démarrer, modifier ou supprimer le runtime d’intégration. Le dernier lien permet d’afficher le code JSON pour le runtime d’intégration. Les boutons Modifier et Supprimer sont activés uniquement lorsque le runtime d’intégration est arrêté. 

   ![Liens de la colonne Actions](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png) 

## <a name="create-an-azure-ssis-integration-runtime"></a>Créer un runtime d’intégration Azure-SSIS

1. Dans l’interface utilisateur d’Azure Data Factory, basculez vers l’onglet **Modifier**, sélectionnez **Connexions**, puis basculez vers l’onglet **Runtimes d’intégration** pour afficher les runtimes d’intégration existants dans votre Data Factory. 

   ![Sélections pour l’affichage des runtimes d’intégration existants](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. Sélectionnez **Nouveau** pour créer un runtime d’intégration Azure-SSIS. 

   ![Runtime d’intégration via le menu](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. Dans la **fenêtre de configuration de runtime d’intégration**, sélectionnez **Faire une migration lift-and-shift des packages SSIS existants à exécuter dans Azure**, puis cliquez sur **Suivant**. 

   ![Spécifier le type de runtime d’intégration](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Consultez la section [Approvisionner un runtime d’intégration Azure-SSIS](#provision-an-azure-ssis-integration-runtime) pour connaître les autres étapes de configuration d’un runtime d’intégration Azure-SSIS. 

## <a name="deploy-ssis-packages"></a>Déployer des packages SSIS
Utilisez maintenant SQL Server Data Tools (SSDT) ou SQL Server Management Studio (SSMS) pour déployer vos packages SSIS dans Azure. Connectez-vous à votre serveur Azure SQL Database qui héberge le catalogue SSIS (base de données SSISDB). Le nom du serveur Azure SQL Database est au format `<servername>.database.windows.net`. 

Consultez les articles suivants de la documentation relative à SSIS : 

- [Déployer, exécuter et surveiller un package SSIS sur Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial) 
- [Se connecter au catalogue SSIS sur Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database) 
- [Planifier l’exécution des packages sur Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages) 
- [Se connecter aux sources de données locales avec l’authentification Windows](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>Étapes suivantes
Dans ce tutoriel, vous avez appris à : 

> [!div class="checklist"]
> * Créer une fabrique de données.
> * Approvisionner un runtime d’intégration Azure-SSIS.

Pour en savoir plus sur la personnalisation de votre runtime d’intégration Azure-SSIS, passez à l’article suivant : 

> [!div class="nextstepaction"]
> [Personnaliser le runtime d’intégration Azure-SSIS](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)
