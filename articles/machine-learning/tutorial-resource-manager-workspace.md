---
title: 'Tutoriel : Créer un espace de travail Azure Machine Learning – Modèle Resource Manager'
description: Dans ce tutoriel, vous utilisez un modèle Azure Resource Manager qui permet de rapidement déployer un espace de travail Azure pour l’apprentissage automatique.
services: machine-learning
author: lobrien
ms.author: laobri
ms.custom: subject-armqs
ms.date: 05/26/2020
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.openlocfilehash: 760406b738d2aa95ef086941850814f4bf39fbb4
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88639866"
---
# <a name="tutorial-deploy-an-azure-machine-learning-workspace-using-an-arm-template"></a>Tutoriel : Déployer un espace de travail Azure Machine Learning à l’aide du modèle ARM

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ce tutoriel explique comment créer un espace de travail Azure Machine Learning à l’aide d’un modèle Azure Resource Manager (modèle ARM). Les espaces de travail Azure Machine Learning organisent l’ensemble de vos ressources d’apprentissage automatique, du jeu de données de référence au modèle déployé. Les espaces de travail constituent un emplacement unique pour collaborer avec des collègues sur la création, l’exécution et la révision d’expériences ; ils permettent également de gérer vos ressources de calcul d’inférence et d’entraînement, et vous donnent la possibilité de superviser et gérer les versions des modèles déployés.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Si votre environnement remplit les prérequis et que vous êtes déjà familiarisé avec l’utilisation des modèles ARM, sélectionnez le bouton **Déployer sur Azure**. Le modèle s’ouvre dans le portail Azure.

[![Déployer sur Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-machine-learning-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/services/machine-learning/) avant de commencer.

* Pour utiliser les commandes CLI dans ce document à partir de votre **environnement local**, vous avez besoin [d’Azure CLI](/cli/azure/install-azure-cli).

## <a name="review-the-template"></a>Vérifier le modèle

Le modèle utilisé dans ce démarrage rapide est tiré des [modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/101-machine-learning-create/).

:::code language="json" source="~/quickstart-templates/101-machine-learning-create/azuredeploy.json":::

Les ressources suivantes sont définies dans le modèle :

* [Microsoft.MachineLearningServices/workspaces](/azure/templates/microsoft.machinelearningservices/workspaces) : Créez un espace de travail Azure ML. Dans ce modèle, l’emplacement et le nom sont des paramètres que l’utilisateur peut passer ou entrer de manière interactive.

## <a name="deploy-the-template"></a>Déployer le modèle

Pour utiliser le modèle à partir de l’interface Azure CLI, connectez-vous, puis choisissez votre abonnement (Voir [Se connecter avec Azure CLI](/cli/azure/authenticate-azure-cli)). Ensuite, exécutez :

```azurecli-interactive
read -p "Enter a project name that is used for generating resource names:" projectName &&
read -p "Enter the location (i.e. centralus):" location &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-machine-learning-create/azuredeploy.json" &&
resourceGroupName="${projectName}rg" &&
workspaceName="${projectName}ws" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri $templateUri --parameters workspaceName=$workspaceName location=$location &&
echo "Press [ENTER] to continue ..." &&
read
```

Lorsque vous exécutez la commande ci-dessus, indiquez :

1. Un nom de projet qui constituera la base du nom de l’espace de travail Azure Machine Learning et celui du groupe de ressources qui ont été créés.
1. L’emplacement Azure dans lequel vous souhaitez effectuer le déploiement.

## <a name="review-deployed-resources"></a>Vérifier les ressources déployées

Pour afficher votre espace de travail Azure Machine Learning :

1. Atteindre https://portal.azure.com.
1. Connectez-vous.
1. Choisissez l’espace de travail que vous venez de créer.

La page d’accueil Azure Machine Learning s’affiche :

:::image type="content" source="media/tutorial-resource-manager-workspace/workspace-home.png" alt-text="Capture d’écran de l’espace de travail Azure Machine Learning":::

Pour afficher toutes les ressources associées au déploiement, cliquez en haut à gauche sur le lien du nom de l’espace de travail (dans la capture d’écran, `my_templated_ws`). Vous accédez au groupe de ressources dans le portail Azure. Le nom du groupe de ressources est `{projectName}rg`, et l’espace de travail est nommé `{projectName}ws`.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous ne souhaitez pas utiliser cet espace de travail, supprimez-le. Étant donné que l’espace de travail est associé à d’autres ressources, par exemple à un compte de stockage, vous souhaiterez probablement supprimer l’intégralité du groupe de ressources que vous avez créé. Vous pouvez supprimer le groupe de ressources par l’intermédiaire du portail en cliquant sur le bouton **Supprimer** et en confirmant ce choix. Vous pouvez également supprimer le groupe de ressources à partir de l’interface CLI avec :

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez créé un espace de travail Azure Machine Learning à partir d’un modèle ARM. Si vous souhaitez explorer Azure Machine Learning, poursuivez avec ce tutoriel.

> [!div class="nextstepaction"]
> [Tutoriel : Bien démarrer avec la création de votre première expérience ML avec le SDK Python](tutorial-1st-experiment-sdk-setup.md)
