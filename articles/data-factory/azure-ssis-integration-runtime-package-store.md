---
title: Gérer les packages avec le magasin de packages Azure-SSIS Integration Runtime
description: Découvrez comment gérer des packages avec le magasin de packages Azure-SSIS Integration Runtime.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 05/25/2020
ms.openlocfilehash: fa1ab60ae5690bd3782e4c849ca7cb5c29d640c2
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2020
ms.locfileid: "84198866"
---
# <a name="manage-packages-with-azure-ssis-integration-runtime-package-store"></a>Gérer les packages avec le magasin de packages Azure-SSIS Integration Runtime

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Pour promouvoir et insérer des charges de travail SQL Server Integration Services (SSIS) locales dans le cloud, vous pouvez configurer Azure-SSIS Integration Runtime (IR) dans Azure Data Factory (ADF). Pour plus d’informations, consultez [Configurer un IR Azure-SSIS](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure). Un IR Azure-SSIS prend en charge :

- L’exécution de packages déployés dans le catalogue SSIS (SSISDB) hébergé par un serveur Azure SQL Database/Managed Instance (modèle de déploiement de projet)
- L’exécution de packages déployés dans le système de fichiers, Azure Files ou une base de données SQL Server (MSDB) hébergée par Azure SQL Managed Instance (modèle de déploiement de package)

