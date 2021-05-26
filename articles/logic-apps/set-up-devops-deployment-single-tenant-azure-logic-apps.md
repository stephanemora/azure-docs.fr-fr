---
title: Configurer DevOps pour Azure Logic Apps monolocataire
description: Découvrez comment configurer le déploiement DevOps pour les workflows dans Azure Logic Apps monolocataire.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 05/25/2021
ms.openlocfilehash: bd35af7ac6602bba7e23bd10eda2f2b0fdff71db
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110379751"
---
# <a name="set-up-devops-deployment-for-single-tenant-azure-logic-apps"></a>Configurer le déploiement DevOps pour Azure Logic Apps monolocataire

Cet article explique comment déployer un projet d’application logique monolocataire à partir de Visual Studio Code sur votre infrastructure en utilisant des outils et de processus DevOps. Selon que vous préférez GitHub ou Azure DevOps pour le déploiement, choisissez la voie et les outils qui conviennent le mieux à votre scénario. Vous pouvez utiliser les exemples inclus qui contiennent des exemples de projets d’application logique ainsi que des exemples de déploiement Azure avec GitHub ou Azure DevOps. Pour plus d’informations sur DevOps pour un locataire unique, consultez [Vue d’ensemble du déploiement DevOps pour Azure Logic Apps monolocataire](devops-deployment-single-tenant-azure-logic-apps.md).

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. Si vous n’avez pas d’abonnement Azure, [créez un compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Un projet d’application logique monolocataire créé avec [Visual Studio Code et l’extension Azure Logic Apps (standard)](create-single-tenant-workflows-visual-studio-code.md#prerequisites).

  Si vous ne disposez pas déjà d’un projet ou d’une infrastructure d’application logique, vous pouvez utiliser les exemples de projets inclus pour déployer un exemple d’application et d’infrastructure, en fonction des options de source et de déploiement que vous préférez utiliser. Pour plus d’informations sur ces exemples de projets et les ressources incluses pour exécuter l’exemple d’application logique, consultez [Déployer votre infrastructure](#deploy-infrastructure).

- Si vous souhaitez effectuer un déploiement sur Azure, vous avez besoin d’une ressource **Application logique (standard)** existante créée dans Azure. Pour créer rapidement une ressource d’application logique vide, consultez [Créer des workflows d’application logique monolocataire - Portail](create-single-tenant-workflows-azure-portal.md).

<a name="deploy-infrastructure"></a>

## <a name="deploy-infrastructure-resources"></a>Déployer des ressources d’infrastructure

Si vous ne disposez pas déjà d’un projet ou d’une infrastructure d’application logique, vous pouvez utiliser les exemples de projets suivants pour déployer un exemple d’application et d’infrastructure, en fonction des options de source et de déploiement que vous préférez utiliser :

- [Exemple GitHub pour Azure Logic Apps monolocataire](https://github.com/Azure/logicapps/tree/master/github-sample)

  Cet exemple comprend un exemple de projet d’application logique pour Azure Logic Apps monolocataire ainsi que des exemples pour le déploiement Azure et pour GitHub Actions.

- [Exemple Azure DevOps pour Azure Logic Apps monolocataire](https://github.com/Azure/logicapps/tree/master/azure-devops-sample)

  Cet exemple comprend un exemple de projet d’application logique pour Azure Logic Apps monolocataire ainsi que des exemples pour le déploiement Azure et pour Azure Pipelines.
  
Les deux exemples incluent les ressources suivantes qu’une application logique utilise pour s’exécuter.

| Nom de la ressource | Obligatoire | Description |
|---------------|----------|-------------|
| Application logique (standard) | Yes | Cette ressource Azure contient les workflows qui s’exécutent dans Azure Logic Apps monolocataire. |
| Plan d’hébergement Functions Premium ou App Service | Yes | Cette ressource Azure spécifie les ressources d’hébergement à utiliser pour l’exécution de votre application logique, telles que le calcul, le traitement, le stockage, le réseau, etc. <p><p>**Important** : Dans l’expérience actuelle, la ressource **Application logique (standard)** nécessite le [plan d’hébergement **Workflow Standard**](logic-apps-pricing.md#standard-pricing), qui est basé sur le plan d’hébergement Functions Premium. |
| Compte Azure Storage | Oui, pour les workflows sans état | Cette ressource Azure stocke les métadonnées, l’état, les entrées, les sorties, l’historique des exécutions et d’autres informations sur vos workflows. |
| Application Insights | Facultatif | Cette ressource Azure fournit des fonctionnalités de supervision pour vos workflows. |
| Connexions d’API | Facultatif, s’il n’en existe aucune | Ces ressources Azure définissent toutes les connexions d’API managées que vos workflows utilisent pour exécuter des opérations de connecteur managé, comme Office 365, SharePoint, etc. <p><p>**Important** : Dans votre projet d’application logique, le fichier **connections.json** contient des métadonnées, des points de terminaison et des clés pour toutes les connexions d’API managées et fonctions Azure utilisées par vos workflows. Pour utiliser des connexions et des fonctions différentes dans chaque environnement, veillez à paramétriser le fichier **connections.json** et à mettre à jour les points de terminaison. <p><p>Pour plus d’informations, consultez [Ressources de connexion d’API et stratégies d’accès](#api-connection-resources). |
| Modèle Azure Resource Manager (ARM) | Facultatif | Cette ressource Azure définit un déploiement d’infrastructure de référence que vous pouvez réutiliser ou [exporter](../azure-resource-manager/templates/template-tutorial-export-template.md). Le modèle comprend également les stratégies d’accès requises, par exemple, pour utiliser des connexions d’API managées. <p><p>**Important** : L’exportation du modèle ARM n’inclut pas tous les paramètres associés pour les ressources de connexion d’API utilisées par vos workflows. Pour plus d’informations, consultez [Rechercher les paramètres de connexion d’API](#find-api-connection-parameters). |
||||

<a name="api-connection-resources"></a>

## <a name="api-connection-resources-and-access-policies"></a>Ressources de connexion d’API et stratégies d’accès

Dans Azure Logic Apps monolocataire, chaque ressource de connexion d’API ou managée dans vos workflows nécessite une stratégie d’accès associée. Cette stratégie a besoin de l’identité de votre application logique pour fournir les autorisations permettant d’accéder à l’infrastructure de connecteur managée. Les exemples de projets inclus comprennent un modèle ARM qui comporte toutes les ressources d’infrastructure nécessaires, y compris ces stratégies d’accès.

Le diagramme suivant montre les dépendances entre votre projet d’application logique et les ressources d’infrastructure :

![Diagramme conceptuel montrant les dépendances d’infrastructure pour un projet d’application logique dans le modèle Azure Logic Apps monolocataire.](./media/set-up-devops-deployment-single-tenant-azure-logic-apps/infrastructure-dependencies.png)

<a name="find-api-connection-parameters"></a>

### <a name="find-api-connection-parameters"></a>Rechercher les paramètres de connexion d’API

Si vos workflows utilisent des connexions d’API managées, l’utilisation de la fonctionnalité d’exportation de modèle n’inclut pas tous les paramètres associés. Dans un modèle ARM, chaque [définition de ressource de connexion d’API](logic-apps-azure-resource-manager-templates-overview.md#connection-resource-definitions) a le format général suivant :

```json
{
   "type": "Microsoft.Web/connections",
   "apiVersion": "2016–06–01",
   "location": "[parameters('location')]",
   "name": "[parameters('connectionName')]",
   "properties": {}
}
```

Pour rechercher les valeurs que vous devez utiliser dans l’objet `properties` afin d’élaborer la définition de la ressource de connexion, vous pouvez utiliser l’API suivante pour un connecteur spécifique :

`PUT https://management.azure.com/subscriptions/{subscription-ID}/providers/Microsoft.Web/locations/{location}/managedApis/{connector-name}?api-version=2018–07–01-preview`

Dans la réponse, recherchez l’objet `connectionParameters`, qui contient toutes les informations permettant d’élaborer la définition de ressource pour ce connecteur spécifique. L’exemple suivant illustre une définition de ressource pour une connexion managée SQL :

```json
{
   "type": "Microsoft.Web/connections",
   "apiVersion": "2016–06–01",
   "location": "[parameters('location')]",
   "name": "[parameters('connectionName')]",
   "properties": {
      "displayName": "sqltestconnector",
      "api": {
         "id": "/subscriptions/{subscription-ID}/providers/Microsoft.Web/locations/{location}/managedApis/sql"
      },
      "parameterValues": {
         "authType": "windows", 
         "database": "TestDB",
         "password": "TestPassword",
         "server": "TestServer",
         "username": "TestUserName"
      }
   }
}
```

Vous pouvez également vérifier la trace réseau en prévision de la création d’une connexion dans le concepteur Logic Apps. Recherchez l’appel `PUT` à l’API managée pour le connecteur comme décrit précédemment, puis examinez le corps de la demande afin d’obtenir toutes les informations dont vous avez besoin.

## <a name="deploy-logic-app-resources-zip-deploy"></a>Déployer des ressources d’application logique (déploiement zip)

Après avoir poussé votre projet d’application logique vers votre dépôt source, vous pouvez configurer des pipelines de build et de mise en production qui déploient des applications logiques sur une infrastructure à l’intérieur ou en dehors d’Azure.

### <a name="build-your-project"></a>Générer votre projet

Pour configurer un pipeline de build basé sur le type de projet de votre application logique, suivez les actions correspondantes :

* Basé sur NuGet : la structure de projet NuGet est basée sur le .NET Framework. Pour générer ces projets, veillez à suivre les étapes de génération de .NET Standard. Pour plus d’informations, consultez la documentation [Créer un package NuGet avec MSBuild](/nuget/create-packages/creating-a-package-msbuild).

* Basé sur un bundle : le projet d’extension basé sur un bundle n’est pas propre à un langage et ne nécessite aucune étape de génération propre au langage. Vous pouvez utiliser n’importe quelle méthode pour compresser vos fichiers projet.

  > [!IMPORTANT]
  > Assurez-vous que le fichier. zip comprend tous les dossiers de workflow, les fichiers de configuration, tels que host.json, connections.json et tout autre fichier associé.

### <a name="release-to-azure"></a>Publier sur Azure

Pour configurer un pipeline de mise en production qui effectue le déploiement sur Azure, choisissez l’option associée pour GitHub, Azure DevOps ou Azure CLI.

> [!NOTE]
> Azure Logic Apps ne prend pas en charge les emplacements de déploiement Azure.

#### <a name="github"></a>[GitHub](#tab/github)

Pour les déploiements GitHub, vous pouvez déployer votre application logique avec [GitHub Actions](https://docs.github.com/actions), par exemple, l’action GitHub dans Azure Functions. Pour effectuer cette action, vous devez transmettre les informations suivantes :

* Votre artefact de build
* Nom d’application logique à utiliser pour le déploiement
* Votre profil de publication

```yaml
- name: 'Run Azure Functions Action'
  uses: Azure/functions-action@v1
  id: fa
  with:
   app-name: {your-logic-app-name}
   package: '{your-build-artifact}.zip'
   publish-profile: {your-logic-app-publish-profile}
```

Pour plus d’informations, consultez la documentation [Livraison continue avec GitHub Actions](../azure-functions/functions-how-to-github-actions.md).

#### <a name="azure-devops"></a>[Azure DevOps](#tab/azure-devops)

Pour les déploiements Azure DevOps, vous pouvez déployer votre application logique en utilisant la [tâche de déploiement d’application de fonction Azure](/devops/pipelines/tasks/deploy/azure-function-app) dans Azure Pipelines. Pour effectuer cette action, vous devez transmettre les informations suivantes :

* Votre artefact de build
* Nom d’application logique à utiliser pour le déploiement
* Votre profil de publication

```yaml
- task: AzureFunctionApp@1
  displayName: 'Deploy logic app workflows'
  inputs:
     azureSubscription: '{your-service-connection}'
     appType: 'workflowapp'
     appName: '{your-logic-app-name}'
     package: '{your-build-artifact}.zip'
     deploymentMethod: 'zipDeploy'
```

Pour plus d’informations, consultez la documentation [Déployer une fonction Azure avec Azure Pipelines](/devops/pipelines/targets/azure-functions-windows).

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Si vous utilisez d’autres outils de déploiement, vous pouvez déployer votre application logique avec les commandes Azure CLI pour Azure Logic Apps monolocataire. Par exemple, pour déployer votre artefact compressé sur un groupe de ressources Azure, exécutez la commande CLI suivante :

`az logicapp deployment source config-zip -g {your-resource-group} --name {your-logic-app-name} --src {your-build-artifact}.zip`

---

### <a name="release-to-containers"></a>Publier sur des conteneurs

Si vous conteneurisez votre application logique, le déploiement fonctionne essentiellement comme tout autre conteneur déployé et géré par vos soins.

Pour obtenir des exemples qui montrent comment implémenter un pipeline de déploiement et de build de conteneur de bout en bout, consultez [CI/CD pour les conteneurs](https://azure.microsoft.com/solutions/architecture/cicd-for-containers/).

## <a name="next-steps"></a>Étapes suivantes

* [déploiement DevOps pour Azure Logic Apps monolocataire](devops-deployment-single-tenant-azure-logic-apps.md)

Nous aimerions que vous nous fassiez part de votre expérience avec Azure Logic Apps monolocataire.

- Pour tout bogue ou problème que vous rencontreriez, [créez un problème dans GitHub](https://github.com/Azure/logicapps/issues).
- Pour toute question, demande, commentaire ou retour d’expérience, utilisez de [formulaire de commentaires](https://aka.ms/logicappsdevops).