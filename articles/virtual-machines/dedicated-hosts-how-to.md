---
title: Déployer des hôtes dédiés Azure
description: Déployez des machines virtuelles et des groupes identiques sur des hôtes dédiés.
author: brittanyrowe
ms.author: brittanyrowe
ms.service: virtual-machines
ms.subservice: dedicated-hosts
ms.topic: how-to
ms.workload: infrastructure
ms.date: 09/01/2021
ms.reviewer: brittanyrowe
ms.openlocfilehash: 3bae87c87c7ab8dd1090f40b4e0589c84e2c54ec
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128557114"
---
# <a name="deploy-vms-and-scale-sets-to-dedicated-hosts"></a>Déployer des machines virtuelles et des groupes identiques sur des hôtes dédiés

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Machines virtuelles Windows :heavy_check_mark: Groupes identiques uniformes

Cet article vous guide dans la création d’un [hôte dédié](dedicated-hosts.md) Azure pour héberger vos machines virtuelles et vos instances de groupe identique.


## <a name="limitations"></a>Limites

- Les tailles et les types de matériel disponibles pour les hôtes dédiés varient selon la région. Pour en savoir plus, consultez la [page de tarification de l’hôte](https://aka.ms/ADHPricing).

## <a name="create-a-host-group"></a>Créer un groupe hôte

Un **groupe hôte** est une ressource qui représente une collection d’hôtes dédiés. Vous créez un groupe hôte dans une région et une zone de disponibilité, et lui ajoutez des hôtes. Lors de la planification de la haute disponibilité, vous pouvez accéder à des options supplémentaires. Vous pouvez utiliser l’une des options suivantes, ou les deux, avec vos hôtes dédiés :
- Application sur plusieurs zones de disponibilité. Dans ce cas, vous devez disposer d’un groupe hôte dans chacune des zones que vous souhaitez utiliser.
- Application sur plusieurs domaines d’erreur mappés à des racks physiques.

Dans les deux cas, vous devez fournir le nombre de domaines d’erreur pour votre groupe hôte. Si vous ne souhaitez pas appliquer plusieurs domaines d’erreur dans votre groupe, utilisez un seul domaine d’erreur.

Vous pouvez également choisir d’utiliser des zones de disponibilité et des domaines d’erreur.

### <a name="portal"></a>[Portail](#tab/portal)

Dans cet exemple, nous allons créer un groupe hôte en utilisant 1 zone de disponibilité et 2 domaines d’erreur.

1. Ouvrez le [Portail Microsoft Azure](https://portal.azure.com).
1. Sélectionnez l’option **Créer une ressource**, en haut à gauche du portail.
1. Recherchez des **groupes hôtes** et sélectionnez **Host Groups** (Groupes hôtes) dans les résultats.
1. Dans la page **Host Groups** (Groupes hôtes), sélectionnez **Créer**.
1. Sélectionnez l’abonnement que vous souhaitez utiliser, puis sélectionnez **Création** pour créer un groupe de ressources.
1. Indiquez *myDedicatedHostsRG* dans la zone **Nom**, puis sélectionnez **OK**.
1. Dans **Host Group Name** (Nom du groupe hôte), saisissez *myHostGroup*.
1. Pour **Emplacement**, sélectionnez **USA Est**.
1. Pour **Zone de disponibilité**, sélectionnez **1**.
1. Pour **Fault Domain Count** (Nombre de domaines d’erreur), sélectionnez **2**.
1. Sélectionnez **Sélection élective automatique** pour attribuer automatiquement des machines virtuelles et des instances de groupes identiques à un hôte disponible dans ce groupe.
1. Sélectionnez **Vérifier + Créer**, puis attendez la validation.
1. Lorsque le message **Validation passed** (Validation réussie) apparaît, sélectionnez **Créer** pour créer le groupe hôte.

Cette opération ne prend que quelques instants.


### <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/cli)

Tous les niveaux tarifaires de l’hôte ne sont pas disponibles dans toutes les régions et zones de disponibilité. Vous pouvez répertorier la disponibilité de l’hôte et les éventuelles restrictions d’offre avant de commencer l’approvisionnement des hôtes dédiés.

```azurecli-interactive
az vm list-skus -l eastus2  -r hostGroups/hosts  -o table
```

Dans cet exemple, nous allons utiliser la commande [az vm host group create](/cli/azure/vm/host/group#az_vm_host_group_create) pour créer un groupe hôte à l’aide de zones de disponibilité et de domaines d’erreur.

```azurecli-interactive
az vm host group create \
   --name myHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 2
```

Ajoutez le paramètre `--automatic-placement true` pour que vos machines virtuelles et vos instances de groupes identiques soient placées automatiquement sur les hôtes au sein d’un groupe hôte. Pour plus d’informations, consultez [Sélection élective manuelle ou automatique](dedicated-hosts.md#manual-vs-automatic-placement).


**Autres exemples**

Vous pouvez également utiliser [az vm host group create](/cli/azure/vm/host/group#az_vm_host_group_create) pour créer un groupe hôte dans la zone de disponibilité 1 (sans domaine d’erreur).

```azurecli-interactive
az vm host group create \
   --name myAZHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 1
```

L’exemple suivant exploite la commande [az vm host group create](/cli/azure/vm/host/group#az_vm_host_group_create) pour créer un groupe hôte à l’aide de domaines d’erreur uniquement (à utiliser dans les régions dans lesquels les zones de disponibilité ne sont pas prises en charge).

```azurecli-interactive
az vm host group create \
   --name myFDHostGroup \
   -g myDHResourceGroup \
   --platform-fault-domain-count 2
```

### <a name="powershell"></a>[PowerShell](#tab/powershell)

Cet exemple utilise [New-AzHostGroup](/powershell/module/az.compute/new-azhostgroup) pour créer un groupe hôte dans la zone 1, avec 2 domaines d’erreur.


```azurepowershell-interactive
$rgName = "myDHResourceGroup"
$location = "EastUS"

New-AzResourceGroup -Location $location -Name $rgName
$hostGroup = New-AzHostGroup `
   -Location $location `
   -Name myHostGroup `
   -PlatformFaultDomain 2 `
   -ResourceGroupName $rgName `
   -Zone 1
```


Ajoutez le paramètre `-SupportAutomaticPlacement true` pour que vos machines virtuelles et vos instances de groupes identiques soient placées automatiquement sur les hôtes au sein d’un groupe hôte. Pour plus d’informations, consultez [Sélection élective manuelle ou automatique](dedicated-hosts.md#manual-vs-automatic-placement).

---


## <a name="create-a-dedicated-host"></a>Créer un hôte dédié

Créez maintenant un hôte dédié dans le groupe hôte. En plus d’un nom pour l’hôte, vous devez fournir la référence SKU pour l’hôte. La référence SKU de l’hôte capture la série de machines virtuelles prises en charge, ainsi que la génération du matériel pour l’hôte dédié.

Pour en savoir plus sur les références SKU et la tarification des hôtes, consultez la section relative à la [tarification des hôtes dédiés Azure](https://aka.ms/ADHPricing).

Si vous définissez un nombre de domaines d’erreur pour votre groupe hôte, vous devez spécifier le domaine d’erreur de votre hôte.

### <a name="portal"></a>[Portail](#tab/portal)

1. Sélectionnez l’option **Créer une ressource**, en haut à gauche du portail.
1. Recherchez un **hôte dédié** et sélectionnez **Dedicated Hosts** (Hôtes dédiés) dans les résultats.
1. Dans la page **Dedicated Hosts** (Hôtes dédiés), sélectionnez **Créer**.
1. Sélectionnez l’abonnement à utiliser.
1. Sélectionnez *myDedicatedHostsRG* parmi les **groupes de ressources**.
1. Dans **Détails de l’instance**, saisissez *myHost* dans la zone **Nom** et sélectionnez l’emplacement *USA Est*.
1. Dans **Hardware Profile** (Profil matériel), sélectionnez *Standard Es3 family – Type 1* (Famille ES3 standard – Type 1) pour **Size Family** (Famille de tailles), *myHostGroup* pour **Host Group** (Groupe hôte) et *1* pour **Fault Domain** (Domaine d’erreur). Conservez les valeurs par défaut pour les autres champs.
1. Cela fait, sélectionnez **Vérifier + Créer** et attendez la validation.
1. Lorsque le message **Validation passed** (Validation réussie) apparaît, sélectionnez **Créer** pour créer l’hôte.

### <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/cli)

Utilisez la commande [az vm host create](/cli/azure/vm/host#az_vm_host_create) pour créer un hôte. Si vous définissez un nombre de domaines d’erreur pour votre groupe hôte, vous êtes invité à spécifier le domaine d’erreur de votre hôte.

```azurecli-interactive
az vm host create \
   --host-group myHostGroup \
   --name myHost \
   --sku DSv3-Type1 \
   --platform-fault-domain 1 \
   -g myDHResourceGroup
```

### <a name="powershell"></a>[PowerShell](#tab/powershell)

Dans cet exemple, nous utilisons [New-AzHost](/powershell/module/az.compute/new-azhost) pour créer un hôte et définir le domaine d’erreur sur 1.


```azurepowershell-interactive
$dHost = New-AzHost `
   -HostGroupName $hostGroup.Name `
   -Location $location -Name myHost `
   -ResourceGroupName $rgName `
   -Sku DSv3-Type1 `
   -AutoReplaceOnFailure 1 `
   -PlatformFaultDomain 1
```

---

## <a name="create-a-vm"></a>Créer une machine virtuelle

Créez maintenant une machine virtuelle sur l’hôte.

### <a name="portal"></a>[Portail](#tab/portal)

1. Choisissez **Créer une ressource** en haut à gauche du Portail Azure.
1. Dans la zone de recherche située au-dessus de la liste des ressources de la Place de marché Azure, recherchez et sélectionnez l'image que vous souhaitez utiliser, puis choisissez **Créer**.
1. Sous l’onglet **De base**, sous **Détails du projet**, vérifiez que l’abonnement approprié est sélectionné, puis sélectionnez *myDedicatedHostsRG* comme **groupe de ressources**.
1. Sous **Détails de l’instance**, tapez *myVM* comme **Nom de la machine virtuelle**, puis choisissez *USA Est* comme **Emplacement**.
1. Dans **Options de disponibilité**, sélectionnez **Zone de disponibilité**, puis sélectionnez *1* dans la liste déroulante.
1. Pour la taille, sélectionnez **Modifier la taille**. Dans la liste des tailles disponibles, choisissez-en une dans la série Esv3, par exemple **Standard E2s v3**. Vous devrez peut-être effacer le filtre afin d’afficher toutes les tailles disponibles.
1. Renseignez les autres champs de l'onglet **De base** selon vos besoins.
1. Si vous souhaitez indiquer quel hôte utiliser pour votre machine virtuelle, sélectionnez l’onglet **Avancé** en haut de la page. Ensuite, sélectionnez *myHostGroup* comme **Groupe hôte** et *myHost* comme **Hôte** dans la section **Hôte**. Sinon, votre machine virtuelle sera automatiquement placée sur un hôte possédant de la capacité.
    ![Sélectionner le groupe hôte et l’hôte](./media/dedicated-hosts-portal/advanced.png)
1. Conservez les valeurs par défaut restantes, puis sélectionnez le bouton **Vérifier + créer** en bas de la page.
1. Lorsque vous voyez le message indiquant que la validation a réussi, sélectionnez **Créer**.

Quelques minutes sont nécessaires pour le déploiement de votre machine virtuelle.


### <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/cli)

Créez une machine virtuelle dans un hôte dédié à l’aide de la commande [az vm create](/cli/azure/vm#az_vm_create). Si vous avez spécifié une zone de disponibilité lors de la création de votre groupe hôte, vous devez utiliser la même zone quand vous créez la machine virtuelle. Remplacez les valeurs comme celles de l’image et du nom d’hôte par les vôtres. Si vous créez une machine virtuelle Windows, supprimez `--generate-ssh-keys` pour être invité à entrer un mot de passe.

```azurecli-interactive
az vm create \
   -n myVM \
   --image myImage \
   --host-group myHostGroup \
   --admin-username azureuser \
   --generate-ssh-keys \
   --size Standard_D4s_v3 \
   -g myDHResourceGroup \
   --zone 1
```

Pour placer la machine virtuelle sur un hôte spécifique, utilisez `--host` au lieu de spécifier le groupe hôte avec `--host-group`.

> [!WARNING]
> Toute machine virtuelle créée sur un hôte qui ne dispose pas d’un nombre de ressources suffisant présente un état ÉCHEC.


### <a name="powershell"></a>[PowerShell](#tab/powershell)

Créez une nouvelle machine virtuelle sur votre hôte à l’aide de la commande [New-AzVM](/powershell/module/az.compute/new-azvm). Pour cet exemple, étant donné que notre groupe hôte se trouve dans la zone 1, nous devons créer la machine virtuelle dans la zone 1.


```azurepowershell-interactive
New-AzVM `
   -Credential $cred `
   -ResourceGroupName $rgName `
   -Location $location `
   -Name myVM `
   -HostId $dhost.Id `
   -Image myImage `
   -Zone 1 `
   -Size Standard_D4s_v3
```

> [!WARNING]
> Toute machine virtuelle créée sur un hôte qui ne dispose pas d’un nombre de ressources suffisant présente un état ÉCHEC.

---

## <a name="create-a-scale-set"></a>Créer un groupe identique

Vous pouvez également créer un groupe identique sur votre hôte.

### <a name="portal"></a>[Portail](#tab/portal)

Lorsque vous déployez un groupe identique, vous spécifiez le groupe hôte.

1. Recherchez *Groupe identique* et sélectionnez **Groupes de machines virtuelles identiques** dans la liste.
1. Sélectionnez **Ajouter** pour créer un groupe identique.
1. Renseignez les champs de l'onglet **De base** comme vous le feriez habituellement, mais veillez à sélectionner une taille de machine virtuelle appartenant à la série que vous avez choisie pour votre hôte dédié, par exemple **Standard E2s v3**.
1. Sous l'onglet **Avancé**, pour **Algorithme de diffusion** sélectionnez **Diffusion maximale**.
1. Sous **Groupe hôte**, sélectionnez le groupe hôte dans la liste déroulante. Si vous venez de créer le groupe, l'ajout à la liste peut prendre une minute.


### <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/cli)

Lorsque vous déployez un groupe identique à l’aide de la commande [az vmss create](/cli/azure/vmss#az_vmss_create), vous spécifiez le groupe hôte avec `--host-group`. Dans cet exemple, nous déployons l’image Ubuntu LTS la plus récente. Pour déployer une image Windows, remplacez la valeur de `--image` et supprimez `--generate-ssh-keys` pour être invité à entrer un mot de passe.

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --host-group myHostGroup \
  --generate-ssh-keys \
  --size Standard_D4s_v3 \
  -g myDHResourceGroup \
  --zone 1
```

Si vous souhaitez choisir manuellement l’hôte sur lequel déployer le groupe identique, ajoutez `--host` et le nom de l’hôte.


### <a name="powershell"></a>[PowerShell](#tab/powershell)

Déployez un groupe identique sur l’hôte à l’aide de la commande [New-AzVMSS](/powershell/module/az.compute/new-azvmss). Lorsque vous déployez un groupe identique, vous spécifiez le groupe hôte.

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -VMScaleSetName "myDHScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic"`
  -HostGroupId $hostGroup.Id
```

Si vous souhaitez choisir manuellement l’hôte sur lequel déployer le groupe identique, ajoutez `--host` et le nom de l’hôte.

---

## <a name="add-an-existing-vm"></a>Ajouter une machine virtuelle existante

Vous pouvez ajouter une machine virtuelle existante à un hôte dédié, mais la machine virtuelle doit d’abord être arrêtée\libérée. Avant de déplacer une machine virtuelle vers un hôte dédié, vérifiez que la configuration de la machine virtuelle est prise en charge :

- La taille de la machine virtuelle doit appartenir à la même famille de tailles que l’hôte dédié. Par exemple, si votre hôte dédié est DSv3, la taille de la machine virtuelle peut être Standard_D4s_v3, mais pas Standard_A4_v2.
- La machine virtuelle doit être située dans la même région que l’hôte dédié.
- La machine virtuelle ne peut pas faire partie d’un groupe de placements de proximité. Supprimez la machine virtuelle du groupe de placements de proximité avant de la déplacer vers un hôte dédié. Pour plus d’informations, consultez [Déplacer une machine virtuelle hors d’un groupe de placements de proximité](./windows/proximity-placement-groups.md#move-an-existing-vm-out-of-a-proximity-placement-group).
- La machine virtuelle ne peut pas se trouver dans un groupe à haute disponibilité.
- Si la machine virtuelle se trouve dans une zone de disponibilité, il doit s’agir de la même zone de disponibilité que celle du groupe hôte. Les paramètres de zone de disponibilité de la machine virtuelle et du groupe hôte doivent correspondre.

### <a name="portal"></a>[Portail](#tab/portal2)

Déplacez la machine virtuelle vers un hôte dédié à l’aide du [portail](https://portal.azure.com).

1. Ouvrez la page de la machine virtuelle.
1. Sélectionnez **Arrêter** pour arrêter\libérer la machine virtuelle.
1. Sélectionnez **Configuration** dans le menu de gauche.
1. Sélectionnez un groupe hôte et un hôte dans les menus déroulants.
1. Lorsque vous avez terminé, sélectionnez **Enregistrer** en haut de la page.
1. Une fois la machine virtuelle ajoutée à l’hôte, sélectionnez **Vue d’ensemble** dans le menu de gauche.
1. En haut de la page, sélectionnez **Démarrer** pour redémarrer la machine virtuelle.


### <a name="powershell"></a>[PowerShell](#tab/powershell2)

Remplacez les valeurs des variables par vos propres informations.

```azurepowershell-interactive
$vmRGName = "movetohost"
$vmName = "myVMtoHost"
$dhRGName = "myDHResourceGroup"
$dhGroupName = "myHostGroup"
$dhName = "myHost"

$myDH = Get-AzHost `
   -HostGroupName $dhGroupName `
   -ResourceGroupName $dhRGName `
   -Name $dhName

$myVM = Get-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName

$myVM.Host = New-Object Microsoft.Azure.Management.Compute.Models.SubResource

$myVM.Host.Id = "$myDH.Id"

Stop-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName -Force

Update-AzVM `
   -ResourceGroupName $vmRGName `
   -VM $myVM -Debug

Start-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName
```

---


## <a name="check-the-status-of-the-host"></a>Vérifier l’état de l’hôte

Si vous avez besoin de savoir combien de capacité est encore disponible sur un hôte, vous pouvez vérifier l’état.

### <a name="portal"></a>[Portail](#tab/portal)

1. Recherchez et sélectionnez l’hôte.
1. Dans la page **Vue d’ensemble** de l’hôte, faites défiler vers le bas pour voir la liste des tailles encore disponibles pour l’hôte. Elle doit ressembler à ceci :

:::image type="content" source="media/dedicated-hosts-portal/host-status.png" alt-text="Vérifier la capacité disponible de l’hôte à partir de la page Vue d’ensemble de l’hôte.":::

### <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/cli)

Vous pouvez vérifier l’état d’intégrité de l’hôte et le nombre de machines virtuelles que vous pouvez encore déployer sur l’hôte à l’aide de la commande [az vm host get-instance-view](/cli/azure/vm/host#az_vm_host_get_instance_view).

```azurecli-interactive
az vm host get-instance-view \
   -g myDHResourceGroup \
   --host-group myHostGroup \
   --name myHost
```

Le résultat ressemble à ce qui suit :

```json
{
  "autoReplaceOnFailure": true,
  "hostId": "6de80643-0f45-4e94-9a4c-c49d5c777b62",
  "id": "/subscriptions/10101010-1010-1010-1010-101010101010/resourceGroups/myDHResourceGroup/providers/Microsoft.Compute/hostGroups/myHostGroup/hosts/myHost",
  "instanceView": {
    "assetId": "12345678-1234-1234-abcd-abc123456789",
    "availableCapacity": {
      "allocatableVms": [
        {
          "count": 31.0,
          "vmSize": "Standard_D2s_v3"
        },
        {
          "count": 15.0,
          "vmSize": "Standard_D4s_v3"
        },
        {
          "count": 7.0,
          "vmSize": "Standard_D8s_v3"
        },
        {
          "count": 3.0,
          "vmSize": "Standard_D16s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32-8s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32-16s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D48s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64-16s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64-32s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64s_v3"
        }
      ]
    },
    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "displayStatus": "Provisioning succeeded",
        "level": "Info",
        "message": null,
        "time": "2019-07-24T21:22:40.604754+00:00"
      },
      {
        "code": "HealthState/available",
        "displayStatus": "Host available",
        "level": "Info",
        "message": null,
        "time": null
      }
    ]
  },
  "licenseType": null,
  "location": "eastus2",
  "name": "myHost",
  "platformFaultDomain": 1,
  "provisioningState": "Succeeded",
  "provisioningTime": "2019-07-24T21:22:40.604754+00:00",
  "resourceGroup": "myDHResourceGroup",
  "sku": {
    "capacity": null,
    "name": "DSv3-Type1",
    "tier": null
  },
  "tags": null,
  "type": null,
  "virtualMachines": [
    {
      "id": "/subscriptions/10101010-1010-1010-1010-101010101010/resourceGroups/MYDHRESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/MYVM",
      "resourceGroup": "MYDHRESOURCEGROUP"
    }
  ]
}

```

### <a name="powershell"></a>[PowerShell](#tab/powershell)


Vous pouvez vérifier l’état d’intégrité de l’hôte et le nombre de machines virtuelles que vous pouvez encore déployer sur l’hôte à l’aide de la commande [Get-AzHost](/powershell/module/az.compute/get-azhost), avec le paramètre `-InstanceView`.

```azurepowershell-interactive
Get-AzHost `
   -ResourceGroupName $rgName `
   -Name myHost `
   -HostGroupName $hostGroup.Name `
   -InstanceView
```

Le résultat ressemble à ce qui suit :

```
ResourceGroupName      : myDHResourceGroup
PlatformFaultDomain    : 1
AutoReplaceOnFailure   : True
HostId                 : 12345678-1234-1234-abcd-abc123456789
ProvisioningTime       : 7/28/2019 5:31:01 PM
ProvisioningState      : Succeeded
InstanceView           :
  AssetId              : abc45678-abcd-1234-abcd-123456789abc
  AvailableCapacity    :
    AllocatableVMs[0]  :
      VmSize           : Standard_D2s_v3
      Count            : 32
    AllocatableVMs[1]  :
      VmSize           : Standard_D4s_v3
      Count            : 16
    AllocatableVMs[2]  :
      VmSize           : Standard_D8s_v3
      Count            : 8
    AllocatableVMs[3]  :
      VmSize           : Standard_D16s_v3
      Count            : 4
    AllocatableVMs[4]  :
      VmSize           : Standard_D32-8s_v3
      Count            : 2
    AllocatableVMs[5]  :
      VmSize           : Standard_D32-16s_v3
      Count            : 2
    AllocatableVMs[6]  :
      VmSize           : Standard_D32s_v3
      Count            : 2
    AllocatableVMs[7]  :
      VmSize           : Standard_D64-16s_v3
      Count            : 1
    AllocatableVMs[8]  :
      VmSize           : Standard_D64-32s_v3
      Count            : 1
    AllocatableVMs[9]  :
      VmSize           : Standard_D64s_v3
      Count            : 1
  Statuses[0]          :
    Code               : ProvisioningState/succeeded
    Level              : Info
    DisplayStatus      : Provisioning succeeded
    Time               : 7/28/2019 5:31:01 PM
  Statuses[1]          :
    Code               : HealthState/available
    Level              : Info
    DisplayStatus      : Host available
Sku                    :
  Name                 : DSv3-Type1
Id                     : /subscriptions/10101010-1010-1010-1010-101010101010/re
sourceGroups/myDHResourceGroup/providers/Microsoft.Compute/hostGroups/myHostGroup/hosts
/myHost
Name                   : myHost
Location               : eastus
Tags                   : {}
```

---

## <a name="deleting-hosts"></a>Suppression d’hôtes 
Vous êtes facturé pour vos hôtes dédiés, même si aucune machine virtuelle n’est déployée. Supprimez tous les hôtes non utilisés pour réduire les coûts.

Vous pouvez supprimer un hôte uniquement lorsqu’il n’est plus utilisé par aucune machine virtuelle.

### <a name="portal"></a>[Portail](#tab/portal)

1. Recherchez et sélectionnez l’hôte.
1. Dans le menu de gauche, sélectionnez **Instances**.
1. Sélectionnez et supprimez chaque machine virtuelle.
1. Une fois toutes les machines virtuelles supprimées, revenez à la page **Vue d’ensemble** de l’hôte et sélectionnez **Supprimer** dans le menu supérieur.
1. Une fois que l’hôte a été supprimé, ouvrez la page du groupe hôte, puis sélectionnez **Supprimer le groupe hôte**.

### <a name="cli"></a>[INTERFACE DE LIGNE DE COMMANDE](#tab/cli)

 Supprimez les machines virtuelles avec la commande [az vm delete](/cli/azure/vm#az_vm_delete).

```azurecli-interactive
az vm delete -n myVM -g myDHResourceGroup
```

Cela fait, vous pouvez supprimer l’hôte, avec la commande [az vm host delete](/cli/azure/vm/host#az_vm_host_delete).

```azurecli-interactive
az vm host delete -g myDHResourceGroup --host-group myHostGroup --name myHost
```

Une fois que vous avez supprimé tous vos hôtes, utilisez la commande [az vm host group delete](/cli/azure/vm/host/group#az_vm_host_group_delete) pour supprimer le groupe hôte.

```azurecli-interactive
az vm host group delete -g myDHResourceGroup --host-group myHostGroup
```

Vous pouvez également supprimer l’intégralité du groupe de ressources via une seule commande. Cette opération supprime toutes les ressources créées dans le groupe, y compris l’ensemble des machines virtuelles, des hôtes et des groupes hôtes.

```azurecli-interactive
az group delete -n myDHResourceGroup
```


### <a name="powershell"></a>[PowerShell](#tab/powershell)

Supprimez les machines virtuelles avec la commande [Remove-AzVM](/powershell/module/az.compute/remove-azvm).

```azurepowershell-interactive
Remove-AzVM -ResourceGroupName $rgName -Name myVM
```

Cela fait, vous pouvez supprimer l’hôte avec la commande [Remove-AzHost](/powershell/module/az.compute/remove-azhost).

```azurepowershell-interactive
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

Une fois que vous avez supprimé tous vos hôtes, utilisez la commande [Remove-AzHostGroup](/powershell/module/az.compute/remove-azhostgroup) pour supprimer le groupe hôte.

```azurepowershell-interactive
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

Vous pouvez également supprimer l’intégralité du groupe de ressources via une seule commande [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup). Cette opération supprime toutes les ressources créées dans le groupe, y compris l’ensemble des machines virtuelles, des hôtes et des groupes hôtes.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $rgName
```

---

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus, voir la page de présentation [Hôtes dédiés](dedicated-hosts.md).

- Un exemple de modèle, disponible dans [Modèles de démarrage rapide Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.compute/vm-dedicated-hosts/README.md), utilise les zones et les domaines d’erreur pour offrir une résilience maximale dans une région.

