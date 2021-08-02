---
title: Conversion de modèles entre JSON et Bicep
description: Décrit les commandes permettant de convertir des modèles Azure Resource Manager de Bicep en JSON et de JSON en Bicep.
ms.topic: conceptual
ms.date: 05/17/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ed273e9cf9af6f29ecc495536fd5d504d2b168f2
ms.sourcegitcommit: 5c136a01bddfccb2cc9f7e7e7741e2cf2651ddbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/03/2021
ms.locfileid: "111353422"
---
# <a name="converting-arm-templates-between-json-and-bicep"></a>Conversion de modèles ARM entre JSON et Bicep

Cet article explique comment convertir des modèles Azure Resource Manager (modèles ARM) de JSON en Bicep et de Bicep en JSON.

Vous devez installer [l’interface CLI Bicep](bicep-install.md) pour pouvoir exécuter les commandes de conversion.

Les commandes de conversion produisent des modèles équivalents d’un point de vue fonctionnel. Toutefois, ils ne sont pas nécessairement identiques dans l’implémentation. La conversion d’un modèle de JSON en Bicep, puis à nouveau en JSON, produit selon toute probabilité un modèle présentant une syntaxe différente de celle du modèle d’origine. Une fois déployés, les modèles convertis produisent les mêmes résultats.

## <a name="convert-from-json-to-bicep"></a>Conversion de JSON en Bicep

L’interface CLI Bicep fournit une commande pour décompiler un modèle JSON existant dans un fichier Bicep.

Si vous avez Azure CLI version 2.20.0 ou ultérieure, l’interface CLI Bicep est installée automatiquement. Pour décompiler un fichier JSON, utilisez : 

```azurecli
az bicep decompile mainTemplate.json
```

Si vous ne disposez pas d’une version récente d’Azure CLI et que vous avez plutôt installé l’interface CLI Bicep manuellement, utilisez :

```bash
bicep decompile mainTemplate.json
```

Cette commande fournit un point de départ pour la création Bicep. Elle ne fonctionne pas pour tous les modèles. Actuellement, il n’est possible de décompiler des modèles imbriqués que s’ils utilisent l’étendue d’évaluation d’expression « interne ». Les modèles qui utilisent des boucles de copie ne peuvent pas être décompilés.

## <a name="convert-from-bicep-to-json"></a>Conversion de Bicep en JSON

L’interface de ligne de commande Bicep fournit également une commande pour convertir Bicep en JSON. 

Pour Azure CLI version 2.20.0 ou ultérieure, utilisez la commande suivante pour générer un fichier JSON :

```azurecli
az bicep build mainTemplate.bicep
```

Si vous avez installé l’interface CLI Bicep manuellement, utilisez :

```bash
bicep build mainTemplate.bicep
```

## <a name="export-template-and-convert"></a>Exportation d’un modèle et conversion

Vous pouvez exporter le modèle pour un groupe de ressources, puis le transmettre directement à la commande decompile. L’exemple suivant montre comment décompiler un modèle exporté.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group export --name "your_resource_group_name" > main.json
az bicep decompile main.json
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Export-AzResourceGroup -ResourceGroupName "your_resource_group_name" -Path ./main.json
bicep decompile main.json
```

# <a name="portal"></a>[Portail](#tab/azure-portal)

[Exportez le modèle](export-template-portal.md) via le portail.

Utilisez `bicep decompile <filename>` sur le fichier téléchargé.

---

## <a name="side-by-side-view"></a>Vue côte à côte

Le [Terrain de jeu Bicep](https://aka.ms/bicepdemo) vous permet d’afficher des fichiers JSON et Bicep équivalents côte à côte. Vous pouvez sélectionner un exemple de modèle pour afficher les deux versions. Vous pouvez également sélectionner `Decompile` pour charger votre propre modèle JSON et afficher le fichier Bicep équivalent.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le langage Bicep, consultez le [Tutoriel Bicep](./bicep-tutorial-create-first-bicep.md).
