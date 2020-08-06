---
title: Présentation des specs de modèle
description: Décrit comment créer des specs de modèle et les partager avec d’autres utilisateurs de votre organisation.
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 47dcf44b35ad5c0b77dd0b88d683071a7f2f4ecb
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87094097"
---
# <a name="azure-resource-manager-template-specs-preview"></a>Specs de modèle Azure Resource Manager (préversion)

Une spec de modèle est un nouveau type de ressource pour le stockage d’un modèle Resource Manager dans Azure en vue d’un déploiement ultérieur. Ce type de ressource vous permet de partager des modèles Ressource Manager avec d’autres utilisateurs de votre organisation. Comme toute autre ressource Azure, vous pouvez utiliser le contrôle d’accès en fonction du rôle (RBAC) pour partager la spec de modèle. Les utilisateurs ont uniquement besoin d’un accès en lecture à la spec de modèle pour en déployer le modèle, de sorte que vous pouvez partager le modèle sans permettre à d’autres utilisateurs de le modifier.

**Microsoft.Resources/templateSpecs** est le nouveau type de ressource pour les specs de modèle. Il se compose d’un modèle principal et d’un nombre quelconque de modèles liés. Azure stocke en toute sécurité les specs de modèle dans des groupes de ressources. Specs de modèle prend en charge le [contrôle de version](#versioning).

Pour déployer la spec de modèle, vous utilisez des outils Azure standard tels que PowerShell, Azure CLI, Portail Azure, REST et d’autres Kits de développement logiciel (SDK) et clients pris en charge. Vous utilisez les mêmes commandes et transmettez les mêmes paramètres pour le modèle.

L’avantage à utiliser des specs de modèle est que les équipes de votre organisation n’ont pas besoin de recréer ni de copier des modèles pour des scénarios courants. Vous créez des modèles canoniques et les partagez. Les modèles que vous incluez dans une spec de modèle doivent être vérifiés par les administrateurs de votre organisation afin de respecter les exigences et les directives de l’organisation.

> [!NOTE]
> La fonctionnalité Specs de modèle est actuellement en préversion. Pour l’utiliser, vous devez [vous inscrire sur la liste d’attente](https://aka.ms/templateSpecOnboarding).

## <a name="create-template-spec"></a>Créer une spec de modèle

L’exemple suivant montre un modèle simple pour la création d’un compte de stockage dans Azure.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "name": "[concat('storage', uniqueString(resourceGroup().id))]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "location": "[resourceGroup().location]",
      "kind": "StorageV2",
      "sku": {
        "name": "Premium_LRS",
        "tier": "Premium"
      }
    }
  ]
}
```

Lorsque vous créez la spec de modèle, les commandes PowerShell ou CLI sont transmises au fichier de modèle principal. Si le modèle principal fait référence à des modèles liés, les commandes les recherchent et les empaquettent pour créer la spec de modèle. Pour plus d’informations, consultez [Créer une spec de modèle avec des modèles liés](#create-a-template-spec-with-linked-templates).

Créez une spec de modèle en utilisant :

```azurepowershell
New-AzTemplateSpec -Name storageSpec -Version 1.0 -ResourceGroupName templateSpecsRg -TemplateJsonFile ./mainTemplate.json
```

Vous pouvez afficher toutes les specs de modèle dans votre abonnement en utilisant :

```azurepowershell
Get-AzTemplateSpec
```

Vous pouvez afficher les détails d’une spec de modèle, notamment ses versions, avec :

```azurepowershell
Get-AzTemplateSpec -ResourceGroupName templateSpecsRG -Name storageSpec
```

## <a name="deploy-template-spec"></a>Déployer une spec de modèle

Une fois que vous avez créé la spec de modèle, les utilisateurs disposant d’un accès **en lecture** à la spec de modèle peuvent la déployer. Pour plus d’informations sur l’octroi de l’accès, consultez [Didacticiel : Accorder à un groupe l’accès aux ressources Azure à l’aide d’Azure PowerShell](../../role-based-access-control/tutorial-role-assignments-group-powershell.md).

Les specs de modèle peuvent être déployées via le portail, PowerShell, Azure CLI, ou en tant que modèle lié dans un déploiement de modèle plus volumineux. Les utilisateurs d’une organisation peuvent déployer une spec de modèle sur n’importe quelle étendue d’Azure (groupe de ressources, abonnement, groupe d’administration ou locataire).

Au lieu de passer par un chemin d’accès ou un URI pour un modèle, vous déployez une spec de modèle en fournissant son ID de ressource. L’ID de ressource possède le format suivant :

**/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Resources/templateSpecs/{template-spec-name}/versions/{template-spec-version}**

Notez que l’ID de ressource contient un numéro de version pour la spec de modèle.

Par exemple, vous déployez une spec de modèle à l’aide de la commande PowerShell suivante.

```azurepowershell
$id = "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/templateSpecsRG/providers/Microsoft.Resources/templateSpecs/storageSpec/versions/1.0"

