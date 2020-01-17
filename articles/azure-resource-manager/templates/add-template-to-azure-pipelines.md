---
title: CI/CD avec Azure Pipelines et des modèles
description: Décrit la configuration de l’intégration continue dans Azure Pipelines en utilisant des projets de déploiement Groupe de ressources Azure dans Visual Studio pour déployer des modèles Resource Manager.
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: 6f5d4846d32b4880ccd3fbd82f062f57948ac15a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75474455"
---
# <a name="integrate-resource-manager-templates-with-azure-pipelines"></a>Intégrer des modèles Azure Resource Manager avec Azure Pipelines

Visual Studio fournit le projet de groupe de ressources Azure pour la création de modèles et leur déploiement dans votre abonnement Azure. Vous pouvez intégrer ce projet avec Azure Pipelines pour l’intégration et déploiement continus (CI/CD).

Il existe deux façons de déployer des modèles avec Azure Pipelines :

* **Ajouter une tâche exécutant un script Azure PowerShell**. Cette option présente l’avantage d’assurer la cohérence tout au long du cycle de développement, car vous utilisez le même script inclus dans le projet Visual Studio (Deploy-azureresourcegroup.ps1). Le script organise les artefacts de votre projet comme un compte de stockage auquel Resource Manager peut accéder. Les artefacts sont des éléments dans votre projet comme les modèles liés, les scripts et les fichiers binaires d’application. Le script déploie ensuite le modèle.

* **Ajouter des tâches pour copier et déployer des tâches**. Cette deuxième option est une alternative pratique au script de projet. Vous configurez deux tâches dans le pipeline. Une tâche prépare les artefacts et l’autre tâche déploie le modèle.

Cette rubrique illustre les deux approches.

## <a name="prepare-your-project"></a>Préparation du projet

Cet article suppose que votre projet Visual Studio et votre organisation Azure DevOps sont prêts pour la création du pipeline. Les étapes suivantes montrent comment vérifier que vous êtes prêt :

* Vous disposez d’une organisation Azure DevOps. Si vous n’en avez pas, [créez-en une gratuitement](/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops). Si votre équipe a déjà une organisation Azure DevOps, assurez-vous d’être un administrateur du projet Azure DevOps que vous souhaitez utiliser.

