---
title: Créer des machines virtuelles dans un groupe identique Flexible à l’aide de l’interface CLI Azure
description: Découvrez comment créer un groupe de machines virtuelles identiques en mode d’orchestration Flexible à l’aide de l’interface CLI Azure.
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: flexible-scale-sets
ms.date: 08/05/2021
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli, vmss-flex
ms.openlocfilehash: a0c6d8dc1f9e032ca76393c52be76d1803873f8b
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130166406"
---
# <a name="create-virtual-machines-in-a-flexible-scale-set-using-azure-cli"></a>Créer des machines virtuelles dans un groupe identique Flexible à l’aide de l’interface CLI Azure

**S’applique à :** :heavy_check_mark: Groupes identiques flexibles


Cet article décrit l’utilisation de l’interface CLI Azure pour créer un groupe identique de machines virtuelles en mode d’orchestration Flexible. Pour plus d’informations sur les groupes identiques Flexibles, consultez le [mode d’orchestration Flexible pour les groupes identiques de machines virtuelles](flexible-virtual-machine-scale-sets.md). 

Assurez-vous que vous avez installé la dernière version d’[Azure CLI](/cli/azure/install-az-cli2) et que vous êtes connecté à un compte Azure avec [az login](/cli/azure/reference-index).


> [!CAUTION]
> Le mode d’orchestration est défini lorsque vous créez le groupe identique et ne peut pas être modifié ou mis à jour ultérieurement.


## <a name="get-started-with-flexible-scale-sets"></a>Prise en main des groupes identiques Flexibles

Créez un groupe de machines virtuelles identiques Flexible à l’aide d’Azure CLI.

### <a name="add-multiple-vms-to-a-scale-set"></a>Ajouter plusieurs machines virtuelles à un groupe identique

Dans l’exemple suivant, nous spécifions un profil de machine virtuelle (type de machine virtuelle, configuration de mise en réseau, type de stockage, etc.) et le nombre d’instances à créer (nombre d’instances = 2).  

```azurecli-interactive
az vmss create
--name $vmssName
--resource-group $rg
--image UbuntuLTS
--instance-count 2
--orchestration-mode flexible
```

### <a name="add-a-single-vm-to-a-scale-set"></a>Ajouter une machine virtuelle unique à un groupe identique

L’exemple suivant illustre la création d’un groupe identique Flexible sans profil de machine virtuelle, où le nombre de domaines par défaut est défini sur 1. Une machine virtuelle est créée, puis ajoutée au groupe identique Flexible.

```azurecli-interactive
vmoname="my-vmss-vmo"
vmname="myVM"
rg="my-resource-group"

az group create -n "$rg" -l $location
az vmss create -n "$vmoname" -g "$rg" -l $location --orchestration-mode vm --platform-fault-domain-count 1
az vm create -n "$vmname" -g "$rg" -l $location --vmss $vmoname --image UbuntuLTS
``` 


## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Découvrez comment créer un groupe identique Flexible dans le portail Azure.](flexible-virtual-machine-scale-sets-portal.md)