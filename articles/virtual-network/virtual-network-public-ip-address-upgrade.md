---
title: Mettre à niveau des adresses IP publiques
titleSuffix: Azure Virtual Network
description: Mettre à niveau des adresses IP publiques du niveau De base à Standard.
services: virtual-network
documentationcenter: na
author: blehr
editor: ''
ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/08/2020
ms.author: blehr
ms.custom: references_regions , devx-track-azurepowershell
ms.openlocfilehash: ebc5e15038574983c7b21a5a47a36059c041519a
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110678644"
---
# <a name="upgrade-public-ip-addresses"></a>Mettre à niveau des adresses IP publiques

Les IP publiques Azure sont créées avec un SKU (De base ou Standard) qui détermine les aspects de leurs fonctionnalités (notamment la méthode d’allocation, la prise en charge des fonctionnalités et les ressources auxquelles elles peuvent être associées). 

Les scénarios abordés dans cet article sont les suivants :
* Mise à niveau d’une IP publique de SKU De base vers une IP publique de SKU Standard (à l’aide de PowerShell ou d’Azure CLI)
* Migration d’une adresse IP réservée classique Azure vers une adresse IP publique de référence SKU De base Azure Resource Manager

## <a name="upgrade-public-ip-address-from-basic-to-standard-sku"></a>Mettre à niveau une adresse IP publique d’une référence SKU De base à Standard

Pour pouvoir mettre à niveau une adresse IP publique, celle-ci ne doit être associée à aucune ressource (consultez [cette page](./virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address) pour en savoir plus sur la façon de dissocier les adresses IP publiques).

>[!IMPORTANT]
>Les adresses IP publiques dont la référence SKU a été mise à niveau de De base vers Standard n’ont toujours aucune [zone de disponibilité](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) garantie.  Gardez cela à l’esprit lorsque vous choisissez les ressources auxquelles associer l’adresse IP.

---
# <a name="basic-to-standard---powershell"></a>[**De base à Standard - PowerShell**](#tab/option-upgrade-powershell)

L’exemple suivant suppose qu’une adresse IP publique de référence SKU De base a été créée auparavant, à l’aide de l’exemple fourni sur [cette page](./create-public-ip-powershell.md?tabs=option-create-public-ip-basic) avec une adresse IP publique De base **myBasicPublicIP** dans **myResourceGroup**.

Pour procéder à la mise à niveau de l’adresse IP, exécutez simplement les commandes ci-dessous à l’aide de PowerShell.  Notez que si l’adresse IP est déjà allouée de manière statique, vous pouvez passer cette section.

```azurepowershell-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$name = 'myBasicPublicIP'
$newsku = 'Standard'
$pubIP = Get-AzPublicIpAddress -name $name -ResourceGroupName $rg

## This section is only needed if the Basic IP is not already set to Static ##
$pubIP.PublicIpAllocationMethod = 'Static'
Set-AzPublicIpAddress -PublicIpAddress $pubIP

## This section is for conversion to Standard ##
$pubIP.Sku.Name = $newsku
Set-AzPublicIpAddress -PublicIpAddress $pubIP
```

# <a name="basic-to-standard---cli"></a>[**De base à Standard - CLI**](#tab/option-upgrade-cli)

L’exemple suivant suppose qu’une adresse IP publique de référence SKU De base a été créée auparavant, à l’aide de l’exemple fourni sur [cette page](./create-public-ip-cli.md?tabs=option-create-public-ip-basic) avec une adresse IP publique De base **myBasicPublicIP** dans **myResourceGroup**.

Pour procéder à la mise à niveau de l’adresse IP, exécutez simplement les commandes ci-dessous à l’aide d’Azure CLI.  Notez que si l’adresse IP est déjà allouée de manière statique, vous pouvez passer cette section.

```azurecli-interactive
## Variables for the command ##
$rg = 'myResourceGroup'
$name = 'myBasicPublicIP'

## This section is only needed if the Basic IP is not already set to Static ##
az network public-ip update \
--resource-group $rg \
--name $name \
--allocation-method Static 

## This section is for conversion to Standard ##
az network public-ip update \
--resource-group $rg \
--name $name \
--sku Standard
```
---

## <a name="upgrade-migrate-a-classic-reserved-ip-to-a-static-public-ip"></a>Mettre à niveau (migrer) une adresse IP réservée classique vers une adresse IP publique statique

Pour tirer parti des nouvelles fonctionnalités d’Azure Resource Manager, vous pouvez migrer les adresses IP statiques publiques existantes (appelées adresses IP réservées) du modèle classique vers le modèle Azure Resource Manager moderne.  L’adresse IP publique migrée aura le type de référence SKU De base.


---

# <a name="reserved-to-basic---powershell"></a>[**Réservé à De base - PowerShell**](#tab/option-migrate-powershell)

