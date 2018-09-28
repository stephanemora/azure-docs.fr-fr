---
title: Créer un runtime d’intégration Azure-SSIS dans Azure Data Factory | Microsoft Docs
description: Découvrez comment créer un runtime d’intégration Azure-SSIS dans Azure Data Factory afin de pouvoir déployer et exécuter des packages SSIS dans Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/23/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: a7ba62a28b65d1cd7152c793bc303e747057cdf8
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46991468"
---
# <a name="create-the-azure-ssis-integration-runtime-in-azure-data-factory"></a>Créer le runtime d’intégration Azure-SSIS dans Azure Data Factory
Cet article explique pas à pas comment configurer un runtime d’intégration Azure-SSIS dans Azure Data Factory. Vous pouvez ensuite utiliser SQL Server Data Tools (SSDT) ou SQL Server Management Studio (SSMS) pour déployer des et exécuter des packages SQL Server Integration Services (SSIS) sur ce runtime dans Azure. 

Le tutoriel : [Tutoriel : déployer des packages SQL Server Integration Services (SSIS) pour Azure](tutorial-create-azure-ssis-runtime-portal.md) vous montre comment créer un runtime d’intégration (IR) à l’aide d’Azure SQL Database pour héberger le catalogue SSIS. Cet article s’appuie sur le didacticiel et vous montre comment effectuer les opérations suivantes : 

