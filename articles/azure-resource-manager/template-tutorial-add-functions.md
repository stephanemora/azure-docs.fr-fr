---
title: Tutoriel - Ajouter des fonctions de modèle
description: Ajoutez des fonctions de modèle à votre modèle Azure Resource Manager pour élaborer des valeurs.
author: mumian
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 03a38178ec66c1c1a10934975d20778369d80dbe
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74405957"
---
# <a name="tutorial-add-template-functions-to-your-resource-manager-template"></a>Didacticiel : Ajouter des fonctions de modèle à votre modèle Resource Manager

Dans ce tutoriel, vous apprenez à ajouter des [fonctions de modèle](resource-group-template-functions.md) à votre modèle. Vous utilisez des fonctions pour élaborer des valeurs de façon dynamique. Outre ces fonctions de modèle fournies par le système, vous pouvez aussi créer des [fonctions définies par l’utilisateur](./template-user-defined-functions.md). Ce tutoriel dure environ **7 minutes**.

## <a name="prerequisites"></a>Prérequis

Nous vous recommandons de suivre le [tutoriel sur les paramètres](template-tutorial-add-parameters.md), mais il n’est pas obligatoire.

Vous devez disposer de Visual Studio Code avec l’extension Outils Resource Manager et, au choix, d’Azure PowerShell ou d’Azure CLI. Pour plus d’informations, consultez les [outils de modèle](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Vérifier le modèle

À la fin du précédent tutoriel, votre modèle présentait le code JSON suivant :

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.json)]

L’emplacement du compte de stockage est codé en dur sur **USA Est**. Toutefois, vous pouvez avoir besoin de déployer le compte de stockage dans d’autres régions. Vous êtes à nouveau confronté à un manque de flexibilité de votre modèle. L’ajout d’un paramètre pour l’emplacement serait envisageable, mais si sa valeur par défaut était plus logique qu’une simple valeur codée en dur, ce serait sensationnel.

## <a name="use-function"></a>Utiliser une fonction

Si vous avez suivi le tutoriel précédent de cette série, vous vous êtes déjà servi d’une fonction. Quand vous avez ajouté  **"[parameters('storageName')]"** , vous avez utilisé la fonction [parameters](resource-group-template-functions-deployment.md#parameters). Les crochets indiquent que la syntaxe à l’intérieur des crochets est une [expression de modèle](template-expressions.md). Resource Manager résout la syntaxe au lieu de la traiter en tant que valeur littérale.

Les fonctions ajoutent de la flexibilité à votre modèle en obtenant de manière dynamique des valeurs pendant le déploiement. Dans ce tutoriel, vous utilisez une fonction pour obtenir l’emplacement du groupe de ressources que vous utilisez pour le déploiement.

L’exemple suivant met en évidence les modifications à apporter pour ajouter un paramètre appelé **location**.  La valeur par défaut du paramètre appelle la fonction [resourceGroup](resource-group-template-functions-resource.md#resourcegroup). Cette fonction retourne un objet avec des informations sur le groupe de ressources utilisé pour le déploiement. L’une des propriétés de l’objet est une propriété d’emplacement. Lorsque vous utilisez la valeur par défaut, l’emplacement du compte de stockage est le même que celui du groupe de ressources. Les ressources à l’intérieur d’un groupe de ressources n’ont pas à partager le même emplacement. Vous pouvez également fournir un autre emplacement lorsque cela est nécessaire.

Copiez l’intégralité du fichier et remplacez votre modèle par son contenu.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.json?range=1-44&highlight=24-27,34)]

## <a name="deploy-template"></a>Déployer un modèle

Dans les tutoriels précédents, vous avez créé un compte de stockage dans la région USA Est, mais votre groupe de ressources a été créé dans USA Centre. Pour les besoins de ce tutoriel, votre compte de stockage est créé dans la même région que le groupe de ressources. Utilisez la valeur par défaut pour location, afin de ne pas avoir à fournir cette valeur de paramètre. Vous devez indiquer un nouveau nom pour le compte de stockage, car vous créez un compte de stockage à un autre emplacement. Par exemple, utilisez **store2** comme préfixe au lieu de **store1**.

Si vous n’avez pas créé le groupe de ressources, consultez [Créer un groupe de ressources](template-tutorial-create-first-template.md#create-resource-group). L’exemple suppose que vous avez défini la variable **templateFile** sur le chemin du fichier de modèle, comme indiqué dans le [premier tutoriel](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addlocationparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{new-unique-name}"
```

# <a name="azure-clitabazure-cli"></a>[Interface de ligne de commande Azure](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addlocationparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={new-unique-name}
```

---

## <a name="verify-deployment"></a>Vérifier le déploiement

Vous pouvez vérifier le déploiement en explorant le groupe de ressources à partir du portail Azure.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Dans le menu de gauche, sélectionnez **Groupes de ressources**.
1. Sélectionnez le groupe de ressources sur lequel vous avez effectué le déploiement.
1. Vous voyez qu’une ressource de compte de stockage a été déployée et qu’elle a le même emplacement que le groupe de ressources.

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous passez au tutoriel suivant, vous n’avez pas besoin de supprimer le groupe de ressources.

Si vous arrêtez maintenant, vous pouvez nettoyer les ressources que vous avez déployées en supprimant le groupe de ressources.

1. Dans le portail Azure, sélectionnez **Groupe de ressources** dans le menu de gauche.
2. Entrez le nom du groupe de ressources dans le champ **Filtrer par nom**.
3. Sélectionnez le nom du groupe de ressources.
4. Sélectionnez **Supprimer le groupe de ressources** dans le menu supérieur.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez utilisé une fonction lors de la définition de la valeur par défaut d’un paramètre. Dans cette série de tutoriels, vous allez poursuivre votre utilisation des fonctions. À la fin de la série, vous ajouterez des fonctions à chaque section du modèle.

> [!div class="nextstepaction"]
> [Ajouter des variables](template-tutorial-add-variables.md)