Lorsque vous utilisez un modèle de déploiement de package, vous pouvez choisir de configurer votre IR Azure-SSIS avec des magasins de packages qui fournissent une couche de gestion des packages au sommet du système de fichiers/Azure Files/MSDB hébergé par Azure SQL Managed Instance. Le magasin de packages Azure-SSIS IR vous permet d’importer/exporter/supprimer/exécuter des packages et superviser/arrêter l’exécution de packages par le biais de SQL Server Management Studio (SSMS), à l’image du [magasin de packages SSIS hérités](https://docs.microsoft.com/sql/integration-services/service/package-management-ssis-service?view=sql-server-2017). 

## <a name="connect-to-azure-ssis-ir"></a>Se connecter à l’IR Azure-SSIS

Une fois votre IR Azure-SSIS configuré, vous pouvez vous y connecter et parcourir ses magasins de packages sur SSMS.

![Se connecter à l’IR Azure-SSIS](media/azure-ssis-integration-runtime-package-store/ssms-package-store-connect.png)

Dans la fenêtre **Explorateur d’objets** de SSMS, sélectionnez **Azure-SSIS Integration Runtime** dans le menu déroulant **Se connecter**. Connectez-vous ensuite à Azure et sélectionnez l’abonnement, l’ADF et l’IR Azure-SSIS appropriés auxquels vos magasins de packages sont attachés. Votre IR Azure-SSIS s’affiche au-dessus des nœuds **Exécution des packages** et **Packages stockés**. Développez le nœud **Packages stockés** pour afficher vos magasins de packages sous-jacents. Développez vos magasins de packages pour afficher les dossiers et les packages sous-jacents. Vous pouvez être invité à entrer les informations d’identification d’accès pour vos magasins de packages, si SSMS ne parvient pas à s’y connecter automatiquement. Par exemple, si vous développez un magasin de packages en plus de MSDB, vous pouvez être invité à vous connecter d’abord à votre Managed Instance Azure SQL.

![Se connecter à Azure SQL Managed Instance](media/azure-ssis-integration-runtime-package-store/ssms-package-store-connect2.png)

## <a name="manage-folders-and-packages"></a>Gérer des dossiers et des packages

Lorsque vous parcourez votre IR Azure-SSIS sur SSMS, vous pouvez cliquer avec le bouton droit sur des magasins/dossiers/packages pour afficher un menu puis sélectionner **Nouveau dossier**, **Importer un package**, **Exporter un package**, **Supprimer** ou **Actualiser**.

   ![Gérer des dossiers et des packages](media/azure-ssis-integration-runtime-package-store/ssms-package-store-manage.png)

   *  Sélectionnez **Nouveau dossier** afin de créer un dossier pour les packages importés.

   *  Sélectionnez **Importer un package** pour importer dans votre magasin de packages des packages provenant du **système de fichiers**, de **SQL Server** (MSDB) ou du **magasin de packages SSIS** hérité.

      ![Importer un package](media/azure-ssis-integration-runtime-package-store/ssms-package-store-import.png)

      Selon **l’emplacement du package** d’importation, sélectionnez **Serveur**/**Type d’authentification**, entrez les informations d’identification si nécessaire, spécifiez le **chemin du package**, puis entrez le nouveau **nom du package**. Lorsque vous importez des packages, leur niveau de protection ne peut pas être modifié. Pour le modifier, utilisez SQL Server Data Tools (SSDT) ou l’utilitaire de ligne de commande `dtutil`.

   *  Sélectionnez **Exporter un package** pour exporter des packages de votre magasin de packages vers le **système de fichiers**, **SQL Server** (MSDB) ou le **magasin de packages SSIS** hérité.

      ![Exporter un package](media/azure-ssis-integration-runtime-package-store/ssms-package-store-export.png)

      Selon **l’emplacement du package** d’exportation, sélectionnez **Serveur**/**Type d’authentification**, entrez les informations d'identification si nécessaire, puis sélectionnez le **chemin du package**. Lorsque vous exportez des packages, s’ils sont chiffrés, entrez d’abord les mots de passe pour les déchiffrer, puis modifiez le cas échéant leur niveau de protection, par exemple, pour ne pas enregistrer des données sensibles ou pour chiffrer toutes les données/données sensibles avec la clé/le mot de passe de l’utilisateur.

   *  Sélectionnez **Supprimer** pour supprimer les dossiers/packages existants de votre magasin de packages.

   *  Sélectionnez **Actualiser** pour afficher les dossiers/packages récemment ajoutés à votre magasin de packages.

## <a name="execute-packages"></a>Exécuter des packages

Lorsque vous parcourez votre IR Azure-SSIS sur SSMS, vous pouvez cliquer avec le bouton droit sur n’importe quel package stocké pour afficher un menu et sélectionner **Exécuter le package**.  Cette opération ouvre la boîte de dialogue **Utilitaire d’exécution de package** dans laquelle vous pouvez configurer les exécutions de packages sur l’IR Azure-SSIS en tant qu’activités Exécuter le Package SSIS dans des pipelines ADF.

![Utilitaire d'exécution de package, pages 1 et 2](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute.png)

![Utilitaire d'exécution de package, pages 3 et 4](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute2.png)

Les pages **Général**, **Configurations**, **Options d’exécution** et **Journalisation** de la boîte de dialogue **Utilitaire d'exécution de package** correspondent à l’onglet **Paramètres** de l’activité Exécuter le Package SSIS, où vous pouvez entrer le mot de passe de chiffrement de votre package, les informations d'accès à votre fichier de configuration de package, les références et les propriétés de l'exécution du package, et les informations d'accès à votre dossier de journalisation.  La page **Valeurs définies** de la boîte de dialogue **Utilitaire d'exécution de package** correspond à l’onglet **Substitutions de propriété** de l’activité Execute SSIS Package, où vous pouvez entrer les propriétés de votre package existant à substituer. Pour plus d’informations, consultez [Exécuter des packages SSIS avec des activités Exécuter le Package SSIS dans des pipelines ADF](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

Lorsque vous sélectionnez le bouton **Exécuter** de la boîte de dialogue **Utilitaire d'exécution de package**, un nouveau pipeline ADF avec l’activité Exécuter le Package SSIS sera automatiquement généré puis déclenché. Si un pipeline ADF avec les mêmes paramètres d’exécution de package existe déjà, il sera réexécuté et aucun nouveau pipeline ne sera généré. Le pipeline ADF et l’activité Exécuter le Package SSIS seront nommés `Pipeline_SSMS_YourPackageName_HashString` et `Activity_SSMS_YourPackageName`, respectivement.

![Utilitaire d'exécution de package, bouton](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute3.png)

![Activité Exécuter le package SSIS](media/azure-ssis-integration-runtime-package-store/ssis-activity-package-store.png)

## <a name="monitor-and-stop-running-packages"></a>Analyser et arrêter des packages en cours d'exécution

Lorsque vous parcourez votre IR Azure-SSIS sur SSMS, vous pouvez développer le nœud **Exécution des packages** pour afficher les packages en cours d’exécution sous-jacents.  Cliquez avec le bouton droit sur l’un d’eux pour afficher un menu, puis sélectionnez **Arrêter** ou **Actualiser**.

   ![Analyser et arrêter des packages en cours d'exécution](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor.png)

   *  Sélectionnez **Arrêter** pour annuler le pipeline ADF en cours d’exécution qui exécute le package en tant qu’activité Exécuter le Package SSIS.

   *  Sélectionnez **Actualiser** pour afficher les packages qui viennent d’être exécutés à partir de vos magasins de packages.

## <a name="monitor-azure-ssis-ir-and-edit-package-stores"></a>Analyser l’IR Azure-SSIS et modifier des magasins de packages

Lorsque vous parcourez votre IR Azure-SSIS sur SSMS, vous pouvez cliquer dessus avec le bouton droit pour afficher un menu et sélectionner **Accéder au portail Azure Data Factory** ou **Actualiser**.

   ![Accéder au portail ADF](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor2.png)

   *  Sélectionnez **Accédez au portail Azure Data Factory** pour ouvrir la page **Runtimes d’intégration** du hub d’analyse ADF, où vous pouvez analyser votre IR Azure-SSIS. Dans la vignette **Magasins de packages**, vous pouvez voir le nombre de magasins de packages attachés à votre IR Azure-SSIS.  Si vous sélectionnez ce nombre, une fenêtre s’affiche dans laquelle vous pouvez modifier les services liés ADF qui stockent les informations d’accès de vos magasins de packages.

      ![Modifier des magasins de packages](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor3.png)

   *  Sélectionnez **Actualiser** pour afficher les dossiers/packages récemment ajoutés dans vos magasins de packages et les packages en cours d’exécution dans vos magasins de packages.

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez réexécuter/modifier les pipelines ADF générés automatiquement avec des activités Exécuter le Package SSIS, ou en créer d’autres sur le portail ADF. Pour plus d’informations, consultez [Exécuter des packages SSIS avec des activités Exécuter le Package SSIS dans des pipelines ADF](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).