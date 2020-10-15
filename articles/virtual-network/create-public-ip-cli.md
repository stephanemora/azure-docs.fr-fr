---
title: Créer une adresse IP publique - Azure CLI
description: Découvrez comment créer une adresse IP publique à l’aide d’Azure CLI
services: virtual-network
documentationcenter: na
author: blehr
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2020
ms.author: blehr
ms.openlocfilehash: d868a2d9bc88be7faea161779c35110f13e2b2ac
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2020
ms.locfileid: "91939051"
---
# <a name="quickstart-create-a-public-ip-address-using-azure-cli"></a>Démarrage rapide : Créer une adresse IP publique à l’aide d’Azure CLI

Cet article explique comment créer une ressource d’adresse IP publique à l’aide d’Azure CLI. Pour savoir quelles ressources peuvent être associées, connaître la différence entre les références SKU De base et Standard et obtenir d’autres informations connexes, consultez [Adresses IP publiques](https://docs.microsoft.com/azure/virtual-network/public-ip-addresses).  Cet exemple traite seulement des adresses IPv4. Pour plus d’informations sur les adresses IPv6, consultez [IPv6 pour les réseaux virtuels Azure](https://docs.microsoft.com/azure/virtual-network/ipv6-overview).

## <a name="prerequisites"></a>Prérequis

- Azure CLI installé localement ou Azure Cloud Shell

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Si vous choisissez d’installer et d’utiliser l’interface CLI en local, ce guide de démarrage nécessite Azure CLI version 2.0.28 ou ultérieure. Pour connaître la version de l’interface, exécutez `az --version`. Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.

Avec la commande [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create), créez un groupe de ressources nommé **myResourceGroup** dans l’emplacement **eastus2**.

```azurecli-interactive
  az group create \
    --name myResourceGroup \
    --location eastus2
```
---
# <a name="standard-sku---using-zones"></a>[**Référence SKU Standard - Avec des zones**](#tab/option-create-public-ip-standard-zones)

>[!NOTE]
>La commande suivante est disponible pour l’API version 2020-08-01 ou ultérieure.  Pour plus d’informations sur la version de l’API actuellement utilisée, consultez [Fournisseurs et types de ressources](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types).

Utilisez la commande [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) pour créer une adresse IP publique redondante interzone standard nommée **myStandardZRPublicIP** dans **myResourceGroup**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myStandardZRPublicIP \
    --sku Standard
    --zone 1,2,3
```
> [!IMPORTANT]
> Avec des versions de l’API antérieures à 2020-08-01, exécutez la commande ci-dessus sans spécifier de paramètre de zone pour créer une adresse IP redondante interzone. 
>

Pour créer une adresse IP publique zonale standard dans la zone 2 nommée **myStandardZonalPublicIP** dans **myResourceGroup**, exécutez la commande suivante :

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myStandardZonalPublicIP \
    --sku Standard \
    --zone 2
```

Notez que les sélections des options de zone ci-dessus sont valides uniquement dans des régions avec des [zones de disponibilité](https://docs.microsoft.com/azure/availability-zones/az-overview?toc=/azure/virtual-network/toc.json#availability-zones).

# <a name="standard-sku---no-zones"></a>[**Référence SKU Standard - Sans zones**](#tab/option-create-public-ip-standard)

>[!NOTE]
>La commande suivante est disponible pour l’API version 2020-08-01 ou ultérieure.  Pour plus d’informations sur la version de l’API actuellement utilisée, consultez [Fournisseurs et types de ressources](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types).

Utilisez la commande [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) pour créer une adresse IP publique standard en tant que ressource non zonale nommée **myStandardPublicIP** dans **myResourceGroup**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myStandardPublicIP \
    --sku Standard
```
Cette sélection est valide dans toutes les régions. Il s’agit de la sélection par défaut pour les adresses IP publiques standard dans les régions sans [zones de disponibilité](https://docs.microsoft.com/azure/availability-zones/az-overview?toc=/azure/virtual-network/toc.json#availability-zones).

# <a name="basic-sku"></a>[**Référence De base**](#tab/option-create-public-ip-basic)

Utilisez la commande [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) pour créer une adresse IP publique statique de base nommée **myBasicPublicIP** dans **myResourceGroup**.  Les adresses IP publiques de base n’intègrent pas le concept de zones de disponibilité.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myBasicPublicIP \
    --sku Basic \
    --allocation-method Static
```
S’il est acceptable que l’adresse IP change au fil du temps, l’attribution **Dynamique** d’adresse IP peut être sélectionnée en changeant la méthode d’allocation par « Dynamique ».

---

## <a name="additional-information"></a>Informations supplémentaires 

Pour plus d’informations sur chacune des variables listées ci-dessus, consultez [Gérer les adresses IP publiques](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address#create-a-public-ip-address).

## <a name="next-steps"></a>Étapes suivantes
- Associer une [adresse IP publique à une machine virtuelle](https://docs.microsoft.com/azure/virtual-network/associate-public-ip-address-vm#azure-portal).
- En savoir plus sur les [adresses IP publiques](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) dans Azure.
- En savoir plus sur tous les [paramètres d’adresse IP publique](virtual-network-public-ip-address.md#create-a-public-ip-address).
