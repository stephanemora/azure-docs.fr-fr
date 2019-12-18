---
title: Étiqueter les ressources pour l’organisation logique
description: Indique comment appliquer des étiquettes afin d'organiser des ressources Azure dédiées à la facturation et à la gestion.
ms.topic: conceptual
ms.date: 12/05/2019
ms.openlocfilehash: a0ba5ba89b966de4aa1d1394f7d90c99f8352115
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74893023"
---
# <a name="use-tags-to-organize-your-azure-resources"></a>Organisation des ressources Azure à l'aide d'étiquettes

Vous allez appliquer des balises à vos ressources Azure pour les organiser de façon logique dans une taxonomie. Chaque balise se compose d’une paire nom-valeur. Par exemple, vous pouvez appliquer le nom « Environnement » et la valeur « Production » à toutes les ressources en production.

Une fois que vous avez appliqué une balise, vous pouvez utiliser son nom et sa valeur pour récupérer toutes les ressources dans votre abonnement. Les balises vous permettent de récupérer les ressources associées de groupes de ressources différents. Cette approche est utile lorsque vous devez organiser les ressources à des fins de facturation ou de gestion.

Votre taxonomie doit prendre en compte une stratégie de balisage des métadonnées en libre-service en plus d’une stratégie de balisage automatique pour réduire la charge pesant sur les utilisateurs et augmenter la précision.

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="limitations"></a>Limites

Les limites suivantes s’appliquent aux balises :

