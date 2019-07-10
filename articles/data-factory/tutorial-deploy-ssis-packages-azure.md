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
ms.date: 06/26/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: bd2c055d2f7f1e90918cb160cfdebfe88f7f8ea4
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67484784"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory"></a>Provisionner le runtime d’intégration Azure-SSIS dans Azure Data Factory

Ce tutoriel décrit les différentes étapes d’utilisation du portail Azure pour approvisionner un runtime d’intégration (IR) Azure-SSIS (SQL Server Integration Services) dans Azure Data Factory. Azure-SSIS prend en charge l’exécution de packages déployés dans le catalogue SSIS (SSISDB) hébergé par le serveur de base de données SQL/l’instance gérée (modèle de déploiement de projet) et de ceux déployés dans les systèmes de fichiers/les partages de fichiers/Azure Files (modèle de déploiement de package). Une fois que le runtime d’intégration Azure-SSIS est configuré, vous pouvez utiliser des outils familiers tels que SQL Server Data Tools (SSDT)/SQL Server Management Studio (SSMS) et des utilitaires à ligne de commande, comme `dtinstall` / `dtutil` / `dtexec`, pour déployer et exécuter vos packages dans Azure. Pour obtenir des informations conceptuelles sur les runtimes d’intégration (IR) Azure-SSIS, consultez [Vue d’ensemble du runtime d’intégration Azure-SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime).

Dans ce tutoriel, vous allez effectuer les étapes suivantes :

