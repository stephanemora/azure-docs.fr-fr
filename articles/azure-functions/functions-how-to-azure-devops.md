---
title: Proposer en permanence des mises à jour de code fonction à l’aide d’Azure DevOps
description: Découvrez comment configurer un pipeline Azure DevOps ciblant Azure Functions.
author: ahmedelnably
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: aelnably
ms.custom: ''
ms.openlocfilehash: 9806a982982971b1b3ac9c28454e17813b2ad2a5
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67479881"
---
# <a name="continuous-delivery-using-azure-devops"></a>Livraison continue à l’aide d’Azure DevOps

Vous pouvez déployer automatiquement votre fonction sur une application de fonction Azure à l’aide d’[Azure Pipelines](/azure/devops/pipelines/).
Pour définir votre pipeline, vous pouvez utiliser les éléments suivants :

- Fichier YAML : ce fichier décrit le pipeline. Il peut comporter une section relative aux étapes de génération et une autre relative à la mise en production. Le fichier YAML doit être dans le même référentiel que l’application.

- Modèles : les modèles sont des tâches prêtes à l’emploi qui créent ou déploient votre application.

## <a name="yaml-based-pipeline"></a>Pipeline basé sur le fichier YAML

### <a name="build-your-app"></a>Générer votre application

La génération de votre application dans Azure Pipelines varie selon le langage de programmation de votre application.
Chaque langage possède des étapes de génération spécifiques pour créer un artefact de déploiement, qui peut être utilisé pour déployer votre application de fonction dans Azure.

#### <a name="net"></a>.NET

Vous pouvez utiliser l’exemple suivant pour créer votre fichier YAML pour générer votre application .NET.

```yaml
pool:
      vmImage: 'VS2017-Win2016'
steps:
- script: |
    dotnet restore
    dotnet build --configuration Release
- task: DotNetCoreCLI@2
  inputs:
    command: publish
    arguments: '--configuration Release --output publish_output'
    projects: '*.csproj'
    publishWebProjects: false
    modifyOutputPath: true
    zipAfterPublish: false
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)/publish_output"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    name: 'drop'
```

#### <a name="javascript"></a>JavaScript

Vous pouvez utiliser l’exemple suivant pour créer votre fichier YAML pour générer votre application JavaScript :

```yaml
pool:
      vmImage: ubuntu-16.04 # Use 'VS2017-Win2016' if you have Windows native +Node modules
steps:
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    npm install 
    npm run build --if-present
    npm prune --production
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    name: 'drop'
```

#### <a name="python"></a>Python

Vous pouvez utiliser l’exemple suivant pour créer votre fichier YAML pour générer votre application Python. Python est uniquement pris en charge pour Linux Azure Functions :

```yaml
pool:
      vmImage: ubuntu-16.04
steps:
- task: UsePythonVersion@0
  displayName: "Setting python version to 3.6 as required by functions"
  inputs:
    versionSpec: '3.6'
    architecture: 'x64'
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    python3.6 -m venv worker_venv
    source worker_venv/bin/activate
    pip3.6 install setuptools
    pip3.6 install -r requirements.txt
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    name: 'drop'
```
#### <a name="powershell"></a>PowerShell

Vous pouvez utiliser l’exemple suivant pour créer votre fichier YAML pour générer votre application PowerShell. PowerShell est uniquement pris en charge pour Windows Azure Functions :

```yaml
pool:
      vmImage: 'VS2017-Win2016'
steps:
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    name: 'drop'
```

### <a name="deploy-your-app"></a>Déployer votre application

Selon le système d’exploitation hôte, vous devez inclure l’exemple YAML suivant dans votre fichier YAML.

#### <a name="windows-function-app"></a>Application de fonction Windows

L’extrait de code suivant peut être utilisé pour effectuer un déploiement sur une application de fonction Windows

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionApp
    appName: '<Name of function app>'
    #Uncomment the next lines to deploy to a deployment slot
    #deployToSlotOrASE: true
    #resourceGroupName: '<Resource Group Name>'
    #slotName: '<Slot name>'
