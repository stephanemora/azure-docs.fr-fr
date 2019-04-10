---
title: Déployer des applications logiques avec les modèles Azure Resource Manager - Azure Logic Apps
description: Déployer des applications logiques à l’aide de modèles Azure Resource Manager
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.assetid: 7574cc7c-e5a1-4b7c-97f6-0cffb1a5d536
ms.date: 10/15/2017
ms.openlocfilehash: bbb10bf0174b6e06e28d171510345ed92b6642d9
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2019
ms.locfileid: "59357094"
---
# <a name="deploy-logic-apps-with-azure-resource-manager-templates"></a>Déployer des applications logiques avec des modèles Azure Resource Manager

Après avoir créé un modèle Azure Resource Manager pour déployer votre application logique, vous pouvez déployer votre modèle des façons suivantes :

* [Portail Azure](#portal)
* [Azure PowerShell](#powershell)
* [Azure CLI](#cli)
* [API REST Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy-rest.md)
* [Azure DevOps Azure Pipelines](#azure-pipelines)

<a name="portal"></a>

## <a name="deploy-through-azure-portal"></a>Déployer via le portail Azure

Pour déployer automatiquement un modèle d’application logique sur Azure, vous pouvez choisir les éléments suivants **déployer sur Azure** bouton, ce qui se connecte au portail Azure et vous demande les informations relatives à votre application logique. Vous pouvez puis apporter les modifications nécessaires pour le modèle d’application logique ou les paramètres.

[![Déployer vers Azure](./media/logic-apps-create-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

Par exemple, vous êtes invité à entrer ces informations après que vous être connecté au portail Azure :

* Nom de l’abonnement Azure
* Groupe de ressources que vous souhaitez utiliser
* Emplacement de l’application logique
* Nom de l’application logique.
* URI de test
* Acceptation des termes et conditions spécifiés

Pour plus d’informations, consultez [déployer des ressources avec des modèles Azure Resource Manager et le portail Azure](../azure-resource-manager/resource-group-template-deploy-portal.md).

## <a name="authorize-oauth-connections"></a>Autoriser des connexions OAuth

Après le déploiement, l’application logique fonctionne de bout en bout avec des paramètres valides. Toutefois, vous devez autoriser des connexions OAuth pour générer un jeton d’accès valide. Pour les déploiements automatisés, vous pouvez utiliser un script qui donne son consentement à chacune des connexions OAuth, telle que celle-ci [exemple de script dans le projet GitHub LogicAppConnectionAuth](https://github.com/logicappsio/LogicAppConnectionAuth). Vous pouvez également autoriser les connexions OAuth via le portail Azure ou dans Visual Studio en ouvrant votre application logique dans le Concepteur d’applications logiques.

<a name="powershell"></a>

## <a name="deploy-with-azure-powershell"></a>Déployer avec Azure PowerShell

Pour déployer à un spécifique *groupe de ressources Azure*, utilisez la commande suivante :

```powershell
New-AzResourceGroupDeployment -ResourceGroupName <Azure-resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json 
```

Pour déployer sur un abonnement Azure spécifique, utilisez cette commande :

```powershell
New-AzDeployment -Location <location> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json 
```

* [Déployer des ressources à l’aide de modèles Resource Manager et d’Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)
* [`New-AzResourceGroupDeployment`](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment)
* [`New-AzDeployment`](/powershell/module/az.resources/new-azdeployment)

<a name="cli"></a>

## <a name="deploy-with-azure-cli"></a>Déploiement avec l’interface de ligne de commande Azure

Pour déployer à un spécifique *groupe de ressources Azure*, utilisez la commande suivante :

```azurecli
az group deployment create -g <Azure-resource-group-name> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

Pour déployer sur un abonnement Azure spécifique, utilisez cette commande :

```azurecli
az deployment create --location <location> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

Pour plus d’informations, consultez les rubriques suivantes : 

* [Déployer des ressources à l’aide de modèles Resource Manager et dAzure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md) 
* [`az group deployment create`](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create)
* [`az deployment create`](https://docs.microsoft.com/cli/azure/deployment?view=azure-cli-latest#az-deployment-create)

<a name="azure-pipelines"></a>

## <a name="deploy-with-azure-devops"></a>Déployer avec Azure DevOps

Pour déployer des modèles d’application logique et gérer des environnements, les équipes utilisent généralement un outil tel que [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/what-is-azure-pipelines) dans [Azure DevOps](https://docs.microsoft.com/azure/devops/user-guide/what-is-azure-devops-services). Les Pipelines Azure fournit un [tâche Déploiement de groupe de ressources Azure](https://github.com/Microsoft/azure-pipelines-tasks/tree/master/Tasks/AzureResourceGroupDeploymentV2) que vous pouvez ajouter à n’importe quel build ou mise en production de pipeline.
Pour l’autorisation déployer et générer le pipeline de mise en production, vous devez également un Azure Active Directory (AD) [principal du service](../active-directory/develop/app-objects-and-service-principals.md). En savoir plus sur [à l’aide des principaux de service avec les Pipelines Azure](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure). 

Voici les étapes générales pour l’utilisation des Pipelines d’Azure :

1. Dans les Pipelines d’Azure, créez un pipeline vide.

1. Choisissez les ressources que vous avez besoin pour le pipeline, telles que votre modèle d’application logique et les fichiers de paramètres de modèle, ce qui vous générez manuellement ou dans le cadre du processus de génération.

1. Votre travail d’agent, rechercher et ajouter la **déploiement de groupe de ressources Azure** tâche.

   ![Ajouter la tâche « Déploiement de groupe de ressources Azure »](./media/logic-apps-create-deploy-template/add-azure-resource-group-deployment-task.png)

1. Configurer avec un [principal du service](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure). 

1. Ajoutez des références à votre modèle d’application logique et les fichiers de paramètres de modèle.

1. Continuez à suivre les étapes du processus de version pour les autres environnements, tests automatisés ou approbateurs dont vous avez besoin.

## <a name="get-support"></a>Obtenir de l’aide

Si vous avez des questions, consultez le [forum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Surveiller les applications logiques](../logic-apps/logic-apps-monitor-your-logic-apps.md)