New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName demoRG
```

Dans la pratique, vous exécutez généralement `Get-AzTemplateSpec` pour obtenir l’ID de la spec de modèle que vous souhaitez déployer.

```azurepowershell
$id = (Get-AzTemplateSpec -Name storageSpec -ResourceGroupName templateSpecsRg -Version 1.0).Version.Id

New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName demoRG
```

## <a name="create-a-template-spec-with-linked-templates"></a>Créer une spec de modèle avec des modèles liés

Si le modèle principal de votre spec de modèle fait référence à des modèles liés, les commandes PowerShell et CLI peuvent automatiquement trouver et empaqueter les modèles liés à partir de votre disque local. Vous n’avez pas besoin de configurer manuellement les référentiels ou les comptes de stockage pour héberger les specs de modèle : tout est autonome dans la ressource de spec de modèle.

L’exemple suivant est constitué d’un modèle principal et de deux modèles liés. L’exemple n’est qu’un extrait du modèle. Notez qu’il utilise une propriété nommée `relativePath` pour créer un lien vers les autres modèles. Vous devez utiliser `apiVersion` de `2020-06-01` ou une version ultérieure pour la ressource de déploiement.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    ...
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-06-01",
            ...
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "relativePath": "artifacts/webapp.json"
                }
            }
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-06-01",
            ...
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "relativePath": "artifacts/database.json"
                }
            }
        }
    ],
    "outputs": {}
}

```

Lorsque la commande PowerShell ou CLI permettant de créer la spec de modèle est exécutée pour l’exemple précédent, la commande recherche trois fichiers (le modèle principal, le modèle d’application web [`webapp.json`] et le modèle de base de données [`database.json`]), et les empaquette dans la spec de modèle.

Pour plus d’informations, consultez [Didacticiel : Créer une spec de modèle avec des modèles liés](template-specs-create-linked.md).

## <a name="deploy-template-spec-as-a-linked-template"></a>Déployer la spec de modèle en tant que modèle lié

Une fois que vous avez créé une spec de modèle, il est facile de la réutiliser à partir d’un modèle Resource Manager ou d’une autre spec de modèle. Vous pouvez créer un lien vers une spec de modèle en ajoutant son ID de ressource à votre modèle. La spec de modèle lié est déployée automatiquement lorsque vous déployez le modèle principal. Ce comportement vous permet de développer des specs de modèle modulaires et de les réutiliser en fonction des besoins.

Par exemple, vous pouvez créer une spec de modèle qui déploie des ressources réseau et une autre spec de modèle qui déploie des ressources de stockage. Dans les modèles Resource Manager, vous pouvez établir un lien vers ces deux specs de modèle chaque fois que vous avez besoin de configurer des ressources réseau ou de stockage.

L’exemple suivant est similaire à l’exemple précédent, mais vous utilisez la propriété `id` pour créer un lien vers une spec de modèle plutôt que la propriété `relativePath` pour créer un lien vers un modèle local. Utilisez `2020-06-01` pour la version d’API de la ressource de déploiement. Dans l’exemple, les specs de modèle se trouvent dans un groupe de ressources nommé **templateSpecsRG**.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    ...
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-06-01",
            "name": "networkingDeployment",
            ...
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "id": "[resourceId('templateSpecsRG', 'Microsoft.Resources/templateSpecs/versions', 'networkingSpec', '1.0')]"
                }
            }
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-06-01",
            "name": "storageDeployment",
            ...
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "id": "[resourceId('templateSpecsRG', 'Microsoft.Resources/templateSpecs/versions', 'storageSpec', '1.0')]"
                }
            }
        }
    ],
    "outputs": {}
}
```

Pour plus d’informations sur la liaison de specs de modèle, consultez [Didacticiel : Déployer une spec de modèle en tant que modèle lié](template-specs-deploy-linked-template.md).

## <a name="versioning"></a>Contrôle de version

Lorsque vous créez une spec de modèle, vous fournissez un numéro de version pour celle-ci. Lorsque vous itérez sur le code du modèle, vous pouvez mettre à jour une version existante (pour les correctifs logiciels) ou publier une nouvelle version. La version est une chaîne de texte. Vous êtes libre de choisir le système de contrôle de version, notamment le contrôle de version sémantique. Les utilisateurs de la spec de modèle peuvent fournir le numéro de version qu’ils souhaitent utiliser lors de son déploiement.

## <a name="next-steps"></a>Étapes suivantes

* Pour créer et déployer une spec de modèle, consultez [Démarrage rapide : Créer et déployer une spec de modèle](quickstart-create-template-specs.md).

* Pour plus d’informations sur la liaison de modèles dans les specs de modèle, consultez [Didacticiel : Créer une spec de modèle avec des modèles liés](template-specs-create-linked.md).

* Pour plus d’informations sur le déploiement d’une spec de modèle en tant que modèle lié, consultez [Didacticiel : Déployer une spec de modèle en tant que modèle lié](template-specs-deploy-linked-template.md).
