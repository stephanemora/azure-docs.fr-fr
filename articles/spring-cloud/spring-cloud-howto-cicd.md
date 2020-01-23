---
title: CI/CD pour Azure Spring Cloud
description: CI/CD pour Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/04/2019
ms.author: brendm
ms.openlocfilehash: f329fb5472c5a2eab6f22a2e81b19d90e7045330
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2020
ms.locfileid: "76278508"
---
# <a name="cicd-for-azure-spring-cloud"></a>CI/CD pour Azure Spring Cloud

Les outils d’intégration continue et de livraison continue permettent aux développeurs de déployer rapidement des mises à jour d’applications existantes avec un minimum d’effort et de risque. Azure DevOps vous aide à organiser et à contrôler ces travaux clés. Actuellement, Azure Spring Cloud n’offre pas de plug-in Azure DevOps spécifique.  Toutefois, vous pouvez intégrer vos applications Spring Cloud à DevOps à l’aide d’une [tâche Azure CLI](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-cli?view=azure-devops). Cet article montre comment utiliser une tâche Azure CLI avec Azure Spring Cloud pour l’intégration avec Azure DevOps.

## <a name="create-an-azure-resource-manager-service-connection"></a>Créer une connexion de service Azure Resource Manager

Pour découvrir comment créer une connexion de service Azure Resource Manager à votre projet Azure DevOps, consultez [cet article](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure?view=azure-devops). Veillez à sélectionner le même abonnement que celui utilisé pour votre instance de service Azure Spring Cloud.

## <a name="azure-cli-task-templates"></a>Modèles de tâche Azure CLI

### <a name="deploy-artifacts"></a>Déployer des artefacts

Vous pouvez générer et déployer vos projets à l’aide d’une série de `tasks`. Cet extrait de code définit tout d’abord une tâche Maven pour générer l’application, suivie d’une deuxième tâche qui déploie le fichier JAR à l’aide de l’extension Azure CLI Azure Spring Cloud.

```yaml
steps:
- task: Maven@3
  inputs:
    mavenPomFile: 'pom.xml'
- task: AzureCLI@1
  inputs:
    azureSubscription: <your service connection name>
    scriptLocation: inlineScript
    inlineScript: |
      az extension add -y --name spring-cloud
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name> --jar-path ./target/your-result-jar.jar
      # deploy other app
```

### <a name="deploy-from-source"></a>Déployer à partir de la source

Il est possible de déployer directement sur Azure sans étape de génération distincte.

```yaml
- task: AzureCLI@1
  inputs:
    azureSubscription: <your service connection name>
    scriptLocation: inlineScript
    inlineScript: |
      az extension add -y --name spring-cloud
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name>

      # or if it is a multi-module project
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name> --target-module relative/path/to/module
```