* Vous avez configuré une [connexion de service](/azure/devops/pipelines/library/connect-to-azure?view=azure-devops) pour votre abonnement Azure. Les tâches dans le pipeline s’exécutent sous l’identité du principal du service. Pour obtenir des instructions sur la création d’une connexion, consultez [Créer un projet DevOps](template-tutorial-use-azure-pipelines.md#create-a-devops-project).

* Vous avez un projet Visual Studio créé à partir du modèle de démarrage **Groupe de ressources Azure**. Pour plus d’informations sur la création de ce type de projet, consultez [Création et déploiement des groupes de ressources Azure via Visual Studio](create-visual-studio-deployment-project.md).

* Votre projet Visual Studio est [connecté à un projet Azure DevOps](/azure/devops/repos/git/share-your-code-in-git-vs-2017?view=azure-devops).

## <a name="create-pipeline"></a>Création d’un pipeline

1. Si vous n’avez pas ajouté un pipeline précédemment, vous devez en créer un nouveau. À partir de votre organisation Azure DevOps, sélectionnez **Pipelines** et **Nouveau pipeline**.

   ![Ajouter un nouveau pipeline](./media/add-template-to-azure-pipelines/new-pipeline.png)

1. Spécifiez où votre code est stocké. L’illustration suivante montre la sélection du **Git Azure Repos**.

   ![Sélectionner une source de code](./media/add-template-to-azure-pipelines/select-source.png)

1. À partir de cette source, sélectionnez le référentiel contenant le code de votre projet.

   ![Sélectionner un référentiel](./media/add-template-to-azure-pipelines/select-repo.png)

1. Sélectionnez le type de pipeline à créer. Vous pouvez sélectionner **Pipeline de démarrage**.

   ![Sélectionner un pipeline](./media/add-template-to-azure-pipelines/select-pipeline.png)

Vous êtes prêt à ajouter une tâche Azure PowerShell ou le fichier de copie et à déployer des tâches.

## <a name="azure-powershell-task"></a>Tâche Azure PowerShell

Cette section montre comment configurer le déploiement continu à l’aide d’une seule tâche qui exécute le script PowerShell dans votre projet. Le fichier YAML suivant crée une [tâche Azure PowerShell](/azure/devops/pipelines/tasks/deploy/azure-powershell?view=azure-devops):

```yaml
pool:
  name: Hosted Windows 2019 with VS2019
  demands: azureps

steps:
- task: AzurePowerShell@3
  inputs:
    azureSubscription: 'demo-deploy-sp'
    ScriptPath: 'AzureResourceGroupDemo/Deploy-AzureResourceGroup.ps1'
    ScriptArguments: -ResourceGroupName 'demogroup' -ResourceGroupLocation 'centralus'
    azurePowerShellVersion: LatestVersion
```

Lorsque vous définissez la tâche sur `AzurePowerShell@3`, le pipeline utilise des commandes à partir du module AzureRM pour authentifier la connexion. Par défaut, le script PowerShell dans le projet Visual Studio utilise le module AzureRM. Si vous avez mis à jour votre script pour utiliser le [module Az](/powershell/azure/new-azureps-module-az), configurez la tâche sur `AzurePowerShell@4`.

```yaml
steps:
- task: AzurePowerShell@4
```

Pour `azureSubscription`, indiquez le nom de la connexion de service créée.

```yaml
inputs:
    azureSubscription: '<your-connection-name>'
```

Pour `scriptPath`, fournissez le chemin d’accès relatif à partir du fichier de pipeline vers votre script. Vous pouvez rechercher dans votre référentiel pour afficher le chemin d’accès.

```yaml
ScriptPath: '<your-relative-path>/<script-file-name>.ps1'
```

Si vous n’avez pas besoin d’indexer des artefacts, transmettez simplement le nom et l’emplacement d’un groupe de ressources pour utiliser pour le déploiement. Le script Visual Studio crée le groupe de ressources s’il n’existe pas déjà.

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>'
```

Si vous avez besoin d’indexer des artefacts à un compte de stockage existant, utilisez :

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>' -UploadArtifacts -ArtifactStagingDirectory '$(Build.StagingDirectory)' -StorageAccountName '<your-storage-account>'
```

Maintenant que vous comprenez comment créer la tâche, passons en revue les étapes visant à modifier le pipeline.

1. Ouvrez votre pipeline et remplacez le contenu par votre YAML :

   ```yaml
   pool:
     name: Hosted Windows 2019 with VS2019
     demands: azureps

   steps:
   - task: AzurePowerShell@3
     inputs:
       azureSubscription: 'demo-deploy-sp'
       ScriptPath: 'AzureResourceGroupDemo/Deploy-AzureResourceGroup.ps1'
       ScriptArguments: -ResourceGroupName 'demogroup' -ResourceGroupLocation 'centralus' -UploadArtifacts -ArtifactStagingDirectory '$(Build.StagingDirectory)' -StorageAccountName 'stage3a4176e058d34bb88cc'
       azurePowerShellVersion: LatestVersion
   ```

1. Sélectionnez **Enregistrer**.

   ![Enregistrer le pipeline](./media/add-template-to-azure-pipelines/save-pipeline.png)

1. Fournissez un message pour la validation et validez directement sur **master**.

1. Lorsque vous sélectionnez **Enregistrer**, le pipeline de build s’exécute automatiquement. Revenez au résumé de votre pipeline de build et regardez l’état.

   ![Afficher les résultats](./media/add-template-to-azure-pipelines/view-results.png)

Vous pouvez sélectionner le pipeline en cours d’exécution pour afficher plus d’informations sur les tâches. Lorsqu’il se termine, vous consultez les résultats pour chaque étape.

## <a name="copy-and-deploy-tasks"></a>Tâches de copie et de déploiement

Cette section montre comment configurer le déploiement continu à l’aide de deux tâches pour indexer les artefacts et déployer le modèle.

Le YAML suivant montre la [tâche de copie de fichiers Azure](/azure/devops/pipelines/tasks/deploy/azure-file-copy?view=azure-devops):

```yaml
- task: AzureFileCopy@3
  displayName: 'Stage files'
  inputs:
    SourcePath: 'AzureResourceGroup1'
    azureSubscription: 'demo-deploy-sp'
    Destination: 'AzureBlob'
    storage: 'stage3a4176e058d34bb88cc'
    ContainerName: 'democontainer'
    outputStorageUri: 'artifactsLocation'
    outputStorageContainerSasToken: 'artifactsLocationSasToken'
    sasTokenTimeOutInMinutes: '240'
```

Il existe plusieurs parties de cette tâche à réviser pour votre environnement. `SourcePath` indique l’emplacement des artefacts relatifs au fichier de pipeline. Dans cet exemple, les fichiers existent dans un dossier nommé `AzureResourceGroup1` qui était le nom du projet.

```yaml
SourcePath: '<path-to-artifacts>'
```

Pour `azureSubscription`, indiquez le nom de la connexion de service créée.

```yaml
azureSubscription: '<your-connection-name>'
```

Pour le nom du stockage et du conteneur, fournissez les noms du compte de stockage et du conteneur que vous souhaitez utiliser pour stocker les artefacts. Le compte de stockage doit exister.

```yaml
storage: '<your-storage-account-name>'
ContainerName: '<container-name>'
```

Le YAML suivant montre la [tâche de déploiement du modèle Azure Resource Manager](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md) :

```yaml
- task: AzureResourceGroupDeployment@2
  displayName: 'Deploy template'
  inputs:
    deploymentScope: 'Resource Group'
    ConnectedServiceName: 'demo-deploy-sp'
    subscriptionName: '01234567-89AB-CDEF-0123-4567890ABCDEF'
    action: 'Create Or Update Resource Group'
    resourceGroupName: 'demogroup'
    location: 'Central US'
    templateLocation: 'URL of the file'
    csmFileLink: '$(artifactsLocation)WebSite.json$(artifactsLocationSasToken)'
    csmParametersFileLink: '$(artifactsLocation)WebSite.parameters.json$(artifactsLocationSasToken)'
    overrideParameters: '-_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken "$(artifactsLocationSasToken)"'
    deploymentMode: 'Incremental'
```

Il existe plusieurs parties de cette tâche à réviser pour votre environnement.

- `deploymentScope`: Sélectionnez l’étendue du déploiement à l’aide des options : `Management Group`, `Subscription` et `Resource Group`. Utilisez **Groupe de ressources** dans cette présentation. Pour en savoir plus sur les étendues, consultez [Étendues de déploiement](deploy-rest.md#deployment-scope).

- `ConnectedServiceName`: Indiquez le nom de la connexion de service créée.

    ```yaml
    ConnectedServiceName: '<your-connection-name>'
    ```

- `subscriptionName`: Spécifiez l’ID de l’abonnement cible. Cette propriété s’applique uniquement à l’étendue de déploiement du groupe de ressources et du déploiement de l’abonnement.

- `resourceGroupName` et `location` : indiquez le nom et l’emplacement du groupe de ressources au sein duquel vous souhaitez faire le déploiement. La tâche crée le groupe de ressources s’il n’existe pas.

    ```yaml
    resourceGroupName: '<resource-group-name>'
    location: '<location>'
    ```

La tâche de déploiement se lie à un modèle nommé `WebSite.json` et un fichier de paramètres nommé WebSite.parameters.json. Utilisez les noms de votre modèle et de vos fichiers de paramètres.

Maintenant que vous comprenez comment créer la tâche, passons en revue les étapes visant à modifier le pipeline.

1. Ouvrez votre pipeline et remplacez le contenu par votre YAML :

   ```yaml
   pool:
     name: Hosted Windows 2019 with VS2019

   steps:
   - task: AzureFileCopy@3
     displayName: 'Stage files'
     inputs:
       SourcePath: 'AzureResourceGroup1'
       azureSubscription: 'demo-deploy-sp'
       Destination: 'AzureBlob'
       storage: 'stage3a4176e058d34bb88cc'
       ContainerName: 'democontainer'
       outputStorageUri: 'artifactsLocation'
       outputStorageContainerSasToken: 'artifactsLocationSasToken'
       sasTokenTimeOutInMinutes: '240'
    - task: AzureResourceGroupDeployment@2
      displayName: 'Deploy template'
      inputs:
        deploymentScope: 'Resource Group'
        ConnectedServiceName: 'demo-deploy-sp'
        subscriptionName: '01234567-89AB-CDEF-0123-4567890ABCDEF'
        action: 'Create Or Update Resource Group'
        resourceGroupName: 'demogroup'
        location: 'Central US'
        templateLocation: 'URL of the file'
        csmFileLink: '$(artifactsLocation)WebSite.json$(artifactsLocationSasToken)'
        csmParametersFileLink: '$(artifactsLocation)WebSite.parameters.json$(artifactsLocationSasToken)'
        overrideParameters: '-_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken "$(artifactsLocationSasToken)"'
        deploymentMode: 'Incremental'
   ```

1. Sélectionnez **Enregistrer**.

1. Fournissez un message pour la validation et validez directement sur **master**.

1. Lorsque vous sélectionnez **Enregistrer**, le pipeline de build s’exécute automatiquement. Revenez au résumé de votre pipeline de build et regardez l’état.

   ![Afficher les résultats](./media/add-template-to-azure-pipelines/view-results.png)

Vous pouvez sélectionner le pipeline en cours d’exécution pour afficher plus d’informations sur les tâches. Lorsqu’il se termine, vous consultez les résultats pour chaque étape.

## <a name="next-steps"></a>Étapes suivantes

Pour un processus pas à pas sur l’utilisation d’Azure Pipelines et des modèles Resource Manager, consultez [Didacticiel : Intégration continue de modèles Azure Resource Manager avec Azure Pipelines](template-tutorial-use-azure-pipelines.md).