> [!div class="checklist"]
> * Créer une fabrique de données.
> * Approvisionner un runtime d’intégration Azure-SSIS.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Abonnement Azure**. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.
- **Serveur de base de données Azure SQL (facultatif)** . Si vous n’avez pas encore de serveur de base de données, créez-en un dans le portail Azure avant de commencer. ADF crée à son tour SSISDB sur ce serveur de base de données. Nous vous recommandons de créer le serveur de base de données dans la même région Azure que le runtime d’intégration. Cette configuration permet au runtime d’intégration d’écrire des journaux d’activité d’exécution dans SSISDB sans dépasser les régions Azure. 
    - En fonction du serveur de base de données sélectionné, SSISDB peut être créée à votre place en tant que base de données unique, faisant partie d’un pool élastique ou dans une instance gérée de type Managed Instance, et accessible sur un réseau public ou en rejoignant un réseau virtuel. Pour obtenir de l’aide sur le choix du type de serveur de base de données pour héberger SSISDB, consultez [Comparer les bases de données uniques/pools élastiques/Azure SQL Database et Managed Instance](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). Si vous utilisez Azure SQL Database avec des points de terminaison de service de réseau virtuel/Managed Instance pour héberger SSISDB, ou si vous avez besoin d’accéder à des données locales, vous devez joindre votre runtime d’intégration Azure-SSIS IR à un réseau virtuel. Consultez [Créer un runtime d’intégration Azure-SSIS IR dans un réseau virtuel](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
    - Vérifiez que le paramètre **Autoriser l’accès aux services Azure** est activé pour votre serveur de base de données. Ceci ne s’applique pas lors de l’utilisation du serveur Azure SQL Database avec des points de terminaison de service de réseau virtuel/Managed Instance dans un réseau virtuel pour héberger SSISDB. Pour en savoir plus, consultez [Sécuriser votre base de données Azure SQL](../sql-database/sql-database-security-tutorial.md#create-firewall-rules). Pour savoir comment activer ce paramètre à l’aide de PowerShell, consultez [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).
    - Ajoutez l’adresse IP de l’ordinateur client ou une plage d’adresses IP qui inclut l’adresse IP de l’ordinateur client à la liste d’adresses IP client dans les paramètres de pare-feu du serveur de base de données. Pour plus d’informations, consultez [Règles de pare-feu au niveau du serveur et de la base de données Azure SQL Database](../sql-database/sql-database-firewall-configure.md).
    - Vous pouvez vous connecter au serveur de base de données à l’aide de l’authentification SQL avec vos identifiants administrateur du serveur ou l’authentification Azure Active Directory (AAD) avec l’identité managée de votre Azure Data Factory. Pour cette dernière, vous devez ajouter l’identité managée de votre ADF dans un groupe AAD avec autorisations d’accès au serveur de base de données. Consultez [Créer un runtime d’intégration Azure-SSIS avec l’authentification AAD](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
    - Vérifiez que votre serveur de base de données n’a pas déjà un SSISDB. Le provisionnement d’Azure-SSIS IR ne prend pas en charge l’utilisation d’une base de données SSISDB existante.

> [!NOTE]
> - Pour obtenir la liste des régions Azure dans lesquelles ADF et Azure-SSIS IR sont actuellement disponibles, consultez [Disponibilité d’Azure Data Factory et de SSIS IR par région](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all). 

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
1. Pour **Version**, sélectionnez **V2 (préversion)** . 
1. Pour **Emplacement**, sélectionnez l’emplacement de la fabrique de données. Seuls les emplacements pris en charge pour la création de fabriques de données sont affichés dans la liste. 
1. Sélectionnez **Épingler au tableau de bord**. 
1. Sélectionnez **Créer**. 
1. Sur le tableau de bord, vous voyez la vignette suivante avec l’état **Déploiement de Data Factory** : 

   ![Vignette « Déploiement de Data Factory »](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)

1. Une fois la création terminée, la page **Data Factory** s’affiche. 

   ![Page d’accueil de la fabrique de données](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)

1. Sélectionnez **Créer et surveiller** pour ouvrir l’interface utilisateur de Data Factory dans un onglet séparé. 

## <a name="create-an-azure-ssis-integration-runtime"></a>Créer un runtime d’intégration Azure-SSIS

### <a name="from-the-data-factory-overview"></a>À partir de la vue d’ensemble de Data Factory

1. Dans la page de **prise en main**, cliquez sur la vignette **Configurer un runtime d’intégration SSIS**. 

   ![Vignette Configurer un runtime d’intégration SSIS](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

1. Consultez la section [Approvisionner un runtime d’intégration Azure-SSIS](#provision-an-azure-ssis-integration-runtime) pour connaître les autres étapes de configuration d’un runtime d’intégration Azure-SSIS. 

### <a name="from-the-authoring-ui"></a>À partir de l’interface utilisateur de création

1. Dans l’interface utilisateur d’Azure Data Factory, basculez vers l’onglet **Modifier**, sélectionnez **Connexions**, puis basculez vers l’onglet **Runtimes d’intégration** pour afficher les runtimes d’intégration existants dans votre Data Factory. 

   ![Sélections pour l’affichage des runtimes d’intégration existants](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. Sélectionnez **Nouveau** pour créer un runtime d’intégration Azure-SSIS. 

   ![Runtime d’intégration via le menu](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. Dans la **fenêtre de configuration de runtime d’intégration**, sélectionnez **Faire une migration lift-and-shift des packages SSIS existants à exécuter dans Azure**, puis cliquez sur **Suivant**. 

   ![Spécifier le type de runtime d’intégration](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Consultez la section [Approvisionner un runtime d’intégration Azure-SSIS](#provision-an-azure-ssis-integration-runtime) pour connaître les autres étapes de configuration d’un runtime d’intégration Azure-SSIS. 

## <a name="provision-an-azure-ssis-integration-runtime"></a>Approvisionner un runtime d’intégration Azure-SSIS

1. Sur la page **Paramètres généraux** de **Configuration du runtime d’intégration**, procédez comme suit : 

   ![Paramètres généraux :](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   a. Dans **Nom**, saisissez le nom de votre runtime d’intégration. 

   b. Dans **Description**, saisissez la description de votre runtime d’intégration. 

   c. Pour **Emplacement**, sélectionnez l’emplacement de votre runtime d’intégration. Seuls les emplacements pris en charge sont affichés. Nous vous conseillons de sélectionner le même emplacement que votre serveur de base de données pour héberger SSISDB. 

   d. Pour **Taille du nœud**, sélectionnez la taille du nœud du cluster de votre runtime d’intégration. Seules les tailles de nœuds prises en charge sont affichées. Sélectionnez une taille de nœud importante si vous voulez exécuter de nombreux packages nécessitant beaucoup de calcul/mémoire. 

   e. Pour **Nombre de nœud**, sélectionnez le nombre de nœuds du cluster de votre runtime d’intégration. Seuls les nombres de nœuds pris en charge sont affichés. Sélectionnez un grand cluster avec plusieurs nœuds, si vous voulez exécuter de nombreux packages en parallèle. 

   f. Pour **Édition/Licence**, sélectionnez l’édition/licence de SQL Server pour votre runtime d’intégration : Standard ou Entreprise. Sélectionnez Entreprise, si vous voulez utiliser les fonctionnalités avancées/premium de votre runtime d’intégration. 

   g. Pour **Économiser de l’argent**, sélectionnez l’option Azure Hybrid Benefit (AHB) pour votre runtime d’intégration : Oui ou Non. Sélectionnez Oui si vous souhaitez mettre votre propre licence SQL Server avec Software Assurance pour faire des économies grâce à une utilisation hybride. 

   h. Cliquez sur **Suivant**. 

1. Sur la page **Paramètres SQL**, procédez comme suit : 

   ![Paramètres SQL](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   a. Sur la case **Créer catalogue SSIS...** , sélectionnez le modèle de déploiement pour que les packages s’exécutent sur votre runtime d’intégration Azure-SSIS : Le modèle de déploiement de projet dans lequel les packages sont déployés dans SSISDB hébergé par votre serveur de base de données ou modèle de déploiement de package dans lequel les packages sont déployés dans vos systèmes de fichiers/partages de fichiers/Azure Files. Si vous le cochez, vous devez apporter votre propre serveur de base de données pour héberger le SSISDB qui nous allons créer et gérer à votre place.
   
   b. Pour **Abonnement**, sélectionnez l’abonnement Azure comprenant votre serveur de base de données pour héberger la base de données SSISDB. 

   c. Pour **Emplacement**, sélectionnez l’emplacement de votre serveur de base de données pour héberger la base de données SSISDB. Nous vous recommandons de sélectionner le même emplacement que celui de runtime d’intégration. 

   d. Pour **Point de terminaison du serveur de base de données du catalogue**, sélectionnez le point de terminaison de votre serveur de base de données pour héberger SSISDB. En fonction du serveur de base de données sélectionné, SSISDB peut être créée à votre place en tant que base de données unique, faisant partie d’un pool élastique ou dans une instance gérée de type Managed Instance, et accessible sur un réseau public ou en rejoignant un réseau virtuel. Pour obtenir de l’aide sur le choix du type de serveur de base de données pour héberger SSISDB, consultez [Comparer les bases de données uniques/pools élastiques/Azure SQL Database et Managed Instance](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). Si vous sélectionnez Azure SQL Database avec des points de terminaison de service de réseau virtuel/une instance managée (en préversion) pour héberger SSISDB ou si vous avez besoin d’accéder à des données locales, vous devez joindre votre runtime d'intégration Azure-SSIS à un réseau virtuel. Consultez [Créer un runtime d'intégration Azure-SSIS dans un réseau virtuel](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 

   e. Dans la case à cocher **Utiliser l’authentification AAD**, sélectionnez la méthode d’authentification pour l’hébergement de SSISDB par votre serveur de base de données : L’authentification SQL ou AAD avec l’identité gérée pour votre ADF. Si vous la vérifiez, vous devez ajouter l’identité managée de votre ADF dans un groupe AAD avec autorisations d’accès à votre serveur de base de données. Consultez [Créer un runtime d’intégration Azure-SSIS avec l’authentification AAD](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 

   f. Pour **Nom d’utilisateur administrateur**, entrez le nom d’utilisateur d’authentification SQL pour votre serveur de base de données accueillant la base de données SSISDB. 

   g. Pour **Mot de passe administrateur**, entrez le mot de passe d’authentification SQL pour votre serveur de base de données accueillant la base de données SSISDB. 

   h. Pour **Niveau de service de base de données de catalogue**, sélectionnez le niveau de service pour l’hébergement de SSISDB par votre serveur de base de données : niveau De base/Standard/Premium ou nom du pool élastique. 

   i. Cliquez sur **Tester la connexion** et en cas de réussite, cliquez sur **Suivant**. 

1. Sur la page **Paramètres avancés**, procédez comme suit : 

   ![Paramètres avancés](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   a. Pour **Exécutions parallèles maximales par nœud**, sélectionnez le nombre maximal de packages exécutables simultanément par nœud au sein de votre cluster de runtime d’intégration. Seuls les numéros des packages pris en charge sont affichés. Sélectionnez un petit nombre, si vous souhaitez utiliser plusieurs cœurs pour exécuter un package volumineux/lourd intensif sur le calcul/la mémoire. Sélectionnez un nombre plus élevé, si vous souhaitez exécuter un ou plusieurs packages petits/légers dans un seul cœur. 

   b. Pour une **URI SAS de conteneur d’installation personnalisée**, vous pouvez également entrer l’URI (Uniform Resource Identifier) de la signature d’accès partagé (SAS) de votre conteneur Azure Storage Blob dans lequel votre script d’installation et les fichiers associés sont stockés, consultez [Installation personnalisée du runtime d’intégration SSIS Azure](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup). 

   c. Dans la case à cocher **Sélectionner un réseau virtuel...** , indiquez si vous voulez joindre votre runtime d’intégration à un réseau virtuel. Cochez-la si vous utilisez un serveur Azure SQL Database avec des points de terminaison de service de réseau virtuel/Managed Instance dans un réseau virtuel pour héberger SSISDB, ou si vous avez besoin d’accéder à des données locales. Consultez [Créer un runtime d’intégration Azure-SSIS dans un réseau virtuel](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 

1. Cliquez sur **Terminer** pour démarrer la création du runtime d’intégration. 

   > [!NOTE]
   > En dehors des réglages personnalisés, ce processus devrait se terminer dans les 5 minutes.
   >
   > Si vous utilisez SSISDB, le service ADF se connectera à votre serveur de base de données pour préparer SSISDB. 
   > 
   > Quand vous provisionnez Azure-SSIS IR, les composants Access Redistributable et Azure Feature Pack pour SSIS sont également installés. Ces composants fournissent la connectivité aux fichiers Excel/Access et à diverses sources de données Azure, en plus des sources de données déjà prises en charge par les composants intégrés. Vous pouvez également installer des composants supplémentaires ; consultez [Configuration personnalisée pour Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md) pour en savoir plus.

1. Dans l’onglet **Connexions**, basculez vers **Runtimes d’intégration** si nécessaire. Sélectionnez **Actualiser** pour actualiser l’état. 

   ![État de la création avec bouton Actualiser](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

1. Utilisez les liens sous la colonne **Actions** pour arrêter/démarrer, modifier ou supprimer le runtime d’intégration. Le dernier lien permet d’afficher le code JSON pour le runtime d’intégration. Les boutons Modifier et Supprimer sont activés uniquement lorsque le runtime d’intégration est arrêté. 

   ![Liens de la colonne Actions](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png) 

## <a name="deploy-ssis-packages"></a>Déployer des packages SSIS

Si vous utilisez SSISDB, vous pouvez déployer vos packages dedans et les exécuter sur le runtime d’intégration Azure-SSIS à l’aide des outils SSDT/SSMS qui se connectent à votre serveur de base de données par le biais de son point de terminaison. Pour un serveur de base de données Azure SQL/une instance gérée avec un point de terminaison public, le format de point de terminaison est `<server name>.database.windows.net` / `<server name>.public.<dns prefix>.database.windows.net,3342`, respectivement. Si vous n’utilisez pas SSISDB, vous pouvez déployer vos packages dans les systèmes de fichiers/partages de fichiers/Azure Files et les exécuter sur le runtime d’intégration Azure-SSIS avec les utilitaires de ligne de commande `dtinstall`/`dtutil`/`dtexec`. Pour plus d’informations, consultez [Déployer des packages SSIS](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). Dans les deux cas, vous pouvez également exécuter vos packages déployés sur le runtime d’intégration Azure-SSIS à le l’aide Exécuter le package SSIS dans les pipelines ADF ; consultez [Appeler une exécution de package SIS comme activité ADF de première classe](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) pour en savoir plus. 

Consultez aussi les articles suivants de la documentation relative à SSIS : 

- [Déployer, exécuter et surveiller des packages SSIS dans Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial) 
- [Se connecter à SSISDB dans Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database) 
- [Planifier les exécutions de packages sur Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages) 
- [Se connecter aux sources de données locales avec l’authentification Windows](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à : 

> [!div class="checklist"]
> * Créer une fabrique de données.
> * Approvisionner un runtime d’intégration Azure-SSIS.
> * Déployer des packages SSIS

Pour en savoir plus sur la personnalisation de votre runtime d’intégration Azure-SSIS, passez à l’article suivant : 

> [!div class="nextstepaction"]
> [Personnaliser le runtime d’intégration Azure-SSIS](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)