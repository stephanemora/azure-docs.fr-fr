---
title: Publication automatisée pour l’intégration et la livraison continues
description: Découvrez comment publier automatiquement pour une intégration et une livraison continues.
ms.service: data-factory
author: nabhishek
ms.author: abnarain
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: 9056dd0be8e84fdff6934b2aecbd4a553f540811
ms.sourcegitcommit: dd425ae91675b7db264288f899cff6add31e9f69
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2021
ms.locfileid: "108331572"
---
# <a name="automated-publishing-for-continuous-integration-and-delivery"></a>Publication automatisée pour l’intégration et la livraison continues

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>Vue d’ensemble

L’intégration continue consiste à tester automatiquement chaque modification apportée à votre codebase. Dès que possible, la livraison continue fait suite au test effectué pendant l’intégration continue et envoie (push) les modifications à un système de mise en lots ou de production.

Dans Azure Data Factory, l’intégration continue et la livraison continue (CI/CD) impliquent de déplacer des pipelines Data Factory d’un environnement (développement, test et production) vers un autre. Data Factory utilise des [modèles Azure Resource Manager (modèles ARM)](../azure-resource-manager/templates/overview.md) pour stocker la configuration de vos différentes entités Data Factory, telles que les pipelines, les jeux de données et les flux de données.

Deux méthodes sont recommandées pour promouvoir une fabrique de données dans un autre environnement :

- Déploiement automatisé grâce à l’intégration de Data Factory avec [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines).
- Chargement manuel d’un modèle ARM en utilisant l’intégration de l’expérience utilisateur de Data Factory à Azure Resource Manager.

Pour plus d’informations, consultez [Intégration et livraison continues dans Azure Data Factory](continuous-integration-deployment.md).

Cet article se concentre sur les améliorations du déploiement continu et la fonctionnalité de publication automatisée pour CI/CD.

## <a name="continuous-deployment-improvements"></a>Améliorations du déploiement continu