- Utilisez éventuellement Azure SQL Database avec des points de terminaison de service de réseau virtuel/Managed Instance comme serveur de base de données pour héberger votre catalogue SSIS (base de données SSISDB). Pour obtenir des conseils sur le choix du type de serveur de base de données pour héberger SSISDB, consultez [Comparer un serveur logique SQL Database et SQL Database Managed Instance](create-azure-ssis-integration-runtime.md#compare-sql-database-logical-server-and-sql-database-managed-instance). Comme prérequis, vous devez joindre votre runtime d’intégration Azure-SSIS à un réseau virtuel et configurer les paramètres et les autorisations de réseau virtuel si nécessaire. Consultez [Joindre le runtime d’intégration Azure-SSIS à un réseau virtuel](https://docs.microsoft.com/en-us/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). 

- Utilisez éventuellement l’authentification Azure Active Directory (AAD) avec votre Managed Service Identity (MSI) de Azure Data Factory pour que le runtime d'intégration SSIS Azure se connecte au serveur de base de données. Comme condition préalable, vous devez ajouter votre MSI Data Factory dans un groupe AAD avec autorisations d’accès au serveur de base de données. voir [Autoriser l’authentification AAD pour un runtime d'intégration SSIS Azure](https://docs.microsoft.com/en-us/azure/data-factory/enable-aad-authentication-azure-ssis-ir). 

## <a name="overview"></a>Vue d’ensemble
Cet article présente différentes façons de provisionner un runtime d’intégration Azure-SSIS : 

- [Portail Azure](#azure-portal) 
- [Azure PowerShell](#azure-powershell) 
- [Modèle Azure Resource Manager](#azure-resource-manager-template) 

Quand vous créez un runtime d’intégration Azure-SSIS, Data Factory se connecte à votre base de données SQL Azure pour préparer la base de données du catalogue SSIS (SSISDB). Le script configure également les autorisations et les paramètres pour votre réseau virtuel, s’il est spécifié, et relie la nouvelle instance du runtime d’intégration Azure-SSIS au réseau virtuel. 

Si vous approvisionnez une instance d’un runtime d’intégration Azure-SSIS, les composants Azure Feature Pack pour SSIS et Access Redistributable sont également installés. Ces composants fournissent la connectivité aux fichiers Excel et Access et à diverses sources de données Azure, en plus des sources de données prises en charge par les composants intégrés. Vous pouvez également installer des composants supplémentaires. Pour plus d’informations, consultez [Custom setup for the Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md) (Configuration personnalisée du runtime d’intégration Azure-SSIS). 

## <a name="prerequisites"></a>Prérequis 
- **Abonnement Azure**. Si vous ne disposez d’aucun abonnement, vous pouvez créer un compte d’[essai gratuit](http://azure.microsoft.com/pricing/free-trial/). 

- **Serveur logique Azure SQL Database ou Managed Instance**. Si vous n’avez pas encore de serveur de base de données, créez-en un dans le portail Azure avant de commencer. Ce serveur héberge la base de données du catalogue SSIS (SSISDB). Nous vous recommandons de créer le serveur de base de données dans la même région Azure que le runtime d’intégration. Cette configuration permet au runtime d’intégration d’écrire des journaux d’exécution dans SSISDB sans dépasser les régions Azure. En fonction du serveur de base de données sélectionné, SSISDB peut être créé pour vous en tant que base de données indépendante, partie d’un pool élastique ou dans Managed Instance et accessible sur un réseau public ou en rejoignant un réseau virtuel. Pour obtenir la liste des niveaux tarifaires pris en charge pour Azure SQL Database, consultez [Limites de ressources pour SQL Database](../sql-database/sql-database-resource-limits.md). 

    Vérifiez que votre serveur logique Azure SQL Database ou Managed Instance n’a pas déjà un catalogue SSIS (base de données SSIDB). L’approvisionnement du runtime d’intégration SSIS Azure ne prend pas en charge l’utilisation d’un catalogue SSIS existant. 

- **Réseau virtuel Azure Resource Manager ou classique (facultatif)**. Vous devez disposer d’un réseau virtuel Azure si au moins une des conditions suivantes est vraie : 
    - Vous hébergez la base de données du catalogue SSIS dans Azure SQL Database avec des points de terminaison de service de réseau virtuel/Managed Instance à l’intérieur d’un réseau virtuel. 
    - Vous souhaitez vous connecter à des magasins de données sur site à partir de packages SSIS en cours d’exécution sur un runtime d’intégration Azure-SSIS. 

- **Azure PowerShell**. Suivez les instructions de [Comment installer et configurer Azure PowerShell](/powershell/azure/install-azurerm-ps), si vous utilisez PowerShell pour exécuter un script pour configurer un runtime d’intégration SSIS Azure qui exécute des packages SSIS dans le cloud. 

### <a name="region-support"></a>Prise en charge de la région
Pour obtenir la liste des régions Azure dans lesquelles Data Factory est actuellement disponible, sélectionnez les régions qui vous intéressent sur la page suivante, puis développez **Analytique** pour localiser **Data Factory** : [Disponibilité des produits par région](https://azure.microsoft.com/global-infrastructure/services/).

Pour obtenir la liste des régions Azure dans lesquelles le runtime d’intégration Azure-SSIS est actuellement disponible, sélectionnez les régions qui vous intéressent dans la page suivante, puis développez **Analytique** pour localiser **Runtime d’intégration Azure-SSIS** : [Disponibilité des produits par région](https://azure.microsoft.com/global-infrastructure/services/).### Comparer SQL Database et Managed Instance

### <a name="compare-sql-database-logical-server-and-sql-database-managed-instance"></a>Comparer un serveur logique SQL Database et SQL Database Managed Instance

Le tableau suivant compare certaines fonctionnalités d’un serveur logique SQL Database et de SQL Database Managed Instance pour ce qui est du runtime d’intégration Azure-SSIR :

| Fonctionnalité | Serveur logique SQL Database| SQL Database Managed Instance |
|---------|--------------|------------------|
| **Planification** | SQL Server Agent n’est pas disponible.<br/><br/>Consultez [Planifier un package dans le cadre d’un pipeline Azure Data Factory](/sql/integration-services/lift-shift/ssis-azure-schedule-packages#activity).| SQL Server Agent est disponible. |
| **Authentification** | Vous pouvez créer une base de données avec un compte d’utilisateur de base de données autonome qui représente tout utilisateur Azure Active Directory dans le rôle **dbmanager**.<br/><br/>Consultez [Activer Azure AD sur Azure SQL Database](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database). | Vous ne pouvez pas créer une base de données avec un compte d’utilisateur de base de données autonome qui représente tout utilisateur Azure Active Directory autre qu’un administrateur Azure AD. <br/><br/>Consultez [Activer Azure AD sur Azure SQL Database Managed Instance](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database-managed-instance). |
| **Niveau de service** | Quand vous créez le runtime d’intégration Azure-SSIS sur SQL Database, vous pouvez sélectionner le niveau de service pour SSISDB. Il existe plusieurs niveaux de services. | Quand vous créez le runtime d’intégration Azure-SSIS sur Managed Instance, vous ne pouvez pas sélectionner le niveau de service pour SSISDB. Toutes les bases de données sur la même instance gérée partagent la même ressource allouée à cette instance. |
| **Réseau virtuel** | Prend en charge Azure Resource Manager et les réseaux virtuels classiques. | Prend uniquement en charge le réseau virtuel Azure Resource Manager. Le réseau virtuel est obligatoire.<br/><br/>Si vous joignez votre runtime d’intégration Azure-SSIS au même réseau virtuel que Managed Instance, vérifiez que le runtime d’intégration Azure-SSIS est sur un sous-réseau différent de celui de Managed Instance. Si vous joignez le runtime d’intégration Azure-SSIS à un réseau virtuel différent de celui de Managed Instance, nous recommandons l’appairage de réseau virtuel (qui est limité à la même région) ou une connexion de réseau virtuel à réseau virtuel. Consultez [Connecter votre application à Azure SQL Database Managed Instance](../sql-database/sql-database-managed-instance-connect-app.md). |
| **Transactions distribuées** | Les transactions MSDTC (Microsoft Distributed Transaction Coordinator) ne sont pas prises en charge. Si vos packages utilisent MSDTC pour coordonner les transactions distribuées, vous pourrez peut-être implémenter une solution temporaire à l’aide de transactions élastiques pour SQL Database. À ce stade, SSIS n’intègre pas la prise en charge de transactions élastiques. Pour utiliser Transactions élastiques dans votre package SSIS, vous devez écrire un code ADO.NET personnalisé dans une tâche Script. Cette tâche de script doit inclure le début et la fin de la transaction, et toutes les actions qui doivent se produire au sein de la transaction.<br/><br/>Pour plus d’informations sur le codage des transactions élastiques, consultez [Transactions élastiques avec Azure SQL Database](https://azure.microsoft.com/en-us/blog/elastic-database-transactions-with-azure-sql-database/). Pour en savoir plus sur les transactions élastiques en général, consultez [Transactions distribuées entre bases de données cloud](../sql-database/sql-database-elastic-transactions-overview.md). | Non pris en charge. |
| | | |

## <a name="azure-portal"></a>Portail Azure
Dans cette section, vous utilisez le portail Azure, plus précisément l’interface utilisateur de Data Factory, pour créer un runtime d’intégration Azure-SSIS. 

### <a name="create-a-data-factory"></a>Créer une fabrique de données 
1. Lancez le navigateur web **Microsoft Edge** ou **Google Chrome**. L’interface utilisateur de Data Factory n’est actuellement prise en charge que par les navigateurs web Microsoft Edge et Google Chrome. 
1. Connectez-vous au [portail Azure](https://portal.azure.com/). 
1. Cliquez sur **Nouveau** dans le menu de gauche, puis sur **Données + analyse** et sur **Data Factory**. 

   ![Nouveau -> DataFactory](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)

1. Sur la page **Nouvelle fabrique de données**, saisissez **ADFTutorialDataFactory** comme **nom**. 

   ![Page Nouvelle fabrique de données](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)

   Le nom de la fabrique de données Azure doit être un nom **global unique**. Si l’erreur suivante s’affiche, changez le nom de la fabrique de données (par exemple, votrenomMyAzureSsisDataFactory), puis tentez de la recréer. Consultez l’article [Data Factory - Règles d’affectation des noms](naming-rules.md) pour savoir comment nommer les artefacts Data Factory. 

   `Data factory name “MyAzureSsisDataFactory” is not available`

1. Sélectionnez l’**abonnement** Azure dans lequel vous voulez créer la fabrique de données. 
1. Pour le **groupe de ressources**, effectuez l’une des opérations suivantes : 

   - Sélectionnez **Utiliser l’existant**, puis sélectionnez un groupe de ressources existant dans la liste déroulante. 
   - Sélectionnez **Créer**, puis entrez le nom d’un groupe de ressources. 

   Pour plus d’informations sur les groupes de ressources, consultez [Utilisation des groupes de ressources pour gérer vos ressources Azure](../azure-resource-manager/resource-group-overview.md). 

1. Sélectionnez **V2** pour la **version**. 
1. Sélectionnez **l’emplacement** de la fabrique de données. Seuls les emplacements pris en charge pour la création de fabriques de données sont affichés dans la liste. 
1. Sélectionnez **Épingler au tableau de bord**. 
1. Cliquez sur **Créer**. 
1. Sur le tableau de bord, vous voyez la mosaïque suivante avec l’état : **Déploiement de fabrique de données**. 

    ![mosaïque déploiement de fabrique de données](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)

1. Une fois la création terminée, la page **Data Factory** s’affiche comme sur l’image. 

    ![Page d’accueil Data Factory](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)

1. Cliquez sur **Créer et surveiller** pour lancer l’interface utilisateur (IU) de Data Factory dans un onglet séparé. 

### <a name="provision-an-azure-ssis-integration-runtime"></a>Approvisionner un runtime d’intégration Azure SSIS 
1. Dans la page de prise en main, cliquez sur la vignette **Configurer un runtime d’intégration SSIS**. 

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

    c. Pour **Point de terminaison du serveur de base de données du catalogue**, sélectionnez le point de terminaison de votre serveur de base de données pour héberger SSISDB. En fonction du serveur de base de données sélectionné, SSISDB peut être créé pour vous en tant que base de données indépendante, partie d’un pool élastique ou dans Managed Instance et accessible sur un réseau public ou en rejoignant un réseau virtuel. 

    d. Sur la case à cocher **Utiliser l’authentification AAD...** , sélectionnez la méthode d’authentification pour votre serveur de base de données accueillant la base de données SSISDB : SQL ou Azure Active Directory (AAD) avec votre Managed Service Identity (MSI) de Azure Data Factory. Si vous la cochez, vous devez ajouter votre MSI Data Factory dans un groupe AAD avec autorisations d’accès au serveur de base de données. voir [Autoriser l’authentification AAD pour un runtime d'intégration SSIS Azure](https://docs.microsoft.com/en-us/azure/data-factory/enable-aad-authentication-azure-ssis-ir). 

    e. Pour **Nom d’utilisateur administrateur**, entrez le nom d’utilisateur d’authentification SQL pour votre serveur de base de données accueillant la base de données SSISDB. 

    f. Pour **Mot de passe administrateur**, entrez le mot de passe d’authentification SQL pour votre serveur de base de données accueillant la base de données SSISDB. 

    g. Pour **Niveau de service de la base de données du catalogue**, sélectionnez le niveau de service pour votre serveur de base de données accueillant la base de données SSISDB : Niveau De base/Standard/Premium ou nom de pool élastique. 

    h. Cliquez sur **Tester la connexion** et en cas de réussite, cliquez sur **Suivant**. 

1.  Sur la page **Paramètres avancés**, procédez comme suit : 

    ![Paramètres avancés](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

    a. Pour **Exécutions parallèles maximales par nœud**, sélectionnez le nombre maximal de packages exécutables simultanément par nœud au sein de votre cluster de runtime d’intégration. Seuls les numéros des packages pris en charge sont affichés. Sélectionnez un petit nombre, si vous souhaitez utiliser plusieurs cœurs pour exécuter un package volumineux/lourd intensif sur le calcul/la mémoire. Sélectionnez un nombre plus élevé, si vous souhaitez exécuter un ou plusieurs packages petits/légers dans un seul cœur. 

    b. Pour une **URI SAS de conteneur d’installation personnalisée**, vous pouvez également entrer l’URI (Uniform Resource Identifier) de la signature d’accès partagé (SAS) de votre conteneur Azure Storage Blob dans lequel votre script d’installation et les fichiers associés sont stockés, consultez [Installation personnalisée du runtime d’intégration SSIS Azure](https://docs.microsoft.com/en-us/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup). 

1. Dans la case à cocher **Sélectionner un réseau virtuel...** , indiquez si vous voulez joindre votre runtime d'intégration à un réseau virtuel. Vérifiez si vous utilisez Azure SQL Database avec des points de terminaison de service de réseau virtuel/Managed Instance pour héberger la base de données SSISDB ou si vous avez besoin d’accéder à des données locales ; autrement dit, vous avez des sources/destinations de données locales dans vos packages SSIS. Consultez [Joindre le runtime d’intégration Azure-SSIS à un réseau virtuel](https://docs.microsoft.com/en-us/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Si vous la cochez, effectuez les tâches suivantes : 

   ![Paramètres avancés avec un réseau virtuel](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

    a. Dans **Abonnement**, sélectionnez l’abonnement Azure possédant votre réseau virtuel. 

    b. Pour **Emplacement**, sélectionnez le même emplacement que celui de votre runtime d’intégration. 

    c. Pour **Type**, sélectionnez le type de votre réseau virtuel : classique ou Azure Resource Manager. Nous vous recommandons de sélectionner le réseau virtuel Azure Resource Manager, car le réseau virtuel classique sera bientôt déconseillé. 

    d. Pour **Nom du réseau virtuel**, sélectionnez le nom de votre réseau virtuel. Ce réseau virtuel doit être le même que celui utilisé pour Azure SQL Database avec des points de terminaison de service de réseau virtuel/Managed Instance pour héberger la base de données SSISDB et/ou celle connectée à votre réseau local. 

    e. Pour **Nom du sous-réseau**, sélectionnez le nom du sous-réseau de votre réseau virtuel. Il doit s’agir d’un sous-réseau différent de celui utilisé pour Managed Instance afin d’héberger la base de données SSISDB. 

1. Cliquez sur **Validation du réseau virtuel** et en cas de réussite, cliquez sur **Terminer** pour démarrer la création de votre runtime d'intégration SSIS Azure. 

    > [!IMPORTANT]
    > - Ce processus prend de 20 à 30 minutes.
    > - Le service Data Factory se connecte à votre base de données SQL Azure pour préparer la base de données du catalogue SSIS (SSISDB). Le script configure également les autorisations et les paramètres pour votre réseau virtuel, s’il est spécifié, et relie la nouvelle instance du runtime d’intégration Azure-SSIS au réseau virtuel. 

1. Dans la fenêtre **Connexions**, basculez vers **Runtimes d’intégration** si nécessaire. Cliquez sur **Actualiser** pour actualiser l’état. 

   ![État de la création](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

1. Utilisez les liens sous la colonne **Actions** pour arrêter/démarrer, modifier ou supprimer le runtime d’intégration. Le dernier lien permet d’afficher le code JSON pour le runtime d’intégration. Les boutons Modifier et Supprimer sont activés uniquement lorsque le runtime d’intégration est arrêté. 

   ![Runtime d’intégration Azure SSIS - actions](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>Runtimes d’intégration Azure SSIS sur le portail
1. Dans l’interface utilisateur de Azure Data Factory, basculez vers l’onglet **Modifier**, cliquez sur **Connexions**, puis basculez vers l’onglet **Runtimes d’intégration** pour afficher les runtimes d’intégration existants dans votre Data Factory. 

   ![Afficher les runtimes d’intégration existants](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. Cliquez sur **Nouveau** pour créer un nouveau runtime d’intégration Azure SSIS. 

   ![Runtime d’intégration via le menu](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. Pour créer un runtime d’intégration Azure-SSIS, cliquez sur **Nouveau** comme indiqué sur l’image. 
1. Dans la fenêtre de configuration de runtime d’intégration, sélectionnez **Faire une migration lift-and-shift des packages SSIS existants à exécuter dans Azure**, puis cliquez sur **Suivant**. 

   ![Spécifier le type de runtime d’intégration](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Consultez la section [Approvisionner un runtime d’intégration Azure SSIS](#provision-an-azure-ssis-integration-runtime) pour connaître les autres étapes de configuration d’un runtime d’intégration Azure-SSIS. 

## <a name="azure-powershell"></a>Azure PowerShell
Dans cette section, vous utilisez Azure PowerShell pour créer un runtime d’intégration Azure-SSIS.

### <a name="create-variables"></a>Créer des variables
Définissez des variables à utiliser dans le script de ce didacticiel :

```powershell
### Azure Data Factory information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$".
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$DataFactoryLocation = "EastUS" 

### Azure-SSIS integration runtime information - This is the Data Factory compute resource for running SSIS packages
$AzureSSISName = "[specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[specify a description for your Azure-SSIS IR]"
$AzureSSISLocation = "EastUS" 
# Only Standard_A4_v2|Standard_A8_v2|Standard_D1_v2|Standard_D2_v2|Standard_D3_v2|Standard_D4_v2 are supported.
$AzureSSISNodeSize = "Standard_D4_v2"
# Only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 8 
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database with virtual network service endpoints/Managed Instance/on-premises data, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon    
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use a different subnet than the one used for your Managed Instance

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name or Managed Instance name.DNS prefix].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database or leave it empty for Managed Instance]"
```

### <a name="log-in-and-select-subscription"></a>Se connecter et sélectionner un abonnement
Ajoutez le code suivant au script pour vous connecter et sélectionner votre abonnement Azure : 

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection-to-database"></a>Valider la connexion à la base de données
Ajoutez le script suivant pour valider votre point de terminaison de serveur Azure SQL Database. 

```powershell
# Validate only when you do not use VNet nor AAD authentication
if([string]::IsNullOrEmpty($VnetId) -and [string]::IsNullOrEmpty($SubnetName))
{
    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
    {
        $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword    
        $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
        Try
        {
            $sqlConnection.Open();
        }
        Catch [System.Data.SqlClient.SqlException]
        {
            Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
            Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
            $yn = Read-Host
            if(!($yn -ieq "Y"))
            {
                Return;
            } 
        }
    }
}
```

### <a name="configure-virtual-network"></a>Configurer un réseau virtuel
Ajoutez le script suivant afin de configurer automatiquement les paramètres/autorisations du réseau virtuel pour le runtime d’intégration Azure-SSIS.

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-a-resource-group"></a>Créer un groupe de ressources
Créez un [groupe de ressources Azure](../azure-resource-manager/resource-group-overview.md) avec la commande [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées en tant que groupe. 

```powershell
New-AzureRmResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

### <a name="create-a-data-factory"></a>Créer une fabrique de données
Exécutez la commande suivante pour créer la fabrique de données.

```powershell
Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
```

### <a name="create-an-integration-runtime"></a>Créer un runtime d’intégration
Exécutez les commandes suivantes pour créer un runtime d’intégration Azure-SSIS qui exécute des packages SSIS dans Azure. 

Si vous n’utilisez pas Azure SQL Database avec des points de terminaison de service de réseau virtuel/Managed Instance pour héberger une base de données SSISDB ou si vous n’avez pas besoin d’accéder à des données locales, vous pouvez omettre les paramètres VNetId et de sous-réseau ou passer des valeurs vides pour eux. Dans le cas contraire, vous ne pouvez pas les omettre et vous devez passer les valeurs valides de votre configuration de réseau virtuel, voir [Joindre un runtime d'intégration SSIS Azure à un réseau virtuel](https://docs.microsoft.com/en-us/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). 

Si vous utilisez Managed Instance pour accueillir une base de données SSISDB, vous pouvez omettre le paramètre CatalogPricingTier ou passer une valeur vide pour lui. Dans le cas contraire, vous ne pouvez pas l’omettre et vous devez passer une valeur valide à partir des niveaux tarifaires pris en charge pour Azure SQL Database, consultez [Limites de ressources de SQL Database](../sql-database/sql-database-resource-limits.md). 

Si vous utilisez l’authentification Azure Active Directory (AAD) avec votre Managed Service Identity (MSI) de Azure Data Factory pour vous connecter au serveur de base de données, vous pouvez omettre le paramètre CatalogAdminCredential, mais vous devez ajouter votre fichier MSI Data Factory dans un groupe AAD avec des privilèges d’accès au serveur de base de données, consultez [Activer l’authentification AAD d’activer pour un runtime d'intégration SSIS Azure](https://docs.microsoft.com/en-us/azure/data-factory/enable-aad-authentication-azure-ssis-ir). Sinon, vous ne pouvez pas l’omettre et vous devez passer un objet valide formé à partir de votre nom d’utilisateur et mot de passe d’administrateur de serveur pour l’authentification SQL.

```powershell               
Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Type Managed `
                                           -Name $AzureSSISName `
                                           -Description $AzureSSISDescription `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -SetupScriptContainerSasUri $SetupScriptContainerSasUri `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogPricingTier $SSISDBPricingTier

if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
{
    $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
    $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogAdminCredential $serverCreds
}
```

### <a name="start-integration-runtime"></a>Démarrer le runtime d’intégration
Exécutez la commande suivante pour démarrer le runtime d’intégration Azure-SSIS : 

```powershell
write-host("##### Starting #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")                                  
```

Cette commande dure de **20 à 30 minutes**. 

### <a name="full-script"></a>Script complet
Voici le script complet qui crée un runtime d’intégration Azure-SSIS. 

```powershell
### Azure Data Factory information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$".
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$DataFactoryLocation = "EastUS" 

### Azure-SSIS integration runtime information - This is the Data Factory compute resource for running SSIS packages
$AzureSSISName = "[specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[specify a description for your Azure-SSIS IR]"
$AzureSSISLocation = "EastUS" 
# Only Standard_A4_v2|Standard_A8_v2|Standard_D1_v2|Standard_D2_v2|Standard_D3_v2|Standard_D4_v2 are supported.
$AzureSSISNodeSize = "Standard_D4_v2"
# Only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 8 
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database with virtual network service endpoints/Managed Instance/on-premises data, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon    
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use a different subnet than the one used for your Managed Instance

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name or Managed Instance name.DNS prefix].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database or leave it empty for Managed Instance]"

### Log in and select subscription
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

### Validate the connection to database
# Validate only when you do not use VNet nor AAD authentication
if([string]::IsNullOrEmpty($VnetId) -and [string]::IsNullOrEmpty($SubnetName))
{
    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
    {
        $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword    
        $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
        Try
        {
            $sqlConnection.Open();
        }
        Catch [System.Data.SqlClient.SqlException]
        {
            Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
            Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
            $yn = Read-Host
            if(!($yn -ieq "Y"))
            {
                Return;
            } 
        }
    }
}

### Configure virtual network
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}

### Create a data factory
Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName

### Create an integration runtime
Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Type Managed `
                                           -Name $AzureSSISName `
                                           -Description $AzureSSISDescription `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -SetupScriptContainerSasUri $SetupScriptContainerSasUri `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogPricingTier $SSISDBPricingTier

if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
{
    $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
    $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogAdminCredential $serverCreds
}

### Start integration runtime   
write-host("##### Starting your Azure-SSIS integration runtime. This command takes 20 to 30 minutes to complete. #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

## <a name="azure-resource-manager-template"></a>Modèle Azure Resource Manager
Dans cette section, vous utilisez le modèle Azure Resource Manager pour créer un runtime d’intégration Azure-SSIS. Voici un exemple de procédure : 

1. Créez un fichier JSON avec le modèle Azure Resource Manager suivant. Remplacez les valeurs entre chevrons (espaces réservés) par vos propres valeurs. 

    ```json
    {
        "contentVersion": "1.0.0.0",
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {},
        "variables": {},
        "resources": [{
            "name": "<Specify a name for your data factory>",
            "apiVersion": "2017-09-01-preview",
            "type": "Microsoft.DataFactory/factories",
            "location": "East US",
            "properties": {},
            "resources": [{
                "type": "integrationruntimes",
                "name": "<Specify a name for your Azure-SSIS IR>",
                "dependsOn": [ "<The name of the data factory you specified at the beginning>" ],
                "apiVersion": "2017-09-01-preview",
                "properties": {
                    "type": "Managed",
                    "typeProperties": {
                        "computeProperties": {
                            "location": "East US",
                            "nodeSize": "Standard_D4_v2",
                            "numberOfNodes": 1,
                            "maxParallelExecutionsPerNode": 8
                        },
                        "ssisProperties": {
                            "catalogInfo": {
                                "catalogServerEndpoint": "<Azure SQL Database server name>.database.windows.net",
                                "catalogAdminUserName": "<Azure SQL Database server admin username>",
                                "catalogAdminPassword": {
                                    "type": "SecureString",
                                    "value": "<Azure SQL Database server admin password>"
                                },
                                "catalogPricingTier": "Basic"
                            }
                        }
                    }
                }
            }]
        }]
    }
    ```
    
1. Pour déployer le modèle Azure Resource Manager, exécutez la commande New-AzureRmResourceGroupDeployment comme indiqué dans l’exemple suivant, où ADFTutorialResourceGroup est le nom de votre groupe de ressources et ADFTutorialARM.json est le fichier qui contient la définition JSON de votre fabrique de données et du runtime d'intégration SSIS Azure. 

    ```powershell
    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    Cette commande crée votre fabrique de données et un runtime d’intégration Azure-SSIS dans cette fabrique, mais elle ne démarre pas le runtime d’intégration. 

1. Pour démarrer votre runtime d’intégration Azure-SSIS, exécutez la commande Start-AzureRmDataFactoryV2IntegrationRuntime : 

    ```powershell
    Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name>" `
                                                 -DataFactoryName "<Data Factory Name>" `
                                                 -Name "<Azure SSIS IR Name>" `
                                                 -Force
    ``` 

## <a name="deploy-ssis-packages"></a>Déployer des packages SSIS
Utilisez maintenant SQL Server Data Tools (SSDT) ou SQL Server Management Studio (SSMS) pour déployer vos packages SSIS dans Azure. Connectez-vous à votre serveur de base de données qui héberge le catalogue SSIS (SSISDB). Le nom du serveur de base de données est au format suivant : &lt;nom de serveur Azure SQL Database&gt;.database.windows.net ou &lt;Nom Managed Instance.préfixe DNS&gt;.database.windows.net. Consultez l’article [Déployer des packages](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server) pour obtenir des instructions. 

## <a name="next-steps"></a>Étapes suivantes
Consultez les autres rubriques relatives au runtime d’intégration Azure SSIS dans cette documentation : 

- [Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime) (Runtime d’intégration Azure-SSIS). Cet article fournit des informations conceptuelles sur les runtimes d’intégration en général, y compris sur le runtime d’intégration Azure-SSIS. 
- [Didacticiel : deploy SSIS packages to Azure](tutorial-create-azure-ssis-runtime-portal.md) (Déployer des packages SSIS vers Azure). Cet article fournit des instructions détaillées pour créer un runtime d’intégration Azure-SSIS qui utilise une base de données Azure SQL pour héberger le catalogue SSIS. 
- [Monitor an Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime) (Surveiller le runtime d’intégration Azure-SSIS). Cet article explique comment récupérer des informations sur un runtime d’intégration Azure-SSIS ainsi que des descriptions d’état dans les informations renvoyées. 
- [Manage an Azure-SSIS IR](manage-azure-ssis-integration-runtime.md) (Gérer un runtime d’intégration Azure-SSIS). Cet article vous explique comment arrêter, démarrer ou supprimer un runtime d’intégration Azure-SSIS. Il vous montre également comment le faire évoluer en lui ajoutant des nœuds supplémentaires. 
- [Joindre un runtime d’intégration Azure-SSIS à un réseau virtuel](join-azure-ssis-integration-runtime-virtual-network.md). Cet article fournit des informations conceptuelles sur la façon d’attacher votre runtime d’intégration Azure-SSIS à un réseau virtuel Azure. Il décrit également les étapes nécessaires pour utiliser le portail Azure afin de configurer le réseau virtuel de sorte que le runtime d’intégration Azure-SSIS puisse le rejoindre. 