* Les types de ressources ne prennent pas tous en charge les étiquettes. Pour déterminer si vous pouvez appliquer une étiquette à un type de ressource, consultez [Prise en charge des étiquettes pour les ressources Azure](tag-support.md).
* Chaque ressource ou groupe de ressources peut inclure un maximum de 50 paires nom/valeur de balise. Si vous devez appliquer plus de balises que le nombre maximal autorisé, utilisez une chaîne JSON comme valeur de balise. La chaîne JSON peut contenir plusieurs valeurs appliquées à un seul nom de balise. Un groupe de ressources peut contenir de nombreuses ressources qui ont chacune 50 paires nom/valeur de balise.
* Le nom de balise est limité à 512 caractères, et la valeur de balise à 256 caractères. Pour les comptes de stockage, le nom de balise est limité à 128 caractères, et la valeur de balise à 256 caractères.
* Les machines virtuelles généralisées ne prennent pas en charge les balises.
* Les ressources d’un groupe de ressources n’héritent pas des balises appliquées à ce groupe de ressources.
* Les balises ne peuvent pas être appliquées à des ressources classiques comme les Services cloud.
* Les noms de balise ne peuvent pas contenir ces caractères : `<`, `>`, `%`, `&`, `\`, `?`, `/`

   > [!NOTE]
   > Actuellement les zones Azure DNS et les services Traffic Manager n’autorisent pas non plus l’utilisation des espaces dans la balise. 

## <a name="required-access"></a>Accès requis

Pour appliquer des étiquettes à des ressources, l'utilisateur doit disposer de l'accès en écriture pour ce type de ressource. Pour appliquer des étiquettes à tous les types de ressources, utilisez le rôle [Contributeur](../role-based-access-control/built-in-roles.md#contributor). Pour appliquer des étiquettes à un seul type de ressource, utilisez le rôle Contributeur correspondant à cette ressource. Par exemple, pour appliquer des étiquettes aux machines virtuelles, utilisez le rôle [Contributeur de machines virtuelles](../role-based-access-control/built-in-roles.md#virtual-machine-contributor).

## <a name="policies"></a>Stratégies

Vous pouvez utiliser [Azure Policy](../governance/policy/overview.md) pour appliquer des règles et des conventions d'étiquetage. En créant une stratégie, vous éviterez que des ressources non conformes aux étiquettes attendues par votre organisation soient déployées dans le cadre de votre abonnement. Au lieu d'appliquer des étiquettes ou de rechercher des ressources non conformes manuellement, vous pouvez créer une stratégie qui appliquera automatiquement les étiquettes nécessaires pendant le déploiement. Les étiquettes peuvent également être appliquées aux ressources existantes avec le nouvel effet [Modifier](../governance/policy/concepts/effects.md#modify) et une [tâche de correction](../governance/policy/how-to/remediate-resources.md). La section suivante présente des exemples de stratégies pour les étiquettes.

[!INCLUDE [Tag policies](../../includes/azure-policy-samples-general-tags.md)]

## <a name="powershell"></a>PowerShell

Pour afficher les étiquettes existantes d'un *groupe de ressources*, utilisez :

```azurepowershell-interactive
(Get-AzResourceGroup -Name examplegroup).Tags
```

Le script retourne les informations au format suivant :

```powershell
Name                           Value
----                           -----
Dept                           IT
Environment                    Test
```

Pour afficher les étiquettes existantes d’une *ressource dont le nom et le groupe de ressources sont spécifiés*, utilisez :

```azurepowershell-interactive
(Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup).Tags
```

Ou, si vous avez l’ID d’une ressource, vous pouvez transmettre cet ID de ressource pour obtenir les étiquettes.

```azurepowershell-interactive
(Get-AzResource -ResourceId /subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>).Tags
```

Pour obtenir *des groupes de ressources qui ont un nom et une valeur d’étiquette spécifiques*, utilisez :

```azurepowershell-interactive
(Get-AzResourceGroup -Tag @{ "Dept"="Finance" }).ResourceGroupName
```

Pour obtenir *des ressources qui ont un nom et une valeur d’étiquette spécifiques*, utilisez :

```azurepowershell-interactive
(Get-AzResource -Tag @{ "Dept"="Finance"}).Name
```

Pour obtenir *les ressources contenant un nom d'étiquette spécifique*, utilisez :

```azurepowershell-interactive
(Get-AzResource -TagName "Dept").Name
```

Chaque fois que vous appliquez des étiquettes à une ressource ou à un groupe de ressources, vous remplacez les étiquettes existantes de cette ressource ou de ce groupe de ressources. Par conséquent, vous devez utiliser une approche différente selon que la ressource ou le groupe de ressources comporte ou non des étiquettes existantes.

Pour ajouter des étiquettes à un *groupe de ressources sans étiquettes existantes*, utilisez :

```azurepowershell-interactive
Set-AzResourceGroup -Name examplegroup -Tag @{ "Dept"="IT"; "Environment"="Test" }
```

Pour ajouter des étiquettes à un *groupe de ressources avec des étiquettes existantes*, récupérez les étiquettes existantes, ajoutez la nouvelle étiquette et réappliquez les étiquettes :

```azurepowershell-interactive
$tags = (Get-AzResourceGroup -Name examplegroup).Tags
$tags.Add("Status", "Approved")
Set-AzResourceGroup -Tag $tags -Name examplegroup
```

Pour ajouter des étiquettes à une *ressource sans étiquettes existantes*, utilisez :

```azurepowershell-interactive
$resource = Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup
Set-AzResource -Tag @{ "Dept"="IT"; "Environment"="Test" } -ResourceId $resource.ResourceId -Force
```

Vous pouvez avoir plusieurs ressources portant le même nom dans un groupe de ressources. Dans ce cas, vous pouvez définir chaque ressource à l’aide des commandes suivantes :

```azurepowershell-interactive
$resource = Get-AzResource -ResourceName sqlDatabase1 -ResourceGroupName examplegroup
$resource | ForEach-Object { Set-AzResource -Tag @{ "Dept"="IT"; "Environment"="Test" } -ResourceId $_.ResourceId -Force }
```

Pour ajouter des étiquettes à une *ressource avec des étiquettes existantes*, utilisez :

```azurepowershell-interactive
$resource = Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup
$resource.Tags.Add("Status", "Approved")
Set-AzResource -Tag $resource.Tags -ResourceId $resource.ResourceId -Force
```

Pour appliquer toutes les étiquettes d’un groupe de ressources à ses ressources *sans conserver les étiquettes existantes*, utilisez le script suivant :

```azurepowershell-interactive
$group = Get-AzResourceGroup -Name examplegroup
Get-AzResource -ResourceGroupName $group.ResourceGroupName | ForEach-Object {Set-AzResource -ResourceId $_.ResourceId -Tag $group.Tags -Force }
```

Pour appliquer toutes les étiquettes d’un groupe de ressources à ses ressources et *conserver les étiquettes existantes sur les ressources qui ne sont pas des doublons*, utilisez le script suivant :

```azurepowershell-interactive
$group = Get-AzResourceGroup -Name examplegroup
if ($null -ne $group.Tags) {
    $resources = Get-AzResource -ResourceGroupName $group.ResourceGroupName
    foreach ($r in $resources)
    {
        $resourcetags = (Get-AzResource -ResourceId $r.ResourceId).Tags
        if ($resourcetags)
        {
            foreach ($key in $group.Tags.Keys)
            {
                if (-not($resourcetags.ContainsKey($key)))
                {
                    $resourcetags.Add($key, $group.Tags[$key])
                }
            }
            Set-AzResource -Tag $resourcetags -ResourceId $r.ResourceId -Force
        }
        else
        {
            Set-AzResource -Tag $group.Tags -ResourceId $r.ResourceId -Force
        }
    }
}
```

Pour supprimer toutes les étiquettes, utilisez une table de hachage vide :

```azurepowershell-interactive
Set-AzResourceGroup -Tag @{} -Name examplegroup
```

## <a name="azure-cli"></a>Azure CLI

Pour afficher les étiquettes existantes d'un *groupe de ressources*, utilisez :

```azurecli
az group show -n examplegroup --query tags
```

Le script retourne les informations au format suivant :

```json
{
  "Dept"        : "IT",
  "Environment" : "Test"
}
```

Alternativement, pour afficher les étiquettes existantes d'une *ressource dont le nom, le type et le groupe sont spécifiés*, utilisez :

```azurecli
az resource show -n examplevnet -g examplegroup --resource-type "Microsoft.Network/virtualNetworks" --query tags
```

Durant un bouclage sur une collection de ressources, vous préférez peut-être afficher la ressource par ID de ressource. Un exemple complet est présenté plus loin dans cet article. Pour afficher les étiquettes existantes d'une *ressource dont l'ID de ressource est spécifié*, utilisez :

```azurecli
az resource show --id <resource-id> --query tags
```

Pour obtenir des groupes de ressources contenant une étiquette spécifique, utilisez `az group list` :

```azurecli
az group list --tag Dept=IT
```

Pour obtenir toutes les ressources contenant une étiquette et une valeur spécifiques, utilisez `az resource list` :

```azurecli
az resource list --tag Dept=Finance
```

Chaque fois que vous appliquez des étiquettes à une ressource ou à un groupe de ressources, vous remplacez les étiquettes existantes de cette ressource ou de ce groupe de ressources. Par conséquent, vous devez utiliser une approche différente selon que la ressource ou le groupe de ressources comporte ou non des étiquettes existantes.

Pour ajouter des étiquettes à un *groupe de ressources sans étiquettes existantes*, utilisez :

```azurecli
az group update -n examplegroup --set tags.Environment=Test tags.Dept=IT
```

Pour ajouter des étiquettes à une *ressource sans étiquettes existantes*, utilisez :

```azurecli
az resource tag --tags Dept=IT Environment=Test -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Pour ajouter des étiquettes à une ressource qui en comporte déjà, récupérez les étiquettes existantes et reformatez cette valeur, puis réappliquez les étiquettes nouvelles et existantes :