La fonctionnalité de publication automatisée prend les fonctionnalités **Valider tout** et **Exporter le modèle ARM** de l’expérience utilisateur de Data Factory et rend la logique consommable via un package npm [@microsoft/azure-data-factory-utilities](https://www.npmjs.com/package/@microsoft/azure-data-factory-utilities) disponible publiquement. Pour cette raison, vous pouvez déclencher ces actions par programmation au lieu d’accéder à l’interface utilisateur de Data Factory et de sélectionner un bouton manuellement. Cette capacité donnera à vos pipelines de CI/CD une expérience d’intégration continue plus vraie.

### <a name="current-cicd-flow"></a>Flux actuel de CI/CD

1. Chaque utilisateur apporte des modifications à ses branches privées.
1. L’envoi (push) vers le master n’est pas autorisé. Les utilisateurs doivent créer une demande de tirage (pull request) pour apporter des modifications.
1. Les utilisateurs doivent charger l’interface utilisateur de Data Factory et sélectionner **Publier** pour déployer les modifications apportées à Data Factory et générer les modèles ARM dans la branche de publication.
1. Le pipeline de mise en production DevOps est configuré pour créer une nouvelle version et déployer le modèle ARM chaque fois qu’une nouvelle modification est envoyée (push) à la branche de publication.

![Diagramme qui montre le flux de CI/CD actuel.](media/continuous-integration-deployment-improvements/current-ci-cd-flow.png)

### <a name="manual-step"></a>Étape manuelle

Dans le flux de CI/CD actuel, l’expérience utilisateur est l’intermédiaire permettant de créer le modèle ARM. Par conséquent, un utilisateur doit accéder à l’interface utilisateur de Data Factory et sélectionner manuellement **Publier** pour démarrer la génération du modèle ARM et le déposer dans la branche de publication.

### <a name="the-new-cicd-flow"></a>Nouveau flux de CI/CD

1. Chaque utilisateur apporte des modifications à ses branches privées.
1. L’envoi (push) vers le master n’est pas autorisé. Les utilisateurs doivent créer une demande de tirage (pull request) pour apporter des modifications.
1. La création du pipeline Azure DevOps est déclenchée chaque fois qu’une nouvelle validation est effectuée sur le master. Cela valide les ressources et génère un modèle ARM comme artefact si la validation réussit.
1. Le pipeline de mise en production DevOps est configuré pour créer une nouvelle version et déployer le modèle ARM chaque fois qu’une nouvelle BLD est disponible.

![Diagramme montrant le nouveau flux de CI/CD.](media/continuous-integration-deployment-improvements/new-ci-cd-flow.png)

### <a name="what-changed"></a>Qu’est ce qui a changé ?

- Nous disposons maintenant d’un processus de génération qui utilise un pipeline de build DevOps.
- Le pipeline de build utilise le package NPM ADFUtilities, qui valide toutes les ressources et génère les modèles ARM. Ces modèles peuvent être uniques et liés.
- Le pipeline de build est responsable de la validation des ressources Data Factory et de la génération du modèle ARM à la place de l’interface utilisateur de Data Factory (bouton **Publier**).
- La définition de mise en production DevOps consomme désormais ce nouveau pipeline de build au lieu de l’artefact Git.

> [!NOTE]
> Vous pouvez continuer à utiliser le mécanisme existant, qui est la branche `adf_publish`, ou vous pouvez utiliser le nouveau flux. Les deux sont pris en charge.

## <a name="package-overview"></a>Vue d’ensemble du package

Deux commandes sont actuellement disponibles dans le package :

- Exporter un modèle ARM
- Valider

### <a name="export-arm-template"></a>Exporter un modèle ARM

Exécutez `npm run start export <rootFolder> <factoryId> [outputFolder]` pour exporter le modèle ARM en utilisant les ressources d’un dossier donné. Cette commande exécute également une vérification de la validation avant de générer le modèle ARM. Voici un exemple :

```
npm run start export C:\DataFactories\DevDataFactory /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/DevDataFactory ArmTemplateOutput
```

- `RootFolder` est un champ obligatoire qui indique où se trouvent les ressources Data Factory.
- `FactoryId` est un champ obligatoire qui représente l’ID de la ressource Data Factory au format `/subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.DataFactory/factories/<dfName>`.
- `OutputFolder` est un paramètre facultatif qui spécifie le chemin d’accès relatif pour enregistrer le modèle ARM généré.
 
> [!NOTE]
> Le modèle ARM généré n’est pas publié dans la version en ligne de la fabrique. Le déploiement doit être effectué à l’aide d’un pipeline de CI/CD.
 
### <a name="validate"></a>Valider

Exécutez `npm run start validate <rootFolder> <factoryId>` pour valider toutes les ressources d’un dossier donné. Voici un exemple :

```
npm run start validate C:\DataFactories\DevDataFactory /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/DevDataFactory
```

- `RootFolder` est un champ obligatoire qui indique où se trouvent les ressources Data Factory.
- `FactoryId` est un champ obligatoire qui représente l’ID de la ressource Data Factory au format `/subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.DataFactory/factories/<dfName>`.

## <a name="create-an-azure-pipeline"></a>Créer un pipeline Azure

Bien que les packages npm puissent être consommés de différentes façons, l’un des principaux avantages est d’être consommé via [Azure Pipelines](https://nam06.safelinks.protection.outlook.com/?url=https:%2F%2Fdocs.microsoft.com%2F%2Fazure%2Fdevops%2Fpipelines%2Fget-started%2Fwhat-is-azure-pipelines%3Fview%3Dazure-devops%23:~:text%3DAzure%2520Pipelines%2520is%2520a%2520cloud%2Cit%2520available%2520to%2520other%2520users.%26text%3DAzure%2520Pipelines%2520combines%2520continuous%2520integration%2Cship%2520it%2520to%2520any%2520target.&data=04%7C01%7Cabnarain%40microsoft.com%7C5f064c3d5b7049db540708d89564b0bc%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C1%7C637423607000268277%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=jo%2BkIvSBiz6f%2B7kmgqDN27TUWc6YoDanOxL9oraAbmA%3D&reserved=0). À chaque fusion dans votre branche de collaboration, vous pouvez déclencher un pipeline qui valide d’abord tout le code, puis exporte le modèle ARM dans un [artefact de build](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2F%2Fazure%2Fdevops%2Fpipelines%2Fartifacts%2Fbuild-artifacts%3Fview%3Dazure-devops%26tabs%3Dyaml%23how-do-i-consume-artifacts&data=04%7C01%7Cabnarain%40microsoft.com%7C5f064c3d5b7049db540708d89564b0bc%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C1%7C637423607000278113%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=dN3t%2BF%2Fzbec4F28hJqigGANvvedQoQ6npzegTAwTp1A%3D&reserved=0) qui peut être consommé par un pipeline de mise en production. La différence avec le processus actuel de CI/CD est que vous *pointerez votre pipeline de mise en production sur cet artefact au lieu de la branche `adf_publish` existante*.

Effectuez d’abord ces étapes :

1.  Ouvrez un projet Azure DevOps et accédez à **Pipelines**. Sélectionnez **Nouveau pipeline**.

    ![Capture d’écran illustrant le bouton Nouveau pipeline.](media/continuous-integration-deployment-improvements/new-pipeline.png)
    
1.  Sélectionnez le référentiel dans lequel vous souhaitez enregistrer le script YAML de votre pipeline. Nous vous recommandons de l’enregistrer dans un dossier build au sein du même référentiel que vos ressources Data Factory. Assurez-vous qu’il existe un fichier *package.json* dans le référentiel qui contient le nom du package, comme indiqué dans l’exemple suivant :

    ```json
    {
        "scripts":{
            "build":"node node_modules/@microsoft/azure-data-factory-utilities/lib/index"
        },
        "dependencies":{
            "@microsoft/azure-data-factory-utilities":"^0.1.5"
        }
    } 
    ```
    
1.  Sélectionnez **Pipeline de démarrage**. Si vous avez chargé ou fusionné le fichier YAML, comme indiqué dans l’exemple suivant, vous pouvez également pointer directement sur ce fichier et le modifier.

    ![Capture d’écran montrant le pipeline Starter.](media/continuous-integration-deployment-improvements/starter-pipeline.png)

    ```yaml
    # Sample YAML file to validate and export an ARM template into a build artifact
    # Requires a package.json file located in the target repository
    
    trigger:
    - main #collaboration branch
    
    pool:
      vmImage: 'ubuntu-latest'
    
    steps:
    
    # Installs Node and the npm packages saved in your package.json file in the build
    
    - task: NodeTool@0
      inputs:
        versionSpec: '10.x'
      displayName: 'Install Node.js'
    
    - task: Npm@1
      inputs:
        command: 'install'
        workingDir: '$(Build.Repository.LocalPath)/<folder-of-the-package.json-file>' #replace with the package.json folder
        verbose: true
      displayName: 'Install npm package'
    
    # Validates all of the Data Factory resources in the repository. You'll get the same validation errors as when "Validate All" is selected.
    # Enter the appropriate subscription and name for the source factory.
    
    - task: Npm@1
      inputs:
        command: 'custom'
        workingDir: '$(Build.Repository.LocalPath)/<folder-of-the-package.json-file>' #replace with the package.json folder
        customCommand: 'run build validate $(Build.Repository.LocalPath) /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName'
      displayName: 'Validate'
    
    # Validate and then generate the ARM template into the destination folder, which is the same as selecting "Publish" from the UX.
    # The ARM template generated isn't published to the live version of the factory. Deployment should be done by using a CI/CD pipeline. 
    
    - task: Npm@1
      inputs:
        command: 'custom'
        workingDir: '$(Build.Repository.LocalPath)/<folder-of-the-package.json-file>' #replace with the package.json folder
        customCommand: 'run build export $(Build.Repository.LocalPath) /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName "ArmTemplate"'
      displayName: 'Validate and Generate ARM template'
    
    # Publish the artifact to be used as a source for a release pipeline.
    
    - task: PublishPipelineArtifact@1
      inputs:
        targetPath: '$(Build.Repository.LocalPath)/<folder-of-the-package.json-file>/ArmTemplate' #replace with the package.json folder
        artifact: 'ArmTemplates'
        publishLocation: 'pipeline'
    ```

1.  Entrez votre code YAML. Nous vous recommandons d’utiliser le fichier YAML comme point de départ.
1.  Enregistrez et exécutez. Si vous avez utilisé le YAML, il se déclenche à chaque mise à jour de la branche primaire.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’intégration et la livraison continues dans Data Factory :

- [Intégration et livraison continues dans Azure Data Factory](continuous-integration-deployment.md)
