---
title: Intégration continue avec Azure Pipelines
description: Découvrez comment générer, tester et déployer des modèles Azure Resource Manager en continu.
author: mumian
ms.date: 10/29/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: b117861a528b6983876d28d5b343ea88c2bcadc0
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260681"
---
# <a name="tutorial-continuous-integration-of-azure-resource-manager-templates-with-azure-pipelines"></a>Tutoriel : Intégration continue de modèles Azure Resource Manager avec Azure Pipelines

Découvrez comment utiliser Azure Pipelines pour générer et déployer des projets de modèle Azure Resource Manager en continu.

Azure DevOps fournit des services de développement pour prendre en charge les équipes afin de planifier le travail, collaborer sur le développement de code, mais aussi générer et déployer des applications. Les développeurs peuvent travailler dans le cloud à l’aide d’Azure DevOps Services. Azure DevOps fournit un ensemble de fonctionnalités auxquelles vous pouvez accéder via votre navigateur web ou un client IDE. Azure Pipelines est une de ces fonctionnalités. Azure Pipelines est un service complet d’intégration et de diffusion en continu. Il fonctionne avec votre fournisseur Git par défaut et peut être déployé sur la plupart des services cloud majeurs. Une fois déployé, il vous permet d’automatiser la génération, le test et le déploiement de votre code pour Microsoft Azure, Google Cloud Platform, ou Amazon Web Services.

