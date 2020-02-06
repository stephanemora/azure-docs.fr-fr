---
title: Déployer des modèles d’application logique
description: Découvrez comment déployer des modèles Azure Resource Manager créés pour Azure Logic Apps.
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/01/2019
ms.openlocfilehash: 662f8c2a4184e858d33dc40effed363ef3ec17e5
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76904961"
---
# <a name="deploy-azure-resource-manager-templates-for-azure-logic-apps"></a>Déployer des modèles Azure Resource Manager pour Azure Logic Apps

Après avoir créé un modèle Azure Resource Manager pour votre application logique, vous pouvez le déployer des manières suivantes :

* [Azure portal](#portal)
* [Visual Studio](#visual-studio)
* [Azure PowerShell](#powershell)
* [Azure CLI](#cli)
* [API Azure Resource Manager REST](../azure-resource-manager/templates/deploy-rest.md)
* [Azure DevOps Azure Pipelines](#azure-pipelines)

<a name="portal"></a>

## <a name="deploy-through-azure-portal"></a>Déployer par le biais du portail Azure

Pour déployer automatiquement un modèle d’application logique sur Azure, vous pouvez utiliser le bouton **Déployer sur Azure** suivant. Vous êtes alors connecté au portail Azure, puis invité à entrer des informations relatives à votre application logique. Vous pouvez ensuite apporter les modifications nécessaires au modèle ou aux paramètres de l’application logique.

[![Déployer sur Azure](./media/logic-apps-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

Par exemple, vous êtes invité à entrer les informations suivantes une fois que vous êtes connecté au portail Azure :

* Nom de l’abonnement Azure
* Groupe de ressources que vous souhaitez utiliser
* Emplacement de l’application logique
* Nom de l’application logique.
* URI de test
* Acceptation des termes et conditions spécifiés

Pour plus d’informations, consultez les rubriques suivantes :

* [Vue d’ensemble : Automatiser le déploiement pour les applications logiques avec des modèles Azure Resource Manager](logic-apps-azure-resource-manager-templates-overview.md)
* [Déployer des ressources à l’aide de modèles Azure Resource Manager et du portail Azure](../azure-resource-manager/templates/deploy-portal.md)

<a name="visual-studio"></a>

## <a name="deploy-with-visual-studio"></a>Déployer avec Visual Studio

Pour déployer un modèle d’application logique à partir d’un projet de groupe de ressources Azure que vous avez créé à l’aide de Visual Studio, effectuez ces [étapes pour déployer manuellement votre application logique](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure) sur Azure.

<a name="powershell"></a>

## <a name="deploy-with-azure-powershell"></a>Déployer avec Azure PowerShell

Pour effectuer le déploiement dans un *groupe de ressources Azure* spécifique, utilisez cette commande :

```powershell
New-AzResourceGroupDeployment -ResourceGroupName <Azure-resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

Pour plus d’informations, consultez les rubriques suivantes :

* [Déployer des ressources à l’aide de modèles Resource Manager et d’Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)
* [`New-AzResourceGroupDeployment`](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment)

<a name="cli"></a>

## <a name="deploy-with-azure-cli"></a>Déploiement avec l’interface de ligne de commande Azure

Pour effectuer le déploiement dans un *groupe de ressources Azure* spécifique, utilisez cette commande :

```azurecli
az group deployment create -g <Azure-resource-group-name> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

Pour plus d’informations, consultez les rubriques suivantes :

* [Déployer des ressources à l’aide de modèles Resource Manager et de l’interface de ligne de commande Azure](../azure-resource-manager/templates/deploy-cli.md) 
* [`az group deployment create`](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create)

<a name="azure-pipelines"></a>

## <a name="deploy-with-azure-devops"></a>Déployer avec Azure DevOps

Pour déployer des modèles d’application logique et gérer les environnements, les équipes se servent généralement d’un outil comme [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/what-is-azure-pipelines) dans [Azure DevOps](https://docs.microsoft.com/azure/devops/user-guide/what-is-azure-devops-services). Azure Pipelines fournit une [tâche de déploiement de groupe de ressources Azure](https://github.com/Microsoft/azure-pipelines-tasks/tree/master/Tasks/AzureResourceGroupDeploymentV2) que vous pouvez ajouter à n’importe quel pipeline de build ou de mise en production. Pour l’autorisation de déployer et générer le pipeline de mise en production, vous avez également besoin d’un [principal de service](../active-directory/develop/app-objects-and-service-principals.md) Azure Active Directory. Découvrez-en plus sur l’[utilisation des principaux de service avec Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure).

Pour plus d'informations sur l'intégration continue et le déploiement continu (CI/CD) pour les modèles Azure Resource Manager avec Azure Pipelines, consultez les rubriques et exemples suivants :

* [Intégrer des modèles Azure Resource Manager avec Azure Pipelines](../azure-resource-manager/templates/add-template-to-azure-pipelines.md)
* [Tutoriel : Intégration continue pour les modèles Azure Resource Manager avec Azure Pipelines](../azure-resource-manager/templates/template-tutorial-use-azure-pipelines.md)
* [Exemple : Se connecter à des files d’attente Azure Service Bus à partir d’Azure Logic Apps et déployer avec Azure Pipelines dans Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Exemple : Se connecter à des comptes Stockage Azure à partir d'Azure Logic Apps et déployer avec Azure Pipelines dans Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Exemple : Configurer une action Function App pour Azure Logic Apps et déployer avec Azure Pipelines dans Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Exemple : Se connecter à un compte d'intégration à partir d'Azure Logic Apps et déployer avec Azure Pipelines dans Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)

Voici les principales étapes pour utiliser Azure Pipelines :

1. Dans Azure Pipelines, créez un pipeline vide.

1. Choisissez les ressources nécessaires pour le pipeline, telles que les fichiers de modèle et de paramètres de modèle de votre application logique, que vous générez manuellement ou dans le cadre du processus de build.

1. Pour votre travail d’agent, recherchez et ajoutez la tâche **Déploiement de groupe de ressources Azure**.

   ![Ajouter une tâche « Déploiement de groupe de ressources Azure »](./media/logic-apps-deploy-azure-resource-manager-templates/add-azure-resource-group-deployment-task.png)

1. Configurez avec un [principal de service](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure).

1. Ajoutez des références aux fichiers de modèle et de paramètres de modèle de votre application logique.

1. Continuez à suivre les étapes du processus de version pour les autres environnements, tests automatisés ou approbateurs dont vous avez besoin.

<a name="authorize-oauth-connections"></a>

## <a name="authorize-oauth-connections"></a>Autoriser des connexions OAuth

Après le déploiement, votre application logique fonctionne de bout en bout avec des paramètres valides. Toutefois, vous devez encore autoriser les connexions OAuth à générer des jetons d’accès valides pour l’[authentification de vos informations d’identification](../active-directory/develop/authentication-scenarios.md). Voici comment vous pouvez autoriser les connexions OAuth :

* Pour les déploiements automatisés, vous pouvez utiliser un script qui fournit le consentement pour chaque connexion OAuth. Vous trouverez un exemple de script sur GitHub dans le projet [LogicAppConnectionAuth](https://github.com/logicappsio/LogicAppConnectionAuth).

* Pour autoriser manuellement les connexions OAuth, ouvrez votre application logique dans le Concepteur d’application logique, dans le portail Azure ou dans Visual Studio. Dans le concepteur, autorisez toutes les connexions requises.

Si vous utilisez un [principal de service](../active-directory/develop/app-objects-and-service-principals.md) Azure Active Directory (Azure AD) à la place pour autoriser les connexions, découvrez comment [spécifier les paramètres du principal du service dans votre modèle d’application logique](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#authenticate-connections).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Surveiller les applications logiques](../logic-apps/monitor-logic-apps.md)
