---
title: 'Tutoriel : Ajouter des fonctions aux fichiers Bicep Azure Resource Manager'
description: Ajoutez des fonctions à vos fichiers Bicep pour construire des valeurs.
author: mumian
ms.date: 03/02/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: references_regions
ms.openlocfilehash: 9c66ab132d3343115b1a9bd852d3eb3d83ae4ca7
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101742865"
---
# <a name="tutorial-add-functions-to-azure-resource-manager-bicep-file"></a>Tutoriel : Ajouter des fonctions au fichier Bicep Azure Resource Manager

Dans ce tutoriel, vous apprenez à ajouter des [fonctions de modèle](template-functions.md) à votre fichier Bicep. Vous utilisez des fonctions pour élaborer des valeurs de façon dynamique. Outre ces fonctions de modèle fournies par le système, vous pouvez aussi créer des [fonctions définies par l’utilisateur](./template-user-defined-functions.md). Ce tutoriel dure environ **7 minutes**.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Prérequis

Nous vous recommandons de suivre le [tutoriel sur les paramètres](bicep-tutorial-add-parameters.md), mais il n’est pas obligatoire.

Vous devez disposer de Visual Studio Code avec l’extension Bicep et, au choix, d’Azure PowerShell ou d’Azure CLI. Pour plus d’informations, consultez [Outils Bicep](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>Vérifier le fichier Bicep

À la fin du précédent tutoriel, votre fichier Bicep présentait le contenu suivant :

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.bicep":::

L’emplacement du compte de stockage est codé en dur sur **USA Est**. Toutefois, vous pouvez avoir besoin de déployer le compte de stockage dans d’autres régions. Vous êtes à nouveau confronté à un manque de flexibilité de votre fichier Bicep. L’ajout d’un paramètre pour l’emplacement serait envisageable, mais si sa valeur par défaut était plus logique qu’une simple valeur codée en dur, ce serait sensationnel.

## <a name="use-function"></a>Utiliser une fonction

Les fonctions ajoutent de la flexibilité à votre fichier Bicep en obtenant de manière dynamique des valeurs pendant le déploiement. Dans ce tutoriel, vous utilisez une fonction pour obtenir l’emplacement du groupe de ressources que vous utilisez pour le déploiement.

L’exemple suivant met en évidence les modifications à apporter pour ajouter un paramètre appelé `location`. La valeur par défaut du paramètre appelle la fonction [resourceGroup](template-functions-resource.md#resourcegroup). Cette fonction retourne un objet avec des informations sur le groupe de ressources utilisé pour le déploiement. L’une des propriétés de l’objet est une propriété d’emplacement. Lorsque vous utilisez la valeur par défaut, l’emplacement du compte de stockage est le même que celui du groupe de ressources. Les ressources à l’intérieur d’un groupe de ressources n’ont pas à partager le même emplacement. Vous pouvez également fournir un autre emplacement lorsque cela est nécessaire.

Copiez l’intégralité du fichier et remplacez votre fichier Bicep par son contenu.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.bicep" range="1-30" highlight="18,22":::

## <a name="deploy-bicep-file"></a>Déployer le fichier Bicep

Dans les tutoriels précédents, vous avez créé un compte de stockage dans la région USA Est, mais votre groupe de ressources a été créé dans USA Centre. Pour les besoins de ce tutoriel, votre compte de stockage est créé dans la même région que le groupe de ressources. Utilisez la valeur par défaut pour location, afin de ne pas avoir à fournir cette valeur de paramètre. Vous devez indiquer un nouveau nom pour le compte de stockage, car vous créez un compte de stockage à un autre emplacement. Par exemple, utilisez **store2** comme préfixe au lieu de **store1**.

Si vous n’avez pas créé le groupe de ressources, consultez [Créer un groupe de ressources](bicep-tutorial-create-first-bicep.md#create-resource-group). L’exemple suppose que vous avez défini la variable `bicepFile` sur le chemin du fichier Bicep, comme indiqué dans le [premier tutoriel](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pour exécuter cette cmdlet de déploiement, vous devez disposer de la [version la plus récente](/powershell/azure/install-az-ps) d’Azure PowerShell.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addlocationparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storageName "{new-unique-name}"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour exécuter cette commande de déploiement, vous devez disposer de la [dernière version](/cli/azure/install-azure-cli) d’Azure CLI.

```azurecli
az deployment group create \
  --name addlocationparameter \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storageName={new-unique-name}
```

---

> [!NOTE]
> Si le déploiement a échoué, utilisez le commutateur `verbose` pour obtenir des informations sur les ressources en cours de création. Utilisez le commutateur `debug` pour obtenir des informations supplémentaires sur le débogage.

## <a name="verify-deployment"></a>Vérifier le déploiement

Vous pouvez vérifier le déploiement en explorant le groupe de ressources à partir du portail Azure.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Dans le menu de gauche, sélectionnez **Groupes de ressources**.
1. Sélectionnez le groupe de ressources sur lequel vous avez effectué le déploiement.
1. Vous voyez qu’une ressource de compte de stockage a été déployée et qu’elle a le même emplacement que le groupe de ressources.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous passez au tutoriel suivant, vous n’avez pas besoin de supprimer le groupe de ressources.

Si vous arrêtez maintenant, vous pouvez nettoyer les ressources que vous avez déployées en supprimant le groupe de ressources.

1. Dans le portail Azure, sélectionnez **Groupe de ressources** dans le menu de gauche.
2. Entrez le nom du groupe de ressources dans le champ **Filtrer par nom**.
3. Sélectionnez le nom du groupe de ressources.
4. Sélectionnez **Supprimer le groupe de ressources** dans le menu supérieur.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez utilisé une fonction lors de la définition de la valeur par défaut d’un paramètre. Dans cette série de tutoriels, vous allez poursuivre votre utilisation des fonctions. À la fin de la série, vous ajouterez des fonctions à chaque section du fichier Bicep.

> [!div class="nextstepaction"]
> [Ajouter des variables](bicep-tutorial-add-variables.md)
