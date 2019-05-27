---
title: Proposer en permanence la fonction mises à jour de code à l’aide d’Azure DevOps
description: Découvrez comment configurer un pipeline Azure DevOps ciblant Azure Functions.
author: ahmedelnably
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: aelnably
ms.custom: ''
ms.openlocfilehash: 27b5dc9ccee8647d4fbb617063865df18b80bc5d
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65990274"
---
# <a name="continuous-delivery-using-azure-devops"></a>Livraison continue à l’aide d’Azure DevOps

Vous pouvez déployer automatiquement votre fonction à une application de fonction Azure à l’aide [Azure Pipelines](/azure/devops/pipelines/).
Pour définir votre pipeline, vous pouvez utiliser :

- Fichier YAML : Ce fichier décrit le pipeline, elle peut avoir une section d’étapes de build et une section de mise en production. Le fichier YAML doit être dans le même référentiel que l’application.

- modèles : Les modèles sont prêts tâches que vous générez ou déploiement votre application.

## <a name="yaml-based-pipeline"></a>En fonction du YAML de pipeline

### <a name="build-your-app"></a>Générer votre application

Génération de votre application dans les Pipelines Azure varie selon le langage de programmation de votre application.
Chaque langage possède des étapes de génération spécifique pour créer un artefact de déploiement, ce qui peut être utilisé pour déployer votre function app dans Azure.

#### <a name="net"></a>.NET

Vous pouvez utiliser l’exemple suivant pour créer votre fichier YAML pour générer votre application .NET.

```yaml
jobs:
  - job: Build
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

Vous pouvez utiliser l’exemple suivant pour créer votre fichier YAML pour générer votre application JavaScript :

```yaml
jobs:
  - job: Build
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

Vous pouvez utiliser l’exemple suivant pour créer votre fichier YAML pour générer votre application Python, Python est uniquement pris en charge pour Linux Azure Functions :

```yaml
jobs:
  - job: Build
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

### <a name="deploy-your-app"></a>Déployer votre application

Selon le système d’exploitation hôte, vous devez inclure l’exemple YAML suivant dans votre fichier YAML.

#### <a name="windows-function-app"></a>Fonction Windows application

L’extrait de code suivant peut être utilisé pour déployer vers une application de fonction Windows

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionApp
    appName: '<Name of function app>'
```

#### <a name="linux-function-app"></a>Linux function App

L’extrait de code suivant peut être utilisé pour déployer vers une application de fonction Linux

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionAppLinux
    appName: '<Name of function app>'
```

## <a name="template-based-pipeline"></a>Basé sur le modèle de pipeline

Les modèles dans Azure, DevOps, sont des groupes prédéfinis de tâches que vous générez ou déployez une application.

### <a name="build-your-app"></a>Générer votre application

Génération de votre application dans les Pipelines Azure varie selon le langage de programmation de votre application. Chaque langage possède des étapes de génération spécifique pour créer un artefact de déploiement, ce qui peut être utilisé pour mettre à jour votre function app dans Azure.
Pour utiliser les modèles de build intégrées, lors de la création d’un nouveau pipeline de build, choisissez **utiliser l’éditeur classique** pour créer un pipeline à l’aide des modèles de concepteur

![Éditeur classique des Pipelines Azure](media/functions-how-to-azure-devops/classic-editor.png)

Après avoir configuré la source de votre code, recherchez Azure Functions de modèles de build et choisissez le modèle qui correspond à la langue de votre application.

![Azure Functions aux modèles de build](media/functions-how-to-azure-devops/build-templates.png)

#### <a name="javascript-apps"></a>Applications JavaScript

Si votre application JavaScript ont une dépendance sur les modules natifs de Windows, vous devez mettre à jour :

- La version de Pool d’agents à **hébergée VS2017**

  ![Modification de l’Agent de Build du système d’exploitation](media/functions-how-to-azure-devops/change-agent.png)

- Le script dans le **générer des extensions** dans le modèle à l’étape `IF EXIST *.csproj dotnet build extensions.csproj --output ./bin`

  ![Script de modification](media/functions-how-to-azure-devops/change-script.png)

### <a name="deploy-your-app"></a>Déployer votre application

Lorsque vous créez un nouveau pipeline de mise en production, recherchez le modèle de version d’Azure Functions.

![](media/functions-how-to-azure-devops/release-template.png)

## <a name="creating-an-azure-pipeline-using-the-azure-cli"></a>Création d’un Pipeline Azure à l’aide de l’interface CLI Azure

À l’aide de la `az functionapp devops-pipeline create` [commande](/cli/azure/functionapp/devops-pipeline#az-functionapp-devops-pipeline-create), un pipeline Azure sera créé pour générer et publier les modifications de code dans votre référentiel. La commande génère un nouveau fichier YAML qui définit le pipeline de génération et de mise en production et valider dans votre référentiel.
Les conditions préalables pour cette commande varient selon l’emplacement de votre code :

- Si votre code se trouve dans GitHub :

    - Vous devez avoir **écrire** autorisation à votre abonnement.

    - Vous êtes l’administrateur de projet dans Azure DevOps.

    - Vous êtes autorisé à créer un GitHub jeton d’accès personnel disposant d’autorisations suffisantes. [Conditions d’autorisation PAT GitHub.](https://aka.ms/azure-devops-source-repos)

    - Vous êtes autorisé à valider dans la branche principale dans votre référentiel GitHub pour valider le fichier YAML généré automatiquement.

- Si votre code se trouve dans les référentiels Azure :

    - Vous devez avoir **écrire** autorisation à votre abonnement.

    - Vous êtes l’administrateur de projet dans Azure DevOps.

## <a name="next-steps"></a>Étapes suivantes

+ [Vue d’ensemble d’Azure Functions](functions-overview.md)
+ [Vue d’ensemble de DevOps Azure](/azure/devops/pipelines/)
