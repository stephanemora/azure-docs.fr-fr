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
ms.openlocfilehash: 09f0d1f3f39a43ece445863023fd2ff7772404b9
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122699192"
---
# <a name="preview-create-virtual-machines-in-a-flexible-scale-set-using-azure-cli"></a>Préversion : Créer des machines virtuelles dans un groupe identique Flexible à l’aide de l’interface CLI Azure

**S’applique à :** :heavy_check_mark: Groupes identiques flexibles


Cet article décrit l’utilisation de l’interface CLI Azure pour créer un groupe identique de machines virtuelles en mode d’orchestration Flexible. Pour plus d’informations sur les groupes identiques Flexibles, consultez le [mode d’orchestration Flexible pour les groupes identiques de machines virtuelles](flexible-virtual-machine-scale-sets.md). 

Assurez-vous que vous avez installé la dernière version d’[Azure CLI](/cli/azure/install-az-cli2) et que vous êtes connecté à un compte Azure avec [az login](/cli/azure/reference-index).


> [!IMPORTANT]
> Les groupes de machines virtuelles identiques en mode d’orchestration Flexible sont actuellement en préversion publique. Une procédure de consentement est requise pour utiliser la fonctionnalité en préversion publique décrite ci-dessous.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


> [!CAUTION]
> Le mode d’orchestration est défini lorsque vous créez le groupe identique et ne peut pas être modifié ou mis à jour ultérieurement.


## <a name="register-for-flexible-orchestration-mode"></a>S’inscrire au mode d’orchestration Flexible

Avant de pouvoir déployer des groupes de machines virtuelles identiques en mode d’orchestration Flexible, vous devez inscrire la fonctionnalité d’évaluation pour votre abonnement. L’inscription peut prendre plusieurs minutes.

Utilisez [az feature register](/cli/azure/feature#az_feature_register) pour activer la préversion pour votre abonnement.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name VMOrchestratorMultiFD
az feature register --namespace microsoft.compute --name VMOrchestratorSingleFD
az feature register --namespace Microsoft.Compute --name VMScaleSetFlexPreview 
az feature register --namespace Microsoft.Compute --name SkipPublicIpWriteRBACCheckForVMNetworkInterfaceConfigurationsPublicPreview
```

L’inscription de la fonctionnalité peut prendre jusqu’à 15 minutes. Pour vérifier l’état de l’inscription :

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name VMOrchestratorMultiFD
```


## <a name="get-started-with-flexible-scale-sets"></a>Prise en main des groupes identiques Flexibles

Créez un groupe de machines virtuelles identiques Flexible à l’aide d’Azure CLI.

### <a name="add-multiple-vms-to-a-scale-set"></a>Ajouter plusieurs machines virtuelles à un groupe identique

Dans l’exemple suivant, nous spécifions un profil de machine virtuelle (type de machine virtuelle, configuration de mise en réseau, type de stockage, etc.) et le nombre d’instances à créer (nombre d’instances = 2).  

```azurecli-interactive
az vmss create
--resource-group $rg
--name $vmssName
--single-placement-group false
--orchestration-mode flexible
--platform-fault-domain-count 1
--image UbuntuLTS
--admin-username azureuser
--instance-count 2
--vm-sku 'Standard_D2s_v3'
--network-api-version '2020-11-01'
--computer-name-prefix $computerNamePrefix
--vnet-name $vnetName
--subnet $subnetName
--public-ip-per-vm
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