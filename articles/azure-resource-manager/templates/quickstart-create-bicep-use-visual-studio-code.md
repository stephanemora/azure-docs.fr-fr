---
title: Créer des fichiers Bicep - Visual Studio Code
description: Utiliser Visual Studio Code et l’extension Bicep avec des fichiers Bicep pour déployer des ressources Azure
author: mumian
ms.date: 03/26/2021
ms.topic: quickstart
ms.author: jgao
ms.openlocfilehash: 4d1064351ddfacdebfa67fd9b2f517f592de3a7c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105612883"
---
# <a name="quickstart-create-bicep-files-with-visual-studio-code"></a>Démarrage rapide : Créer des fichiers Bicep avec Visual Studio Code

L’extension Bicep pour Visual Studio Code fournit la prise en charge du langage et la saisie semi-automatique des ressources. Ces outils facilitent la création et la validation des fichiers [Bicep](./bicep-overview.md). Dans ce guide de démarrage rapide, vous allez utiliser l’extension pour créer un fichier Bicep à partir de zéro. Ce faisant, vous découvrirez différentes possibilités d’extension telles que la validation et la saisie semi-automatique.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

Pour suivre ce guide de démarrage rapide, vous avez besoin de [Visual Studio Code](https://code.visualstudio.com/), ainsi que de l’[extension Bicep](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep). Vous avez également besoin soit de la version d’[Azure CLI](/cli/azure/), soit du [module Azure PowerShell](/powershell/azure/new-azureps-module-az) le plus récent, que vous devez authentifier après leur installation.

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="create-a-bicep-file"></a>Créer un fichier Bicep

Dans Visual Studio Code, créez puis ouvrez avec un fichier nommé *azuredeploy.bicep*.

## <a name="add-an-azure-resource"></a>Ajouter une ressource Azure

Ajoutez une ressource de compte de stockage de base au fichier Bicep.

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: 'storageaccount1' // must be globally unique
  location: 'eastus'
  tags:{
    diplayName: 'storageaccount1'
  }
  sku: {
      name: 'Standard_LRS'
      tier: 'Standard'
  }
  kind: 'Storage'
}
```

La déclaration de ressource se compose de quatre éléments :

- **ressource** : mot clé.
- **nom symbolique** (stg) : le nom symbolique est un identificateur permettant de référencer la ressource dans l’ensemble de votre fichier Bicep. Il ne s’agit pas du nom de la ressource lorsqu’elle sera déployée. Le nom de la ressource est défini par la propriété **name**.  Consultez le quatrième élément de cette liste.
- **type de ressource** (Microsoft.Storage/storageAccounts@2019-06-01) : il se compose du fournisseur de ressources (Microsoft.Storage), du type de ressource (storageAccounts) et de l’élément apiVersion (01-06-2019). Chaque fournisseur de ressources publiant ses propres versions d’API, cette valeur est donc propre au type. Vous trouverez d’autres types et éléments apiVersion pour différentes ressources Azure dans la section [Informations de référence sur les modèles ARM](/azure/templates/).
- **propriétés** (tout ce qui se trouve dans = {...}) : spécifiez les propriétés du type de ressource. Chaque ressource comporte une propriété `name`. La plupart des ressources possèdent également une propriété `location`, qui définit la région dans laquelle la ressource est déployée. Les autres propriétés varient selon le type de la ressource et la version de l’API.

## <a name="completion-and-validation"></a>Autocomplétion et validation

L’une des fonctionnalités les plus puissantes de l’extension est son intégration aux schémas Azure. Les schémas Azure permettent à l’extension de bénéficier de fonctionnalités comme la validation et l’autocomplétion des ressources. Nous allons modifier le compte de stockage pour voir à quoi ressemble la validation et l’autocomplétion.

Tout d’abord, mettez à jour le type de compte de stockage avec une valeur non valide, par exemple `megaStorage`. Notez que cette action génère un avertissement indiquant que `megaStorage` n’est pas une valeur valide.

![Image montrant une configuration de stockage non valide](./media/quickstart-create-bicep-use-visual-studio-code/azure-resource-manager-template-bicep-visual-studio-code-validation.png)

Pour utiliser les fonctionnalités d’autocomplétion, supprimez `megaStorage`, placez le curseur à l’intérieur des guillemets simples, puis appuyez sur `ctrl` + `space`. Cette action présente une liste de complétion comprenant des valeurs valides.

![Image montrant l’autocomplétion dans l’extension](./media/quickstart-create-bicep-use-visual-studio-code/azure-resource-manager-template-bicep-visual-studio-code-auto-completion.png)

## <a name="add-parameters"></a>Ajouter des paramètres

À présent, vous allez créer et utiliser un paramètre pour spécifier le nom du compte de stockage.

Ajoutez le code suivant au début du fichier :

```bicep
@minLength(3)
@maxLength(24)
@description('Specify a storage account name.')
param storageAccountName string
```

Le nom d’un compte de stockage Azure doit être constitué de 3 à 24 caractères. Utilisez `minLength` et `maxLength` pour fournir les valeurs appropriées.

Maintenant, dans la ressource de stockage, mettez à jour la propriété Name pour utiliser le paramètre. Pour ce faire, supprimez le nom de la ressource de stockage actuelle, y compris les guillemets simples. Appuyez sur `ctrl` + `space`. Sélectionnez le paramètre **storageAccountName** dans la liste. Notez que les paramètres peuvent être référencés directement à l’aide de leurs noms dans Bicep. Les modèles JSON requièrent une fonction parameter().

![Image montrant la saisie semi-automatique lors de l’utilisation de paramètres dans des ressources Bicep](./media/quickstart-create-bicep-use-visual-studio-code/azure-resource-manager-template-bicep-visual-studio-code-valid-param.png)

## <a name="deploy-the-bicep-file"></a>Déployer le fichier Bicep

Ouvrez le terminal Visual Studio code intégré à l’aide de la combinaison de touches `ctrl` + ```` ` ````, remplacez le répertoire actif par l’emplacement où se trouve le fichier Bicep, puis utilisez le module Azure CLI ou Azure PowerShell pour déployer le fichier Bicep.

# <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/CLI)

```azurecli
az group create --name arm-vscode --location eastus

az deployment group create --resource-group arm-vscode --template-file azuredeploy.bicep --parameters storageAccountName={your-unique-name}
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell
New-AzResourceGroup -Name arm-vscode -Location eastus

New-AzResourceGroupDeployment -ResourceGroupName arm-vscode -TemplateFile ./azuredeploy.bicep -storageAccountName "{your-unique-name}"
```

---

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque les ressources Azure ne sont plus nécessaires, utilisez Azure CLI ou le module Azure PowerShell afin de supprimer le groupe de ressources qui a été utilisé pour ce guide de démarrage rapide.

# <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/CLI)

```azurecli
az group delete --name arm-vscode
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell
Remove-AzResourceGroup -Name arm-vscode
```

---

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Tutoriels sur Bicep pour débutants](./bicep-tutorial-create-first-bicep.md)
