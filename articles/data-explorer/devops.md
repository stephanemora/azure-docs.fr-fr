---
title: Tâche pour l’Explorateur de données Azure DevOps Azure
description: Dans cette rubrique, vous allez créer un pipeline de versions et le déploiement
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 05/05/2019
ms.openlocfilehash: a70a887ccb19d9c1cbdb5f8ebf6aa8d4b25a0dfd
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65161069"
---
# <a name="azure-devops-task-for-azure-data-explorer"></a>Tâche DevOps Azure pour Explorer des données Azure

[Les Services Azure DevOps](https://azure.microsoft.com/services/devops/) fournit des outils de collaboration tels que les pipelines de hautes performances, dépôts Git privés et gratuits, des tableaux Kanban configurables et fonctionnalités étendues de tests automatisées et continues de développement. [Les Pipelines Azure](https://azure.microsoft.com/services/devops/pipelines/) est une fonctionnalité Azure DevOps qui vous permet de gérer CI/CD pour déployer votre code avec des pipelines haute performance qui fonctionnent avec n’importe quel langage, la plate-forme et le cloud.
[Explorateur de données Azure - commandes d’administration](https://marketplace.visualstudio.com/items?itemName=Azure-Kusto.PublishToADX) est la tâche de Pipelines d’Azure qui vous permet de créer des pipelines de lancement et de déployer votre base de données passe à vos bases de données de l’Explorateur de données Azure. Il est disponible gratuitement dans le [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

Ce document décrit un exemple simple de l’utilisation de la **Explorateur de données Azure – commandes d’administration** modification de la tâche pour déployer votre schéma vers votre base de données. Pour les pipelines CI/CD complets, consultez [documentation d’Azure DevOps](/azure/devops/user-guide/what-is-azure-devops?view=azure-devops#vsts).

## <a name="prerequisites"></a>Conditions préalables

* Si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer.
* Configuration de Cluster de l’Explorateur de données Azure :
    * Un [cluster de l’Explorateur de données Azure et base de données](/azure/data-explorer/create-cluster-database-portal)
    * Créer l’application Azure Active Directory (Azure AD) par [mise en service une application Azure AD](/azure/kusto/management/access-control/how-to-provision-aad-app).
    * Accorder l’accès à votre application Azure AD sur votre base de données de l’Explorateur de données Azure par [la gestion des autorisations de base de données de l’Explorateur de données Azure](/azure/data-explorer/manage-database-permissions).
* Azure DevOps le programme d’installation :
    * [S’inscrire pour une organisation gratuite](/azure/devops/user-guide/sign-up-invite-teammates?view=azure-devops)
    * [Création d’une organisation](/azure/devops/organizations/accounts/create-organization?view=azure-devops)
    * [Créez un projet dans Azure DevOps](/azure/devops/organizations/projects/create-project?view=azure-devops)
    * [Code avec Git](/azure/devops/user-guide/code-with-git?view=azure-devops)

## <a name="create-folders"></a>Créez les dossiers.

Créez les dossiers d’exemple suivants (*fonctions*, *stratégies*, *Tables*) dans votre référentiel Git. Copiez les fichiers du [ici](https://github.com/Azure/azure-kusto-docs-samples/tree/master/DevOps_release_pipeline) dans les dossiers respectifs tels que présentés ci-dessous et valider les modifications. Les fichiers d’exemple sont fournis pour exécuter le workflow suivant.

![Créez les dossiers.](media/devops/create-folders.png)

> [!TIP]
> Lorsque vous créez votre propre flux de travail, nous vous recommandons de donner votre idempotent de code. Par exemple, utilisez [table de fusion et créez](/azure/kusto/management/tables#create-merge-tables) au lieu de [créez table](/azure/kusto/management/tables#create-table)et utiliser [créez ou alter](/azure/kusto/management/functions#create-or-alter-function) fonction au lieu de [créez](/azure/kusto/management/functions#create-function) fonction.

## <a name="create-a-release-pipeline"></a>Créer un pipeline de mise en production

1. Connectez-vous à votre [Azure DevOps organisation](https://dev.azure.com/).
1. Sélectionnez **Pipelines** > **versions** de menu de gauche, puis sélectionnez **nouveau pipeline**.

    ![Nouveau pipeline](media/devops/new-pipeline.png)

1. Le **nouveau pipeline de version** fenêtre s’ouvre. Dans le **Pipelines** sous l’onglet le **sélectionner un modèle** volet, sélectionnez **travaux vides**.

     ![Sélectionner un modèle](media/devops/select-template.png)

1. Sélectionnez **étape** bouton. Dans **étape** volet, ajoutez le **nom de la phase**. Sélectionnez **enregistrer** pour enregistrer votre pipeline.

    ![Nom de la scène](media/devops/stage-name.png)

1. Sélectionnez **ajouter un artefact** bouton. Dans le **ajouter un artefact** volet, sélectionnez le référentiel dans lequel votre code existe, remplissez les informations pertinentes, puis cliquez sur **ajouter**. Sélectionnez **enregistrer** pour enregistrer votre pipeline.

    ![Ajouter un artefact](media/devops/add-artifact.png)

1. Dans le **Variables** onglet, sélectionnez **+ ajouter** pour créer une variable pour **URL de point de terminaison** qui sera utilisé dans la tâche. Écrire le **nom** et **valeur** du point de terminaison. Sélectionnez **enregistrer** pour enregistrer votre pipeline. 

    ![Créer la variable](media/devops/create-variable.png)

    Pour rechercher votre Endpoint_URL, la page Vue d’ensemble de votre **Cluster de l’Explorateur de données Azure** dans le portail Azure portal contient l’URI de cluster de l’Explorateur de données Azure. Construire l’URI au format suivant `https://<Azure Data Explorer cluster URI>?DatabaseName=<DBName>`.  Par exemple, https://kustodocs.westus.kusto.windows.net?DatabaseName=SampleDB

    ![URI de cluster de l’Explorateur de données Azure](media/devops/adx-cluster-uri.png)

## <a name="create-tasks-to-deploy"></a>Créer des tâches de déploiement

1. Dans le **Pipeline** onglet, cliquez sur **1 travail, tâche 0** pour ajouter des tâches. 

    ![Ajouter des tâches](media/devops/add-task.png)

1. Créez trois tâches pour déployer **Tables**, **fonctions**, et **stratégies**, dans cet ordre. 

1. Dans le **tâches** onglet, sélectionnez **+** par **travail de l’Agent**. Recherchez **Azure Data Explorer**. Dans **place de marché**, installer le **Explorateur de données Azure – commandes d’administration** extension. Ensuite, sélectionnez **ajouter** dans **exécuter une commande de l’Explorateur de données Azure**.

     ![Ajouter des commandes d’administration](media/devops/add-admin-commands.png)

1. Cliquez sur **Kusto commande** sur la gauche et la mise à jour la tâche avec les informations suivantes :
    * **Nom d'affichage** : Nom de la tâche
    * **Chemin d’accès du fichier**: Dans le **Tables** de tâches, spécifiez */Tables/*.csl dans la mesure où les fichiers de la création de table sont dans le *Table* dossier.
    * **URL de point de terminaison**: entrez le `EndPoint URL`variable créée à l’étape précédente.
    * Sélectionnez **point de terminaison de Service usage** et sélectionnez **+ nouveau**.

    ![Mettre à jour de la tâche de commande Kusto](media/devops/kusto-command-task.png)

1. Complétez les informations suivantes dans le **connexion de service d’ajouter un Explorateur de données Azure** fenêtre :

    |Paramètre  |Valeur suggérée  |
    |---------|---------|
    |**Nom de connexion**     |    Entrez un nom pour identifier ce point de terminaison de service     |
    |**Url du cluster**    |    Valeur se trouve dans la section vue d’ensemble de votre Cluster de l’Explorateur de données Azure dans le portail Azure | 
    |**Id de principal du service**    |    Entrez l’ID d’application AAD (créée en tant que condition préalable)     |
    |**Clé d’application principal du service**     |    Entrez la clé d’application AAD (créée en tant que condition préalable)    |
    |**Id de locataire AAD**    |      Entrez votre client AAD (par exemple, microsoft.com, contoso.com...)    |

    Sélectionnez **autoriser tous les pipelines à utiliser cette connexion** case à cocher. Sélectionnez **OK**.

    ![Ajouter une connexion de service](media/devops/add-service-connection.png)

1. Répétez les étapes 1 à 5 un autre deux fois pour déployer des fichiers à partir de la *fonctions* et *stratégies* dossiers. Sélectionnez **Enregistrer**. Dans le **tâches** , consultez la rubrique les trois tâches créés : **Déployez les Tables**, **déployer des fonctions**, et **déployer des stratégies**.

    ![Déployer tous les dossiers](media/devops/deploy-all-folders.png)

1. Sélectionnez **+ mise en production** > **créer mise en production** pour créer une mise en production.

    ![Créer une mise en production](media/devops/create-release.png)

1. Dans le **journaux** onglet, vérifiez l’état du déploiement est réussi.

    ![Le déploiement a réussi](media/devops/deployment-successful.png)

Vous avez maintenant terminé la création d’un pipeline de mise en production pour le déploiement de trois tâches de préproduction.