```

#### <a name="linux-function-app"></a>Application de fonction Linux

L’extrait de code suivant peut être utilisé pour effectuer un déploiement sur une application de fonction Linux

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionAppLinux
    appName: '<Name of function app>'
    #Uncomment the next lines to deploy to a deployment slot
    #Note that deployment slots is not supported for Linux Dynamic SKU
    #deployToSlotOrASE: true
    #resourceGroupName: '<Resource Group Name>'
    #slotName: '<Slot name>'
```

## <a name="template-based-pipeline"></a>Pipeline basé sur un modèle

Les modèles dans Azure, DevOps, sont des groupes prédéfinis de tâches qui génèrent ou déploient une application.

### <a name="build-your-app"></a>Générer votre application

La génération de votre application dans Azure Pipelines varie selon le langage de programmation de votre application. Chaque langage possède des étapes de génération spécifiques pour créer un artefact de déploiement, qui peut être utilisé pour mettre à jour votre application de fonction dans Azure.
Pour utiliser les modèles de build intégrés, lors de la création d’un nouveau pipeline de build, choisissez **Utiliser l’éditeur classique** pour créer un pipeline à l’aide des modèles de concepteur

![Éditeur classique d’Azure Pipelines](media/functions-how-to-azure-devops/classic-editor.png)

Après avoir configuré la source de votre code, recherchez les modèles de build Azure Functions et choisissez le modèle qui correspond au langage de votre application.

![Modèles de build Azure Functions](media/functions-how-to-azure-devops/build-templates.png)

Dans certains cas, les artefacts de build ont une structure de dossiers spécifiques et vous devrez peut-être cocher l’option **Placer le nom du dossier racine au début des chemins d’archives**.

![Placer le nom du dossier racine au début](media/functions-how-to-azure-devops/prepend-root-folder.png)

#### <a name="javascript-apps"></a>Applications JavaScript

Si votre application JavaScript a une dépendance à l’égard des modules natifs Windows, vous devez mettre à jour les éléments suivants :

- La version de Pool Agent sur **Hosted VS2017**

  ![Modifier le système d’exploitation de l’agent de build](media/functions-how-to-azure-devops/change-agent.png)

### <a name="deploy-your-app"></a>Déployer votre application

Lorsque vous créez un nouveau pipeline de mise en production, recherchez le modèle de mise en production d’Azure Functions.

![](media/functions-how-to-azure-devops/release-template.png)

Le déploiement vers un emplacement de déploiement n’est pas pris en charge dans le modèle de mise en production.

## <a name="creating-an-azure-pipeline-using-the-azure-cli"></a>Créer un pipeline Azure à l’aide de l’interface de ligne de commande Azure

La [commande](/cli/azure/functionapp/devops-pipeline#az-functionapp-devops-pipeline-create) `az functionapp devops-pipeline create` permet de créer un pipeline Azure pour générer et publier des modifications de code dans votre référentiel. La commande génère un nouveau fichier YAML qui définit le pipeline de génération et de mise en production et le valide dans votre référentiel. Le déploiement vers un emplacement de déploiement n’est pas pris en charge par l’interface de ligne de commande Azure.
Les conditions préalables de cette commande varient selon l’emplacement de votre code :

- Si votre code se trouve dans GitHub :

    - Vous devez disposer d’une autorisation en **écriture** dans votre abonnement.

    - Vous êtes l’administrateur de projet dans Azure DevOps.

    - Vous êtes autorisé à créer un jeton d’accès personnel GitHub disposant d’autorisations suffisantes. [Autorisations requises du jeton d’accès personnel GitHub.](https://aka.ms/azure-devops-source-repos)

    - Vous êtes autorisé à valider dans la branche principale de votre référentiel GitHub pour valider le fichier YAML généré automatiquement.

- Si votre code se trouve dans Azure Repos :

    - Vous devez disposer d’une autorisation en **écriture** dans votre abonnement.

    - Vous êtes l’administrateur de projet dans Azure DevOps.

## <a name="next-steps"></a>Étapes suivantes

+ [Vue d’ensemble d’Azure Functions](functions-overview.md)
+ [Vue d’ensemble d’Azure DevOps](/azure/devops/pipelines/)