L’exemple suivant suppose qu’une adresse IP réservée Azure classique **myReservedIP** a été créée auparavant dans **myResourceGroup**. Autre prérequis pour la migration, l’abonnement Azure Resource Manager doit être inscrit pour la migration. Ce sujet est traité en détail dans les étapes 3 et 4 de [cette page](../virtual-machines/migration-classic-resource-manager-ps.md).

Pour procéder à la migration de l’adresse IP réservée, exécutez les commandes ci-dessous à l’aide de PowerShell.  Notez que si l’adresse IP n’est associée à aucun service (dans l’exemple ci-dessous il y en a un, appelé **myService**), vous pouvez passer cette étape.

```azurepowershell-interactive
## Variables for the command ##
$name = 'myReservedIP'

## This section is only needed if the Reserved IP is not already disassociated from any Cloud Services ##
$serviceName = 'myService'
Remove-AzureReservedIPAssociation -ReservedIPName $name -ServiceName $service

$validate = Move-AzureReservedIP -ReservedIPName $name -Validate
$validate.ValidationMessages
```
La commande précédente affiche les avertissements et erreurs qui bloquent la migration. Si la validation se déroule correctement, vous pouvez exécuter les étapes suivantes pour préparer et valider la migration :
```azurepowershell-interactive
Move-AzureReservedIP -ReservedIPName $name -Prepare
Move-AzureReservedIP -ReservedIPName $name -Commit
```
Un nouveau groupe de ressources dans Azure Resource Manager est créé à l’aide du nom de l’adresse IP réservée qui a été migrée (dans l’exemple ci-dessus, il s’agit du groupe de ressources **myReservedIP-Migrated**).

# <a name="reserved-to-basic---cli"></a>[**Réservé à De base - CLI**](#tab/option-migrate-cli)

L’exemple suivant suppose qu’une adresse IP réservée Azure classique **myReservedIP** a été créée auparavant dans **myResourceGroup**. Autre prérequis pour la migration, l’abonnement Azure Resource Manager doit être inscrit pour la migration. Ce sujet est traité en détail dans les étapes 3 et 4 de [cette page](../virtual-machines/migration-classic-resource-manager-cli.md).

Pour procéder à la migration de l’adresse IP réservée, exécutez les commandes ci-dessous à l’aide d’Azure CLI.  Notez que si l’adresse IP n’est associée à aucun service (dans l’exemple ci-dessous il y a un service, appelé **myService**, et un déploiement, **myDeployment**), vous pouvez passer cette étape.

```azurecli-interactive
## Variables for the command ##
$name = 'myReservedIP'

## This section is only needed if the Reserved IP is not already disassociated from any Cloud Services ##
$service = 'myService'
$deployment = 'myDeployment'
azure network reserved-ip disassociate $name $service $deployment

azure network reserved-ip validate-migration $name
```
La commande précédente affiche les avertissements et erreurs qui bloquent la migration. Si la validation se déroule correctement, vous pouvez exécuter les étapes suivantes pour préparer et valider la migration :
```azurecli-interactive
azure network reserved-ip prepare-migration $name
azure network reserved-ip commit-migration $name
```
Un nouveau groupe de ressources dans Azure Resource Manager est créé à l’aide du nom de l’adresse IP réservée qui a été migrée (dans l’exemple ci-dessus, il s’agit du groupe de ressources **myReservedIP-Migrated**).

---

## <a name="limitations"></a>Limites

* Pour pouvoir mettre à niveau une adresse IP publique De base, celle-ci ne doit être associée à aucune ressource Azure.  Consultez [cette page](./virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address) pour en savoir plus sur la façon de dissocier des adresses IP publiques.  De même, pour pouvoir migrer une adresse IP réservée, celle-ci ne doit être associée à aucun service cloud.  Consultez [cette page](./remove-public-ip-address-vm.md) pour en savoir plus sur la façon de dissocier des adresses IP réservées.  
* Les adresses IP publiques dont la référence SKU a été mise à niveau de De base vers Standard n’ont toujours aucune [zone de disponibilité](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) garantie.  Gardez cela à l’esprit lorsque vous choisissez les ressources auxquelles associer l’adresse IP.
* Vous ne pouvez pas passer du niveau Standard au niveau De base.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les [adresses IP publiques](./public-ip-addresses.md#public-ip-addresses) dans Azure, notamment sur la différence entre les types de référence SKU, ainsi que sur les [paramètres d’adresse IP publique](virtual-network-public-ip-address.md#create-a-public-ip-address).
- Découvrez comment [mettre à niveau Azure Public Load Balancer du niveau De base à Standard](../load-balancer/upgrade-basic-standard.md).
- Découvrez les [adresses IP réservées Azure classiques](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) et la [migration de ressources classiques vers Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-overview.md).