Ce didacticiel est conçu pour les développeurs de modèles Azure Resource Manager qui découvrent Azure DevOps Services et Azure Pipelines. Si vous êtes déjà familiarisé avec GitHub et DevOps, vous pouvez passer à l’étape [Créer un pipeline](#create-a-pipeline).

> [!NOTE]
> Sélectionnez un nom de projet. Lorsque vous parcourez le didacticiel, remplacez chaque occurrence de **AzureRmPipeline** par le nom de votre projet.

Ce tutoriel décrit les tâches suivantes :

> [!div class="checklist"]
> * Préparer un dépôt GitHub
> * Créer un projet Azure DevOps
> * Créer un pipeline Azure
> * Vérifier le déploiement du pipeline
> * Mettre à jour le modèle et le redéployer
> * Nettoyer les ressources

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce qui est décrit dans cet article, vous avez besoin des éléments suivants :

* **Un compte GitHub**, que vous utiliserez pour créer un référentiel pour vos modèles. Si vous n’en avez pas, vous pouvez en créer un [gratuitement](https://github.com). Pour plus d’informations sur l’utilisation de référentiels GitHub, consultez [Développer des référentiels GitHub](/azure/devops/pipelines/repos/github).
* **Installer Git**. Lors de ce didacticiel, nous utiliserons *Git Bash* ou *Git Shell*. Pour en savoir plus sur leur installation, veuillez consulter la page [Installer Git]( https://www.atlassian.com/git/tutorials/install-git).
* **Une organisation Azure DevOps**. Si vous n’en avez pas, vous pouvez en créer une gratuitement. Pour en savoir plus, veuillez consulter [Créer une organisation ou une collection de projet]( https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops).
* Visual Studio Code avec l’extension Outils Resource Manager. Consultez [Utiliser Visual Studio Code pour créer des modèles Azure Resource Manager](use-vs-code-to-create-template.md).

## <a name="prepare-a-github-repository"></a>Préparer un dépôt GitHub

GitHub est utilisé pour stocker le code source de votre projet, y compris les modèles Resource Manager. Pour connaître les autres référentiels pris en charge, veuillez consulter [Référentiels pris en charge par Azure DevOps](/azure/devops/pipelines/repos/?view=azure-devops).

### <a name="create-a-github-repository"></a>Créer un référentiel GitHub

Si vous n’avez pas de compte GitHub, veuillez consulter la section [Conditions préalables](#prerequisites).

1. Connectez-vous à [GitHub](https://github.com).
2. Sélectionnez votre image de compte dans le coin supérieur droit, puis **vos référentiels**.

    ![Azure Resource Manager, Azure DevOps Azure Pipelines, créer un référentiel GitHub](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-github-repository.png)

1. Sélectionnez le bouton vert **New** (Nouveau).
1. Dans **Repository name** (nom du référentiel), entrez un nom de référentiel.  Par exemple, **AzureRmPipeline-repo**. N’oubliez pas de remplacer chaque occurrence de **AzureRmPipeline** par le nom de votre projet. Vous pouvez sélectionner **Public** ou **Private** (Privé) pour ce didacticiel. Puis sélectionnez **Create repository** (Créer un référentiel).
1. Notez l’URL. L’URL du référentiel est au format suivant :

    ```url
    https://github.com/[YourAccountName]/[YourRepositoryName]
    ```

Ce référentiel est appelé *remote repository* (référentiel distant). Chacun des développeurs du même projet peut cloner son *référentiel local*, puis fusionner ses modifications dans le référentiel distant.

### <a name="clone-the-remote-repository"></a>Cloner le référentiel distant

1. Ouvrez Git Shell ou Git Bash.  Consultez les [Conditions préalables](#prerequisites).
1. Vérifiez votre dossier actif est **github**.
1. Exécutez la commande suivante :

    ```bash
    git clone https://github.com/[YourAccountName]/[YourGitHubRepositoryName]
    cd [YourGitHubRepositoryName]
    mkdir CreateAzureStorage
    cd CreateAzureStorage
    pwd
    ```

    Remplacez **[YourAccountName]** (NomDeVotreCompte) par le nom de votre compte GitHub, puis remplacez **[YourGitHubRepositoryName]** (NomDeVotreRéférentielGitHub) par le nom de du référentiel que vous avez créé lors de la procédure précédente.

    La capture d’écran suivante présente un exemple.

    ![Azure Resource Manager, Azure DevOps, Azure Pipelines, créer un référentiel GitHub, Bash](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-github-bash.png)

Le modèle est stocké dans le dossier **CreateAzureStorage** (CréerUnStockageAzure). La commande **pwd** affiche le chemin d’accès au dossier. C’est dans ce chemin d’accès que vous enregistrerez le modèle lors de la procédure suivante.

### <a name="download-a-quickstart-template"></a>Télécharger un modèle de démarrage rapide

Au lieu de créer un modèle, vous pouvez télécharger un [Quickstart template]( https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) (modèle de démarrage rapide). Ce modèle crée un compte Stockage Azure.

1. Ouvrez Visual Studio Code. Consultez les [Conditions préalables](#prerequisites).
2. Ouvrez le modèle avec l’URL suivante :

    ```URL
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

3. Enregistrez le fichier sous **azuredeploy.json** dans le dossier **CreateAzureStorage**. Le nom du dossier et le nom du fichier sont utilisés tels quels dans le pipeline.  Si vous modifiez ces noms, vous devez mettre à jour les noms utilisés dans le pipeline.

### <a name="push-the-template-to-the-remote-repository"></a>Transmettre le modèle vers le référentiel distant

Le fichier azuredeploy.json a été ajouté dans le référentiel local. Ensuite, vous chargez le modèle dans le référentiel distant.

1. Si ce n’est pas déjà fait, ouvrez *Git Shell* ou *Git Bash*.
1. Changez le répertoire dans le dossier CreateAzureStorage de votre référentiel local.
1. Vérifiez que le fichier **azuredeploy.json** se trouve dans le dossier.
1. Exécutez la commande suivante :

    ```bash
    git add .
    git commit -m “Add a new create storage account template.”
    git push origin master
    ```

    Vous pouvez obtenir un avertissement sur LF. Vous pouvez ignorer cet avertissement. La branche principale s’intitule **master**.  Généralement, il faut créer une branche pour chaque mise à jour. Pour simplifier ce didacticiel, nous utilisons directement la branche principale.
1. Accédez à votre référentiel GitHub à partir d’un navigateur.  L’URL est **https://github.com/ [LeNomDeVotreCompte]/[VotreRéférentielGitHub]** . Vous devriez voir le dossier **CreateAzureStorage**, qui doit contenir le fichier **Azuredeploy.json**.

Pour le moment, vous avez créé un référentiel GitHub et chargé un modèle dans le référentiel.

## <a name="create-a-devops-project"></a>Créer un projet DevOps

Une organisation DevOps est nécessaire avant de procéder à la procédure suivante.  Si vous n’en avez pas, veuillez consulter la section [Conditions préalables](#prerequisites).

1. Connectez-vous à [Azure DevOps](https://dev.azure.com).
1. Sélectionnez une organisation DevOps sur la gauche.

    ![Azure Resource Manager, Azure DevOps, Azure Pipelines, créer un projet Azure DevOps](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-create-devops-project.png)

1. Sélectionnez **Créer un projet**. Si aucun projet ne s’affiche, la page Create project (Créer un projet) s’ouvre automatiquement.
1. Saisissez les valeurs suivantes :

    * **Project name** (Nom du projet) : nommez votre projet. Vous pouvez utiliser le nom du projet que vous avez choisi au tout début du didacticiel.
    * **Version control** (Contrôle de version) : Sélectionnez **Git**. Vous devrez peut-être développer **Advanced** (Avancé) pour voir **Version control** (Contrôle de version).

    Utilisez les valeurs par défaut pour les autres propriétés.
1. Sélectionnez **Créer un projet**.

Pour créer une connexion de service qui servira à déployer des projets vers Azure :

1. En bas du menu situé à gauche, sélectionnez **Project settings** (Paramètres du projet).
1. Sous **Pipelines**, sélectionnez **Service connections** (Connexions au service).
1. Sélectionnez **New Service connection** (Nouvelle connexion au service), puis **AzureResourceManager**.
1. Saisissez les valeurs suivantes :

    * **Connection name** (Nom de la connexion) : nommez la connexion. Par exemple, **AzureRmPipeline-conn**. Notez ce nom : vous en aurez besoin lors de la création de votre pipeline.
    * **Scope level** (Niveau d’étendue) : sélectionnez **Subscription** (Abonnement).
    * **Subscription** (Abonnement) : sélectionnez votre abonnement.
    * **Groupe de ressources** : Laissez cette valeur vide.
    * **Allow all pipelines to use this connection** (Autoriser tous les pipelines à utiliser cette connexion). (sélectionné)
1. Sélectionnez **OK**.

## <a name="create-a-pipeline"></a>Créer un pipeline

Pour le moment, vous avez mené à bien les tâches suivantes :  Si vous avez ignoré les sections précédentes, car vous êtes familiarisé avec GitHub et DevOps, vous devez effectuer ces tâches avant de continuer.

- Création d’un référentiel GitHub, puis enregistrement de [ce modèle](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) dans le dossier **CreateAzureStorage** dans le référentiel.
- Création d’un projet DevOps et création d’une connexion au service Azure Resource Manager.

Pour créer un pipeline avec une étape pour déployer un modèle :

1. Dans le menu situé à gauche, sélectionnez **Pipelines**.
1. Sélectionnez **Nouveau pipeline**.
1. Sous l’onglet **Connect** (Connexion), sélectionnez **GitHub**. Si le système vous y invite, entrez vos informations d’identification de GitHub, puis suivez les instructions. Si vous voyez l’écran suivant, sélectionnez **Only select repositories** (Sélectionner uniquement les référentiels), puis vérifiez que votre référentiel se trouve dans la liste avant de sélectionner **Approve & Install** (Valider et installer).

    ![Azure Resource Manager, Azure DevOps, Azure Pipelines, sélectionner uniquement les référentiels](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-only-select-repositories.png)

1. Sous l’onglet **Select** (Sélection), sélectionnez votre référentiel.  Son nom par défaut est **[NomDeVotreCompte]/[NomDeVotreRéférentielGitHub]** .
1. Sous l’onglet **Configure** (Configuration), sélectionnez **Starter pipeline** (Pipeline de démarrage). Il indique le fichier de pipeline **azure-pipelines.yml** avec deux étapes de script.
1. Remplacez la section **steps** (étapes) par le YAML suivant :

    ```yaml
    steps:
    - task: AzureResourceManagerTemplateDeployment@3
      inputs:
        deploymentScope: 'Resource Group'
        ConnectedServiceName: '[EnterYourServiceConnectionName]'
        subscriptionName: '[EnterTheTargetSubscriptionID]'
        action: 'Create Or Update Resource Group'
        resourceGroupName: '[EnterANewResourceGroupName]'
        location: 'Central US'
        templateLocation: 'Linked artifact'
        csmFile: 'CreateAzureStorage/azuredeploy.json'
        deploymentMode: 'Incremental'
    ```

    Il doit ressembler à :

    ![Azure Resource Manager, Azure DevOps, Azure Pipelines, yaml](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-yml.png)

    Effectuez les modifications suivantes :

    * **deploymentScope** : sélectionnez l’étendue du déploiement à l’aide des options `Management Group`, `Subscription` et `Resource Group`. Dans ce tutoriel, choisissez **Groupe de ressources**. Pour en savoir plus sur les étendues, consultez [Étendues de déploiement](deploy-rest.md#deployment-scope).
    * **ConnectedServiceName** : spécifiez le nom de la connexion au service que vous avez créée précédemment.
    * **SubscriptionName** :  spécifiez l’ID de l’abonnement cible.
    * **action** : l’action **Create Or Update Resource Group** (Créer ou mettre à jour le groupe des ressources) effectue 2 actions : d’abord, il crée un groupe de ressources si un nouveau nom de groupe de ressources est fourni. Ensuite, il déploie le modèle spécifié.
    * **resourceGroupName** (NomDuGroupeDesRessources) : spécifiez un nouveau nom pour le groupe des ressources. Par exemple, **AzureRmPipeline-rg**.
    * **location** (emplacement) : spécifiez l’emplacement du groupe des ressources.
    * **templateLocation** (EmplacementDuModèle) : si vous spécifiez **Linked artifact** (Artefact lié), la tâche recherche le fichier de modèle directement à partir du référentiel connecté.
    * **csmFile** est le chemin d’accès au fichier du modèle. Vous n’avez pas besoin de spécifier un fichier de paramètres pour le modèle, car tous les paramètres définis dans le modèle ont des valeurs par défaut.

    Pour plus d’informations sur la tâche, consultez [Tâche Déploiement de groupe de ressources Azure](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment) et [Tâche Déploiement de modèle Azure Resource Manager](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md)
1. Sélectionnez **Enregistrer et exécuter**.
1. Sélectionnez de nouveau **Save and run** (Enregistrer et exécuter). Une copie du fichier YAML est enregistrée dans le référentiel connecté. Vous pouvez voir le fichier YAML en accédant à votre référentiel.
1. Vérifiez que le pipeline est exécuté avec succès.

    ![Azure Resource Manager, Azure DevOps, Azure Pipelines, yaml](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-status.png)

## <a name="verify-the-deployment"></a>Vérifier le déploiement

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Ouvrez le groupe de ressources. Son nom correspond à ce que vous avez spécifié dans le fichier YAML du pipeline.  Vous devriez voir un compte de stockage créé.  Le nom du compte de stockage commence par **store** (stocker).
1. Sélectionnez le nom du compte de stockage pour l’ouvrir.
1. Sélectionner **Propriétés**. Notez que le paramètre **Réplication** a pour valeur **Stockage localement redondant (LRS)** .

    ![Azure Resource Manager, Azure DevOps, Azure Pipelines, vérification du portail](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-portal-verification.png)

## <a name="update-and-redeploy"></a>Mettre à jour et redéployer

Lorsque vous mettez à jour le modèle et transmettez les modifications dans le référentiel distant, le pipeline met automatiquement à jour les ressources (ici, il s’agit du compte de stockage).

1. Ouvrez **azuredeploy.json** à partir de votre référentiel local dans Visual Studio Code.
1. Mettez à jour le **defaultValue** de **storageAccountType** sur **Standard_GRS**. Voir la capture d’écran suivante :

    ![Azure Resource Manager, Azure DevOps, Azure Pipelines, mettre à jour yaml](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-update-yml.png)

1. Enregistrez les modifications.
1. Transmettez les modifications vers le référentiel distant en exécutant les commandes suivantes à partir de Git Bash/Shell.

    ```bash
    git pull origin master
    git add .
    git commit -m “Add a new create storage account template.”
    git push origin master
    ```

    La première commande synchronise le référentiel local avec le référentiel distant. N’oubliez pas que le fichier YAML du pipeline a été ajouté au référentiel distant.

    Une fois la branche principale du référentiel distant mise à jour, le pipeline redéclenché.

Pour vérifier les modifications, vous pouvez examiner la propriété Réplication du compte de stockage.  Veuillez consulter la section [Vérifier le déploiement](#verify-the-deployment).

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous n’en avez plus besoin, nettoyez les ressources Azure que vous avez déployées en supprimant le groupe de ressources.

1. Dans le portail Azure, sélectionnez **Groupe de ressources** dans le menu de gauche.
2. Entrez le nom du groupe de ressources dans le champ **Filtrer par nom**.
3. Sélectionnez le nom du groupe de ressources.
4. Sélectionnez **Supprimer le groupe de ressources** dans le menu supérieur.

Nous vous recommandons également de supprimer le référentiel GitHub et le projet Azure DevOps.

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous créez un pipeline Azure DevOps pour déployer un modèle Azure Resource Manager. Pour savoir comment déployer des ressources Azure dans plusieurs régions et comment utiliser des pratiques de déploiement sécurisées, consultez

> [!div class="nextstepaction"]
> [Utiliser des pratiques de déploiement sécurisé](./deployment-manager-tutorial.md)
