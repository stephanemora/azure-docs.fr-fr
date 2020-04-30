---
title: Provisionner le runtime d’intégration Azure-SSIS
description: Découvrez comment provisionner le runtime d’intégration Azure-SSIS dans Azure Data Factory afin de pouvoir déployer et exécuter des packages SSIS dans Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 12/23/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.openlocfilehash: 91962b578f9620384af86d25a33ba35830abb285
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2020
ms.locfileid: "81418624"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory"></a>Provisionner le runtime d’intégration Azure-SSIS dans Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ce tutoriel décrit les différentes étapes d’utilisation du portail Azure pour provisionner un runtime d’intégration Azure-SSIS (SQL Server Integration Services) dans Azure Data Factory. Un IR Azure-SSIS prend en charge :

- L’exécution de packages déployés dans le catalogue SSIS (SSISDB) hébergé par un serveur Azure SQL Database ou une instance managée (modèle de déploiement de projet)
- L’exécution de packages déployés dans les systèmes de fichiers, les partages de fichiers ou Azure Files (modèle de déploiement de package) 

Une fois qu’un IR Azure-SSIS est provisionné, vous pouvez utiliser des outils familiers pour déployer et exécuter vos packages dans Azure. Ces outils incluent SQL Server Data Tools (SDDT), SQL Server Management Studio (SSMS) et des outils en ligne de commande comme `dtinstall`, `dtutil` et `dtexec`.

Pour obtenir des informations conceptuelles sur les runtimes d’intégration (IR) Azure-SSIS, consultez [Vue d’ensemble du runtime d’intégration Azure-SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime).

Dans ce tutoriel, vous allez effectuer les étapes suivantes :

