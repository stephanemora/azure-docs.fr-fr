---
title: Comment baliser une machine virtuelle Azure Linux
description: Découvrez comment baliser une machine virtuelle Azure Linux créée dans Azure à l’aide du modèle de déploiement de Resource Manager.
services: virtual-machines-linux
documentationcenter: ''
author: mmccrory
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ca0e17e5-d78e-42e6-9dad-c1e8f1c58027
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/28/2017
ms.author: memccror
ms.openlocfilehash: 3dd46f0c1c1e36e0373311ce6dcbf5d2f155dacc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616277"
---
# <a name="how-to-tag-a-linux-virtual-machine-in-azure"></a>Comment baliser une machine virtuelle Linux dans Azure
Cet article décrit différentes façons d’ajouter des balises à une machine virtuelle Linux dans Azure à l’aide du modèle de déploiement Azure Resource Manager. Les balises sont des paires clé/valeur définies par l’utilisateur, qui peuvent être placées directement sur une ressource ou sur un groupe de ressources. Azure prend actuellement en charge jusqu’à 50 étiquettes par ressource et par groupe de ressources. Les balises peuvent être placées sur une ressource au moment de la création ou bien ajoutées à une ressource existante. Notez que les balises ne sont prises en charge que pour les ressources créées via le modèle de déploiement Azure Resource Manager.

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-azure-cli"></a>Balisage avec l’interface de ligne de commande Azure

Pour commencer, la dernière version [d’Azure CLI](/cli/azure/install-azure-cli) doit être installée et vous devez vous connecter à un compte Azure avec la commande [az login](/cli/azure/reference-index#az-login).

Vous pouvez afficher toutes les propriétés d’une machine virtuelle donnée, y compris les balises, à l’aide de cette commande :

```azurecli
az vm show --resource-group MyResourceGroup --name MyTestVM
```

Pour ajouter une nouvelle balise de machine virtuelle via l'interface de ligne de commande Azure, vous pouvez utiliser la commande `azure vm update` avec le paramètre de balise **--set** :

```azurecli
az vm update \
    --resource-group MyResourceGroup \
    --name MyTestVM \
    --set tags.myNewTagName1=myNewTagValue1 tags.myNewTagName2=myNewTagValue2
```

Pour supprimer les balises, vous pouvez utiliser le paramètre **--remove** dans la commande `azure vm update`.

```azurecli
az vm update --resource-group MyResourceGroup --name MyTestVM --remove tags.myNewTagName1
```

Maintenant que nous avons appliqué des balises à nos ressources via l’interface de ligne de commande et le portail, examinons les détails d’utilisation pour afficher les balises dans le portail de facturation.

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Étapes suivantes
* Pour en savoir plus sur le balisage de vos ressources Azure, consultez [Présentation d’Azure Resource Manager][Azure Resource Manager Overview] et [Organisation des ressources Azure à l’aide de balises][Using Tags to organize your Azure Resources].
* Pour voir en quoi les balises peuvent vous aider à gérer votre utilisation des ressources Azure, consultez [Comprendre votre facture Azure][Understanding your Azure Bill] et [Obtenir une vue d’ensemble de votre consommation des ressources Microsoft Azure][Gain insights into your Microsoft Azure resource consumption].

[Azure CLI environment]: ../../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Azure Resource Manager Overview]: ../../azure-resource-manager/management/overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/management/tag-resources.md
[Understanding your Azure Bill]:../../cost-management-billing/understand/review-individual-bill.md
[Gain insights into your Microsoft Azure resource consumption]:../../cost-management-billing/manage/usage-rate-card-overview.md
