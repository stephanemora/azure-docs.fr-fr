---
title: Restaurer en cas d’erreur vers un déploiement réussi
description: Spécifiez qu’un déploiement ayant échoué doit être restauré vers un déploiement réussi.
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: 742a8f16a2dce3204b48085759091540586a4522
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99492210"
---
# <a name="rollback-on-error-to-successful-deployment"></a>Restaurer en cas d’erreur vers un déploiement réussi

En cas d'échec du déploiement, vous pouvez automatiquement relancer un déploiement antérieur réussi à partir de votre historique de déploiement. Cette fonctionnalité est utile si le déploiement de votre infrastructure était dans un état correct connu et que vous souhaitez le restaurer à cet état. Vous pouvez spécifier un déploiement antérieur particulier ou le dernier déploiement réussi.

> [!IMPORTANT]
> Cette fonctionnalité restaure un déploiement qui a échoué en redéployant un déploiement antérieur. Ce résultat peut être différent de ce que vous attendriez de l’annulation du déploiement ayant échoué. Veillez à bien comprendre comment le déploiement antérieur est redéployé.

## <a name="considerations-for-redeploying"></a>Considérations relatives au redéploiement

Avant d’utiliser cette fonctionnalité, tenez compte des détails ci-après sur la façon dont le redéploiement est géré :

- Le déploiement précédent est exécuté à l’aide du [mode complet](./deployment-modes.md#complete-mode), même si vous avez utilisé le [mode incrémentiel](./deployment-modes.md#incremental-mode) pendant le déploiement d’avant. Le redéploiement en mode complet peut produire des résultats inattendus si le déploiement antérieur utilisait le mode incrémentiel. Le mode complet signifie que toutes les ressources qui ne sont pas incluses dans le déploiement précédent sont supprimées. Spécifiez un déploiement antérieur qui représente toutes les ressources et leurs états que vous souhaitez voir figurer dans le groupe de ressources. Pour plus d’informations, consultez [Modes de déploiement](./deployment-modes.md).
- Le redéploiement est exécuté exactement comme le déploiement précédent, avec les mêmes paramètres. Vous ne pouvez pas changer les paramètres.
- Le redéploiement concerne uniquement les ressources ; il n’inclut pas les modifications de données.
- Vous pouvez utiliser cette fonctionnalité uniquement avec les déploiements de groupes de ressources. Elle ne prend pas en charge les déploiements de niveau abonnement, groupe d’administration ou locataire. Pour plus d’informations sur le déploiement au niveau de l’abonnement, consultez [Créer des groupes de ressources et des ressources au niveau de l’abonnement](./deploy-to-subscription.md).
- Vous pouvez uniquement utiliser cette option avec les déploiements de niveau racine. Les déploiements à partir d’un modèle imbriqué ne sont pas disponibles pour le redéploiement.

Pour utiliser cette option, vos déploiements doivent avoir des noms uniques dans l’historique des déploiements. Seul un nom unique permet d’identifier un déploiement spécifique. Si les noms ne sont pas uniques, un déploiement en échec peut remplacer un déploiement réussi dans l’historique.

Si vous spécifiez un déploiement antérieur qui n’existe pas dans l’historique des déploiements, la restauration retourne une erreur.

## <a name="powershell"></a>PowerShell

Pour redéployer le dernier déploiement réussi, ajoutez le paramètre `-RollbackToLastDeployment` comme indicateur.

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollbackToLastDeployment
```

Pour redéployer un déploiement spécifique, utilisez le paramètre `-RollBackDeploymentName` et indiquez le nom du déploiement. Le déploiement spécifié doit avoir réussi.

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollBackDeploymentName ExampleDeployment01
```

## <a name="azure-cli"></a>Azure CLI

Pour redéployer le dernier déploiement réussi, ajoutez le paramètre `--rollback-on-error` comme indicateur.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error
```

Pour redéployer un déploiement spécifique, utilisez le paramètre `--rollback-on-error` et indiquez le nom du déploiement. Le déploiement spécifié doit avoir réussi.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment02 \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error ExampleDeployment01
```

## <a name="rest-api"></a>API REST

Pour redéployer le dernier déploiement réussi en cas d’échec du déploiement actuel, utilisez :

```json
{
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
      "contentVersion": "1.0.0.0"
    },
    "mode": "Incremental",
    "parametersLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
      "contentVersion": "1.0.0.0"
    },
    "onErrorDeployment": {
      "type": "LastSuccessful",
    }
  }
}
```

Pour redéployer un déploiement spécifique en cas d’échec du déploiement actuel, utilisez :

```json
{
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
      "contentVersion": "1.0.0.0"
    },
    "mode": "Incremental",
    "parametersLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
      "contentVersion": "1.0.0.0"
    },
    "onErrorDeployment": {
      "type": "SpecificDeployment",
      "deploymentName": "<deploymentname>"
    }
  }
}
```

Le déploiement spécifié doit avoir réussi.

## <a name="next-steps"></a>Étapes suivantes

- Pour comprendre les modes complet et incrémentiel, consultez [Modes de déploiement Azure Resource Manager](deployment-modes.md).
- Pour comprendre comment définir des paramètres dans votre modèle, consultez [Comprendre la structure et la syntaxe des modèles Azure Resource Manager](template-syntax.md).
