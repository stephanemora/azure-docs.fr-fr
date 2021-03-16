---
title: Comparer la syntaxe des modèles Azure Resource Manager dans JSON et Bicep
description: Compare des modèles Azure Resource Manager développés avec JSON et Bicep, et montre comment opérer une conversion entre les langages.
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 29c2b9948957ebc10a26f22f0fe3daf383dfe5ba
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102036212"
---
# <a name="comparing-json-and-bicep-for-templates"></a>Comparaison de JSON et Bicep pour les modèles

Cet article compare les syntaxes Bicep et JSON pour les modèles Azure Resource Manager (modèles ARM). Dans la plupart des cas, Bicep fournit une syntaxe moins détaillée que l’équivalent dans JSON.

## <a name="syntax-equivalents"></a>Équivalents de syntaxe

Si vous êtes familiarisé avec l’utilisation de JSON pour développer des modèles ARM, utilisez le tableau suivant pour découvrir la syntaxe équivalente pour Bicep.

| Scénario | Bicep | JSON |
| -------- | ------------ | ----- |
| Créer une expression | `func()` | `"[func()]"` |
| Obtenir une valeur de paramètre | `exampleParameter` | `[parameters('exampleParameter'))]` |
| Obtenir une valeur de variable | `exampleVar` | `[variables('exampleVar'))]` |
| Concaténer des chaînes | `'${namePrefix}-vm'` | `[concat(parameters('namePrefix'), '-vm')]` |
| Définir une propriété de ressource | `sku: '2016-Datacenter'` | `"sku": "2016-Datacenter",` |
| Retourner l’opérateur logique AND | `isMonday && isNovember` | `[and(parameter('isMonday'), parameter('isNovember'))]` |
| Obtenir l’ID d’une ressource dans modèle | `nic1.id` | `[resourceId('Microsoft.Network/networkInterfaces', variables('nic1Name'))]` |
| Obtenir une propriété d’une ressource dans le modèle | `diagsAccount.properties.primaryEndpoints.blob` | `[reference(resourceId('Microsoft.Storage/storageAccounts', variables('diagStorageAccountName'))).primaryEndpoints.blob]` |
| Définir une valeur de manière conditionnelle | `isMonday ? 'valueIfTrue' : 'valueIfFalse'` | `[if(parameters('isMonday'), 'valueIfTrue', 'valueIfFalse')]` |
| Scinder une solution en plusieurs fichiers | Utiliser des modules | Utiliser des modèles liés |
| Définir l’étendue cible du déploiement | `targetScope = 'subscription'` | `"$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#"` |
| Définir une dépendance | S’appuyer sur la détection automatique des dépendances ou définir manuellement une dépendance avec `dependsOn: [ stg ]` | `"dependsOn": ["[resourceId('Microsoft.Storage/storageAccounts', 'parameters('storageAccountName'))]"]` |
| Déclaration de ressource | `resource vm 'Microsoft.Compute/virtualMachines@2020-06-01' = {...}` | `"resources": [ { "type": "Microsoft.Compute/virtualMachines", "apiVersion": "2020-06-01", ... } ]` |

## <a name="recommendations"></a>Recommandations

* Dans la mesure du possible, évitez d’utiliser les fonctions [reference](template-functions-resource.md#reference) et [resourceId](template-functions-resource.md#resourceid) dans votre fichier Bicep. Lorsque vous référencez une ressource dans le même déploiement Bicep, utilisez l’identificateur de ressource à la place. Par exemple, si vous avez déployé une ressource dans votre fichier Bicep avec `stg` en tant qu’identificateur de ressource, utilisez une syntaxe comme `stg.id` ou `stg.properties.primaryEndpoints.blob` pour obtenir des valeurs de propriété. En utilisant l’identificateur de ressource, vous créez une dépendance implicite entre des ressources. Vous n’avez pas besoin de définir explicitement la dépendance avec la propriété dependsOn.
* Utilisez une casse cohérente pour les identificateurs. Si vous n’êtes pas sûr du type de casse à utiliser, essayez la casse mixte. Par exemple : `param myCamelCasedParameter string`.
* Ajoutez une description à un paramètre uniquement quand la description fournit des informations essentielles aux utilisateurs. Vous pouvez utiliser des commentaires `//` pour certaines informations.

## <a name="decompile-json-to-bicep"></a>Décompiler JSON vers Bicep

L’interface de ligne de commande Bicep fournit une commande pour décompiler tout modèle ARM existant dans un fichier Bicep. Pour décompiler un fichier JSON, utilisez : `bicep decompile "path/to/file.json"`

Cette commande fournit un point de départ pour la création de Bicep, mais la commande ne fonctionne pas pour tous les modèles. La commande peut échouer ou vous devrez peut-être résoudre des problèmes après la décompilation. Actuellement, il n’est possible de décompiler des modèles imbriqués que s’ils utilisent l’étendue d’évaluation d’expression « interne ».

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

Pour plus d’informations sur le langage Bicep, consultez le [Tutoriel Bicep](./bicep-tutorial-create-first-bicep.md).
