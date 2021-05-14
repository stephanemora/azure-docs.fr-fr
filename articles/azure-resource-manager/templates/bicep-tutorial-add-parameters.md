---
title: 'Tutoriel : Ajouter des paramètres à un fichier Bicep Azure Resource Manager'
description: Ajoutez des paramètres à votre fichier Bicep pour le rendre réutilisable.
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c017595d33856ba7a2902ec6d86f7c36a8c53107
ms.sourcegitcommit: 52491b361b1cd51c4785c91e6f4acb2f3c76f0d5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108320328"
---
# <a name="tutorial-add-parameters-to-azure-resource-manager-bicep-file"></a>Tutoriel : Ajouter des paramètres à un fichier Bicep Azure Resource Manager

Dans le [tutoriel précédent](bicep-tutorial-create-first-bicep.md), vous avez appris à déployer un compte de stockage. Dans ce tutoriel, vous allez découvrir comment améliorer le fichier Bicep en y ajoutant des paramètres. Ce tutoriel dure environ **14 minutes**.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Prérequis

Nous vous recommandons de [créer d’abord le fichier Bicep](bicep-tutorial-create-first-bicep.md), mais ce n’est pas obligatoire.

Vous devez disposer de Visual Studio Code avec l’extension Bicep et, au choix, d’Azure PowerShell ou d’Azure CLI. Pour plus d’informations, consultez [Outils Bicep](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>Vérifier le fichier Bicep

À la fin du tutoriel précédent, votre code Bicep ressemblait à ceci :

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-storage/azuredeploy.bicep":::

Vous avez peut-être remarqué qu’il y a un problème avec ce fichier Bicep. Le nom du compte de stockage est codé en dur. Vous pouvez utiliser ce fichier Bicep uniquement pour déployer le même compte de stockage à chaque fois. Pour déployer un compte de stockage avec un nom différent, vous devez créer un nouveau fichier Bicep, ce qui n’est évidemment pas un moyen pratique d’automatiser vos déploiements.

## <a name="make-bicep-file-reusable"></a>Rendre le fichier Bicep réutilisable

Vous pouvez rendre votre fichier Bicep réutilisable en ajoutant un paramètre que vous définissez pour transmettre un nom de compte de stockage. Le fichier Bicep suivant montre ce qui a changé dans votre fichier. Le paramètre `storageName` est identifié en tant que chaîne. La longueur maximale est définie sur 24 caractères pour empêcher les noms trop longs.

Copiez l’intégralité du fichier et remplacez-le par le contenu suivant.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-name/azuredeploy.bicep" range="1-15" highlight="1-3,6":::

Notez que les paramètres peuvent être référencés directement en utilisant leurs noms dans le code Bicep, alors qu’il faut [parameters('storageName')] dans le modèle JSON ARM.

## <a name="deploy-bicep-file"></a>Déployer le fichier Bicep

Déployons le fichier Bicep. L’exemple suivant déploie le fichier Bicep avec Azure CLI ou PowerShell. Notez que vous fournissez le nom du compte de stockage en tant que valeur dans la commande de déploiement. En guise de nom de compte de stockage, indiquez le nom que vous avez utilisé dans le tutoriel précédent.

Si vous n’avez pas créé le groupe de ressources, consultez [Créer un groupe de ressources](bicep-tutorial-create-first-bicep.md#create-resource-group). L’exemple suppose que vous avez défini la variable `bicepFile` sur le chemin du fichier Bicep, comme indiqué dans le [premier tutoriel](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pour exécuter cette cmdlet de déploiement, vous devez disposer de la [version la plus récente](/powershell/azure/install-az-ps) d’Azure PowerShell.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnameparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour exécuter cette commande de déploiement, vous devez disposer de la [dernière version](/cli/azure/install-azure-cli) d’Azure CLI.

```azurecli
az deployment group create \
  --name addnameparameter \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storageName={your-unique-name}
```

---

## <a name="understand-resource-updates"></a>Comprendre les mises à jour des ressources

Dans la section précédente, vous avez déployé un compte de stockage portant le même nom que celui que vous avez créé auparavant. Vous vous demandez peut-être de quelle manière le redéploiement se répercute sur la ressource.

Si la ressource existe déjà et qu’aucune modification n’est détectée dans les propriétés, aucune action n’est effectuée. Si la ressource existe déjà et qu’une propriété a été modifiée, la ressource est mise à jour. Si la ressource n’existe pas, elle est créée.

Cette façon de gérer les mises à jour signifie que votre fichier Bicep peut inclure toutes les ressources dont vous avez besoin pour une solution Azure. Vous pouvez redéployer le fichier Bicep en toute sécurité, sachant que les ressources seront modifiées ou créées uniquement lorsque cela sera nécessaire. Par exemple, si vous avez ajouté des fichiers à votre compte de stockage, vous pouvez redéployer ce compte sans perdre ces fichiers.

## <a name="customize-by-environment"></a>Personnaliser par environnement

Les paramètres vous permettent de personnaliser le déploiement grâce à des valeurs adaptées à un environnement particulier. Par exemple, vous pouvez passer des valeurs différentes selon que vous effectuez un déploiement dans un environnement de développement, de test et de production.

Le fichier Bicep précédent a toujours déployé un compte de stockage **Standard_LRS**. Vous pouvez souhaiter avoir le choix de déployer différentes références SKU en fonction de l’environnement. L’exemple suivant montre les modifications apportées pour ajouter un paramètre de référence (SKU). Copiez l’intégralité du fichier et collez-le à la place de votre fichier Bicep.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.bicep" range="1-27" highlight="5-15,21":::

Le paramètre `storageSKU` possède une valeur par défaut. Cette valeur est utilisée quand aucune valeur n’est spécifiée pendant le déploiement. Il présente également une liste de valeurs autorisées. Ces valeurs correspondent aux valeurs nécessaires à la création d’un compte de stockage. Vous ne souhaitez pas que les utilisateurs de votre fichier Bicep transmettent des références SKU qui ne fonctionnent pas.

## <a name="redeploy-bicep-file"></a>Redéployer un fichier Bicep

Vous êtes prêt à déployer de nouveau. La référence SKU par défaut étant définie sur **Standard_LRS**, vous n’avez pas besoin de fournir de valeur pour ce paramètre.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --name addskuparameter \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storageName={your-unique-name}
```

---

> [!NOTE]
> Si le déploiement a échoué, utilisez le commutateur `verbose` pour obtenir des informations sur les ressources en cours de création. Utilisez le commutateur `debug` pour obtenir des informations supplémentaires sur le débogage.

Pour voir la flexibilité de votre fichier Bicep, procédez de nouveau au déploiement. Cette fois-ci, définissez le paramètre SKU sur **Standard_GRS**. Vous pouvez soit passer un nouveau nom pour créer un compte de stockage différent, soit utiliser le même nom pour mettre à jour votre compte de stockage existant. Les deux options fonctionnent.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name usenondefaultsku `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storageName "{your-unique-name}" `
  -storageSKU Standard_GRS
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --name usenondefaultsku \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storageSKU=Standard_GRS storageName={your-unique-name}
```

---

Pour finir, nous allons exécuter un test supplémentaire et voir ce qui se passe lorsque vous transmettez une référence qui ne fait pas partie des valeurs autorisées. Ici, nous testons le scénario dans lequel un utilisateur de votre fichier Bicep pense que **basic** fait partie des références SKU.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name testskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storageName "{your-unique-name}" `
  -storageSKU basic
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --name testskuparameter \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storageSKU=basic storageName={your-unique-name}
```

---

La commande échoue immédiatement avec un message d’erreur précisant les valeurs autorisées. Resource Manager identifie l’erreur avant le démarrage du déploiement.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous passez au tutoriel suivant, vous n’avez pas besoin de supprimer le groupe de ressources.

Si vous arrêtez maintenant, vous pouvez nettoyer les ressources que vous avez déployées en supprimant le groupe de ressources.

1. Dans le portail Azure, sélectionnez **Groupe de ressources** dans le menu de gauche.
2. Entrez le nom du groupe de ressources dans le champ **Filtrer par nom**.
3. Sélectionnez le nom du groupe de ressources.
4. Sélectionnez **Supprimer le groupe de ressources** dans le menu supérieur.

## <a name="next-steps"></a>Étapes suivantes

Vous avez amélioré le fichier Bicep créé dans le [premier tutoriel](bicep-tutorial-create-first-bicep.md) en y ajoutant des paramètres. Dans le tutoriel suivant, vous allez découvrir les fonctions Bicep.

> [!div class="nextstepaction"]
> [Ajouter des fonctions](bicep-tutorial-add-functions.md)
