---
title: Mise à niveau automatique des extensions pour les machines virtuelles et les groupes identiques dans Azure
description: Découvrez comment activer la mise à niveau automatique des extensions pour vos machines virtuelles et groupes de machines virtuelles identiques dans Azure.
author: mayanknayar
ms.service: virtual-machines
ms.subservice: automatic-extension-upgrade
ms.workload: infrastructure
ms.topic: how-to
ms.date: 08/10/2021
ms.author: manayar
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6af80da8c05df4f56fe04ae45169c5e1a63fcd9c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122525336"
---
# <a name="automatic-extension-upgrade-for-vms-and-scale-sets-in-azure"></a>Mise à niveau automatique des extensions pour les machines virtuelles et les groupes identiques dans Azure

La mise à niveau automatique des extensions est disponible pour les machines virtuelles Azure et les groupes de machines virtuelles identiques Azure. Lorsque la mise à niveau automatique des extensions est activée sur une machine virtuelle ou un groupe identique, l’extension est automatiquement mise à niveau chaque fois que l’éditeur de l’extension publie une nouvelle version pour cette extension.

 La mise à niveau automatique des extensions présente les fonctionnalités suivantes :
- Prise en charge pour les machines virtuelles Azure et les groupes de machines virtuelles identiques Azure.
- Les mises à niveau sont appliquées dans un modèle de déploiement selon le principe de première disponibilité (détaillé ci-dessous).
- Pour un groupe de machines virtuelles identiques, au maximum 20 % des machines virtuelles du groupe identique seront mises à niveau par lot. La taille de lot minimale est d’une machine virtuelle.
- Fonctionne pour toutes les tailles de machine virtuelle et pour les extensions tant Windows que Linux.
- Vous pouvez désactiver les mises à jour automatiques à tout moment.
- La mise à niveau automatique des extensions peut être activée sur un groupe de machines virtuelles identiques de n’importe quelle taille.
- Chaque extension prise en charge est inscrite individuellement, et vous pouvez choisir les extensions à mettre à niveau automatiquement.
- Prise en charge dans toutes les régions du cloud public.

## <a name="how-does-automatic-extension-upgrade-work"></a>Comment fonctionne la mise à niveau automatique des extensions ?
Le processus de mise à niveau d’extension remplace la version existante de l’extension sur une machine virtuelle par une nouvelle version lors de la publication de celle-ci par l’éditeur de l’extension. L’intégrité de la machine virtuelle est analysée après l’installation de la nouvelle extension. Si la machine virtuelle n’est pas dans un état sain dans les cinq minutes suivant la fin de la mise à niveau, la version précédente est restaurée.

Une mise à jour d’extension qui a échoué fait automatiquement l’objet d’une nouvelle tentative. Une nouvelle tentative est effectuée automatiquement à quelques jours d’intervalle, sans intervention de l’utilisateur.

### <a name="availability-first-updates"></a>Mises à jour selon la première disponibilité
Le modèle de première disponibilité pour les mises à jour orchestrées de la plateforme garantit que les configurations de disponibilité dans Azure sont respectées sur plusieurs niveaux de disponibilité.

Pour un groupe de machines virtuelles en cours de mise à jour, la plateforme Azure orchestre les mises à jour :

**Entre les régions :**
- Une mise à jour sera déployée sur Azure dans le monde entier de manière progressive afin d’éviter les échecs de déploiement à l’échelle d’Azure.
- Une « phase » peut englober une ou plusieurs régions, et une mise à jour ne change pas de phase tant que les machines virtuelles éligibles dans la phase précédente n’ont pas été correctement mises à jour.
- Les régions associées géographiquement ne seront pas mises à jour simultanément et ne pourront pas dépendre de la même phase régionale.
- La réussite d’une mise à jour est mesurée par le suivi de l’intégrité d’une machine virtuelle après sa mise à jour. L’intégrité de la machine virtuelle est suivie via les indicateurs d’intégrité de la plateforme pour la machine virtuelle. Pour des groupes de machines virtuelles identiques, l’intégrité des machines virtuelles est suivie à l’aide de sondes d’intégrité d’application ou de l’extension Intégrité de l’application si elle est appliquée au groupe identique.

