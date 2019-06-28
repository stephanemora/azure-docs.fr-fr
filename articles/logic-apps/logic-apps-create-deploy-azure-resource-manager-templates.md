---
title: Déployer des applications logiques à l’aide de modèles Azure Resource Manager - Azure Logic Apps
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
ms.openlocfilehash: 21603ff213bc8babb4145209a76aee0b4150cc12
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60681737"
---
# <a name="deploy-logic-apps-with-azure-resource-manager-templates"></a>Déployer des applications logiques à l’aide de modèles Azure Resource Manager

Après avoir créé un modèle Azure Resource Manager avec lequel déployer votre application logique, vous pouvez le déployer à l’aide de l’une des méthodes suivantes :

* [Portail Azure](#portal)
* [Azure PowerShell](#powershell)
* [Interface de ligne de commande Azure](#cli)
* [API Azure Resource Manager REST](../azure-resource-manager/resource-group-template-deploy-rest.md)
* [Azure DevOps Azure Pipelines](#azure-pipelines)

<a name="portal"></a>

## <a name="deploy-through-azure-portal"></a>Déployer par le biais du portail Azure

Pour déployer automatiquement un modèle d’application logique sur Azure, vous pouvez utiliser le bouton **Déployer sur Azure** suivant. Vous êtes alors connecté au portail Azure, puis invité à entrer des informations relatives à votre application logique. Vous pouvez ensuite apporter les modifications nécessaires au modèle ou aux paramètres de l’application logique.

[![Déployer sur Azure](./media/logic-apps-create-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

Par exemple, vous êtes invité à entrer ces informations une fois que vous êtes connecté au portail Azure :

* Nom de l’abonnement Azure
* Groupe de ressources que vous souhaitez utiliser
* Emplacement de l’application logique
* Nom de l’application logique.
* URI de test
* Acceptation des termes et conditions spécifiés

Pour plus d’informations, consultez [Déployer des ressources à l’aide de modèles Azure Resource Manager et du portail Azure](../azure-resource-manager/resource-group-template-deploy-portal.md).

## <a name="authorize-oauth-connections"></a>Autoriser des connexions OAuth

Après le déploiement, l’application logique fonctionne de bout en bout avec des paramètres valides. Toutefois, vous devez autoriser des connexions OAuth pour générer un jeton d’accès valide. Dans le cas des déploiements automatisés, vous pouvez utiliser un script qui donne son consentement à chaque connexion OAuth, comme dans cet [exemple de script pour le projet LogicAppConnectionAuth sur GitHub](https://github.com/logicappsio/LogicAppConnectionAuth). Vous pouvez également autoriser les connexions OAuth via le portail Azure ou dans Visual Studio en ouvrant votre application logique dans le Concepteur d’applications logiques.

<a name="powershell"></a>

## <a name="deploy-with-azure-powershell"></a>Déployer avec Azure PowerShell

Pour effectuer le déploiement dans un *groupe de ressources Azure* spécifique, utilisez cette commande :

```powershell
New-AzResourceGroupDeployment -ResourceGroupName <Azure-resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json 
```

Pour effectuer le déploiement dans un abonnement Azure spécifique, utilisez cette commande :

```powershell
New-AzDeployment -Location <location> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json 
```

* [Déployer des ressources à l’aide de modèles Resource Manager et d’Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)
* [`New-AzResourceGroupDeployment`](/powershell/module/az.resources/new-azresourcegroupdeployment)
* [`New-AzDeployment`](/powershell/module/az.resources/new-azdeployment)

<a name="cli"></a>

## <a name="deploy-with-azure-cli"></a>Déploiement avec l’interface de ligne de commande Azure

Pour effectuer le déploiement dans un *groupe de ressources Azure* spécifique, utilisez cette commande :

```azurecli
az group deployment create -g <Azure-resource-group-name> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

Pour effectuer le déploiement dans un abonnement Azure spécifique, utilisez cette commande :

```azurecli
az deployment create --location <location> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

Pour plus d’informations, consultez les rubriques suivantes : 

* [Déployer des ressources à l’aide de modèles Resource Manager et de l’interface de ligne de commande Azure](../azure-resource-manager/resource-group-template-deploy-cli.md) 
* [`az group deployment create`](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create)
* [`az deployment create`](https://docs.microsoft.com/cli/azure/deployment?view=azure-cli-latest#az-deployment-create)

<a name="azure-pipelines"></a>

## <a name="deploy-with-azure-devops"></a>Déployer avec Azure DevOps

Pour déployer des modèles d’application logique et gérer les environnements, les équipes se servent généralement d’un outil comme [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/what-is-azure-pipelines) dans [Azure DevOps](https://docs.microsoft.com/azure/devops/user-guide/what-is-azure-devops-services). Azure Pipelines fournit une [tâche de déploiement de groupe de ressources Azure](https://github.com/Microsoft/azure-pipelines-tasks/tree/master/Tasks/AzureResourceGroupDeploymentV2) que vous pouvez ajouter à n’importe quel pipeline de build ou de mise en production.
Pour l’autorisation de déployer et générer le pipeline de mise en production, vous avez également besoin d’un [principal de service](../active-directory/develop/app-objects-and-service-principals.md) Azure Active Directory. Découvrez-en plus sur l’[utilisation des principaux de service avec Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure). 

Voici les principales étapes pour utiliser Azure Pipelines :

1. Dans Azure Pipelines, créez un pipeline vide.

1. Choisissez les ressources nécessaires pour le pipeline, telles que les fichiers de modèle et de paramètres de modèle de votre application logique, que vous générez manuellement ou dans le cadre du processus de build.

1. Pour votre travail d’agent, recherchez et ajoutez la tâche **Déploiement de groupe de ressources Azure**.

   ![Ajouter une tâche « Déploiement de groupe de ressources Azure »](./media/logic-apps-create-deploy-template/add-azure-resource-group-deployment-task.png)

1. Configurez avec un [principal de service](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure). 

1. Ajoutez des références aux fichiers de modèle et de paramètres de modèle de votre application logique.

1. Continuez à suivre les étapes du processus de version pour les autres environnements, tests automatisés ou approbateurs dont vous avez besoin.

## <a name="get-support"></a>Obtenir de l’aide

Si vous avez des questions, consultez le [forum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Surveiller les applications logiques](../logic-apps/logic-apps-monitor-your-logic-apps.md)
