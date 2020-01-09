---
title: Tutoriel - Utiliser un fichier de paramètres pour déployer un modèle
description: Utilisez les fichiers de paramètres qui contiennent les valeurs à utiliser pour le déploiement de votre modèle Azure Resource Manager.
author: mumian
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: abafa00febe0431acf5c678cc179c6c114fa9179
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75470834"
---
# <a name="tutorial-use-parameter-files-to-deploy-your-resource-manager-template"></a>Tutoriel : Utiliser les fichiers de paramètres pour déployer votre modèle Resource Manager

Dans ce tutoriel, vous apprenez à utiliser des [fichiers de paramètres](parameter-files.md) pour stocker les valeurs que vous transmettez pendant le déploiement. Dans les tutoriels précédents, vous avez utilisé des paramètres incorporés à votre commande de déploiement. Cette approche fonctionnait pour tester votre modèle, mais lors de l’automatisation des déploiements, il peut être plus facile de passer un ensemble de valeurs pour votre environnement. Les fichiers de paramètres facilitent la création de package des valeurs de paramètre pour un environnement spécifique. Dans ce tutoriel, vous allez créer des fichiers de paramètres pour les environnements de développement et de production. Comptez environ **12 minutes** pour suivre ce tutoriel.

## <a name="prerequisites"></a>Conditions préalables requises

Nous vous recommandons de suivre le [tutoriel sur les étiquettes](template-tutorial-add-tags.md), mais ce n’est pas obligatoire.

Vous devez disposer de Visual Studio Code avec l’extension Outils Resource Manager et, au choix, d’Azure PowerShell ou d’Azure CLI. Pour plus d’informations, consultez les [outils de modèle](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Vérifier le modèle

Votre modèle contient de nombreux paramètres que vous pouvez fournir pendant le déploiement. À la fin du tutoriel précédent, votre modèle ressemblait à ceci :

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.json)]

Ce modèle fonctionne bien, mais vous souhaitez maintenant gérer sans peine les paramètres que vous transmettez pour le modèle.

## <a name="add-parameter-files"></a>Ajouter des fichiers de paramètres

Les fichiers de paramètres sont des fichiers JSON dont la structure est similaire à celle de votre modèle. Dans le fichier, vous fournissez les valeurs de paramètre que vous souhaitez transmettre pendant le déploiement.

Dans VS Code, créez un fichier avec le contenu suivant. Enregistrez le fichier sous le nom **azuredeploy.parameters.dev.json**.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.dev.json)]

Il s’agit de votre fichier de paramètres pour l’environnement de développement. Notez qu’il utilise Standard_LRS pour le compte de stockage, nomme les ressources avec un préfixe **dev** et définit l’étiquette **Environment** sur **Dev**.

Encore une fois, créez un fichier avec le contenu suivant. Enregistrez le fichier sous le nom **azuredeploy.parameters.prod.json**.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.prod.json)]

Il s’agit de votre fichier de paramètres pour l’environnement de production. Notez qu’il utilise Standard_GRS pour le compte de stockage, nomme les ressources avec un préfixe **contoso** et définit l’étiquette **Environment** sur **Production**. Dans un environnement de production réel, vous pouvez également avoir envie d’utiliser un service d’application avec une référence SKU payante, cependant, pour les besoins de ce tutoriel, nous allons continuer à utiliser cette référence.

## <a name="deploy-template"></a>Déployer un modèle

Utilisez Azure CLI ou Azure PowerShell pour déployer le modèle.

En guise de test final pour votre modèle, vous allez créer deux nouveaux groupes de ressources. L’un pour l’environnement de développement, et l’autre pour l’environnement de production.

Tout d’abord, nous allons procéder au déploiement dans l’environnement de développement.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$templateFile = "{provide-the-path-to-the-template-file}"
$parameterFile="{path-to-azuredeploy.parameters.dev.json}"
New-AzResourceGroup `
  -Name myResourceGroupDev `
  -Location "East US"
New-AzResourceGroupDeployment `
  -Name devenvironment `
  -ResourceGroupName myResourceGroupDev `
  -TemplateFile $templateFile `
  -TemplateParameterFile $parameterFile
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
templateFile="{provide-the-path-to-the-template-file}"
az group create \
  --name myResourceGroupDev \
  --location "East US"
az group deployment create \
  --name devenvironment \
  --resource-group myResourceGroupDev \
  --template-file $templateFile \
  --parameters azuredeploy.parameters.dev.json
```

---

À présent, nous allons procéder au déploiement dans l’environnement de production.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$parameterFile="{path-to-azuredeploy.parameters.prod.json}"
New-AzResourceGroup `
  -Name myResourceGroupProd `
  -Location "West US"
New-AzResourceGroupDeployment `
  -Name prodenvironment `
  -ResourceGroupName myResourceGroupProd `
  -TemplateFile $templateFile `
  -TemplateParameterFile $parameterFile
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group create \
  --name myResourceGroupProd \
  --location "West US"
az group deployment create \
  --name prodenvironment \
  --resource-group myResourceGroupProd \
  --template-file $templateFile \
  --parameters azuredeploy.parameters.prod.json
```

---

## <a name="verify-deployment"></a>Vérifier le déploiement

Vous pouvez vérifier le déploiement en explorant les groupes de ressources à partir du portail Azure.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Dans le menu de gauche, sélectionnez **Groupes de ressources**.
1. Vous voyez les deux nouveaux groupes de ressources que vous avez déployés dans ce tutoriel.
1. Sélectionnez un groupe de ressources et affichez les ressources déployées. Remarquez qu’elles correspondent aux valeurs que vous avez spécifiées dans votre fichier de paramètres pour cet environnement.

## <a name="clean-up-resources"></a>Nettoyer les ressources

1. Dans le portail Azure, sélectionnez **Groupe de ressources** dans le menu de gauche.
2. Entrez le nom du groupe de ressources dans le champ **Filtrer par nom**. Si vous avez terminé cette série, vous devez supprimer trois groupes de ressources : myResourceGroup, myResourceGroupDev et myResourceGroupProd.
3. Sélectionnez le nom du groupe de ressources.
4. Sélectionnez **Supprimer le groupe de ressources** dans le menu supérieur.

## <a name="next-steps"></a>Étapes suivantes

Félicitations, vous avez terminé cette présentation sur le déploiement de modèles dans Azure. Faites-nous part de vos réflexions et de vos suggestions dans la section des commentaires. Merci !

Vous êtes prêt à passer à des concepts plus élaborés sur les modèles. Le tutoriel suivant détaille l’utilisation de la documentation de référence sur les modèles, afin de vous aider à définir les ressources à déployer.

> [!div class="nextstepaction"]
> [Utiliser la référence de modèle](template-tutorial-create-encrypted-storage-accounts.md)
