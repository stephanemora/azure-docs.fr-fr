---
title: "Modifier les paramètres d'adresse IP de la passerelle : Azure CLI"
titleSuffix: Azure VPN Gateway
description: Apprenez à modifier les préfixes d'adresse IP de votre passerelle réseau locale à partir d'Azure CLI.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: 5e03da37a727c87995496878b191cbf48cfe0347
ms.sourcegitcommit: 49bd8e68bd1aff789766c24b91f957f6b4bf5a9b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2021
ms.locfileid: "108229609"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-cli"></a>Modifier les paramètres de la passerelle du réseau local à l’aide d’Azure CLI

Parfois, les paramètres de la passerelle de réseau local AddressPrefix ou Gateway IP Address changent. Cet article vous montre comment modifier les paramètres de passerelle de votre réseau local. Vous pouvez également modifier ces paramètres à l’aide d’une méthode différente en sélectionnant une autre option dans la liste suivante :

> [!div class="op_single_selector"]
> * [Azure portal](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>
>

## <a name="before-you-begin"></a><a name="before"></a>Avant de commencer

Installez la dernière version des commandes CLI (version 2.0 ou ultérieure). Pour plus d’informations sur l’installation des commandes CLI, consultez l’article [Installer l’interface de ligne de commande Azure](/cli/azure/install-azure-cli).

[!INCLUDE [CLI-login](../../includes/vpn-gateway-cli-login-include.md)]

## <a name="modify-ip-address-prefixes"></a><a name="ipaddprefix"></a>Modifier les préfixes d’adresse IP

[!INCLUDE [modify-prefix](../../includes/vpn-gateway-modify-ip-prefix-cli-include.md)]

## <a name="modify-the-gateway-ip-address"></a><a name="gwip"></a>Modifier l’adresse IP de la passerelle

[!INCLUDE [modify-gateway-IP](../../includes/vpn-gateway-modify-lng-gateway-ip-cli-include.md)]

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez vérifier votre connexion à la passerelle. Consultez [Vérifier la connexion à une passerelle](vpn-gateway-verify-connection-resource-manager.md).