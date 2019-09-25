---
title: Créer un Azure-SSIS Integration Runtime dans Azure Data Factory | Microsoft Docs
description: Découvrez comment créer un Azure-SSIS Integration Runtime dans Azure Data Factory afin de pouvoir déployer et exécuter des packages SSIS dans Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/15/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 36cb4d306cd74dcde09fa55fc582a043bc324f65
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/16/2019
ms.locfileid: "71010027"
---
# <a name="create-azure-ssis-integration-runtime-in-azure-data-factory"></a>Créer un Azure-SSIS Integration Runtime dans Azure Data Factory

Ce tutoriel décrit les différentes étapes de provisionnement d’un runtime d’intégration (IR) Azure SQL Server Integration Services (SSIS) dans Azure Data Factory. Azure-SSIS IR prend en charge l’exécution de packages déployés dans le catalogue SSIS (SSISDB) hébergé par le serveur Azure SQL Database/Managed Instance (modèle de déploiement de projet) et de ceux déployés dans les systèmes de fichiers/les partages de fichiers/Azure Files (modèle de déploiement de package). Une fois qu’Azure-SSIS IR est provisionné, vous pouvez utiliser des outils familiers, comme SQL Server Data Tools (SSDT)/SQL Server Management Studio (SSMS), et des utilitaires en ligne de commande, comme `dtinstall`/`dtutil`/`dtexec`, pour déployer et exécuter vos packages dans Azure.

Le [Tutoriel : Provisionnement d’Azure-SSIS IR](tutorial-create-azure-ssis-runtime-portal.md) vous montre comment créer un runtime d’intégration Azure-SSIS à l’aide du portail Azure/de l’application ADF et éventuellement utiliser le serveur Azure SQL Database/Managed Instance pour héberger SSISDB. Cet article s’appuie sur le didacticiel et vous montre comment effectuer les opérations suivantes :

- Si vous le souhaitez, vous pouvez utiliser le serveur Azure SQL Database avec des points de terminaison de service de réseau virtuel/Managed Instance avec un point de terminaison privé pour héberger SSISDB. Vous devez au préalable configurer les autorisations/paramètres de réseau virtuel pour que votre runtime d’intégration Azure-SSIS soit joint à un réseau virtuel.

- Utilisez éventuellement l’authentification Azure Active Directory (AAD) avec l’identité managée pour que votre service ADF se connecte à un serveur Azure SQL Database/Managed Instance. Vous devez au préalable ajouter l’identité managée pour votre ADF comme utilisateur de base de données capable de créer une base de données SSISDB.

- Si vous le souhaitez, vous pouvez joindre votre runtime Azure-SSIS IR à un réseau virtuel/configurer un IR auto-hébergé en tant que proxy pour votre runtime Azure-SSIS IR pour accéder aux données locales.

## <a name="overview"></a>Vue d'ensemble

Cet article présente différentes façons de provisionner Azure-SSIS IR :

