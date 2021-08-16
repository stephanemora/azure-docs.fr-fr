---
title: Automatiser les déploiements d’applications dans Azure Spring Cloud
description: Décrit comment utiliser la tâche Azure Spring Cloud pour Azure Pipelines.
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 05/12/2021
ms.author: brendm
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 2df1c8f0e091e553e045efb8aea5665d29bbc53f
ms.sourcegitcommit: 1ee13b62c094a550961498b7a52d0d9f0ae6d9c0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2021
ms.locfileid: "109839326"
---
# <a name="automate-application-deployments-to-azure-spring-cloud"></a>Automatiser les déploiements d’applications dans Azure Spring Cloud

Les outils d’intégration continue et de livraison continue vous permettent de déployer rapidement des mises à jour d’applications existantes avec un minimum d’effort et de risque. Azure DevOps vous aide à organiser et à contrôler ces travaux clés. 

Cet article explique comment utiliser la [tâche Azure Spring Cloud pour Azure pipelines](/azure/devops/pipelines/tasks/deploy/azure-spring-cloud) afin de déployer des applications.

## <a name="create-an-azure-resource-manager-service-connection"></a>Créer une connexion de service Azure Resource Manager

Pour découvrir comment créer une connexion de service Azure Resource Manager à votre projet Azure DevOps, consultez [cet article](/azure/devops/pipelines/library/connect-to-azure). Veillez à sélectionner le même abonnement que celui utilisé pour votre instance de service Azure Spring Cloud.

## <a name="build-and-deploy-apps"></a>Créer et déployer des applications

::: zone pivot="programming-language-csharp"
### <a name="deploy-artifacts"></a>Déployer des artefacts

Vous pouvez générer et déployer vos projets à l’aide d’une série de tâches. Cet extrait de code définit des variables, une tâche .NET Core pour générer l’application et une tâche Azure Spring Cloud pour déployer l’application.

```yaml
variables:
  workingDirectory: './steeltoe-sample'
  planetMainEntry: 'Microsoft.Azure.SpringCloud.Sample.PlanetWeatherProvider.dll'
  solarMainEntry: 'Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.dll'
  planetAppName: 'planet-weather-provider'
  solarAppName: 'solar-system-weather'
  serviceName: '<your service name>'


steps:
# Restore, build, publish and package the zipped planet app
- task: DotNetCoreCLI@2
  inputs:
    command: 'publish'
    publishWebProjects: false
    arguments: '--configuration Release'
    zipAfterPublish: false
    modifyOutputPath: false
    workingDirectory: $(workingDirectory)

# Deploy the planet app
- task: AzureSpringCloud@0
  inputs:
    azureSubscription: '<Service Connection Name>'
    Action: 'Deploy'
    AzureSpringCloud: $(serviceName)
    AppName: 'testapp'
    UseStagingDeployment: false
    DeploymentName: 'default'
    Package: $(workingDirectory)/src/$(planetAppName)/publish-deploy-planet.zip
    RuntimeVersion: 'NetCore_31'
    DotNetCoreMainEntryPath: $(planetMainEntry)

# Deploy the solar app
- task: AzureSpringCloud@0
  inputs:
    azureSubscription: '<Service Connection Name>'
    Action: 'Deploy'
    AzureSpringCloud: $(serviceName)
    AppName: 'testapp'
    UseStagingDeployment: false
    DeploymentName: 'default'
    Package: $(workingDirectory)/src/$(solarAppName)/publish-deploy-solar.zip
    RuntimeVersion: 'NetCore_31'
    DotNetCoreMainEntryPath: $(solarMainEntry)
```

::: zone-end
::: zone pivot="programming-language-java"
### <a name="deploy-artifacts"></a>Déployer des artefacts

#### <a name="to-production"></a>En production

Vous pouvez générer et déployer vos projets à l’aide d’une série de tâches. Cet extrait de code définit tout d’abord une tâche Maven pour générer l’application, suivie d’une deuxième tâche qui déploie le fichier JAR à l’aide de la tâche Azure Spring Cloud pour Azure Pipelines.

```yaml
steps:
- task: Maven@3
  inputs:
    mavenPomFile: 'pom.xml'
- task: AzureSpringCloud@0
  inputs:
    azureSubscription: '<your service connection name>'
    Action: 'Deploy'
    AzureSpringCloud: <your Azure Spring Cloud service>
    AppName: <app-name>
    UseStagingDeployment: false
    DeploymentName: 'default'
    Package: ./target/your-result-jar.jar
```

#### <a name="blue-green-deployments"></a>Déploiement Blue-Green

Le déploiement présenté dans la section précédente reçoit immédiatement le trafic de l’application lors du déploiement. Parfois, les développeurs souhaitent tester leurs applications dans l’environnement de production, mais avant que l’application ne reçoive le trafic des clients.

L’extrait de code suivant génère l’application de la même façon que ci-dessus, puis la déploie dans un déploiement intermédiaire. Dans cet exemple, le déploiement intermédiaire doit déjà exister. Pour une autre approche, consultez [Stratégies de déploiement bleu-vert](concepts-blue-green-deployment-strategies.md).


```yaml
steps:
- task: Maven@3
  inputs:
    mavenPomFile: 'pom.xml'
- task: AzureSpringCloud@0
  inputs:
    azureSubscription: '<your service connection name>'
    Action: 'Deploy'
    AzureSpringCloud: <your Azure Spring Cloud service>
    AppName: <app-name>
    UseStagingDeployment: true
    Package: ./target/your-result-jar.jar
- task: AzureSpringCloud@0
  inputs:
    azureSubscription: '<your service connection name>'
    Action: 'Set Production'
    AzureSpringCloud: <your Azure Spring Cloud service>
    AppName: <app-name>
    UseStagingDeployment: true
```

### <a name="deploy-from-source"></a>Déployer à partir de la source

Il est possible de déployer directement sur Azure sans étape de génération distincte.

```yaml
- task: AzureSpringCloud@0
  inputs:
    azureSubscription: '<your service connection name>'
    Action: 'Deploy'
    AzureSpringCloud: <your Azure Spring Cloud service>
    AppName: <app-name>
    UseStagingDeployment: false
    DeploymentName: 'default'
    Package: $(Build.SourcesDirectory)
```
::: zone-end

## <a name="next-steps"></a>Étapes suivantes

* [Démarrage rapide : Déployer votre première application Azure Spring Cloud](./quickstart.md)

