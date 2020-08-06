---
title: Configurer une préférence de routage pour une adresse IP publique à l’aide d’Azure CLI
titlesuffix: Azure Virtual Network
description: Découvrez comment créer une adresse IP publique avec une préférence de routage du trafic Internet
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2020
ms.author: mnayak
ms.custom: devx-track-azurecli
ms.openlocfilehash: 64284b198fc76c219ffe0dfbc57461b587b23130
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87504600"
---
# <a name="configure-routing-preference-for-a-public-ip-address-using-azure-cli"></a>Configurer une préférence de routage pour une adresse IP publique à l’aide d’Azure CLI

Cet article montre comment configurer une préférence de routage via un réseau de fournisseur de services Internet (option **Internet**) pour une adresse IP publique à l’aide d’Azure CLI. Une fois l’adresse IP publique créée, vous pouvez l’associer aux ressources Azure suivantes pour les trafics entrant et sortant via Internet :

* Machine virtuelle
* Jeu de mise à l’échelle de machine virtuelle
* Azure Kubernetes Service (AKS)
* Équilibreur de charge accessible via Internet
* Application Gateway
* Pare-feu Azure

Par défaut, la préférence de routage pour l’adresse IP publique est définie sur le réseau Microsoft mondial pour tous les services Azure, et peut être associée à n’importe lequel de ceux-ci.

> [!IMPORTANT]
> La préférence de routage est actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) maintenant.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]
Si vous décidez d’installer et d’utiliser Azure CLI en local, ce guide de démarrage rapide nécessite que vous utilisiez Azure CLI version 2.0.49 ou ultérieure. Exécutez `az --version` pour rechercher la version installée. Pour des informations d'installation ou de mise à niveau, consultez [Installer Azure CLI](/cli/azure/install-azure-cli).

## <a name="register-the-feature-for-your-subscription"></a>Inscrire la fonctionnalité pour votre abonnement
La fonctionnalité de préférence de routage est actuellement en préversion. Inscrivez la fonctionnalité pour votre abonnement comme suit :
```azurecli
az feature register --namespace Microsoft.Network --name AllowRoutingPreferenceFeature
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources
Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#az-group-create). L’exemple suivant crée un groupe de ressources dans la région Azure **USA Est** :

```azurecli
  az group create --name myResourceGroup --location eastus
```
## <a name="create-a-public-ip-address"></a>Créer une adresse IP publique

Créez une adresse IP publique avec une préférence de routage de type « Internet » en utilisant la commande [az network public-ip create](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create), avec le format indiqué ci-dessous.

La commande suivante crée une adresse IP publique avec la préférence de routage **Internet** dans la région Azure **USA Est**.

```azurecli
az network public-ip create \
--name MyRoutingPrefIP \
--resource-group MyResourceGroup \
--location eastus \
--ip-tags 'RoutingPreference=Internet' \
--sku STANDARD \
--allocation-method static \
--version IPv4
```

> [!NOTE]
>  La préférence de routage ne prend actuellement en charge que les adresses IP publiques IPV4.

Vous pouvez associer l’adresse IP publique créée ci-dessus à une machine virtuelle [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Utilisez la section CLI sur la page du tutoriel : [Associez une adresse IP publique à une machine virtuelle](associate-public-ip-address-vm.md#azure-cli) pour associer l’adresse IP publique à votre machine virtuelle. Vous pouvez associer l’adresse IP publique créée ci-dessus avec un [équilibreur de charge Azure](../load-balancer/load-balancer-overview.md) en l’assignant à la configuration **frontale** de l’équilibreur de charge. L’adresse IP publique sert d’adresse IP virtuelle (VIP) à charge équilibrée.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur la [préférence de routage dans les adresses IP publiques](routing-preference-overview.md). 
- [Configurer la préférence de routage pour une machine virtuelle à l’aide d’Azure CLI](configure-routing-preference-virtual-machine-cli.md).

