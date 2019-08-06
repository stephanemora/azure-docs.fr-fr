---
title: Utiliser des groupes de placements de proximité en préversion pour les machines virtuelles Linux | Microsoft Docs
description: En savoir plus sur la création et l’utilisation de groupes de placements de proximité pour les machines virtuelles Linux dans Azure.
services: virtual-machines-linux
author: cynthn
manager: jeconnoc
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/01/2019
ms.author: cynthn
ms.openlocfilehash: a3009c9863d8322e3fe34bd99d64b93f0aa3d858
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385732"
---
# <a name="preview-deploy-vms-to-proximity-placement-groups-using-azure-cli"></a>Aperçu : Déployer des machines virtuelles dans des groupes de placements avec Azure CLI

Pour que les machines virtuelles soient aussi proches que possible, avec la latence la plus faible possible, déployez-les dans un [groupe de placements de proximité](co-location.md#preview-proximity-placement-groups).

Le groupe de placements de proximité est un regroupement logique utilisé pour s’assurer que les ressources de calcul Azure se trouvent proches les unes des autres. Les groupes de placements de proximité sont utiles pour les charges de travail où une latence faible est requise.

> [!IMPORTANT]
> Les groupes de placements de proximité sont actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> Les groupes de placements de proximité ne sont pas disponibles dans ces régions en préversion : **Japon Est**, **Australie Est** et **Inde Centre**.

## <a name="create-the-proximity-placement-group"></a>Créer le groupe de placements de proximité
Créez un groupe de placements de proximité à l’aide de [`az ppg create`](/cli/azure/ppg#az-ppg-create). 

```azurecli-interactive
az group create --name myPPGGroup --location westus
az ppg create \
   -n myPPG \
   -g myPPGGroup \
   -l westus \
   -t standard 
```

## <a name="list-proximity-placement-groups"></a>Liste de groupes de placements de proximité

Vous pouvez répertorier tous vos groupes de placements de proximité à l’aide de la commande [az ppg list](/cli/azure/ppg#az-ppg-list).

```azurecli-interactive
az ppg list -o table
```

## <a name="create-a-vm"></a>Créer une machine virtuelle

Créez une machine virtuelle dans le groupe placements de proximité avec la commande [new az vm](/cli/azure/vm#az-vm-create).
```azurecli-interactive
az vm create \
   -n myVM \
   -g myPPGGroup \
   --image UbuntuLTS \
   --ppg myPPG  \
   --generate-ssh-keys \
   --size Standard_D1_v2  \
   -l westus
```

Vous pouvez voir la machine virtuelle dans le groupe de placements de proximité avec [az ppg show](/cli/azure/ppg#az-ppg-show).

```azurecli-interactive
az ppg show --name myppg --resource-group myppggroup --query "virtualMachines"
```

## <a name="availability-sets"></a>Groupes à haute disponibilité
Vous pouvez également créer un groupe à haute disponibilité dans votre groupe de placements de proximité. Utilisez le même paramètre `--ppg` avec la cmdlet [az vm availability-set create](/cli/azure/vm/availability-set#az-vm-availability-set-create) pour créer un groupe à haute disponibilité et toutes les machines virtuelles dans le groupe à haute disponibilité seront également créées dans le même groupe de placements de proximité.

## <a name="scale-sets"></a>Groupes identiques

Vous pouvez également créer un groupe identique dans votre groupe de placements de proximité. Utilisez le même paramètre `--ppg` avec [az vmss create](/cli/azure/vmss?view=azure-cli-latest#az-vmss-create) pour créer un jeu de mise à l’échelle et toutes les instances seront créées dans le même groupe de placements de proximité.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les commandes [Azure CLI](/cli/azure/ppg) pour les groupes de placements de proximité.
