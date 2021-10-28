---
title: Intégration de la passerelle NAT – Azure App Service | Microsoft Docs
description: Décrit comment la passerelle NAT s’intègre à Azure App Service.
services: app-service
author: madsd
ms.assetid: 0a84734e-b5c1-4264-8d1f-77e781b28426
ms.service: app-service
ms.workload: web
ms.topic: article
ms.date: 08/04/2021
ms.author: madsd
ms.custom: seodec18
ms.openlocfilehash: e5a6f2fe52a9b74f74047a5a9512e40bb267161f
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130245281"
---
# <a name="virtual-network-nat-gateway-integration"></a>Intégration de la passerelle NAT au réseau virtuel

La passerelle NAT est un service complètement managé et hautement résilient, qui peut être associé à un ou plusieurs sous-réseaux et garantit que tout le trafic Internet sortant sera routé via la passerelle. Avec App Service, il existe deux scénarios importants dans lesquels vous pouvez utiliser la passerelle NAT. 

La passerelle NAT vous donne une adresse IP publique prévisible statique pour le trafic Internet sortant. Cela augmente également considérablement les [ports SNAT](../troubleshoot-intermittent-outbound-connection-errors.md) disponibles dans les scénarios où vous disposez d’un grand nombre de connexions simultanées à la même combinaison port/adresse publique.

Pour plus d’informations et connaître les prix, accédez à la [présentation de la passerelle NAT](../../virtual-network/nat-gateway/nat-overview.md).

:::image type="content" source="./media/nat-gateway-integration/nat-gateway-overview.png" alt-text="Diagramme montrant le trafic Internet circulant vers une passerelle NAT dans un réseau virtuel Azure":::

> [!Note] 
> L’utilisation de la passerelle NAT avec App Service dépend de l’intégration au réseau virtuel régional. Par conséquent, le plan App Service **Standard**, **Premium**, **PremiumV2** ou **PremiumV3** est requis.

## <a name="configuring-nat-gateway-integration"></a>Configuration de l’intégration de la passerelle NAT

Pour configurer l’intégration de la passerelle NAT avec App Service, vous devez effectuer les étapes suivantes :

* Configurez l’intégration au réseau virtuel régional avec votre application, comme décrit dans [Intégrer votre application à un réseau virtuel Azure](../overview-vnet-integration.md).
* Assurez-vous que l’option [Tout acheminer](../overview-vnet-integration.md#routes) est activée pour votre intégration au réseau virtuel, afin que le trafic Internet lié soit affecté par les routes dans votre réseau virtuel.
* Provisionnez une passerelle NAT avec une adresse IP publique et associez-la au sous-réseau d’intégration au réseau virtuel.

Configurez la passerelle NAT via le portail :

1. Accédez à l’interface utilisateur de **mise en réseau** dans le portail App Service et sélectionnez l’intégration au réseau virtuel dans la section Trafic sortant. Assurez-vous que votre application est intégrée à un sous-réseau et que l’option **Tout acheminer** a été activée.
:::image type="content" source="./media/nat-gateway-integration/nat-gateway-route-all-enabled.png" alt-text="Capture d’écran montrant l’option Tout acheminer activée pour l’intégration au réseau virtuel":::
1. Dans le menu du Portail Azure ou dans la page **Accueil**, sélectionnez **Créer une ressource**. La fenêtre **Nouvelle** apparaît.
1. Recherchez « Passerelle NAT » et sélectionnez-la dans la liste des résultats.
1. Renseignez les informations **de base** et sélectionnez la région où se trouve votre application.
:::image type="content" source="./media/nat-gateway-integration/nat-gateway-create-basics.png" alt-text="Capture d’écran de l’onglet De base dans Créer une passerelle NAT":::
1. Sous l’onglet **Adresse IP sortante**, créez ou sélectionnez une adresse IP publique.
:::image type="content" source="./media/nat-gateway-integration/nat-gateway-create-outbound-ip.png" alt-text="Capture d’écran de l’onglet Adresse IP sortante dans Créer une passerelle NAT":::
1. Sous l’onglet **Sous-réseau**, sélectionnez le sous-réseau utilisé pour l’intégration au réseau virtuel.
:::image type="content" source="./media/nat-gateway-integration/nat-gateway-create-subnet.png" alt-text="Capture d’écran de l’onglet Sous-réseau dans Créer une passerelle NAT":::
1. Renseignez les étiquettes si nécessaire et **créez** la passerelle NAT. Une fois la passerelle NAT provisionnée, cliquez sur **Accéder au groupe de ressources** et sélectionnez la nouvelle passerelle NAT. Vous pouvez voir l’adresse IP publique que votre application utilisera pour le trafic Internet sortant dans le panneau Adresse IP sortante.
:::image type="content" source="./media/nat-gateway-integration/nat-gateway-public-ip.png" alt-text="Capture d’écran du panneau Adresse IP sortante dans le portail de la passerelle NAT"::: 

Si vous préférez utiliser l’interface CLI pour configurer votre environnement, il s’agit des commandes importantes. Comme prérequis, vous devez créer une application web avec l’intégration au réseau virtuel configurée.

Assurez-vous que l’option **Tout acheminer** est configurée pour votre intégration au réseau virtuel (*Remarque* : La version `az version` minimale requise est 2.27) :

```azurecli-interactive
az webapp config set --resource-group [myResourceGroup] --name [myWebApp] --vnet-route-all-enabled
```

Créez une IP publique et une passerelle NAT :

```azurecli-interactive
az network public-ip create --resource-group [myResourceGroup] --name myPublicIP --sku standard --allocation static

az network nat gateway create --resource-group [myResourceGroup] --name myNATgateway --public-ip-addresses myPublicIP --idle-timeout 10
```

Associez la passerelle NAT au sous-réseau d’intégration au réseau virtuel :

```azurecli-interactive
az network vnet subnet update --resource-group [myResourceGroup] --vnet-name [myVnet] --name [myIntegrationSubnet] --nat-gateway myNATgateway
```

## <a name="scaling-nat-gateway"></a>Mise à l’échelle de la passerelle NAT

La même passerelle NAT peut être utilisée sur plusieurs sous-réseaux dans le même réseau virtuel, ce qui permet d’utiliser une passerelle NAT sur plusieurs applications et plans App Service.

La passerelle NAT prend en charge les adresses IP publiques et les préfixes d’IP publique. Une passerelle NAT peut prendre en charge jusqu’à 16 adresses IP sur des adresses IP et des préfixes individuels. Chaque adresse IP alloue 64 000 ports (ports SNAT), ce qui permet d’avoir jusqu’à 1 million de ports disponibles. Apprenez-en davantage dans la [section sur la mise à l’échelle](../../virtual-network/nat-gateway/nat-gateway-resource.md#scaling) de la passerelle NAT.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur la passerelle NAT, consultez la [documentation relative à la passerelle NAT](../../virtual-network/nat-gateway/nat-overview.md).

Pour plus d’informations sur l’intégration au réseau virtuel, consultez la [documentation relative à l’intégration au réseau virtuel](../overview-vnet-integration.md).