---
title: Publication automatisée pour l’intégration et la livraison continues
description: Découvrez comment publier automatiquement pour une intégration et une livraison continues.
ms.service: data-factory
author: nabhishek
ms.author: abnarain
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: 49ec43e59989f3fdad8f5731867953cc7cbb5757
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101699706"
---
# <a name="automated-publishing-for-continuous-integration-and-delivery"></a>Publication automatisée pour l’intégration et la livraison continues

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>Vue d’ensemble

L’intégration continue consiste à tester chaque modification apportée à votre codebase automatiquement et le plus tôt possible. La livraison continue suit les tests qui ont lieu pendant l’intégration continue et envoie (push) les modifications à un système de mise en lots ou de production.

Dans Azure Data Factory, l’intégration et la livraison continues (CI/CD) impliquent de déplacer des pipelines Data Factory d’un environnement (développement, test, production) vers un autre. Azure Data Factory utilise des [modèles Azure Resource Manager](../azure-resource-manager/templates/overview.md) pour stocker la configuration de vos diverses entités ADF (pipelines, jeux de données, flux de données, etc.). Deux méthodes sont recommandées pour promouvoir une fabrique de données dans un autre environnement :

- Déploiement automatisé grâce à l’intégration de Data Factory avec [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines).
- Chargement manuel d’un modèle Resource Manager en tirant parti de l’intégration de l’expérience utilisateur de Data Factory avec Azure Resource Manager.

Pour plus d’informations, consultez [Intégration et livraison continues dans Azure Data Factory](continuous-integration-deployment.md).

Dans cet article, nous nous concentrons sur les améliorations du déploiement continu et la fonctionnalité de publication automatisée pour CI/CD.

## <a name="continuous-deployment-improvements"></a>Améliorations du déploiement continu

