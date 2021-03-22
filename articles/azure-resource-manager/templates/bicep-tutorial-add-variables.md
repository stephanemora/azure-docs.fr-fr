---
title: 'Tutoriel : Ajouter une variable à un fichier Bicep Azure Resource Manager'
description: Ajoutez des variables à votre fichier Bicep pour simplifier la syntaxe.
author: mumian
ms.date: 03/01/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 1706bafb85834cfd3abae1f5d6a0090da9041bb5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101742842"
---
# <a name="tutorial-add-variables-to-azure-resource-manager-bicep-file"></a>Tutoriel : Ajouter des variables à un fichier Bicep Azure Resource Manager

Dans ce tutoriel, vous apprenez à ajouter une variable à votre fichier Bicep. Les variables simplifient vos fichiers Bicep en vous permettant d’écrire une expression une fois et de la réutiliser dans tout le fichier Bicep. Ce tutoriel dure environ **7 minutes**.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Prérequis

Nous vous recommandons de suivre le [tutoriel sur les fonctions](bicep-tutorial-add-functions.md), mais ce n’est pas obligatoire.

Vous devez disposer de Visual Studio Code avec l’extension Bicep et, au choix, d’Azure PowerShell ou d’Azure CLI. Pour plus d’informations, consultez [Outils Bicep](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>Vérifier le fichier Bicep

À la fin du précédent tutoriel, votre fichier Bicep présentait le contenu suivant :

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.bicep":::

Le paramètre pour le nom de compte de stockage est difficile à utiliser, car vous devez fournir un nom unique. Si vous avez suivi les tutoriels précédents de cette série, vous êtes probablement fatigué de chercher un nom unique. Pour résoudre ce problème, ajoutez une variable qui élabore un nom unique pour le compte de stockage.

## <a name="use-variable"></a>Utiliser une variable

L’exemple suivant met en évidence les modifications à apporter pour ajouter une variable à votre fichier Bicep qui crée un nom de compte de stockage unique. Copiez l’intégralité du fichier et remplacez votre fichier Bicep par son contenu.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.bicep" range="1-31" highlight="1-3,19,22":::

Notez qu’elle comprend une variable nommée `uniqueStorageName`. Cette variable utilise trois fonctions pour composer une valeur de chaîne.

Vous maîtrisez la fonction [resourceGroup](template-functions-resource.md#resourcegroup). Dans ce cas, vous obtenez la propriété `id` au lieu de la propriété `location`, comme illustré dans le tutoriel précédent. La propriété `id` retourne l’identificateur complet du groupe de ressources, notamment l’ID d’abonnement et le nom du groupe de ressources.

La fonction [uniqueString](template-functions-string.md#uniquestring) crée une valeur de hachage de 13 caractères. La valeur retournée est déterminée par les paramètres que vous transmettez. Pour ce tutoriel, vous utilisez l’ID de groupe de ressources en tant qu’entrée pour la valeur de hachage. Cela signifie que vous pouvez déployer ce fichier Bicep dans différents groupes de ressources et obtenir une valeur de chaîne unique différente. Par contre, vous recevez la même valeur si vous déployez sur le même groupe de ressources.

Bicep prend en charge une syntaxe d’[interpolation de chaîne](https://en.wikipedia.org/wiki/String_interpolation#). Pour cette variable, elle prend la chaîne à partir du paramètre et la chaîne à partir de la fonction `uniqueString`, puis les combine en une seule chaîne.

Le paramètre `storagePrefix` vous permet de transmettre un préfixe qui vous aide à identifier les comptes de stockage. Vous pouvez créer votre propre convention de nommage ; dans un longue liste de ressources, celle-ci facilite l’identification des comptes de stockage après le déploiement.

Enfin, remarquez que le nom de stockage est maintenant défini sur la variable, et non sur un paramètre.

## <a name="deploy-bicep-file"></a>Déployer le fichier Bicep

Déployons le fichier Bicep. Le déploiement de ce fichier Bicep est plus facile que les fichiers Bicep précédents, car vous fournissez uniquement le préfixe du nom de stockage.

Si vous n’avez pas créé le groupe de ressources, consultez [Créer un groupe de ressources](bicep-tutorial-create-first-bicep.md#create-resource-group). L’exemple suppose que vous avez défini la variable `bicepFile` sur le chemin du fichier Bicep, comme indiqué dans le [premier tutoriel](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pour exécuter cette cmdlet de déploiement, vous devez disposer de la [version la plus récente](/powershell/azure/install-az-ps) d’Azure PowerShell.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnamevariable `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour exécuter cette commande de déploiement, vous devez disposer de la [dernière version](/cli/azure/install-azure-cli) d’Azure CLI.

```azurecli
az deployment group create \
  --name addnamevariable \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

> [!NOTE]
> Si le déploiement a échoué, utilisez le commutateur `verbose` pour obtenir des informations sur les ressources en cours de création. Utilisez le commutateur `debug` pour obtenir des informations supplémentaires sur le débogage.

## <a name="verify-deployment"></a>Vérifier le déploiement

Vous pouvez vérifier le déploiement en explorant le groupe de ressources à partir du portail Azure.

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Dans le menu de gauche, sélectionnez **Groupes de ressources**.
1. Sélectionnez le groupe de ressources sur lequel vous avez effectué le déploiement.
1. Vous voyez qu’une ressource de compte de stockage a été déployée. Le nom du compte de stockage est composé de **store** et d’une chaîne de caractères aléatoires.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous passez au tutoriel suivant, vous n’avez pas besoin de supprimer le groupe de ressources.

Si vous arrêtez maintenant, vous pouvez nettoyer les ressources que vous avez déployées en supprimant le groupe de ressources.

1. Dans le portail Azure, sélectionnez **Groupe de ressources** dans le menu de gauche.
2. Entrez le nom du groupe de ressources dans le champ **Filtrer par nom**.
3. Sélectionnez le nom du groupe de ressources.
4. Sélectionnez **Supprimer le groupe de ressources** dans le menu supérieur.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez ajouté une variable qui crée un nom unique pour un compte de stockage. Dans le tutoriel suivant, vous allez retourner une valeur à partir du compte de stockage déployé.

> [!div class="nextstepaction"]
> [Ajouter des sorties](bicep-tutorial-add-outputs.md)
