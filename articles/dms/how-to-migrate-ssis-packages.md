---
title: Migrer des packages SQL Server Integration Services vers Azure | Microsoft Docs
description: Découvrez comment Migrer des packages SQL Server Integration Services vers Azure.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 03/12/2019
ms.openlocfilehash: 884af4624c1e92ee765353c90fd189220664381d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60532376"
---
# <a name="migrate-sql-server-integration-services-packages-to-azure"></a>Migrer des packages SQL Server Integration Services vers Azure
Si vous utilisez SQL Server Integration Services (SSIS) et souhaitez migrer vos projets/packages SSIS à partir de la source SSISDB hébergée par SQL Server vers la destination SSISDB hébergée par un serveur Azure SQL Database SQL Database ou une Azure SQL Database Managed Instance, vous pouvez les redéployer à l’aide de l’Assistant Déploiement d’Integration Services. Vous pouvez lancer l’Assistant à partir de SQL Server Management Studio (SSMS).

Si la version de SSIS que vous utilisez est antérieure à 2012, avant de redéployer vos projets/packages SSIS dans le modèle de déploiement de projet, vous devez les convertir à l’aide de l’Assistant Conversion de projet d’Integration Services, qui peut également être lancé à partir de SSMS. Pour plus d’informations, voir l’article [Conversion de projets en modèle de déploiement de projet](https://docs.microsoft.com/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-2017#convert).

> [!NOTE]
> Azure Database Migration Service (DMS) ne prend actuellement pas en charge la migration d’une SSISDB source, mais vous pouvez redéployer vos projets/packages SSIS en procédant comme suit. 

Dans cet article, vous apprendrez comment :
> [!div class="checklist"]
> * Évaluer des projets/packages SSIS sources.
> * Migrer des projets/packages SSIS vers Azure.

## <a name="prerequisites"></a>Conditions préalables
Pour effectuer cette procédure, vous avez besoin de :

- SSMS version 17.2 ou ultérieure.
- Une instance de votre serveur de base de données cible pour héberger SSISDB.
 
  Si vous n’en avez pas encore :
    - Pour Azure SQL Database, créez un serveur Azure SQL Database (sans base de données) à l’aide du portail Microsoft Azure en accédant au [formulaire](https://ms.portal.azure.com/#create/Microsoft.SQLServer) SQL Server (serveur logique uniquement).
    - Pour Azure SQL Database Managed Instance, suivez les indications détaillées de l’article [Créer une Azure SQL Database Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started).

- SSIS doit être approvisionné dans un Azure Data Factory (ADF) contenant Azure-SSIS Integration Runtime avec la SSISDB de destination hébergée par l’instance de serveur Azure SQL DatabaseAzure SQL Database (comme décrit dans l’article [Approvisionner l’Azure-SSIS Integration Runtime dans Azure Data Factory](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)) ou Azure SQL Database Managed Instance (comme décrit dans l’article [Créer l’Azure-SSIS Integration Runtime dans Azure Data Factory](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)). 

## <a name="assess-source-ssis-projectspackages"></a>Évaluer des projets/packages SSIS sources
Bien que l’évaluation de la SSISDB source ne soit pas encore intégrée dans l’Assistant Migration de base de données (DMA) ou l’Azure Database Migration Service (DMS), vos projets/packages SSIS seront évalués/validés lors de leur redéploiement vers la SSISDB de destination hébergée sur un serveur Azure SQL Database ou une Azure SQL Database Managed Instance.

## <a name="migrate-ssis-projectspackages"></a>Migrer des projets/packages SSIS
Pour migrer des projets/packages SSIS vers un serveur Azure SQL Database ou une Azure SQL Database Managed Instance, procédez comme suit.

1.  Ouvrez SSMS, puis sélectionnez **Options** pour afficher la boîte de dialogue **Se connecter au serveur**.

2.  Sous l’onglet **Connexion**, spécifiez les informations nécessaires pour se connecter au serveur Azure SQL Database ou à l’Azure SQL Database Managed Instance hébergeant la SSISDB de destination.

    ![Onglet Connexion SSIS](media/how-to-migrate-ssis-packages/dms-ssis-login-tab.png)
 
3.  Sous l’onglet **Propriétés de connexion**, dans la zone de texte **Se connecter à une base de données**, sélectionnez ou entrez **SSISDB**, puis choisissez **Se connecter**.

    ![Onglet Propriétés de connexion SSIS](media/how-to-migrate-ssis-packages/dms-ssis-conncetion-properties-tab.png)

4.  Dans l’Explorateur d’objets SSMS, développez le nœud **Catalogues Integration Services**, développez **SSISDB**, puis, s’il n’y a aucun dossier, cliquez avec le bouton droit sur **SSISDB**, puis créez un dossier.

5.  Sous **SSISDB**, développez un dossier quelconque, cliquez avec le bouton droit sur **Projets**, puis sélectionnez **Déployer le projet**.

    ![Nœud SSIS SSISDB développé](media/how-to-migrate-ssis-packages/dms-ssis-ssisdb-node-expanded.png)

6.  Dans l’Assistant Déploiement d’Integration Services, dans la page **Introduction**, examiner les informations, puis sélectionnez **Suivant**.

    ![Page d’Introduction de l’Assistant Déploiement](media/how-to-migrate-ssis-packages/dms-deployment-wizard-introduction-page.png)

7.  Dans la page **Sélectionner une source**, spécifiez le projet SSIS existant à déployer.

    Si SSMS est également connecté au SQL Server hébergeant la SSISDB source, sélectionnez **Catalogue Integration Services**, puis entrez le nom du serveur et le chemin d’accès du projet dans votre catalogue pour déployer votre projet directement.

    Vous pouvez également sélectionner **Fichier de déploiement de projet**, puis spécifier le chemin d’accès d’un fichier de déploiement de projet existant (.ispac) pour déployer votre projet.

    ![Page Sélectionner la source de l’Assistant Déploiement](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-source-page.png)
 
8.  Sélectionnez **Suivant**.
9.  Dans la page **Sélectionner la destination**, spécifiez la destination pour votre projet.

       a. Dans la zone de texte Nom du serveur, entrez le nom complet du serveur Azure SQL Database (<nom_serveur>. database.windows.net) ou le nom de l’Azure SQL Database Managed Instance (<nom_serveur.préfixe_dns>. database.windows.net).
 
       b. Fournissez les informations d’authentification, puis sélectionnez **Connexion**.
    
       ![Page Sélectionner la destination de l’Assistant Déploiement](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-destination-page.png)

    c. Sélectionnez Parcourir pour spécifier le dossier de destination dans SSISDB, puis choisissez Suivant.

    > [!NOTE]
    > Le bouton **Suivant** est activé uniquement après sélection de **Connexion**. 

10. Dans la page **Valider**, affichez les erreurs/avertissements et, si nécessaire, modifiez vos packages en conséquence.

    ![Page Valider de l’Assistant Déploiement](media/how-to-migrate-ssis-packages/dms-deployment-wizard-validate-page.png)

11. Sélectionnez **Suivant**.

12. Dans la page **Révision**, examinez vos paramètres de déploiement.

    > [!NOTE]
    > Vous pouvez modifier vos paramètres en sélectionnant Précédent ou en choisissant un lien d’étape dans le volet gauche.

13. Sélectionnez **Déployer** pour démarrer le processus de déploiement.

14. Une fois le processus de déploiement terminé, vous pouvez afficher la page Résultats qui indique la réussite ou l’échec de chaque action de déploiement.
    a. Si une action a échoué, dans la colonne **Résultat**, sélectionnez **Échec** pour afficher une explication de l’erreur.
    b. Vous pouvez également sélectionner **Enregistrer le rapport** pour enregistrer les résultats dans un fichier XML.

15. Sélectionnez **Fermer** pour quitter l’Assistant Déploiement d’Integration Services.

Si le déploiement de votre projet réussit sans défaillance, vous pouvez sélectionner tous les packages qu’il contient pour s’exécuter sur votre IR Azure-SSIS IR.

## <a name="next-steps"></a>Étapes suivantes
- Lisez l’aide à la migration du [Guide de migration de bases de données](https://datamigration.microsoft.com/) de Microsoft.