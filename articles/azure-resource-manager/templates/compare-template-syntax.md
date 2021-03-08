---
title: Convertir des modèles Azure Resource Manager entre JSON et Bicep
description: Compare les modèles Azure Resource Manager développés avec JSON et Bicep.
ms.topic: conceptual
ms.date: 02/19/2021
ms.openlocfilehash: 9388ed50f13d6885d0a0668b61a9141dae375244
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101743050"
---
# <a name="comparing-json-and-bicep-for-templates"></a>Comparaison de JSON et Bicep pour les modèles

Cet article compare les syntaxes Bicep et JSON pour les modèles Azure Resource Manager (modèles ARM). Dans la plupart des cas, Bicep fournit une syntaxe moins détaillée que l’équivalent dans JSON.

## <a name="syntax-equivalents"></a>Équivalents de syntaxe

Si vous êtes familiarisé avec l’utilisation de JSON pour développer des modèles ARM, utilisez le tableau suivant pour découvrir la syntaxe équivalente pour Bicep.

| Scénario | Modèle ARM | Bicep |
| -------- | ------------ | ----- |
| Créer une expression | `"[func()]"` | `func()` |
| Obtenir une valeur de paramètre | `[parameters('exampleParameter'))]` | `exampleParameter` |
| Obtenir une valeur de variable | `[variables('exampleVar'))]` | `exampleVar` |
| Concaténer des chaînes | `[concat(parameters('namePrefix'), '-vm')]` | `'${namePrefix}-vm'` |
| Définir une propriété de ressource | `"sku": "2016-Datacenter",` | `sku: '2016-Datacenter'` |
| Retourner l’opérateur logique AND | `[and(parameter('isMonday'), parameter('isNovember'))]` | `isMonday && isNovember` |
| Obtenir l’ID d’une ressource dans modèle | `[resourceId('Microsoft.Network/networkInterfaces', variables('nic1Name'))]` | `nic1.id` |
| Obtenir une propriété d’une ressource dans le modèle | `[reference(resourceId('Microsoft.Storage/storageAccounts', variables('diagStorageAccountName'))).primaryEndpoints.blob]` | `diagsAccount.properties.primaryEndpoints.blob` |
| Définir une valeur de manière conditionnelle | `[if(parameters('isMonday'), 'valueIfTrue', 'valueIfFalse')]` | `isMonday ? 'valueIfTrue' : 'valueIfFalse'` |
| Scinder une solution en plusieurs fichiers | Utiliser des modèles liés | Utiliser des modules |
| Définir l’étendue cible du déploiement | `"$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#"` | `targetScope = 'subscription'` |
| Définir une dépendance | `"dependsOn": ["[resourceId('Microsoft.Storage/storageAccounts', 'parameters('storageAccountName'))]"]` | S’appuyer sur la détection automatique des dépendances ou définir manuellement une dépendance avec `dependsOn: [ stg ]` |

Pour déclarer le type et la version d’une ressource, utilisez ce qui suit dans Bicep :

```bicep
resource vm 'Microsoft.Compute/virtualMachines@2020-06-01' = {
  ...
}
```

Au lieu de la syntaxe équivalente dans JSON :

```json
"resources": [
  {
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2020-06-01",
    ...
  }
]
```

## <a name="recommendations"></a>Recommandations

* Dans la mesure du possible, évitez d’utiliser les fonctions [reference](template-functions-resource.md#reference) et [resourceId](template-functions-resource.md#resourceid) dans votre fichier Bicep. Lorsque vous référencez une ressource dans le même déploiement Bicep, utilisez l’identificateur de ressource à la place. Par exemple, si vous avez déployé une ressource dans votre fichier Bicep avec `stg` en tant qu’identificateur de ressource, utilisez une syntaxe comme `stg.id` ou `stg.properties.primaryEndpoints.blob` pour obtenir des valeurs de propriété. En utilisant l’identificateur de ressource, vous créez une dépendance implicite entre des ressources. Vous n’avez pas besoin de définir explicitement la dépendance avec la propriété dependsOn.
* Utilisez une casse cohérente pour les identificateurs. Si vous n’êtes pas sûr du type de casse à utiliser, essayez la casse mixte. Par exemple : `param myCamelCasedParameter string`.
* Ajoutez une description à un paramètre uniquement quand la description fournit des informations essentielles aux utilisateurs. Vous pouvez utiliser des commentaires `//` pour certaines informations.

## <a name="decompile-json-to-bicep"></a>Décompiler JSON vers Bicep

L’interface de ligne de commande Bicep fournit une commande pour décompiler tout modèle ARM existant dans un fichier Bicep. Pour décompiler un fichier JSON, utilisez : `bicep decompile "path/to/file.json"`

Cette commande fournit un point de départ pour la création de Bicep, mais la commande ne fonctionne pas pour tous les modèles. La commande peut échouer ou vous devrez peut-être résoudre des problèmes après la décompilation. Actuellement, la commande présente les limitations suivantes :

* Des modèles utilisant des boucles de copie ne peuvent pas être décompilés.
* Des modèles imbriqués peuvent être décompilés uniquement s’ils utilisent l’étendue d’évaluation d’expression « inner ».

Vous pouvez exporter le modèle pour un groupe de ressources, puis le transmettre directement à la commande decompile de Bicep. L’exemple suivant montre comment décompiler un modèle exporté.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group export --name "your_resource_group_name" > main.json
bicep decompile main.json
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Export-AzResourceGroup -ResourceGroupName "your_resource_group_name" -Path ./main.json
bicep decompile main.json
```

# <a name="portal"></a>[Portail](#tab/azure-portal)

[Exportez le modèle](export-template-portal.md) via le portail. Utilisez `bicep decompile <filename>` sur le fichier téléchargé.

---

## <a name="build-json-from-bicep"></a>Générer JSON à partir de Bicep

L’interface de ligne de commande Bicep fournit également une commande pour convertir Bicep en JSON. Pour créer un fichier JSON, utilisez : `bicep build "path/to/file.json"`

## <a name="side-by-side-view"></a>Vue côte à côte

Le [Terrain de jeu Bicep](https://aka.ms/bicepdemo) vous permet d’afficher des fichiers JSON et Bicep équivalents côte à côte. Vous pouvez sélectionner un exemple de modèle pour afficher les deux versions. Vous pouvez également sélectionner `Decompile` pour charger votre propre modèle JSON et afficher le fichier Bicep équivalent.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le projet Bicep, consultez [Projet Bicep](https://github.com/Azure/bicep).
