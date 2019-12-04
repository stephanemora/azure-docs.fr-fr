---
title: Tutoriel - Utiliser les modèles de démarrage rapide
description: Découvrez comment utiliser les modèles de démarrage rapide Azure pour effectuer le développement de votre modèle.
author: mumian
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 1ddae445fb912b4bb60f257f667784b17b0d6ea5
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74405949"
---
# <a name="tutorial-use-azure-quickstart-templates"></a>Didacticiel : Utiliser les modèles de démarrage rapide Azure

Les [modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/) constituent un référentiel de modèles fournis par la communauté. Vous pouvez utiliser les exemples de modèles dans le développement de votre modèle. Dans ce tutoriel, vous trouvez une définition de ressource de site web et vous l’ajoutez à votre propre modèle. Comptez environ **12 minutes** pour suivre ce tutoriel.

## <a name="prerequisites"></a>Prérequis

Nous vous recommandons de suivre le [tutoriel sur les modèles exportés](template-tutorial-export-template.md), mais ce n’est pas obligatoire.

Vous devez disposer de Visual Studio Code avec l’extension Outils Resource Manager et, au choix, d’Azure PowerShell ou d’Azure CLI. Pour plus d’informations, consultez les [outils de modèle](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Vérifier le modèle

À la fin du précédent tutoriel, votre modèle présentait le code JSON suivant :

[!code-json[](~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.json)]

Ce modèle fonctionne pour le déploiement de comptes de stockage et de plans App Service, mais vous aimeriez peut-être y ajouter un site web. Vous pouvez utiliser des modèles prédéfinis pour découvrir rapidement le code JSON nécessaire au déploiement d’une ressource.

## <a name="find-template"></a>Rechercher un modèle

1. Ouvrez [Modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/).
1. Dans **Rechercher**, entrez **deploy linux web app** (déployer une application web Linux).
1. Sélectionnez celle qui est intitulée **Deploy a basic Linux web app** (Déployer une application web Linux de base). Si vous rencontrez des difficultés pour la trouver, voici le [lien direct](https://azure.microsoft.com/resources/templates/101-webapp-basic-linux/).
1. Sélectionnez **Rechercher sur GitHub**.
1. Sélectionnez **azuredeploy.json**.
1. Vérifiez le modèle. Recherchez en particulier la ressource `Microsoft.Web/sites`.

    ![Modèle Resource Manager - Site web de démarrage rapide](./media/template-tutorial-quickstart-template/resource-manager-template-quickstart-template-web-site.png)

## <a name="revise-existing-template"></a>Réviser le modèle existant

Fusionnez le modèle de démarrage rapide avec le modèle existant :

[!code-json[](~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.json?range=1-108&highlight=32-45,49,85-100)]

Le nom de l’application web doit être unique dans Azure. Pour éviter d’avoir des noms en double, la variable **webAppPortalName** a été mise à jour en passant de **"webAppPortalName": "[concat(parameters('webAppName'), '-webapp')]"** à **"webAppPortalName": "[concat(parameters('webAppName'), uniqueString(resourceGroup().id))]"** .

Ajoutez une virgule à la fin de la définition de `Microsoft.Web/serverfarms` pour séparer la définition de ressource de la définition de `Microsoft.Web/sites`.

Il y a quelques fonctionnalités importantes à noter dans cette nouvelle ressource.

Vous remarquerez qu’elle comporte un élément nommé **dependsOn** qui est défini sur le plan App Service. Ce paramètre est obligatoire, car le plan App Service doit préexister à la création de l’application web. L’élément **dependsOn** indique à Resource Manager comment ordonner les ressources pour le déploiement.

La propriété **serverFarmId** utilise la fonction [resourceId](resource-group-template-functions-resource.md#resourceid). Cette fonction obtient l’identificateur unique pour une ressource. Dans ce cas, elle obtient l’identificateur unique pour le plan App Service. L’application web est associée à un plan App Service spécifique.

## <a name="deploy-template"></a>Déployer un modèle

Utilisez Azure CLI ou Azure PowerShell pour déployer un modèle.

Si vous n’avez pas créé le groupe de ressources, consultez [Créer un groupe de ressources](template-tutorial-create-first-template.md#create-resource-group). L’exemple suppose que vous avez défini la variable **templateFile** sur le chemin du fichier de modèle, comme indiqué dans le [premier tutoriel](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addwebapp `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS `
  -webAppName demoapp
```

# <a name="azure-clitabazure-cli"></a>[Interface de ligne de commande Azure](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addwebapp \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous passez au tutoriel suivant, vous n’avez pas besoin de supprimer le groupe de ressources.

Si vous arrêtez maintenant, vous pouvez nettoyer les ressources que vous avez déployées en supprimant le groupe de ressources.

1. Dans le portail Azure, sélectionnez **Groupe de ressources** dans le menu de gauche.
2. Entrez le nom du groupe de ressources dans le champ **Filtrer par nom**.
3. Sélectionnez le nom du groupe de ressources.
4. Sélectionnez **Supprimer le groupe de ressources** dans le menu supérieur.

## <a name="next-steps"></a>Étapes suivantes

Vous avez appris à utiliser un modèle de démarrage rapide pour le développement de votre modèle. Dans le prochain tutoriel, vous allez ajouter des étiquettes aux ressources.

> [!div class="nextstepaction"]
> [Ajouter des étiquettes](template-tutorial-add-tags.md)
