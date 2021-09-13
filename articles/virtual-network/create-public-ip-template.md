---
title: Créer une IP publique à l’aide d’un modèle Resource Manager
titleSuffix: Azure Virtual Network
description: Apprendre à créer une IP publique à l’aide d’un modèle Resource Manager
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 05/20/2021
ms.author: allensu
ms.openlocfilehash: 5839d4a66b0215c86c9521393b3d04f044b53f09
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2021
ms.locfileid: "113439078"
---
# <a name="create-a-public-ip-address-using-a-resource-manager-template"></a>Créer une adresse IP publique à l’aide d’un modèle Resource Manager

Cet article explique comment créer une ressource d’adresse IP publique dans un modèle Resource Manager.

Pour plus d’informations sur les ressources auxquelles cette adresse IP publique peut être associée et la différence entre les références SKU de base et standard, consultez la section [Adresses IP publiques](./public-ip-addresses.md). 

## <a name="prerequisites"></a>Prérequis

* Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.
* Un groupe de ressources dans votre abonnement Azure.
* Un modèle Azure Resource Manager pour les sections d’IP publique.

## <a name="create-standard-sku-public-ip-with-zones"></a>Créer une IP publique de référence SKU standard avec des zones

Dans cette section, vous allez créer une IP publique avec des zones. Les adresses IP publiques peuvent être redondantes interzones ou être zonales.

### <a name="zone-redundant"></a>Redondant interzone

Le code de cette section crée une adresse IPv4 publique redondante interzone standard nommée **myStandardZRPublicIP**.

Pour créer une adresse IPv6, changez la valeur du paramètre **publicIPAddressVersion** pour **IPv6**.

Section de modèle à ajouter :

```JSON
    {
      "apiVersion": "2020-08-01",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "myStandardZRPublicIP",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard"
      },
      "zones": [
                "1",
                "2",
                "3"
      ],
      "properties": {
        "publicIPAllocationMethod": "Static",
        "publicIPAddressVersion": "IPv4"
      }
```
> [!IMPORTANT]
> Avec les versions de l’API antérieures à 2020-08-01, utilisez le code ci-dessus, sans spécifier de paramètre de zone, pour qu’une référence SKU standard crée une adresse IP redondante interzone. 
>