**Dans une région :**
- Les machines virtuelles de différentes Zones de disponibilité ne sont pas mises à jour simultanément avec la même mise à jour.
- Les machines virtuelles uniques ne faisant pas partie d’un groupe à haute disponibilité sont traitées par lot dans la mesure du possible afin d’éviter les mises à jour simultanées de toutes les machines virtuelles d’un abonnement.  

**Dans un « groupe » :**
- Toutes les machines virtuelles d’un même groupe à haute disponibilité ou groupe identique ne sont pas mises à jour simultanément.  
- Les machines virtuelles d’un même groupe à haute disponibilité sont mises à jour dans les limites du domaine de mise à jour, et les machines virtuelles de différents domaines de mise à jour ne sont pas mises à jour simultanément.  
- Les machines virtuelles d’un groupe de machines virtuelles identiques commun sont regroupées par lots et mises à jour dans les limites du domaine de mise à jour.

### <a name="upgrade-process-for-virtual-machine-scale-sets"></a>Processus de mise à niveau pour les groupes de machines virtuelles identiques
1. Avant de commencer le processus de mise à niveau, l’orchestrateur vérifie qu’il n’y a pas plus de 20 % des machines virtuelles dans tout le groupe identique qui présentent un état non sain (pour une raison ou une autre).

2. L’orchestrateur de mise à niveau identifie le lot d’instances de machine virtuelle à mettre à niveau. Un orchestrateur de mise à niveau peut avoir au maximum 20 % du nombre total de machines virtuelles, sous réserve d’une taille de lot minimale d’une machine virtuelle.

3. Pour les groupes identiques configurés avec des sondes d’intégrité d’application ou l’extension Intégrité de l’application, la mise à niveau attend jusqu’à cinq minutes (ou la durée définie dans la configuration de la sonde d’intégrité) que la machine virtuelle passe à l’état sain avant de mettre à niveau le lot suivant. Si une machine virtuelle ne récupère pas son intégrité après une mise à niveau, par défaut, la version précédente de l’extension sur la machine virtuelle est réinstallée.

4. L’orchestrateur de mise à niveau suit également le pourcentage de machines virtuelles qui deviennent non saines après une mise à niveau. La mise à niveau s’arrête si plus de 20 % des instances mises à niveau passent à l’état non sain pendant le processus de mise à niveau.

Le processus ci-dessus se poursuit jusqu’à ce que toutes les instances dans le groupe identique aient été mises à niveau.

L’orchestrateur de mise à niveau du groupe identique vérifie l’intégrité de tout le groupe identique avant de procéder à la mise à niveau de chaque lot. Durant la mise à niveau d’un lot, il peut arriver que d’autres activités de maintenance planifiées ou non planifiées aient lieu en même temps et aient un impact sur l’intégrité des machines virtuelles de votre groupe identique. Si c’est le cas et que plus de 20 % des instances du groupe identique passent à l’état non sain, la mise à niveau du groupe identique s’arrête à la fin du lot en cours.

## <a name="supported-extensions"></a>Extensions prises en charge
La mise à niveau automatique des extensions prend en charge les extensions suivantes (et d’autres sont ajoutées régulièrement) :
- Dependency Agent – [Linux](./extensions/agent-dependency-linux.md) et [Windows](./extensions/agent-dependency-windows.md)
- [Extension Intégrité de l’application](../virtual-machine-scale-sets/virtual-machine-scale-sets-health-extension.md) - Linux et Windows
- [Extension Configuration d’invité](./extensions/guest-configuration.md) - Linux et Windows
- Key Vault - [Linux](./extensions/key-vault-linux.md) et [Windows](./extensions/key-vault-windows.md)


## <a name="enabling-automatic-extension-upgrade"></a>Activation de la mise à niveau automatique des extensions

Pour activer la mise à niveau automatique des extensions pour une extension, vous devez vous assurer que la propriété `enableAutomaticUpgrade`est définie sur `true` et ajoutée à chaque définition d’extension individuellement.

### <a name="rest-api-for-virtual-machines"></a>API REST pour les machines virtuelles
Pour activer la mise à niveau automatique des extensions pour une extension (dans cet exemple, l’extension Dependency Agent) sur une machine virtuelle Azure, utilisez la commande suivante :

```
PUT on `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachines/<vmName>/extensions/<extensionName>?api-version=2019-12-01`
```

```json
{    
    "name": "extensionName",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "location": "<location>",
    "properties": {
        "autoUpgradeMinorVersion": true,
        "enableAutomaticUpgrade": true, 
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5"
        }
}
```

