---
title: Redéployer des packages SSIS sur une base de données SQL unique
titleSuffix: Azure Database Migration Service
description: Découvrez comment migrer ou redéployer des packages et projets SQL Server Integration Services vers une base de données Azure SQL Database unique à l’aide d’Azure Database Migration Service et de l’Assistant Migration de données.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: 90a39b8fe3604a05f1d35a875ae4e34491b47d72
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77648527"
---
# <a name="redeploy-ssis-packages-to-azure-sql-database-with-azure-database-migration-service"></a>Redéployer des packages SSIS sur une base de données Azure SQL Database avec Azure Database Migration Service

Si vous utilisez SQL Server Integration Services (SSIS) et souhaitez migrer vos projets/packages SSIS à partir de la source SSISDB hébergée par SQL Server vers la destination SSISDB hébergée par Azure SQL Database, vous pouvez les redéployer à l’aide de l’Assistant Déploiement d’Integration Services. Vous pouvez lancer l’Assistant à partir de SQL Server Management Studio (SSMS).

Si la version de SSIS que vous utilisez est antérieure à 2012, avant de redéployer vos projets/packages SSIS dans le modèle de déploiement de projet, vous devez les convertir à l’aide de l’Assistant Conversion de projet d’Integration Services, qui peut également être lancé à partir de SSMS. Pour plus d’informations, voir l’article [Conversion de projets en modèle de déploiement de projet](https://docs.microsoft.com/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-2017#convert).

> [!NOTE]
> Azure Database Migration Service (DMS) ne prend actuellement pas en charge la migration d’une SSISDB source vers un serveur Azure SQL Database, mais vous pouvez redéployer vos projets/packages SSIS en procédant comme suit.

Dans cet article, vous apprendrez comment :
> [!div class="checklist"]
>
> * Évaluer des projets/packages SSIS sources.
> * Migrer des projets/packages SSIS vers Azure.

## <a name="prerequisites"></a>Prérequis

Pour effectuer cette procédure, vous avez besoin de :

* SSMS version 17.2 ou ultérieure.
* Une instance de votre serveur de base de données cible pour héberger SSISDB. Si vous n’en avez pas déjà un, créez un serveur Azure SQL Database (sans base de données) à l’aide du portail Azure en accédant au [formulaire](https://ms.portal.azure.com/#create/Microsoft.SQLServer) SQL Server (serveur logique uniquement).
* SSIS doit être approvisionné dans une instance Azure Data Factory (ADF) contenant Azure-SSIS Integration Runtime avec la SSISDB de destination hébergée par l’instance du serveur Azure SQL Database (comme décrit dans l’article [Provisionner le runtime d’intégration Azure-SSIS dans Azure Data Factory](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)).

## <a name="assess-source-ssis-projectspackages"></a>Évaluer des projets/packages SSIS sources

Bien que l’évaluation de la SSISDB source ne soit pas encore intégrée dans l’Assistant Migration de base de données (DMA) ou Azure Database Migration Service (DMS), vos projets/packages SSIS seront évalués/validés lors de leur redéploiement vers la SSISDB de destination hébergée sur un serveur Azure SQL Database.

## <a name="migrate-ssis-projectspackages"></a>Migrer des projets/packages SSIS

Pour migrer des projets/packages SSIS vers un serveur Azure SQL Database, procédez comme suit.

1. Ouvrez SSMS, puis sélectionnez **Options** pour afficher la boîte de dialogue **Se connecter au serveur**.

2. Sous l’onglet **Connexion**, spécifiez les informations nécessaires pour se connecter au serveur Azure SQL Database qui hébergera la SSISDB de destination.

    ![Onglet Connexion SSIS](media/how-to-migrate-ssis-packages/dms-ssis-login-tab.png)

3. Sous l’onglet **Propriétés de connexion**, dans la zone de texte **Se connecter à une base de données**, sélectionnez ou entrez **SSISDB**, puis choisissez **Se connecter**.

    ![Onglet Propriétés de connexion SSIS](media/how-to-migrate-ssis-packages/dms-ssis-conncetion-properties-tab.png)

4. Dans l’Explorateur d’objets SSMS, développez le nœud **Catalogues Integration Services**, développez **SSISDB**, puis, s’il n’y a aucun dossier, cliquez avec le bouton droit sur **SSISDB**, puis créez un dossier.

5. Sous **SSISDB**, développez un dossier quelconque, cliquez avec le bouton droit sur **Projets**, puis sélectionnez **Déployer le projet**.

    ![Nœud SSIS SSISDB développé](media/how-to-migrate-ssis-packages/dms-ssis-ssisdb-node-expanded.png)

6. Dans l’Assistant Déploiement d’Integration Services, dans la page **Introduction**, examiner les informations, puis sélectionnez **Suivant**.

    ![Page d’Introduction de l’Assistant Déploiement](media/how-to-migrate-ssis-packages/dms-deployment-wizard-introduction-page.png)

7. Dans la page **Sélectionner une source**, spécifiez le projet SSIS existant à déployer.

    Si SSMS est également connecté au SQL Server hébergeant la SSISDB source, sélectionnez **Catalogue Integration Services**, puis entrez le nom du serveur et le chemin d’accès du projet dans votre catalogue pour déployer votre projet directement.

    Vous pouvez également sélectionner **Fichier de déploiement de projet**, puis spécifier le chemin d’accès d’un fichier de déploiement de projet existant (.ispac) pour déployer votre projet.

    ![Page Sélectionner la source de l’Assistant Déploiement](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-source-page.png)
 
8. Sélectionnez **Suivant**.
9. Dans la page **Sélectionner la destination**, spécifiez la destination pour votre projet.

    a. Dans la zone de texte Nom du serveur, entrez le nom du serveur Azure SQL Database complet (<nom_serveur>.database.windows.net).

    b. Fournissez les informations d’authentification, puis sélectionnez **Connexion**.

    ![Page Sélectionner la destination de l’Assistant Déploiement](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-destination-page.png)

    c. Sélectionnez **Parcourir** pour spécifier le dossier de destination dans SSISDB, puis sélectionnez **Suivant**.

    > [!NOTE]
    > Le bouton **Suivant** est activé uniquement après sélection de **Connexion**.

10. Dans la page **Valider**, affichez les erreurs/avertissements et, si nécessaire, modifiez vos packages en conséquence.

    ![Page Valider de l’Assistant Déploiement](media/how-to-migrate-ssis-packages/dms-deployment-wizard-validate-page.png)

11. Sélectionnez **Suivant**.

12. Dans la page **Révision**, examinez vos paramètres de déploiement.

    > [!NOTE]
    > Vous pouvez modifier vos paramètres en sélectionnant **Précédent** ou en choisissant un lien d’étape dans le volet gauche.

13. Sélectionnez **Déployer** pour démarrer le processus de déploiement.

14. Une fois le processus de déploiement terminé, vous pouvez afficher la page Résultats qui indique la réussite ou l’échec de chaque action de déploiement.
    a. Si une action a échoué, dans la colonne **Résultat**, sélectionnez **Échec** pour afficher une explication de l’erreur.
    b. Vous pouvez également sélectionner **Enregistrer le rapport** pour enregistrer les résultats dans un fichier XML.

15. Sélectionnez **Fermer** pour quitter l’Assistant Déploiement d’Integration Services.

Si le déploiement de votre projet réussit sans défaillance, vous pouvez sélectionner tous les packages qu’il contient pour s’exécuter sur votre IR Azure-SSIS IR.

## <a name="next-steps"></a>Étapes suivantes

* Lisez l’aide à la migration du [Guide de migration de bases de données](https://datamigration.microsoft.com/) de Microsoft.