>[!NOTE]
>Les sélections des options de zone ci-dessus sont valides uniquement dans les régions pourvues de [zones de disponibilité](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

### <a name="zonal"></a>Zonal

Le code de cette section crée une adresse IPv4 publique zonale standard nommée **myStandardZonalPublicIP**. 

Pour créer une adresse IP publique zonale standard dans Zone 2, la propriété **« zones »** contient un « 2 ».

Section de modèle à ajouter :

```JSON
    {
      "apiVersion": "2020-08-01",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "myStandardZonalPublicIP",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard"
      },
      "zones": [
                "2"
      ],
      "properties": {
        "publicIPAllocationMethod": "Static",
        "publicIPAddressVersion": "IPv4"
      }
```

>[!NOTE]
>Les sélections des options de zone ci-dessus sont valides uniquement dans les régions pourvues de [zones de disponibilité](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

## <a name="create-standard-public-ip-without-zones"></a>Créer une IP publique standard sans zone

Dans cette section, vous allez créer une adresse IP non zonale. 

Le code de cette section crée une adresse IPv4 publique standard sans zone nommée **myStandardZonalPublicIP**. La section de code est valide pour toutes les régions avec ou sans [zone de disponibilité](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

Pour créer une adresse IPv6, changez la valeur du paramètre **publicIPAddressVersion** pour **IPv6**.

Section de modèle à ajouter :

```JSON
    {
      "apiVersion": "2020-08-01",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "myStandardPublicIP",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard"
      },
      "properties": {
        "publicIPAllocationMethod": "Static",
        "publicIPAddressVersion": "IPv4"
      }
```
> [!IMPORTANT]
> Pour les versions de l’API antérieures à 2020-08-01, le fait de ne pas spécifier de paramètre de zone pour une référence SKU standard créera une adresse IP redondante interzone. 
>


## <a name="create-a-basic-public-ip"></a>Créer une IP publique de base

Dans cette section, vous allez créer une IP de base. Les adresses IP publiques de base ne prennent pas en charge les zones de disponibilité. 

Le code de cette section crée une adresse IPv4 publique de base nommée **myBasicPublicIP**.

Pour créer une adresse IPv6, changez la valeur du paramètre **publicIPAddressVersion** pour **IPv6**. 

Section de modèle à ajouter :

```JSON
    {
      "apiVersion": "2020-08-01",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "myBasicPublicIP",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Basic"
      },
      "properties": {
        "publicIPAllocationMethod": "Static",
        "publicIPAddressVersion": 'IPv4'
      }
```

S’il est acceptable que l’adresse IP change au fil du temps, vous pouvez sélectionner l’affectation d’IP **publicIPAllocationMethod** en remplaçant la valeur du paramètre AllocationMethod par **Dynamic**. 

>[!NOTE]
> Une adresse IPv6 de base doit toujours être « dynamique ».

## <a name="routing-preference-and-tier"></a>Préférence de routage et niveau

Les adresses IPv4 publiques statiques de référence SKU standard prennent en charge la préférence de routage ou la fonctionnalité Niveau global.

### <a name="routing-preference"></a>Préférence de routage

Par défaut, la préférence de routage pour les adresses IP publiques est définie sur le « Réseau Microsoft », qui remet le trafic à l’utilisateur via le réseau étendu mondial de Microsoft.  

La sélection de **Internet** réduit les déplacements sur le réseau de Microsoft en utilisant à la place le réseau du fournisseur de services Internet pour acheminer le trafic à un coût optimisé.  

Pour plus d’informations sur la préférence de routage, consultez la section [Qu’est-ce qu’une préférence de routage (préversion) ?](./routing-preference-overview.md).

Si vous souhaitez utiliser la préférence de routage Internet pour une adresse IPv4 publique redondante interzone standard, la section se rapportant au modèle devrait ressembler à ce qui suit :

```JSON
    {
      "apiVersion": "2020-08-01",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "myStandardZRPublicIP-RP",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard"
      },
      "zones": [
                "1",
                "2",
                "3"
      ],
      "properties": {
        "publicIPAllocationMethod": "Static",
        "publicIPAddressVersion": "IPv4",
            "ipTags": [
          {
           "ipTagType": "RoutingPreference",
            "tag": "Internet"
           }
         ]
      }
    }
```

### <a name="tier"></a>Niveau

Les adresses IP publiques sont associées à une seule région. Le niveau **Global** s’étend sur une adresse IP dans plusieurs régions. Le niveau **Global** est exigé pour les front-ends des équilibreurs de charge interrégionaux.  

Pour plus d’informations, consultez [Équilibreur de charge interrégional](../load-balancer/cross-region-overview.md).

Pour utiliser une adresse IPv4 publique globale standard, la section se rapportant au modèle devrait ressembler à ce qui suit :

```JSON
    {
      "apiVersion": "2020-08-01",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "myStandardPublicIP-Global",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard",
            "tier": "Global"
      },
      "properties": {
        "publicIPAllocationMethod": "Static",
        "publicIPAddressVersion": "IPv4"
      }
```

## <a name="additional-information"></a>Informations supplémentaires 

Pour plus d’informations sur les propriétés IP publiques énumérées dans cet article, consultez [Gérer les adresses IP publiques](./virtual-network-public-ip-address.md#create-a-public-ip-address).

## <a name="next-steps"></a>Étapes suivantes
- Associer une [adresse IP publique à une machine virtuelle](./associate-public-ip-address-vm.md#azure-portal)
- En savoir plus sur les [adresses IP publiques](./public-ip-addresses.md#public-ip-addresses) dans Azure.
- En savoir plus sur tous les [paramètres d’adresse IP publique](virtual-network-public-ip-address.md#create-a-public-ip-address).