### <a name="rest-api-for-virtual-machine-scale-sets"></a>API REST pour les groupes de machines virtuelles identiques
Utilisez ce qui suit pour ajouter l’extension au modèle de groupe identique :

```
PUT on `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachineScaleSets/<vmssName>?api-version=2019-12-01`
```

```json
{
   "location": "<location>",
   "properties": {
        "virtualMachineProfile": {
            "extensionProfile": {
                "extensions": [
                {
                "name": "<extensionName>",
                  "properties": {
                        "autoUpgradeMinorVersion": true,
                        "enableAutomaticUpgrade": true,
                    "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
                    "type": "DependencyAgentWindows",
                    "typeHandlerVersion": "9.5"
                    }
                }
                ]
            }
        }
    }
}
```

### <a name="azure-powershell-for-virtual-machines"></a>Azure PowerShell pour les machines virtuelles
Utilisez la cmdlet [Set-AzVMExtension](/powershell/module/az.compute/set-azvmextension) :

```azurepowershell-interactive
Set-AzVMExtension -ExtensionName "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ExtensionType "DependencyAgentWindows" `
    -TypeHandlerVersion 9.5 `
    -Location WestUS `
    -EnableAutomaticUpgrade $true
```


### <a name="azure-powershell-for-virtual-machine-scale-sets"></a>Azure PowerShell Azure pour les groupes de machines virtuelles identiques
Utilisez la cmdlet [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) pour ajouter l’extension au modèle de groupe identique :

```azurepowershell-interactive
Add-AzVmssExtension -VirtualMachineScaleSet $vmss
    -Name "Microsoft.Azure.Monitoring.DependencyAgent" `
    -Publisher "Microsoft.Azure.Monitoring.DependencyAgent" `
    -Type "DependencyAgentWindows" `
    -TypeHandlerVersion 9.5 `
    -EnableAutomaticUpgrade $true
```

Mettez à jour le groupe identique à l’aide de [Update-AzVmss](/powershell/module/az.compute/update-azvmss) après avoir ajouté l’extension.


### <a name="azure-cli-for-virtual-machines"></a>Interface de ligne de commande Azure pour Machines virtuelles
Utilisez la cmdlet [az vm extension set](/cli/azure/vm/extension#az_vm_extension_set) :

```azurecli-interactive
az vm extension set \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5 \
    --enable-auto-upgrade true
```

### <a name="azure-cli-for-virtual-machine-scale-sets"></a>Azure CLI pour les groupes de machines virtuelles identiques
Utilisez la cmdlet [az vmss extension set](/cli/azure/vmss/extension#az_vmss_extension_set) pour ajouter l’extension au modèle de groupe identique :

```azurecli-interactive
az vmss extension set \
    --resource-group myResourceGroup \
    --vmss-name myVMSS \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5 \
    --enable-auto-upgrade true
```

## <a name="extension-upgrades-with-multiple-extensions"></a>Mises à niveau d’extension avec plusieurs extensions

Une machine virtuelle ou un groupe de machines virtuelles identiques peuvent avoir plusieurs extensions pour lesquelles la mise à niveau d’extension automatique est activée. Les mêmes machine virtuelle ou groupe identique peuvent également avoir d’autres extensions sans que la mise à niveau d’extension automatique soit activée.  

Si plusieurs mises à niveau d’extension sont disponibles pour une machine virtuelle, il est possible de les regrouper par lot, mais chaque mise à niveau d’extension est appliquée individuellement sur une machine virtuelle. Un échec sur une extension n’a aucun impact sur les autres extensions qui peuvent être mises à niveau. Par exemple, si deux extensions sont planifiées pour une mise à niveau et que la mise à niveau de la première extension échoue, la deuxième extension sera quand même mise à niveau.

Les mises à niveau automatiques d’extensions peuvent également être appliquées lorsqu’une machine virtuelle ou un groupe de machines virtuelles identiques a plusieurs extensions configurées avec le [séquencement d’extensions](../virtual-machine-scale-sets/virtual-machine-scale-sets-extension-sequencing.md). Le séquencement d’extensions s’applique au premier déploiement de la machine virtuelle, et toute mise à niveau d’extension future est appliquée de façon indépendante.


## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [En savoir plus sur l’extension Intégrité de l’application](../virtual-machine-scale-sets/virtual-machine-scale-sets-health-extension.md)
