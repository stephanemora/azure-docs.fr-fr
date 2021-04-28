---
title: Erreurs de SKU non disponible
description: Décrit comment résoudre les erreurs de référence SKU non disponible quand vous déployez des ressources avec Azure Resource Manager.
ms.topic: troubleshooting
ms.date: 04/14/2021
ms.openlocfilehash: 3baedf6a5c9f2dbfd3ddf666b458fac649fce2ac
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503896"
---
# <a name="resolve-errors-for-sku-not-available"></a>Résoudre les erreurs de référence SKU non disponible

Cet article décrit comment résoudre les erreurs **SkuNotAvailable**. Si vous ne parvenez pas à trouver de référence SKU appropriée dans cette région/zone ou une autre qui réponde aux besoins de votre entreprise, envoyez une [demande de référence SKU](/troubleshoot/azure/general/region-access-request-process) au support Azure.

## <a name="symptom"></a>Symptôme

Au cours du déploiement d’une ressource (généralement une machine virtuelle), vous pouvez recevoir le message et le code d’erreur suivants :

```
Code: SkuNotAvailable
Message: The requested tier for resource '<resource>' is currently not available in location '<location>'
for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.
```

## <a name="cause"></a>Cause

Vous recevez cette erreur lorsque la ressource de référence SKU que vous avez sélectionnée (par exemple, la taille de la machine virtuelle) n’est pas disponible pour l’emplacement que vous avez sélectionné.

Si vous déployez une machine virtuelle Azure Spot ou une instance de groupe identique Spot, il n’y a aucune capacité pour Azure Spot à cette localisation. Pour plus d’informations, consultez [Messages d’erreur Spot](../../virtual-machines/error-codes-spot.md).

## <a name="solution-1---powershell"></a>Solution 1 : PowerShell

Pour déterminer les références SKU qui sont disponibles dans une région ou une zone, utilisez la commande [Get-AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku). Filtrez les résultats par emplacement. Pour cette commande, vous devez disposer de la version la plus récente de PowerShell.

```azurepowershell-interactive
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

Les résultats incluent une liste de références pour l’emplacement et toutes les restrictions éventuellement liées aux références concernées. Notez que la référence SKU peut être répertoriée en tant que `NotAvailableForSubscription`.

```output
ResourceType          Name           Locations   Zone      Restriction                      Capability           Value
------------          ----           ---------   ----      -----------                      ----------           -----
virtualMachines       Standard_A0    centralus             NotAvailableForSubscription      MaxResourceVolumeMB   20480
virtualMachines       Standard_A1    centralus             NotAvailableForSubscription      MaxResourceVolumeMB   71680
virtualMachines       Standard_A2    centralus             NotAvailableForSubscription      MaxResourceVolumeMB  138240
virtualMachines       Standard_D1_v2 centralus   {2, 1, 3}                                  MaxResourceVolumeMB
```

Pour filtrer par localisation et référence SKU, utilisez :

```azurepowershell-interactive
$SubId = (Get-AzContext).Subscription.Id

$Region = "centralus" # change region here
$VMSku = "Standard_M" # change VM SKU here

$VMSKUs = Get-AzComputeResourceSku | where {$_.Locations.Contains($Region) -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains($VMSku)}

$OutTable = @()

foreach ($SkuName in $VMSKUs.Name)
        {
            $LocRestriction = if ((($VMSKUs | where Name -EQ $SkuName).Restrictions.Type | Out-String).Contains("Location")){"NotAvavalableInRegion"}else{"Available - No region restrictions applied" }
            $ZoneRestriction = if ((($VMSKUs | where Name -EQ $SkuName).Restrictions.Type | Out-String).Contains("Zone")){"NotAvavalableInZone: "+(((($VMSKUs | where Name -EQ $SkuName).Restrictions.RestrictionInfo.Zones)| Where-Object {$_}) -join ",")}else{"Available - No zone restrictions applied"}
            
            
            $OutTable += New-Object PSObject -Property @{
                                                         "Name" = $SkuName
                                                         "Location" = $Region
                                                         "Applies to SubscriptionID" = $SubId
                                                         "Subscription Restriction" = $LocRestriction
                                                         "Zone Restriction" = $ZoneRestriction
                                                         }
         }

$OutTable | select Name, Location, "Applies to SubscriptionID", "Region Restriction", "Zone Restriction" | Sort-Object -Property Name | FT
```

La commande retourne des résultats similaires à ce qui suit :

```output
Name                   Location  Applies to SubscriptionID            Region Restriction                         Zone Restriction                        
----                   --------  -------------------------            ------------------------                   ----------------     
Standard_M128          centralus xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Available - No region restrictions applied Available - No zone restrictions applied
Standard_M128-32ms     centralus xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Available - No region restrictions applied Available - No zone restrictions applied
Standard_M128-64ms     centralus xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Available - No region restrictions applied Available - No zone restrictions applied
Standard_M128dms_v2    centralus xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx NotAvavalableInRegion                      NotAvavalableInZone: 1,2,3
```

## <a name="solution-2---azure-cli"></a>Solution 2 : Azure CLI

Pour déterminer les références SKU qui sont disponibles dans une région, utilisez la commande [az vm list-skus](/cli/azure/vm#az_vm_list_skus). Utilisez le paramètre `--location` pour filtrer la sortie par localisation. Utilisez le paramètre `--size` pour rechercher par un nom partiel. Utilisez le paramètre `--all` pour afficher toutes les informations, y compris les tailles qui ne sont pas disponibles pour l’abonnement actuel.

Vous devez disposer d’Azure CLI 2.15.0 ou version ultérieure. Pour vérifier votre version, utilisez `az --version`. Si nécessaire, [mettez votre installation à jour](/cli/azure/update-azure-cli).

```azurecli-interactive
az vm list-skus --location southcentralus --size Standard_F --all --output table
```

La commande retourne des résultats similaires à ce qui suit :

```output
ResourceType     Locations       Name              Zones    Restrictions
---------------  --------------  ----------------  -------  --------------
virtualMachines  southcentralus  Standard_F1       1,2,3    None
virtualMachines  southcentralus  Standard_F2       1,2,3    None
virtualMachines  southcentralus  Standard_F4       1,2,3    None
...
virtualMachines  southcentralus  Standard_F72s_v2  1,2,3    NotAvailableForSubscription, type: Zone, locations: southcentralus, zones: 1,2,3
...
```

## <a name="solution-3---azure-portal"></a>Solution 3 : Portail Azure

Pour déterminer les références SKU qui sont disponibles dans une région, consultez le [portail](https://portal.azure.com). Connectez-vous au portail et ajoutez une ressource via l’interface. Lorsque vous définissez les valeurs, vous voyez les références (SKU) disponibles pour cette ressource. Vous n’avez pas besoin de terminer le déploiement.

Par exemple, démarrez le processus de création d’une machine virtuelle. Pour voir les autres tailles disponibles, sélectionnez **Modifier la taille**.

![Créer une machine virtuelle](./media/error-sku-not-available/create-vm.png)

Vous pouvez filtrer et faire défiler les tailles disponibles.

![Références SKU disponibles](./media/error-sku-not-available/available-sizes.png)

## <a name="solution-4---rest"></a>Solution 4 : REST

Pour déterminer les références SKU disponibles dans une région, utilisez l’opération [Resource Skus - List](/rest/api/compute/resourceskus/list).

Elle renvoie les références et les régions disponibles dans le format suivant :

```json
{
  "value": [
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A0",
      "tier": "Standard",
      "size": "A0",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A1",
      "tier": "Standard",
      "size": "A1",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    ...
  ]
}
```