> [!div class="checklist"]
> * Créer une fabrique de données.
> * Approvisionner un runtime d’intégration Azure-SSIS.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Abonnement Azure**. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.
- **Serveur SQL Database (facultatif)** . Si vous n’avez pas encore de serveur de base de données, créez-en un dans le portail Azure avant de commencer. Data Factory crée à son tour une instance SSISDB sur ce serveur de base de données. 

  Nous vous recommandons de créer le serveur de base de données dans la même région Azure que le runtime d’intégration. Cette configuration permet au runtime d’intégration d’écrire des journaux des exécutions dans SSISDB sans dépasser les régions Azure.

  Gardez à l’esprit les points suivants :

  - En fonction du serveur de base de données sélectionné, l’instance SSISDB peut être créée à pour vous en tant que base de données unique, dans le cadre d’un pool élastique ou dans une instance managée. Elle peut être accessible dans un réseau public ou en la joignant à un réseau virtuel. Pour obtenir de l’aide sur le choix du type de serveur de base de données pour héberger SSISDB, consultez [Comparer une base de données Azure SQL Database unique, un pool élastique et une instance managée](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance). 
  
    Si vous utilisez un serveur Azure SQL Database avec des règles de pare-feu IP/points de terminaison de service de réseau virtuel ou une instance managée avec un point de terminaison privé pour héberger le catalogue SSISDB, ou si vous avez besoin d’accéder à des données locales sans configurer un runtime d’intégration (IR) auto-hébergé, vous devez joindre votre Azure-SSIS IR à un réseau virtuel. Pour plus d’informations, consultez [Créer un runtime Azure-SSIS IR dans un réseau virtuel](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

  - Vérifiez que le paramètre **Autoriser l’accès aux services Azure** est activé pour votre serveur de base de données. Ce paramètre ne s’applique pas quand vous utilisez un serveur Azure SQL Database avec des règles de pare-feu IP/points de terminaison de service de réseau virtuel ou une instance managée avec un point de terminaison privé pour héberger le catalogue SSISDB. Pour en savoir plus, consultez [Sécuriser votre base de données Azure SQL](../sql-database/sql-database-security-tutorial.md#create-firewall-rules). Pour savoir comment activer ce paramètre à l’aide de PowerShell, consultez [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).

  - Ajoutez l’adresse IP de l’ordinateur client ou une plage d’adresses IP qui inclut l’adresse IP de l’ordinateur client à la liste d’adresses IP client dans les paramètres de pare-feu du serveur de base de données. Pour plus d’informations, consultez [Règles de pare-feu au niveau du serveur et de la base de données Azure SQL Database](../sql-database/sql-database-firewall-configure.md).

  - Vous pouvez vous connecter au serveur de base de données à l’aide de l’authentification SQL avec vos informations d’identification d’administrateur de serveur ou de l’authentification Azure AD avec l’identité managée de votre fabrique de données. Pour cette dernière, vous devez ajouter l’identité managée de votre fabrique de données à un groupe Azure AD avec autorisations d’accès au serveur de base de données. Pour plus d’informations, consultez [Créer un runtime Azure-SSIS IR avec l’authentification AAD](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

  - Vérifiez que votre serveur de base de données ne dispose pas déjà d’une instance SSISDB. Le provisionnement d’un IR Azure-SSIS ne prend pas en charge l’utilisation d’une instance SSISDB existante.


> [!NOTE]
> Pour obtenir la liste des régions Azure dans lesquelles Data Factory et un runtime Azure-SSIS IR sont actuellement disponibles, consultez [Disponibilité d’Azure Data Factory et du runtime Azure-SSIS IR par région](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all). 

## <a name="create-a-data-factory"></a>Créer une fabrique de données

Pour créer votre fabrique de données à l’aide du portail Azure, suivez les instructions pas à pas dans [Créer une fabrique de données à l’aide de l’interface utilisateur](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory). Durant la procédure, sélectionnez **Épingler au tableau de bord** pour y accéder rapidement après sa création. 

Une fois la fabrique de données créée, ouvrez la page de vue d’ensemble dans le portail Azure. Sélectionnez la vignette **Créer et surveiller** pour ouvrir la page **Prise en main** dans un onglet séparé. Vous pourrez continuer à créer votre IR Azure-SSIS à partir de cette page.

## <a name="create-an-azure-ssis-integration-runtime"></a>Créer un runtime d’intégration Azure-SSIS

### <a name="from-the-data-factory-overview"></a>À partir de la vue d’ensemble de Data Factory

1. Dans la page de **prise en main**, cliquez sur la vignette **Configurer un runtime d’intégration SSIS**. 

   ![Vignette Configurer un runtime d’intégration SSIS](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

1. Consultez la section [Approvisionner un runtime d’intégration Azure-SSIS](#provision-an-azure-ssis-integration-runtime) pour connaître les autres étapes de configuration d’un runtime d’intégration Azure-SSIS. 

### <a name="from-the-authoring-ui"></a>À partir de l’interface utilisateur de création

1. Dans l’interface utilisateur Azure Data Factory, basculez vers l’onglet **Modifier** et sélectionnez **Connexions**. Basculez ensuite vers l’onglet **Runtimes d’intégration** pour voir les runtimes d’intégration existants dans votre fabrique de données. 

   ![Sélections pour l’affichage des runtimes d’intégration existants](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. Sélectionnez **Nouveau** pour créer un runtime d’intégration Azure-SSIS. 

   ![Runtime d’intégration via le menu](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. Dans le panneau **Configuration du runtime d’intégration**, sélectionnez la vignette **Faire une migration lift-and-shift des packages SSIS existants à exécuter dans Azure**, puis sélectionnez **Suivant**. 

   ![Spécifier le type de runtime d’intégration](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Consultez la section [Approvisionner un runtime d’intégration Azure-SSIS](#provision-an-azure-ssis-integration-runtime) pour connaître les autres étapes de configuration d’un runtime d’intégration Azure-SSIS. 

## <a name="provision-an-azure-ssis-integration-runtime"></a>Approvisionner un runtime d’intégration Azure-SSIS

1. Dans la section **Paramètres généraux** du panneau **Configuration du runtime d’intégration**, effectuez les étapes suivantes. 

   ![Paramètres généraux :](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   1. Dans **Nom**, saisissez le nom de votre runtime d’intégration. 

   1. Dans **Description**, saisissez la description de votre runtime d’intégration. 

   1. Pour **Emplacement**, sélectionnez l’emplacement de votre runtime d’intégration. Seuls les emplacements pris en charge sont affichés. Nous vous conseillons de sélectionner le même emplacement que votre serveur de base de données pour héberger SSISDB. 

   1. Pour **Taille du nœud**, sélectionnez la taille du nœud du cluster de votre runtime d’intégration. Seules les tailles de nœuds prises en charge sont affichées. Sélectionnez une taille de nœud importante (scale-up) si vous voulez exécuter de nombreux packages nécessitant beaucoup de calcul ou mémoire. 

   1. Pour **Nombre de nœud**, sélectionnez le nombre de nœuds du cluster de votre runtime d’intégration. Seuls les nombres de nœuds pris en charge sont affichés. Sélectionnez un grand cluster avec de nombreux nœuds (scale-out) si vous voulez exécuter de nombreux packages en parallèle. 

   1. Pour **Édition/Licence**, sélectionnez l’édition SQL Server pour votre runtime d’intégration : Standard ou Entreprise. Sélectionnez Entreprise si vous voulez utiliser les fonctionnalités avancées de votre runtime d’intégration. 

   1. Pour **Économiser de l’argent**, sélectionnez l’option Azure Hybrid Benefit pour votre runtime d’intégration : **Oui** ou **Non**. Sélectionnez **Oui** si vous souhaitez utiliser votre propre licence SQL Server avec Software Assurance pour faire des économies grâce à une utilisation hybride. 

   1. Sélectionnez **Suivant**. 

1. Dans la section **Paramètres SQL**, effectuez les étapes suivantes. 

   ![Paramètres SQL](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   1. Cochez la case **Créer un catalogue SSIS (SSISDB) hébergé par un serveur Azure SQL Database/Managed Instance pour stocker vos projets/packages/environnements/journaux d’exécution** pour choisir le modèle de déploiement des packages à exécuter sur votre Azure-SSIS IR. Vous allez choisir le modèle de déploiement de projet dans lequel les packages sont déployés dans SSISDB hébergé par votre serveur de base de données ou le modèle de déploiement de package dans lequel les packages sont déployés dans les systèmes de fichiers, les partages de fichiers ou Azure Files.
   
      Si vous cochez la case, vous devez apporter votre propre serveur de base de données pour héberger la base de données SSISDB que nous allons créer et gérer à votre place.
   
      1. Pour **Abonnement**, sélectionnez l’abonnement Azure comprenant votre serveur de base de données pour héberger la base de données SSISDB. 

      1. Pour **Emplacement**, sélectionnez l’emplacement de votre serveur de base de données pour héberger la base de données SSISDB. Nous vous recommandons de sélectionner le même emplacement que celui de runtime d’intégration.

      1. Pour **Point de terminaison du serveur de base de données du catalogue**, sélectionnez le point de terminaison de votre serveur de base de données pour héberger SSISDB. 
   
         En fonction du serveur de base de données sélectionné, l’instance SSISDB peut être créée à pour vous en tant que base de données unique, dans le cadre d’un pool élastique ou dans une instance managée. Elle peut être accessible dans un réseau public ou en la joignant à un réseau virtuel. Pour obtenir de l’aide sur le choix du type de serveur de base de données pour héberger SSISDB, consultez [Comparer une base de données Azure SQL Database unique, un pool élastique et une instance managée](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance).   

         Si vous sélectionnez un serveur Azure SQL Database avec des règles de pare-feu IP/points de terminaison de service de réseau virtuel ou une instance managée avec un point de terminaison privé pour héberger le catalogue SSISDB, ou si vous avez besoin d’accéder à des données locales sans configurer un runtime d’intégration (IR) auto-hébergé, vous devez joindre votre Azure-SSIS IR à un réseau virtuel. Pour plus d’informations, consultez [Créer un runtime Azure-SSIS IR dans un réseau virtuel](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

      1. Cochez la case **Utiliser l’authentification AAD avec l’identité managée pour votre ADF** afin de choisir la méthode d’authentification de votre serveur de base de données pour héberger le catalogue SSISDB. Vous choisissez l’authentification SQL ou l’authentification Azure AD avec l’identité managée de votre fabrique de données.

         Si vous cochez la case, vous devez ajouter l’identité managée de votre fabrique de données à un groupe Azure AD avec autorisations d’accès à votre serveur de base de données. Pour plus d’informations, consultez [Créer un runtime Azure-SSIS IR avec l’authentification AAD](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
   
      1. Pour **Nom d’utilisateur administrateur**, entrez le nom d’utilisateur d’authentification SQL de votre serveur de base de données pour héberger le catalogue SSISDB. 

      1. Pour **Mot de passe administrateur**, entrez le mot de passe d’authentification SQL de votre serveur de base de données pour héberger le catalogue SSISDB. 

      1. Pour **Niveau de service de base de données de catalogue**, sélectionnez le niveau de service de votre serveur de base de données pour héberger le catalogue SSISDB. Sélectionnez le niveau De base, Standard ou Premium, ou sélectionnez un nom de pool élastique.

      1. Sélectionnez **Test Connection** (Tester la connexion). Si le test réussit, sélectionnez **Suivant**. 

1. Dans la section **Paramètres avancés**, effectuez les étapes suivantes. 

   ![Paramètres avancés](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   1. Pour **Exécutions parallèles maximales par nœud**, sélectionnez le nombre maximal de packages exécutables simultanément par nœud au sein de votre cluster de runtime d’intégration. Seuls les numéros des packages pris en charge sont affichés. Sélectionnez un petit nombre si vous souhaitez utiliser plusieurs cœurs pour exécuter un seul package volumineux nécessitant beaucoup de calcul ou de mémoire. Sélectionnez un nombre élevé si vous souhaitez exécuter un ou plusieurs petits packages dans un seul cœur. 

   1. Cochez la case **Personnaliser votre Azure-SSIS Integration Runtime avec des installations de composants/configurations système supplémentaires** pour indiquer si vous souhaitez ajouter des configurations personnalisées standard/Express sur votre Azure-SSIS IR. Pour plus d’informations, consultez [Personnalisation de l’installation du runtime d’intégration Azure-SSIS](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).
   
   1. Cochez la case **Sélectionner un réseau virtuel à joindre à votre Azure-SSIS Integration Runtime, autoriser ADF à créer certaines ressources réseau et apporter éventuellement vos propres adresses IP publiques statiques** pour indiquer si vous souhaitez joindre votre Azure-SSIS IR à un réseau virtuel.

      Cochez cette case si vous utilisez un serveur Azure SQL Database avec des règles de pare-feu IP/points de terminaison de service de réseau virtuel ou une instance managée avec un point de terminaison privé pour héberger SSISDB, ou si vous avez besoin d’accéder à des données locales sans configurer un runtime d’intégration auto-hébergé. Pour plus d’informations, consultez [Créer un runtime Azure-SSIS IR dans un réseau virtuel](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 
   
   1. Cochez la case **Configurer le runtime d’intégration auto-hébergé en tant que proxy pour votre Azure-SSIS Integration Runtime** pour indiquer si vous voulez configurer un runtime d’intégration auto-hébergé en tant que proxy pour votre Azure-SSIS IR. Pour plus d’informations, consultez [Configurer un runtime d’intégration auto-hébergé en tant que proxy](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis).   

   1. Sélectionnez **Continuer**. 

1. Dans la section **Résumé**, passez en revue tous les paramètres de provisionnement, ajoutez les liens de documentation recommandés aux favoris, puis sélectionnez **Terminer** pour lancer la création de votre runtime d’intégration. 

   > [!NOTE]
   > En dehors des réglages personnalisés, ce processus devrait se terminer dans les 5 minutes.
   >
   > Si vous utilisez le catalogue SSISDB, le service Data Factory se connecte à votre serveur de base de données pour le préparer. 
   > 
   > Quand vous provisionnez un runtime d’intégration Azure-SSIS, les composants Access Redistributable et Azure Feature Pack pour SSIS sont également installés. Ces composants assurent la connectivité aux fichiers Excel et Access ainsi qu’à diverses sources de données Azure, en plus des sources de données déjà prises en charge par les composants intégrés. Pour plus d’informations sur les autres composants que vous pouvez installer, consultez [Personnalisation de l’installation du runtime d’intégration Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

1. Dans l’onglet **Connexions**, basculez vers **Runtimes d’intégration** si nécessaire. Sélectionnez **Actualiser** pour actualiser l’état. 

   ![État de la création avec bouton Actualiser](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

1. Utilisez les liens sous la colonne **Actions** pour arrêter/démarrer, modifier ou supprimer le runtime d’intégration. Le dernier lien permet d’afficher le code JSON pour le runtime d’intégration. Les boutons Modifier et Supprimer sont activés uniquement lorsque le runtime d’intégration est arrêté. 

   ![Liens de la colonne Actions](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png) 

## <a name="deploy-ssis-packages"></a>Déployer des packages SSIS

Si vous utilisez le catalogue SSISDB, vous pouvez y déployer vos packages et les exécuter sur Azure-SSIS IR à l’aide des outils SQL Server Data Tools (SSDT) ou SQL Server Management Studio (SSMS). Ces outils se connectent à votre serveur de base de données via son point de terminaison : 

- Pour un serveur Azure SQL Database, le format du point de terminaison de serveur est `<server name>.database.windows.net`.
- Pour une instance managée avec un point de terminaison privé, le format du point de terminaison de serveur est `<server name>.<dns prefix>.database.windows.net`.
- Pour une instance managée avec un point de terminaison public, le format du point de terminaison de serveur est `<server name>.public.<dns prefix>.database.windows.net,3342`. 

Si vous n’utilisez pas le catalogue SSISDB, vous pouvez déployer vos packages sur des systèmes de fichiers, des partages de fichiers ou Azure Files et les exécuter sur Azure-SSIS IR à l’aide des outils en ligne de commande `dtinstall`, `dtutil` et `dtexec`. Pour plus d’informations, consultez [Déployer des packages SSIS](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). 

Dans les deux cas, vous pouvez également exécuter vos packages déployés sur Azure-SSIS IR à l’aide de l’activité Exécuter le package SSIS dans des pipelines Data Factory. Pour plus d’informations, consultez [Appeler une exécution de package SSIS comme activité Data Factory de première classe](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

Consultez également la documentation SSIS suivante : 

- [Déployer, exécuter et surveiller des packages SSIS dans Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial) 
- [Se connecter à SSISDB dans Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database) 
- [Planifier les exécutions de packages sur Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages) 
- [Se connecter aux sources de données locales avec l’authentification Windows](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la personnalisation de votre runtime d’intégration Azure-SSIS, passez à l’article suivant : 

> [!div class="nextstepaction"]
> [Personnaliser un runtime Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)