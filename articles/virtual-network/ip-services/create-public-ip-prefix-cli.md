---
title: 'Démarrage rapide : Créer un préfixe d’adresse IP publique – Interface Azure CLI'
titlesuffix: Azure Virtual Network
description: Découvrez comment créer un préfixe d’adresse IP publique à l’aide de l’interface Azure CLI.
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: quickstart
ms.date: 10/01/2021
ms.author: allensu
ms.openlocfilehash: 0b72eb4e70fb80602b352c32d6ffbde1cf4336da
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129368922"
---
# <a name="quickstart-create-a-public-ip-address-prefix-using-the-azure-cli"></a>Démarrage rapide : Créer un préfixe d’adresse IP publique en utilisant l’interface Azure CLI

Découvrez les préfixes d’adresse IP publique et comment en créer, changer et supprimer un. Un préfixe d’adresse IP publique est une plage contiguë d’adresses IP publiques à référence SKU standard. 

Lorsque vous créez une ressource d’adresse IP publique, vous pouvez attribuer une adresse IP publique statique à partir du préfixe et l’associer à des machines virtuelles, à des équilibreurs de charge ou à d’autres ressources. Pour plus d’informations, consultez [Vue d’ensemble du préfixe d’adresse IP publique](public-ip-address-prefix.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Ce tutoriel nécessite l’interface Azure CLI version 2.0.28 ou ultérieure. Si vous utilisez Azure Cloud Shell, la version la plus récente est déjà installée.

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.

À l’aide de la commande [az group create](/cli/azure/group#az_group_create), créez un groupe de ressources nommé **QuickStartCreateIPPrefix-rg** dans l’emplacement **eastus2**.

```azurecli-interactive
  az group create \
    --name QuickStartCreateIPPrefix-rg \
    --location eastus2
```

## <a name="create-a-public-ip-address-prefix"></a>Créer un préfixe d’adresse IP publique

Dans cette section, vous allez créer un préfixe d’IP publique non zonale, zonale et redondant interzone à l’aide d’Azure PowerShell. 

Les préfixes des exemples sont les suivants :

* **IPv4** - /28 (16 adresses)

* **IPv6** - /124 (16 adresses)

Pour plus d’informations sur les tailles de préfixe disponibles, consultez [Tailles de préfixe](public-ip-address-prefix.md#prefix-sizes).

Créez un préfixe d’IP publique avec [az network public-ip prefix create](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_create), nommé **myPublicIpPrefix**, dans la région **eastus2**.

## <a name="ipv4"></a>IPv4

# <a name="zone-redundant-ipv4-prefix"></a>[**Préfixe IPv4 redondant interzone**](#tab/ipv4-zone-redundant)

Pour créer un préfixe d’IP publique IPv4, indiquez **IPv4** dans le paramètre **`--version`** . Pour créer un préfixe IPv4 redondant interzone, indiquez **1, 2, 3** dans le paramètre **`--zone`** .

```azurecli-interactive
  az network public-ip prefix create \
    --length 28 \
    --name myPublicIpPrefix \
    --resource-group QuickStartCreateIPPrefix-rg \
    --location eastus2 \
    --version IPv4 \
    --zone 1 2 3
```

# <a name="zonal-ipv4-prefix"></a>[**Préfixe IPv4 zonal**](#tab/ipv4-zonal)

Pour créer un préfixe d’IP publique IPv4, indiquez **IPv4** dans le paramètre **`--version`** . Spécifiez **2** dans le paramètre **`--zone`** pour créer un préfixe d’IP zonale dans la zone 2.

```azurecli-interactive
  az network public-ip prefix create \
    --length 28 \
    --name myPublicIpPrefix-zonal \
    --resource-group QuickStartCreateIPPrefix-rg \
    --location eastus2 \
    --version IPv4 \
    --zone 2
```

>[!NOTE]
>Les sélections des options de zone ci-dessus sont valides uniquement dans les régions pourvues de [zones de disponibilité](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

# <a name="non-zonal-ipv4-prefix"></a>[**Préfixe IPv4 zonal**](#tab/ipv4-non-zonal)

Pour créer un préfixe d’IP publique IPv4, indiquez **IPv4** dans le paramètre **`--version`** . Supprimez le paramètre **`--zone`** pour créer un préfixe d’IP non zonale.

```azurecli-interactive
  az network public-ip prefix create \
    --length 28 \
    --name myPublicIpPrefix-nozone \
    --resource-group QuickStartCreateIPPrefix-rg \
    --location eastus2 \
    --version IPv4
```

La suppression du paramètre **`--zone`** dans la commande est valide dans toutes les régions.  

La suppression du paramètre **`--zone`** constitue la sélection par défaut pour les adresses IP publiques standard dans les régions sans [Zone de disponibilité](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

---



## <a name="ipv6"></a>IPv6

# <a name="zone-redundant-ipv6-prefix"></a>[**Préfixe IPv6 redondant interzone**](#tab/ipv6-zone-redundant)

Pour créer un préfixe d’IP publique IPv6, indiquez **IPv6** dans le paramètre **`--version`** . Pour créer un préfixe IPv6 redondant interzone, indiquez **1, 2, 3** dans le paramètre **`--zone`** .

```azurecli-interactive
  az network public-ip prefix create \
    --length 124 \
    --name myPublicIpPrefix \
    --resource-group QuickStartCreateIPPrefix-rg \
    --location eastus2 \
    --version IPv6 \
    --zone 1 2 3
```

# <a name="zonal-ipv6-prefix"></a>[**Préfixe IPv6 zonal**](#tab/ipv6-zonal)

Pour créer un préfixe d’IP publique IPv6, indiquez **IPv6** dans le paramètre **`--version`** . Spécifiez **2** dans le paramètre **`--zone`** pour créer un préfixe d’IP zonale dans la zone 2.

```azurecli-interactive
  az network public-ip prefix create \
    --length 124 \
    --name myPublicIpPrefix-zonal \
    --resource-group QuickStartCreateIPPrefix-rg \
    --location eastus2 \
    --version IPv6 \
    --zone 2
```

>[!NOTE]
>Les sélections des options de zone ci-dessus sont valides uniquement dans les régions pourvues de [zones de disponibilité](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

# <a name="non-zonal-ipv6-prefix"></a>[**Préfixe IPv6 non zonal**](#tab/ipv6-non-zonal)

Pour créer un préfixe d’IP publique IPv6, indiquez **IPv6** dans le paramètre **`--version`** . Supprimez le paramètre **`--zone`** pour créer un préfixe d’IP non zonale.

```azurecli-interactive
  az network public-ip prefix create \
    --length 124 \
    --name myPublicIpPrefix-nozone \
    --resource-group QuickStartCreateIPPrefix-rg \
    --location eastus2 \
    --version IPv6
```

La suppression du paramètre **`--zone`** dans la commande est valide dans toutes les régions.  

La suppression du paramètre **`--zone`** constitue la sélection par défaut pour les adresses IP publiques standard dans les régions sans [Zone de disponibilité](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

---

## <a name="create-a-static-public-ip-address-from-a-prefix"></a>Créer une adresse IP publique statique à partir d’un préfixe

Une fois que vous avez créé un préfixe, vous devez créer des adresses IP statiques à partir du préfixe. Dans cette section, vous allez créer une adresse IP statique à partir du préfixe que vous avez créé précédemment.

Créez une adresse IP publique à l’aide de la commande [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) dans le préfixe **myPublicIpPrefix**.

# <a name="ipv4-address"></a>[**Adresse IPv4**](#tab/ipv4-address)

Pour créer une adresse IP publique IPv4, indiquez **IPv4** dans le paramètre **`--version`** .

```azurecli-interactive
  az network public-ip create \
    --name myPublicIP \
    --resource-group QuickStartCreateIPPrefix-rg \
    --allocation-method Static \
    --public-ip-prefix myPublicIpPrefix \
    --sku Standard \
    --version IPv4
```

# <a name="ipv6-address"></a>[**Adresse IPv6**](#tab/ipv6-address)

Pour créer un préfixe d’IP publique IPv6, indiquez **IPv6** dans le paramètre **`--version`** .

```azurecli-interactive
  az network public-ip create \
    --name myPublicIP \
    --resource-group QuickStartCreateIPPrefix-rg \
    --allocation-method Static \
    --public-ip-prefix myPublicIpPrefix \
    --sku Standard \
    --version IPv6
```

---

>[!NOTE]
>Seules les adresses IP publiques créées avec la référence SKU standard peuvent être attribuées à partir de la plage du préfixe. Pour en savoir plus sur les références (SKU) d’adresses IP publiques, consultez [Adresse IP publique](public-ip-addresses.md#public-ip-addresses).

## <a name="delete-a-prefix"></a>Supprimer un préfixe

Dans cette section, vous allez apprendre à supprimer un préfixe.

Pour supprimer un préfixe d’IP publique, utilisez [az network public-ip prefix delete](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_delete).

```azurecli-interactive
  az network public-ip prefix delete \
    --name myPublicIpPrefix \
    --resource-group QuickStartCreateIPPrefix-rg
```

>[!NOTE]
>Si les adresses dans le préfixe sont associées à des ressources d’adresse IP publique, vous devez d’abord supprimer les ressources d’adresse IP publique. Consultez [Supprimer une adresse IP publique](virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address).

## <a name="clean-up-resources"></a>Nettoyer les ressources

Dans cet article, vous avez créé un préfixe d’adresse IP publique et une adresse IP publique à partir de ce préfixe. 


Lorsque vous n’avez plus besoin du préfixe d’IP publique, supprimez le groupe de ressources et toutes les ressources qu’il contient avec [az group delete](/cli/azure/group#az_group_delete).

```azurecli-interactive
  az group delete \
    --name QuickStartCreateIPPrefix-rg
```

## <a name="next-steps"></a>Étapes suivantes

Passez à l’article suivant pour apprendre à créer un préfixe d’adresse IP publique à l’aide d’Azure PowerShell :
> [!div class="nextstepaction"]
> [Créer une IP publique à l’aide de l’interface Azure CLI](create-public-ip-cli.md)