```azurecli
jsonrtag=$(az resource show -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks" --query tags -o json)
rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
az resource tag --tags $rt Project=Redesign -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Pour appliquer toutes les étiquettes d’un groupe de ressources à ses ressources *sans conserver les étiquettes existantes*, utilisez le script suivant :

```azurecli
groups=$(az group list --query [].name --output tsv)
for rg in $groups
do
  jsontag=$(az group show -n $rg --query tags -o json)
  t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')
  r=$(az resource list -g $rg --query [].id --output tsv)
  for resid in $r
  do
    az resource tag --tags $t --id $resid
  done
done
```

Pour appliquer toutes les étiquettes d’un groupe de ressources à ses ressources et *conserver les étiquettes existantes*, utilisez le script suivant :

```azurecli
groups=$(az group list --query [].name --output tsv)
for rg in $groups
do
  jsontag=$(az group show -n $rg --query tags -o json)
  t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')
  r=$(az resource list -g $rg --query [].id --output tsv)
  for resid in $r
  do
    jsonrtag=$(az resource show --id $resid --query tags -o json)
    rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
    az resource tag --tags $t$rt --id $resid
  done
done
```

## <a name="templates"></a>Modèles

Pour marquer une ressource au cours du déploiement, ajoutez l’élément `tags` à la ressource que vous déployez. Indiquez le nom et la valeur de la balise.

### <a name="apply-a-literal-value-to-the-tag-name"></a>Appliquer une valeur littérale au nom de balise

L’exemple suivant illustre un compte de stockage avec deux balises (`Dept` et `Environment`) définies sur des valeurs littérales :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "Dept": "Finance",
                "Environment": "Production"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

Pour définir une balise sur une valeur datetime, utilisez la [fonction utcNow](resource-group-template-functions-string.md#utcnow).

### <a name="apply-an-object-to-the-tag-element"></a>Appliquer un objet à l’élément de balise

Vous pouvez définir un paramètre d’objet qui stocke plusieurs balises et appliquer cet objet à l’élément de balise. Chaque propriété de l’objet devient une balise distincte pour la ressource. L’exemple suivant illustre un paramètre nommé `tagValues` appliqué à l’élément de balise.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        },
        "tagValues": {
            "type": "object",
            "defaultValue": {
                "Dept": "Finance",
                "Environment": "Production"
            }
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": "[parameters('tagValues')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-a-json-string-to-the-tag-name"></a>Appliquer une chaîne JSON au nom de balise

Pour stocker plusieurs valeurs dans une seule balise, appliquez une chaîne JSON qui représente les valeurs. La chaîne JSON complète est stockée sous la forme d’une balise ne pouvant pas dépasser 256 caractères. L’exemple illustre une balise unique nommée `CostCenter` qui contient plusieurs valeurs d’une chaîne JSON :  

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-tags-from-resource-group"></a>Appliquer des balises à partir d’un groupe de ressources

Pour appliquer des balises d’un groupe de ressources à une ressource, utilisez la fonction [resourceGroup](resource-group-template-functions-resource.md#resourcegroup). Lors de l’obtention de la valeur de balise, utilisez la syntaxe `tags.[tag-name]` au lieu de la syntaxe `tags.tag-name`, car certains caractères ne sont pas correctement analysés dans la notation par points.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "Dept": "[resourceGroup().tags['Dept']]",
                "Environment": "[resourceGroup().tags['Environment']]"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

## <a name="portal"></a>Portail

[!INCLUDE [resource-manager-tag-resource](../../includes/resource-manager-tag-resources.md)]

## <a name="rest-api"></a>API REST

Le portail Azure et PowerShell utilisent tous deux [l’API REST de Resource Manager](https://docs.microsoft.com/rest/api/resources/) en arrière-plan. Si vous avez besoin d'intégrer l'étiquetage dans un autre environnement, vous pouvez récupérer des étiquettes en utilisant **GET** sur l'ID de ressource et mettre à jour le jeu d'étiquettes en utilisant un appel **PATCH**.

## <a name="tags-and-billing"></a>Étiquettes et facturation

Vous pouvez utiliser des étiquettes pour regrouper vos données de facturation. Par exemple, si vous exécutez plusieurs machines virtuelles pour différentes organisations, vous pouvez recourir aux étiquettes afin de regrouper l'utilisation par centre de coûts. Vous pouvez également utiliser des étiquettes pour catégoriser les coûts par environnement d'exécution ; par exemple, l'utilisation de la facturation pour les machines virtuelles en cours d'exécution dans l'environnement de production.

Vous pouvez récupérer des informations sur les étiquettes par le biais des [API Resource Usage et RateCard](../billing/billing-usage-rate-card-overview.md) ou du fichier de valeurs séparées par des virgules (CSV). Téléchargez le fichier d’utilisation depuis le [Centre des comptes Azure](https://account.azure.com/Subscriptions) ou depuis le portail Azure. Pour plus d’informations, consultez [Télécharger et consulter votre facture Azure et vos données d’utilisation quotidienne](../billing/billing-download-azure-invoice-daily-usage-date.md). Lorsque vous téléchargez le fichier d’utilisation depuis le Centre des comptes Azure, sélectionnez **Version 2**. Pour les services qui prennent en charge les étiquettes avec la facturation, les étiquettes s'affichent dans la colonne **Étiquettes**.

Pour plus d’informations sur les opérations de l’API REST, consultez [Informations de référence sur l’API REST Azure Billing](/rest/api/billing/).

## <a name="next-steps"></a>Étapes suivantes

* Les types de ressources ne prennent pas tous en charge les étiquettes. Pour déterminer si vous pouvez appliquer une étiquette à un type de ressource, consultez [Prise en charge des étiquettes pour les ressources Azure](tag-support.md).
* Pour plus d’informations sur l’utilisation du portail, consultez [Utilisation du portail Azure pour gérer vos ressources Azure](manage-resource-groups-portal.md).  
