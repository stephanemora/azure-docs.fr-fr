---
title: Mettre à niveau une adresse IP publique - Azure CLI
description: Dans cet article, vous allez apprendre à mettre à niveau une adresse IP publique de référence SKU de base à l’aide de l’interface Azure CLI.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 05/20/2021
ms.custom: template-how-to
ms.openlocfilehash: 6e6f348cd8836363e8f3b0023669897fc2123650
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129368519"
---
# <a name="upgrade-a-public-ip-address-using-the-azure-cli"></a>Mettre à niveau une adresse IP publique à l’aide de l’interface Azure CLI

Les adresses IP publiques Azure sont créées avec une référence SKU de base ou standard. La référence SKU détermine la fonctionnalité, ainsi que la méthode d’allocation, la prise en charge des fonctionnalités et les ressources auxquelles elles peuvent être associées. 

Dans cet article, vous allez apprendre à mettre à niveau une adresse IP publique de référence SKU de base vers une référence SKU standard avec l’interface Azure CLI.

## <a name="prerequisites"></a>Prérequis

* Compte Azure avec un abonnement actif. [Créez-en un gratuitement](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Une adresse IP publique de référence SKU de base **statique** dans votre abonnement. Pour plus d’informations, consultez [Créer une adresse IP publique - Portail Azure](../../virtual-network/create-public-ip-portal.md#create-a-basic-sku-public-ip-address).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Ce tutoriel nécessite l’interface Azure CLI version 2.0.28 ou ultérieure. Si vous utilisez Azure Cloud Shell, la version la plus récente est déjà installée.

## <a name="upgrade-public-ip-address"></a>Mettre à niveau une adresse IP publique

Dans cette section, vous allez utiliser l’interface Azure CLI pour mettre à niveau votre adresse IP publique de référence SKU de base vers la référence SKU standard.

```azurecli-interactive
az network public-ip update \
    --resource-group myResourceGroup \
    --name myBasicPublicIP \
    --sku Standard

```
> [!NOTE]
> L’adresse IP publique de base que vous mettez à niveau doit être définie sur le type d’allocation statique. Vous recevrez un avertissement indiquant que l’adresse IP ne peut pas être mise à niveau si vous essayez de mettre à niveau une adresse IP allouée dynamiquement.

> [!WARNING]
> La mise à niveau d’une adresse IP publique de base vers la référence SKU standard ne peut pas être inversée. Les adresses IP publiques dont la référence SKU a été mise à niveau de De base vers Standard n’ont toujours aucune [zone de disponibilité](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones) garantie.

## <a name="verify-upgrade"></a>Vérifier la mise à niveau

Dans cette section, vous allez vérifier que l’adresse IP publique est désormais la référence SKU standard.

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myBasicPublicIP \
  --query sku \
  --output tsv

```
Cette commande doit afficher **Standard**.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez mis à niveau une adresse IP publique de référence SKU de base vers la référence SKU standard.

Pour plus d’informations, sur les adresses IP publiques dans Azure, consultez :

- [Adresses IP publiques dans Azure](public-ip-addresses.md)
- [Créer une adresse IP publique - Portail Azure](../../virtual-network/create-public-ip-portal.md)