La fonctionnalité « Publication automatisée » prend les fonctionnalités *Valider tout* et *Exporter le modèle Azure Resource Manager (ARM)* de l’expérience utilisateur d’ADF et rend la logique consommable via un package npm disponible publiquement [@microsoft/azure-data-factory-utilities](https://www.npmjs.com/package/@microsoft/azure-data-factory-utilities). Cela vous permet de déclencher ces actions par programmation au lieu de devoir accéder à l’interface utilisateur d’ADF et de cliquer sur un bouton. Cela donnera à vos pipelines de CI/CD une expérience d’intégration continue plus vraie.

### <a name="current-cicd-flow"></a>Flux actuel de CI/CD

1. Chaque utilisateur apporte des modifications à ses branches privées.
2. L’envoi (push) vers le master est interdit, les utilisateurs doivent créer une demande de tirage (pull request) adressée au master pour apporter des modifications.
3. Les utilisateurs doivent charger l’interface utilisateur d’ADF, puis cliquer sur Publier pour déployer les modifications apportées à Data Factory et générer les modèles ARM dans la branche Publier.
4. Le pipeline de mise en production DevOps est configuré pour créer une nouvelle version et déployer le modèle ARM chaque fois qu’une nouvelle modification est envoyée (push) à la branche de publication.

![Flux actuel de CI/CD](media/continuous-integration-deployment-improvements/current-ci-cd-flow.png)

### <a name="manual-step"></a>Étape manuelle

Dans le flux actuel de CI/CD, l’expérience utilisateur est l’intermédiaire permettant de créer le modèle ARM. Par conséquent, un utilisateur doit accéder à l’interface utilisateur d’ADF et cliquer manuellement sur Publier pour lancer la génération du modèle ARM et le déplacer dans la branche de publication, ce qui constitue un peu du piratage.

### <a name="the-new-cicd-flow"></a>Nouveau flux de CI/CD

1. Chaque utilisateur apporte des modifications à ses branches privées.
2. L’envoi (push) vers le master est interdit, les utilisateurs doivent créer une demande de tirage (pull request) adressée au master pour apporter des modifications.
3. **La BLD du pipeline Azure DevOps est déclenchée chaque fois qu’une nouvelle validation est effectuée sur le master, valide les ressources et génère un modèle ARM comme artefact si la validation réussit.**
4. Le pipeline de mise en production DevOps est configuré pour créer une nouvelle version et déployer le modèle ARM chaque fois qu’une nouvelle BLD est disponible. 

![Nouveau flux de CI/CD](media/continuous-integration-deployment-improvements/new-ci-cd-flow.png)

### <a name="what-changed"></a>Qu’est ce qui a changé ?

- Nous disposons maintenant d’un processus de génération utilisant un pipeline de build DevOps.
- Le pipeline de build utilise le package NPM ADFUtilities, qui valide toutes les ressources et génère les modèles ARM (modèles uniques et liés).
- Le pipeline de build est chargé de valider les ressources ADF et de générer le modèle ARM au lieu de l’interface utilisateur d’ADF (bouton Publier).
- La définition de mise en version DevOps consomme désormais ce nouveau pipeline de build au lieu de l’artefact Git.

> [!NOTE]
> Vous pouvez continuer à utiliser le mécanisme existant (branche adf_publish) ou utiliser le nouveau flux. Les deux sont pris en charge. 

## <a name="package-overview"></a>Vue d’ensemble du package

Deux commandes sont actuellement disponibles dans le package :
- Exporter un modèle ARM
- Valider

### <a name="export-arm-template"></a>Exporter un modèle ARM

Exécutez la commande npm run start export <rootFolder> <factoryId> [outputFolder] pour exporter le modèle ARM en utilisant les ressources d’un dossier donné. Cette commande exécute également une vérification de la validation avant de générer le modèle ARM. Voici un exemple :

```
npm run start export C:\DataFactories\DevDataFactory /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/DevDataFactory ArmTemplateOutput
```

- RootFolder est un champ obligatoire qui indique où se trouvent les ressources Data Factory.
- FactoryId est un champ obligatoire qui représente l’ID de la ressource Data Factory au format suivant : « /subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.DataFactory/factories/<dfName> ».
- OutputFolder est un paramètre facultatif qui spécifie le chemin d’accès relatif pour enregistrer le modèle ARM généré.
 
> [!NOTE]
> Le modèle ARM généré n’est pas publié dans la version `Live` de la fabrique. Le déploiement doit être effectué à l’aide d’un pipeline de CI/CD. 
 

### <a name="validate"></a>Valider

Exécutez la commande npm run start validate <rootFolder> <factoryId> pour valider toutes les ressources d’un dossier donné. Voici un exemple :
    
```
npm run start validate C:\DataFactories\DevDataFactory /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/DevDataFactory
```

- RootFolder est un champ obligatoire qui indique où se trouvent les ressources Data Factory.
- FactoryId est un champ obligatoire qui représente l’ID de la ressource Data Factory au format suivant : « /subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.DataFactory/factories/<dfName> ».


## <a name="create-an-azure-pipeline"></a>Créer un pipeline Azure

Bien que les packages npm puissent être consommés de différentes façons, l’un des principaux avantages est d’être consommé via un [pipeline Azure](https://nam06.safelinks.protection.outlook.com/?url=https:%2F%2Fdocs.microsoft.com%2F%2Fazure%2Fdevops%2Fpipelines%2Fget-started%2Fwhat-is-azure-pipelines%3Fview%3Dazure-devops%23:~:text%3DAzure%2520Pipelines%2520is%2520a%2520cloud%2Cit%2520available%2520to%2520other%2520users.%26text%3DAzure%2520Pipelines%2520combines%2520continuous%2520integration%2Cship%2520it%2520to%2520any%2520target.&data=04%7C01%7Cabnarain%40microsoft.com%7C5f064c3d5b7049db540708d89564b0bc%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C1%7C637423607000268277%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=jo%2BkIvSBiz6f%2B7kmgqDN27TUWc6YoDanOxL9oraAbmA%3D&reserved=0). À chaque fusion dans votre branche de collaboration, vous pouvez déclencher un pipeline qui valide d’abord tout le code, puis exporte le modèle ARM dans un [artefact de build](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2F%2Fazure%2Fdevops%2Fpipelines%2Fartifacts%2Fbuild-artifacts%3Fview%3Dazure-devops%26tabs%3Dyaml%23how-do-i-consume-artifacts&data=04%7C01%7Cabnarain%40microsoft.com%7C5f064c3d5b7049db540708d89564b0bc%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C1%7C637423607000278113%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=dN3t%2BF%2Fzbec4F28hJqigGANvvedQoQ6npzegTAwTp1A%3D&reserved=0) qui peut être consommé par un pipeline de mise en production. **La différence avec le processus actuel de CI/CD est que vous pointerez votre pipeline de mise en production sur cet artefact au lieu de la branche `adf_publish` existante.**

Suivez les étapes ci-dessous pour commencer :

1.  Ouvrez un projet Azure DevOps et accédez à « Pipelines ». Sélectionnez « Nouveau pipeline ».

    ![Nouveau pipeline](media/continuous-integration-deployment-improvements/new-pipeline.png)
    
2.  Sélectionnez le référentiel dans lequel vous souhaitez enregistrer le script YAML de votre pipeline. Nous vous recommandons de l’enregistrer dans un dossier *build* au sein du même référentiel que vos ressources ADF. Assurez-vous qu’il existe également un fichier **package.json** dans le référentiel, qui contient le nom du package (comme indiqué dans l’exemple ci-dessous).

    ```json
    {
        "scripts":{
            "build":"node node_modules/@microsoft/azure-data-factory-utilities/lib/index"
        },
        "dependencies":{
            "@microsoft/azure-data-factory-utilities":"^0.1.3"
        }
    } 
    ```
    
3.  Sélectionnez *Pipeline de démarrage*. Si vous avez chargé ou fusionné le fichier YAML (comme indiqué dans l’exemple ci-dessous), vous pouvez également pointer directement vers ce fichier et le modifier. 

    ![Pipeline de démarrage](media/continuous-integration-deployment-improvements/starter-pipeline.png) 

    ```yaml
    # Sample YAML file to validate and export an ARM template into a Build Artifact
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
        verbose: true
      displayName: 'Install npm package'
    
    # Validates all of the ADF resources in the repository. You will get the same validation errors as when "Validate All" is clicked
    # Enter the appropriate subscription and name for the source factory 
    
    - task: Npm@1
      inputs:
        command: 'custom'
        customCommand: 'run build validate $(Build.Repository.LocalPath) /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName'
      displayName: 'Validate'
    
    # Validate and then generate the ARM template into the destination folder. Same as clicking "Publish" from UX
    # The ARM template generated is not published to the ‘Live’ version of the factory. Deployment should be done using a CI/CD pipeline. 
    
    - task: Npm@1
      inputs:
        command: 'custom'
        customCommand: 'run build export $(Build.Repository.LocalPath) /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName "ArmTemplate"'
      displayName: 'Validate and Generate ARM template'
    
    # Publish the Artifact to be used as a source for a release pipeline
    
    - task: PublishPipelineArtifact@1
      inputs:
        targetPath: '$(Build.Repository.LocalPath)/ArmTemplate'
        artifact: 'ArmTemplates'
        publishLocation: 'pipeline'
    ```

4.  Entrez dans votre code YAML. Nous vous recommandons de prendre le fichier YAML et de l’utiliser comme point de départ.
5.  Enregistrez et exécutez. Si vous utilisez le YAML, il sera déclenché à chaque mise à jour de la branche « principale ».

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’intégration et la livraison continues dans Data Factory : 

- [Intégration et livraison continues dans Azure Data Factory](continuous-integration-deployment.md)