- [Portail Azure](#azure-portal)
- [Azure PowerShell](#azure-powershell)
- [Modèle Azure Resource Manager](#azure-resource-manager-template)

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Abonnement Azure**. Si vous n’avez pas encore d’abonnement, vous pouvez créer un compte d’[essai gratuit](https://azure.microsoft.com/pricing/free-trial/).
- **Serveur Azure SQL Database/Managed Instance (facultatif)** . Si vous n’avez pas encore de serveur de base de données, créez-en un dans le portail Azure avant de commencer. ADF crée à son tour une base de données SSISDB sur ce serveur de base de données. Nous vous recommandons de créer le serveur de base de données dans la même région Azure que le runtime d’intégration. Cette configuration permet au runtime d’intégration d’écrire des journaux des exécutions dans SSISDB sans dépasser les régions Azure. 
    - En fonction du serveur de base de données sélectionné, SSISDB peut être créée à votre place en tant que base de données unique, faisant partie d’un pool élastique ou dans une instance gérée de type Managed Instance, et accessible sur un réseau public ou en rejoignant un réseau virtuel. Pour obtenir de l’aide sur le choix du type de serveur de base de données pour héberger SSISDB, consultez [Comparer une base de données unique/un pool élastique Azure SQL Database et Managed Instance](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). Si vous utilisez un serveur Azure SQL Database avec des points de terminaison de service de réseau virtuel/Managed Instance avec un point de terminaison privé pour héberger SSISDB, ou si vous avez besoin d’accéder à des données locales sans configurer le runtime d’intégration auto-hébergé, vous devez joindre votre runtime Azure-SSIS IR à un réseau virtuel. Consultez [Joindre le runtime d’intégration Azure-SSIS à un réseau virtuel](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).
    - Vérifiez que le paramètre **Autoriser l’accès aux services Azure** est activé pour votre serveur de base de données. Cela ne s’applique pas quand vous utilisez le serveur Azure SQL Database avec des points de terminaison de service de réseau virtuel/Managed Instance avec un point de terminaison privé pour héberger SSISDB. Pour en savoir plus, consultez [Sécuriser votre base de données Azure SQL](../sql-database/sql-database-security-tutorial.md#create-firewall-rules). Pour savoir comment activer ce paramètre à l’aide de PowerShell, consultez [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).
    - Ajoutez l’adresse IP de l’ordinateur client ou une plage d’adresses IP qui inclut l’adresse IP de l’ordinateur client à la liste d’adresses IP client dans les paramètres de pare-feu du serveur de base de données. Pour plus d’informations, consultez [Règles de pare-feu au niveau du serveur et de la base de données Azure SQL Database](../sql-database/sql-database-firewall-configure.md).
    - Vous pouvez vous connecter au serveur de base de données à l’aide de l’authentification SQL avec vos informations d’identification d’administrateur de serveur ou l’authentification Azure Active Directory (AAD) avec l’identité managée de votre ADF.  Pour cette dernière, vous devez ajouter l’identité managée de votre ADF à un groupe AAD avec autorisations d’accès au serveur de base de données. Pour cela, consultez [Activer l’authentification Azure Active Directory pour Azure-SSIS Integration Runtime](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir).
    - Vérifiez que votre serveur de base de données n’a pas déjà une base de données SSISDB. Le provisionnement d’un runtime d’intégration Azure-SSIS ne prend pas en charge l’utilisation d’une base de données SSISDB existante.
- **Réseau virtuel Azure Resource Manager (facultatif)** . Vous devez disposer d’un réseau virtuel Azure Resource Manager si au moins l’une des conditions suivantes est remplie :
    - Vous hébergez SSISDB sur le serveur Azure SQL Database avec des points de terminaison de service de réseau virtuel/Managed Instance avec un point de terminaison privé.
    - Vous souhaitez vous connecter à des magasins de données locaux à partir de packages SSIS en cours d’exécution sur votre runtime Azure-SSIS IR sans configurer d’IR auto-hébergé.
- **Azure PowerShell (facultatif)** . Suivez les instructions de la rubrique [Guide pratique pour installer et configurer Azure PowerShell](/powershell/azure/install-az-ps) si vous souhaitez exécuter un script PowerShell pour provisionner votre runtime d’intégration Azure-SSIS.

### <a name="region-support"></a>Prise en charge de la région

Pour obtenir la liste des régions Azure dans lesquelles ADF et Azure-SSIS IR sont actuellement disponibles, consultez [Disponibilité d’Azure Data Factory et de SSIS IR par région](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all).

### <a name="compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance"></a>Comparer une base de données unique/un pool élastique Azure SQL Database et SQL Database Managed Instance

Le tableau suivant compare certaines fonctionnalités du serveur Azure SQL Database et Managed Instance concernant l’IR Azure-SSIR :

| Fonctionnalité | base de données unique/pool élastique| Instance gérée |
|---------|--------------|------------------|
| **Planification** | SQL Server Agent n’est pas disponible.<br/><br/>Consultez [Planifier l’exécution de packages SSIS (SQL Server Integration Services) déployés dans Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages?view=sql-server-2017#activity).| L’agent Managed Instance est disponible. |
| **Authentification** | Vous pouvez créer SSISDB avec un utilisateur de base de données autonome représentant un groupe AAD avec l’identité managée de votre service ADF en tant que membre du rôle **db_owner**.<br/><br/>Consultez [Activer l’authentification Azure AD pour créer SSISDB sur le serveur Azure SQL Database](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database). | Vous pouvez créer SSISDB avec un utilisateur de base de données autonome représentant l’identité managée de votre service ADF. <br/><br/>Consultez [Activer l’authentification Azure AD pour créer SSISDB dans Azure SQL Database Managed Instance](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database-managed-instance). |
| **Niveau de service** | Quand vous créez un IR Azure-SSIS avec votre serveur Azure SQL Database, vous pouvez sélectionner le niveau de service pour SSISDB. Il existe plusieurs niveaux de service. | Quand vous créez un Azure-SSIS IR avec votre Managed Instance, vous ne pouvez pas sélectionner le niveau de service pour SSISDB. Toutes les bases de données dans votre Managed Instance partagent la même ressource allouée à cette instance. |
| **Réseau virtuel** | Prend en charge uniquement les réseaux virtuels Azure Resource Manager pour la jonction de votre runtime Azure-SSIS IR si vous utilisez un serveur Azure SQL Database avec des points de terminaison de service de réseau virtuel ou si vous avez besoin d’accéder à des magasins de données locaux sans configurer d’IR auto-hébergé. | Prend uniquement en charge les réseaux virtuels Azure Resource Manager pour la jonction de votre Azure-SSIS IR. Le réseau virtuel est requis quand vous n’activez pas de point de terminaison public pour votre instance managée.<br/><br/>Si vous associez votre Azure-SSIS IR au même réseau virtuel que votre Managed Instance, vérifiez que votre Azure-SSIS IR est sur un sous-réseau différent de celui de votre Managed Instance. Si vous associez votre Azure-SSIS IR à un réseau virtuel différent de celui de votre Managed Instance, nous recommandons l’appairage de réseau virtuel ou une connexion de réseau virtuel à réseau virtuel. Consultez [Connecter votre application à Azure SQL Database Managed Instance](../sql-database/sql-database-managed-instance-connect-app.md). |
| **Transactions distribuées** | Prises en charge par le biais des Transactions élastiques. Les transactions MSDTC (Microsoft Distributed Transaction Coordinator) ne sont pas prises en charge. Si vos packages SSIS utilisent MSDTC pour coordonner les transactions distribuées, envisagez la migration vers les transactions élastiques pour Azure SQL Database. Pour plus d’informations, consultez [Transactions distribuées entre bases de données cloud](../sql-database/sql-database-elastic-transactions-overview.md). | Non pris en charge. |
| | | |

## <a name="azure-portal"></a>Portail Azure

Dans cette section, vous utilisez le portail Azure, plus précisément l’application/interface utilisateur ADF, pour créer Azure-SSIS IR.

### <a name="create-a-data-factory"></a>Créer une fabrique de données

Pour créer votre fabrique ADF à l’aide du portail Azure, suivez la procédure pas à pas [Créer une fabrique de données - Interface utilisateur](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory) et sélectionnez **Épingler au tableau de bord** pour y accéder rapidement après sa création. 

Une fois la fabrique ADF créée, ouvrez sa page de vue d’ensemble sur le portail Azure, puis cliquez sur la vignette **Créer et superviser** pour lancer la page **Commençons** dans un onglet distinct où vous pouvez poursuivre la création de votre runtime d’intégration Azure-SSIS IR.   

### <a name="provision-an-azure-ssis-integration-runtime"></a>Approvisionner un runtime d’intégration Azure SSIS

1. Dans la page **Commençons**, cliquez sur la vignette **Configurer SSIS Integration**.

   ![Vignette Configurer un runtime d’intégration SSIS](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

2. Sur la page **Paramètres généraux** de **Configuration du runtime d’intégration**, procédez comme suit :

   ![Paramètres généraux :](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

    a. Dans **Nom**, saisissez le nom de votre runtime d’intégration.

    b. Dans **Description**, saisissez la description de votre runtime d’intégration.

    c. Pour **Emplacement**, sélectionnez l’emplacement de votre runtime d’intégration. Seuls les emplacements pris en charge sont affichés. Nous vous conseillons de sélectionner le même emplacement que votre serveur de base de données pour héberger SSISDB.

    d. Pour **Taille du nœud**, sélectionnez la taille du nœud du cluster de votre runtime d’intégration. Seules les tailles de nœuds prises en charge sont affichées. Sélectionnez une taille de nœud importante si vous voulez exécuter de nombreux packages nécessitant beaucoup de calcul/mémoire.

    e. Pour **Nombre de nœud**, sélectionnez le nombre de nœuds du cluster de votre runtime d’intégration. Seuls les nombres de nœuds pris en charge sont affichés. Sélectionnez un grand cluster avec plusieurs nœuds, si vous voulez exécuter de nombreux packages en parallèle.

    f. Pour **Édition/Licence**, sélectionnez l’édition/licence de SQL Server pour votre runtime d’intégration : Standard ou Entreprise. Sélectionnez Entreprise, si vous voulez utiliser les fonctionnalités avancées/premium de votre runtime d’intégration.

    g. Pour **Économiser de l’argent**, sélectionnez l’option Azure Hybrid Benefit (AHB) pour votre runtime d’intégration : Oui ou Non. Sélectionnez Oui si vous souhaitez mettre votre propre licence SQL Server avec Software Assurance pour faire des économies grâce à une utilisation hybride.

    h. Cliquez sur **Suivant**.

3. Sur la page **Paramètres SQL**, procédez comme suit :

   ![Paramètres SQL](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

    a. Sur la case **Créer un catalogue SSIS...** , cochez le modèle de déploiement approprié pour que les packages s’exécutent sur votre runtime d’intégration Azure-SSIS : Le modèle de déploiement de projet dans lequel les packages sont déployés dans SSISDB hébergé par votre serveur de base de données ou votre modèle de déploiement de package dans lequel les packages sont déployés dans vos systèmes de fichiers/partages de fichiers/Azure Files. Si vous cochez cette option, vous devez apporter votre propre serveur de base de données pour héberger la base de données SSISDB que nous allons créer et gérer à votre place.
   
    b. Pour **Abonnement**, sélectionnez l’abonnement Azure comprenant votre serveur de base de données pour héberger la base de données SSISDB. 

    c. Pour **Emplacement**, sélectionnez l’emplacement de votre serveur de base de données pour héberger la base de données SSISDB. Nous vous recommandons de sélectionner le même emplacement que celui de runtime d’intégration. 

    d. Pour **Point de terminaison du serveur de base de données du catalogue**, sélectionnez le point de terminaison de votre serveur de base de données pour héberger SSISDB. En fonction du serveur de base de données sélectionné, SSISDB peut être créée à votre place en tant que base de données unique, faisant partie d’un pool élastique ou dans une instance gérée de type Managed Instance, et accessible sur un réseau public ou en rejoignant un réseau virtuel. Pour obtenir de l’aide sur le choix du type de serveur de base de données pour héberger SSISDB, consultez [Comparer une base de données unique/un pool élastique Azure SQL Database et Managed Instance](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). Si vous sélectionnez un serveur Azure SQL Database avec des points de terminaison de service de réseau virtuel/Managed Instance avec un point de terminaison privé pour héberger SSISDB, ou si vous avez besoin d’accéder à des données locales sans configurer de runtime d’intégration auto-hébergé, vous devez joindre votre runtime d’intégration Azure-SSIS IR à un réseau virtuel. Consultez [Joindre le runtime d’intégration Azure-SSIS à un réseau virtuel](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). 

    e. Dans la case à cocher **Utiliser l’authentification AAD**, sélectionnez la méthode d’authentification pour l’hébergement de SSISDB par votre serveur de base de données : l’authentification SQL ou l’authentification AAD avec l’identité managée pour votre ADF. Si vous cochez cette option, vous devez ajouter l’identité managée de votre service Azure Data Factory à un groupe AAD avec autorisations d’accès à votre serveur de base de données. Pour cela, consultez [Activer l’authentification Azure Active Directory pour Azure-SSIS Integration Runtime](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir). 

    f. Pour **Nom d’utilisateur administrateur**, entrez le nom d’utilisateur d’authentification SQL pour votre serveur de base de données accueillant la base de données SSISDB. 

    g. Pour **Mot de passe administrateur**, entrez le mot de passe d’authentification SQL pour votre serveur de base de données accueillant la base de données SSISDB. 

    h. Pour **Niveau de service de base de données de catalogue**, sélectionnez le niveau de service pour l’hébergement de SSISDB par votre serveur de base de données : niveau De base/Standard/Premium ou nom du pool élastique. 

    i. Cliquez sur **Tester la connexion** et en cas de réussite, cliquez sur **Suivant**. 

4. Sur la page **Paramètres avancés**, procédez comme suit :

    ![Paramètres avancés](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

    a. Pour **Exécutions parallèles maximales par nœud**, sélectionnez le nombre maximal de packages exécutables simultanément par nœud au sein de votre cluster de runtime d’intégration. Seuls les numéros des packages pris en charge sont affichés. Sélectionnez un petit nombre, si vous souhaitez utiliser plusieurs cœurs pour exécuter un package volumineux/lourd intensif sur le calcul/la mémoire. Sélectionnez un nombre plus élevé, si vous souhaitez exécuter un ou plusieurs packages petits/légers dans un seul cœur.

    b. Pour une **URI SAS de conteneur d’installation personnalisée**, vous pouvez également entrer l’URI (Uniform Resource Identifier) de la signature d’accès partagé (SAS) de votre conteneur Azure Storage Blob dans lequel votre script d’installation et les fichiers associés sont stockés, consultez [Installation personnalisée du runtime d’intégration SSIS Azure](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).

5. Dans la case à cocher **Sélectionner un réseau virtuel...** , indiquez si vous voulez joindre votre runtime d'intégration à un réseau virtuel. Vérifiez si vous utilisez un serveur Azure SQL Database avec des points de terminaison de service de réseau virtuel/Managed Instance avec un point de terminaison privé pour héberger SSISDB ou si vous avez besoin d’accéder à des données locales (si vous avez des sources/destinations de données locales dans vos packages SSIS) sans configurer d’IR auto-hébergé. Consultez [Joindre le runtime d’intégration Azure-SSIS à un réseau virtuel](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Si vous la cochez, effectuez les tâches suivantes :

   ![Paramètres avancés avec un réseau virtuel](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

    a. Dans **Abonnement**, sélectionnez l’abonnement Azure possédant votre réseau virtuel.

    b. Pour **Emplacement**, sélectionnez le même emplacement que celui de votre runtime d’intégration.

    c. Pour **Type**, sélectionnez le type de votre réseau virtuel : Classique ou Azure Resource Manager. Nous vous recommandons de sélectionner le réseau virtuel Azure Resource Manager, car le réseau virtuel classique sera bientôt déconseillé.

    d. Pour **Nom du réseau virtuel**, sélectionnez le nom de votre réseau virtuel. Ce réseau virtuel doit être le même que celui utilisé pour le serveur Azure SQL Database avec des points de terminaison de service de réseau virtuel/Managed Instance dans un réseau virtuel pour héberger la base de données SSISDB ou celui connecté à votre réseau local.

    e. Pour **Nom du sous-réseau**, sélectionnez le nom du sous-réseau de votre réseau virtuel. Ce doit être un sous-réseau différent de celui utilisé pour Managed Instance dans un réseau virtuel pour héberger SSISDB.

6. À l’aide de la case à cocher **Configurer un runtime d’intégration auto-hébergé...** , indiquez si vous souhaitez configurer un IR auto-hébergé en tant que proxy pour votre runtime Azure-SSIS IR. Consultez [Configurer un runtime d’intégration auto-hébergé en tant que proxy](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis). Si vous la cochez, effectuez les tâches suivantes :

   ![Paramètres avancés avec un IR auto-hébergé](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

    a. Sous **Runtime d’intégration auto-hébergé**, sélectionnez votre IR auto-hébergé existant en tant que proxy pour Azure-SSIS IR.

    b. Sous **Service lié de stockage de préproduction**, sélectionnez votre service lié de stockage Blob Azure existant ou créez-en un pour la préproduction.

    c. Sous **Chemin d’accès intermédiaire**, spécifiez un conteneur d’objets blob dans le stockage Blob Azure sélectionné ou laissez ce champ pour utiliser un conteneur par défaut pour la préproduction.

7. Cliquez sur **Validation de réseau virtuel** ou sur **Suivant**. 

8. Dans la page **Résumé**, passez en revue tous les paramètres de provisionnement, ajoutez les liens de documentation recommandés aux favoris, puis cliquez sur **Terminer** pour lancer la création de votre runtime d’intégration.

    > [!NOTE]
    > En dehors des réglages personnalisés, ce processus devrait se terminer dans les 5 minutes. Toutefois, il peut prendre de 20 à 30 minutes environ pour le runtime d’intégration Azure-SSIS se joignant à un réseau virtuel.
    >
    > Si vous utilisez SSISDB, le service ADF se connecte à votre serveur de base de données pour préparer SSISDB. Il configure également les autorisations et les paramètres de votre réseau virtuel, s’ils sont spécifiés, et joint votre runtime d’intégration Azure-SSIS au réseau virtuel.
    > 
    > Quand vous provisionnez un runtime d’intégration Azure-SSIS, les composants Access Redistributable et Azure Feature Pack pour SSIS sont également installés. Ces composants fournissent la connectivité aux fichiers Excel/Access et à diverses sources de données Azure, en plus des sources de données déjà prises en charge par les composants intégrés. Vous pouvez également installer des composants supplémentaires ; consultez [Configuration personnalisée pour Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md) pour en savoir plus.

7. Dans la fenêtre **Connexions**, basculez vers **Runtimes d’intégration** si nécessaire. Cliquez sur **Actualiser** pour actualiser l’état.

   ![État de la création](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

8. Utilisez les liens sous la colonne **Actions** pour arrêter/démarrer, modifier ou supprimer le runtime d’intégration. Le dernier lien permet d’afficher le code JSON pour le runtime d’intégration. Les boutons Modifier et Supprimer sont activés uniquement lorsque le runtime d’intégration est arrêté.

   ![Runtime d’intégration Azure SSIS - actions](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>Runtimes d’intégration Azure SSIS sur le portail

1. Dans l’interface utilisateur de Azure Data Factory, basculez vers l’onglet **Modifier**, cliquez sur **Connexions**, puis basculez vers l’onglet **Runtimes d’intégration** pour afficher les runtimes d’intégration existants dans votre Data Factory.

   ![Afficher les runtimes d’intégration existants](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

2. Cliquez sur **Nouveau** pour créer un nouveau runtime d’intégration Azure SSIS.

   ![Runtime d’intégration via le menu](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

3. Pour créer un runtime d’intégration Azure-SSIS, cliquez sur **Nouveau** comme indiqué sur l’image.

4. Dans la fenêtre de configuration de runtime d’intégration, sélectionnez **Faire une migration lift-and-shift des packages SSIS existants à exécuter dans Azure**, puis cliquez sur **Suivant**.

   ![Spécifier le type de runtime d’intégration](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

5. Consultez la section [Approvisionner un runtime d’intégration Azure SSIS](#provision-an-azure-ssis-integration-runtime) pour connaître les autres étapes de configuration d’un runtime d’intégration Azure-SSIS.

## <a name="azure-powershell"></a>Azure PowerShell

Dans cette section, vous utilisez Azure PowerShell pour créer un runtime d’intégration Azure-SSIS.

### <a name="create-variables"></a>Créer des variables

Copiez et collez le script suivant. Spécifiez des valeurs pour les variables. 

```powershell
### Azure Data Factory information
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - Must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to (2 x number of cores) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database server with virtual network service endpoints/Managed Instance with a private endpoint/on-premises data without configuring Self-Hosted IR, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints or a different subnet than the one used for your Managed Instance with a private endpoint

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or Managed Instance name.DNS prefix.database.windows.net or Managed Instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, please ensure that there is no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
# For the basic pricing tier, specify "Basic", not "B" - For standard/premium/elastic pool tiers, specify "S0", "S1", "S2", "S3", etc., see https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for Managed Instance]"
```

### <a name="sign-in-and-select-subscription"></a>Se connecter et sélectionner un abonnement

Ajoutez le code suivant au script pour vous connecter et sélectionner votre abonnement Azure :

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection-to-database-server"></a>Valider la connexion au serveur de base de données

Ajoutez le script suivant pour valider votre serveur Azure SQL Database/Managed Instance.

```powershell
# Validate only if you use SSISDB and do not use VNet or AAD authentication
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
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
}
```

### <a name="configure-virtual-network"></a>Configurer un réseau virtuel

Ajoutez le script suivant afin de configurer automatiquement les paramètres/autorisations du réseau virtuel pour le runtime d’intégration Azure-SSIS.

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un [groupe de ressources Azure](../azure-resource-manager/resource-group-overview.md) avec la commande [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées en tant que groupe.

Si votre groupe de ressources existe déjà, ne copiez pas ce code dans votre script. 

```powershell
New-AzResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

### <a name="create-a-data-factory"></a>Créer une fabrique de données

Exécutez la commande suivante pour créer la fabrique de données.

```powershell
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
```

### <a name="create-an-integration-runtime"></a>Créer un runtime d’intégration

Exécutez les commandes suivantes pour créer un runtime d’intégration Azure-SSIS qui exécute des packages SSIS dans Azure.

Si vous n’utilisez pas SSISDB, vous pouvez omettre les paramètres CatalogServerEndpoint, CatalogPricingTier et CatalogAdminCredential.

Si vous n’utilisez pas de serveur Azure SQL Database avec des points de terminaison de service de réseau virtuel/Managed Instance avec un point de terminaison privé pour héberger SSISDB ou si vous avez besoin d’accéder à des données locales, vous pouvez omettre les paramètres VNetId et Subnet ou passer des valeurs vides pour ces paramètres. Vous pouvez également les omettre si vous configurez le runtime d’intégration auto-hébergé en tant que proxy pour votre runtime Azure-SSIS IR pour accéder à des données locales. Dans le cas contraire, vous ne pouvez pas les omettre et vous devez passer les valeurs valides de votre configuration de réseau virtuel, voir [Joindre un runtime d'intégration SSIS Azure à un réseau virtuel](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

Si vous utilisez Managed Instance pour accueillir une base de données SSISDB, vous pouvez omettre le paramètre CatalogPricingTier ou passer une valeur vide pour lui. Dans le cas contraire, vous ne pouvez pas l’omettre et vous devez passer une valeur valide à partir des niveaux tarifaires pris en charge pour Azure SQL Database, consultez [Limites des ressources SQL Database](../sql-database/sql-database-resource-limits.md).

Si vous utilisez l’authentification Azure Active Directory (AAD) avec l’identité managée pour que votre ADF se connecte au serveur de base de données, vous pouvez omettre le paramètre CatalogAdminCredential, mais vous devez ajouter l’identité managée de votre ADF à un groupe AAD avec autorisations d’accès au serveur de base de données. Pour cela, consultez [Activer l’authentification Azure Active Directory pour Azure-SSIS Integration Runtime](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir). Sinon, vous ne pouvez pas l’omettre et vous devez passer un objet valide formé à partir de votre nom d’utilisateur et mot de passe d’administrateur de serveur pour l’authentification SQL.

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName
       
# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier

    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword)) # Add CatalogAdminCredential parameter if you do not use AAD authentication
    {
        $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
        $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                                   -DataFactoryName $DataFactoryName `
                                                   -Name $AzureSSISName `
                                                   -CatalogAdminCredential $serverCreds
    }
}

# Add SetupScriptContainerSasUri parameter if you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}
```

### <a name="start-integration-runtime"></a>Démarrer le runtime d’intégration

Exécutez les commandes suivantes pour démarrer le runtime d’intégration Azure-SSIS.

```powershell
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

> [!NOTE]
> En dehors des réglages personnalisés, ce processus devrait se terminer dans les 5 minutes. Toutefois, il peut prendre de 20 à 30 minutes environ pour le runtime d’intégration Azure-SSIS se joignant à un réseau virtuel.
>
> Si vous utilisez SSISDB, le service ADF se connecte à votre serveur de base de données pour préparer SSISDB. Il configure également les autorisations et les paramètres de votre réseau virtuel, s’ils sont spécifiés, et joint votre runtime d’intégration Azure-SSIS au réseau virtuel.
> 
> Quand vous provisionnez un runtime d’intégration Azure-SSIS, les composants Access Redistributable et Azure Feature Pack pour SSIS sont également installés. Ces composants fournissent la connectivité aux fichiers Excel/Access et à diverses sources de données Azure, en plus des sources de données déjà prises en charge par les composants intégrés. Vous pouvez également installer des composants supplémentaires ; consultez [Configuration personnalisée pour Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md) pour en savoir plus.

### <a name="full-script"></a>Script complet

Voici le script complet qui crée un runtime d’intégration Azure-SSIS.

```powershell
### Azure Data Factory information
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - Must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to (2 x number of cores) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database server with virtual network service endpoints/Managed Instance with a private endpoint/on-premises data without configuring Self-Hosted IR, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use the same subnet as the one used with your Azure SQL Database server with virtual network service endpoints or a different subnet than the one used for your Managed Instance with a private endpoint

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or Managed Instance name.DNS prefix.database.windows.net or Managed Instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, please ensure that there is no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
# For the basic pricing tier, specify "Basic", not "B" - For standard/premium/elastic pool tiers, specify "S0", "S1", "S2", "S3", etc., see https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for Managed Instance]"

### Sign in and select subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName

### Validate the connection to database server
# Validate only if you use SSISDB and do not use VNet or AAD authentication
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
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
}

### Configure virtual network
# Make sure to run this script against the subscription to which the virtual network belongs
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}

### Create a data factory
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName

### Create an integration runtime
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName
       
# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier

    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword)) # Add CatalogAdminCredential parameter if you do not use AAD authentication
    {
        $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
        $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                                   -DataFactoryName $DataFactoryName `
                                                   -Name $AzureSSISName `
                                                   -CatalogAdminCredential $serverCreds
    }
}

# Add SetupScriptContainerSasUri parameter when you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}

### Start integration runtime
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
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
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {},
        "variables": {},
        "resources": [{
            "name": "<Specify a name for your data factory>",
            "apiVersion": "2018-06-01",
            "type": "Microsoft.DataFactory/factories",
            "location": "East US",
            "properties": {},
            "resources": [{
                "type": "integrationruntimes",
                "name": "<Specify a name for your Azure-SSIS IR>",
                "dependsOn": [ "<The name of the data factory you specified at the beginning>" ],
                "apiVersion": "2018-06-01",
                "properties": {
                    "type": "Managed",
                    "typeProperties": {
                        "computeProperties": {
                            "location": "East US",
                            "nodeSize": "Standard_D8_v3",
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

2. Pour déployer le modèle Azure Resource Manager, exécutez la commande New-AzResourceGroupDeployment comme indiqué dans l’exemple suivant, où ADFTutorialResourceGroup est le nom de votre groupe de ressources et ADFTutorialARM.json est le fichier qui contient la définition JSON de votre fabrique de données et d’Azure-SSIS Integration Runtime.

    ```powershell
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    Cette commande crée votre fabrique de données et un runtime d’intégration Azure-SSIS dans cette fabrique, mais elle ne démarre pas le runtime d’intégration.

3. Pour démarrer Azure-SSIS Runtime Integration, exécutez la commande Start-AzDataFactoryV2IntegrationRuntime :

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name>" `
                                                 -DataFactoryName "<Data Factory Name>" `
                                                 -Name "<Azure SSIS IR Name>" `
                                                 -Force
    ```

> [!NOTE]
> En dehors des réglages personnalisés, ce processus devrait se terminer dans les 5 minutes. Toutefois, il peut prendre de 20 à 30 minutes environ pour le runtime d’intégration Azure-SSIS se joignant à un réseau virtuel.
>
> Si vous utilisez SSISDB, le service ADF se connecte à votre serveur de base de données pour préparer SSISDB. Il configure également les autorisations et les paramètres de votre réseau virtuel, s’ils sont spécifiés, et joint votre runtime d’intégration Azure-SSIS au réseau virtuel.
> 
> Quand vous provisionnez un runtime d’intégration Azure-SSIS, les composants Access Redistributable et Azure Feature Pack pour SSIS sont également installés. Ces composants fournissent la connectivité aux fichiers Excel/Access et à diverses sources de données Azure, en plus des sources de données déjà prises en charge par les composants intégrés. Vous pouvez également installer des composants supplémentaires ; consultez [Configuration personnalisée pour Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md) pour en savoir plus.

## <a name="deploy-ssis-packages"></a>Déployer des packages SSIS

Si vous utilisez SSISDB, vous pouvez déployer vos packages dedans et les exécuter sur le runtime d’intégration Azure-SSIS à l’aide des outils SSDT/SSMS qui se connectent à votre serveur de base de données par le biais de son point de terminaison de serveur.  Pour un serveur Azure SQL Database/Managed Instance avec un point de terminaison privé/Managed instance avec un point de terminaison public, le format de point de terminaison de serveur est `<server name>.database.windows.net`/`<server name>.<dns prefix>.database.windows.net`/`<server name>.public.<dns prefix>.database.windows.net,3342`, respectivement. Si vous n’utilisez pas SSISDB, vous pouvez déployer vos packages dans les systèmes de fichiers/partages de fichiers/Azure Files et les exécuter sur le runtime d’intégration Azure-SSIS à l’aide des utilitaires en ligne de commande `dtinstall`/`dtutil`/`dtexec`. Pour plus d’informations, consultez [Déployer des packages SSIS](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). Dans les deux cas, vous pouvez également exécuter vos packages déployés sur le runtime d’intégration Azure-SSIS à l’aide de l’activité Exécuter le package SSIS dans des pipelines ADF ; consultez [Appeler une exécution de package SSIS comme activité ADF de première classe](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) pour en savoir plus.

## <a name="next-steps"></a>Étapes suivantes

Consultez également d’autres rubriques relatives au runtime d’intégration Azure-SSIS de cette documentation :

- [Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime) (Runtime d’intégration Azure-SSIS). Cet article fournit des informations sur les runtimes d’intégration en général, notamment sur le runtime d’intégration Azure-SSIS.
- [Superviser le runtime d’intégration Azure-SSIS](monitor-integration-runtime.md#azure-ssis-integration-runtime). Cet article vous montre comment récupérer et comprendre des informations sur votre runtime d’intégration Azure-SSIS.
- [Gérer le runtime d’intégration Azure-SSIS](manage-azure-ssis-integration-runtime.md). Cet article vous montre comment arrêter, démarrer ou supprimer votre runtime d’intégration Azure-SSIS. Il vous indique également comment effectuer un scale-out de votre runtime d’intégration Azure-SSIS en ajoutant des nœuds supplémentaires.
- [Joindre le runtime d’intégration Azure-SSIS à un réseau virtuel](join-azure-ssis-integration-runtime-virtual-network.md). Cet article fournit des informations sur la façon de joindre votre runtime d’intégration Azure-SSIS à un réseau virtuel.