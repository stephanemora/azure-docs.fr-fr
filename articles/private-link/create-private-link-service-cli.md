---
title: 'Démarrage rapide : Créer un service Azure Private Link en utilisant Azure CLI'
description: Dans ce guide de démarrage rapide, vous découvrez comment créer un service Azure Private Link en utilisant Azure CLI.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 01/22/2021
ms.author: allensu
ms.openlocfilehash: 76fd959c28203132be4695031d96315f258cf53f
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102563045"
---
# <a name="quickstart-create-a-private-link-service-using-azure-cli"></a>Démarrage rapide : Créer un service Private Link en utilisant Azure CLI

Commencez à créer un service Private Link qui fait référence à votre service.  Accordez à Private Link un accès à votre service ou ressource déployé(e) derrière Azure Standard Load Balancer.  Les utilisateurs de votre service ont un accès privé à partir de leur réseau virtuel.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)] 

- Ce guide de démarrage rapide nécessite la version 2.0.28 ou ultérieure de l’interface Azure CLI. Si vous utilisez Azure Cloud Shell, la version la plus récente est déjà installée.

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#az_group_create) :

* Nommé **CreatePrivLinkService-rg**. 
* Dans l’emplacement **eastus**.

```azurecli-interactive
  az group create \
    --name CreatePrivLinkService-rg \
    --location eastus2

```

## <a name="create-an-internal-load-balancer"></a>Créer un équilibrage de charge interne

Dans cette section, vous allez créer un réseau virtuel et un Azure Load Balancer interne.

### <a name="virtual-network"></a>Réseau virtuel

Dans cette section, vous allez créer un réseau virtuel et un sous-réseau pour héberger l’équilibreur de charge qui accède à votre service Private Link.

Créez un réseau virtuel avec la commande [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) :

* Nommé **myVNet**.
* Préfixe d’adresse **10.1.0.0/16**.
* Sous-réseau nommé **mySubnet**.
* Préfixe du sous-réseau **10.1.0.0/24**.
* Dans le groupe de ressources **CreatePrivLinkService-rg**.
* Emplacement **eastus2**.
* Désactivez la stratégie réseau pour le service de liaison privée sur le sous-réseau.

```azurecli-interactive
  az network vnet create \
    --resource-group CreatePrivLinkService-rg \
    --location eastus2 \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefixes 10.1.0.0/24

```

Pour mettre à jour le sous-réseau afin de désactiver les stratégies réseau du service de liaison privée, utilisez [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) :

```azurecli-interactive
az network vnet subnet update \
    --name mySubnet \
    --resource-group CreatePrivLinkService-rg \
    --vnet-name myVNet \
    --disable-private-link-service-network-policies true
```

### <a name="create-standard-load-balancer"></a>Créer un équilibreur de charge standard

Cette section explique en détail comment vous pouvez créer et configurer les composants suivants de l’équilibreur de charge :

  * Un pool d’adresses IP frontal qui reçoit le trafic réseau entrant sur l’équilibreur de charge.
  * Un pool d’adresses IP principal auquel le pool frontal envoie le trafic réseau dont la charge est équilibrée.
  * Une sonde d’intégrité qui détermine l’intégrité des instances de machine virtuelle backend.
  * Une règle d’équilibreur de charge qui définit la distribution du trafic aux machines virtuelles.

### <a name="create-the-load-balancer-resource"></a>Créer la ressource d’équilibreur de charge

Créez un équilibreur de charge public avec la commande [az network lb create](/cli/azure/network/lb#az-network-lb-create) :

* Nommé **myLoadBalancer**.
* Un pool frontal nommé **myFrontEnd**.
* Un pool principal nommé **myBackEndPool**.
* Associé au réseau virtuel **myVNet**.
* Associé au sous-réseau de back-end **mySubnet**.

```azurecli-interactive
  az network lb create \
    --resource-group CreatePrivLinkService-rg \
    --name myLoadBalancer \
    --sku Standard \
    --vnet-name myVnet \
    --subnet mySubnet \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool
```

### <a name="create-the-health-probe"></a>Créer la sonde d’intégrité

Une sonde d’intégrité vérifie toutes les instances de machine virtuelle pour s’assurer qu’elles peuvent transmettre du trafic réseau. 

Une machine virtuelle dont le contrôle de sonde a échoué est supprimée de l’équilibreur de charge. La machine virtuelle est rajoutée à l’équilibreur de charge une fois l’échec résolu.

Créez une sonde d’intégrité avec la commande [az network lb probe create](/cli/azure/network/lb/probe#az-network-lb-probe-create) :

* Analyse l’intégrité des machines virtuelles.
* Nommée **myHealthProbe.** .
* Protocole **TCP**.
* Surveillant le **Port 80**.

```azurecli-interactive
  az network lb probe create \
    --resource-group CreatePrivLinkService-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

### <a name="create-the-load-balancer-rule"></a>Créer la règle d’équilibreur de charge

Une règle d’équilibreur de charge définit les éléments suivants :

* La configuration IP frontale pour le trafic entrant.
* Le pool d’adresses IP principal qui reçoit le trafic.
* Les ports source et de destination requis. 

Créez une règle d’équilibreur de charge avec la commande [az network lb rule create](/cli/azure/network/lb/rule#az-network-lb-rule-create) :

* Nommée **myHTTPRule**.
* Écoutant le **Port 80** dans le pool frontal **myFrontEnd**.
* Envoyant le trafic réseau dont la charge est équilibrée au pool d’adresses principal **myBackEndPool** à l’aide du **Port 80**. 
* Utilisant la sonde d’intégrité **myHealthProbe**.
* Protocole **TCP**.
* Délai d’inactivité de **15 minutes**.
* Activez la réinitialisation TCP.

```azurecli-interactive
  az network lb rule create \
    --resource-group CreatePrivLinkService-rg \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --idle-timeout 15 \
    --enable-tcp-reset true
```

## <a name="create-a-private-link-service"></a>Créer un service Private Link

Dans cette section, créez un service de liaison privée qui utilise l’équilibreur de charge Azure créé à l’étape précédente.

Créez un service de liaison privée à l’aide de la configuration IP front-end de l’équilibreur de charge standard avec la commande [az network private-link-service create](/cli/azure/network/private-link-service#az-network-private-link-service-create) :

* Nommé **myPrivateLinkService**.
* Dans le réseau virtuel **myVNet**.
* Associé à l’équilibreur de charge standard **myLoadBalancer** et à la configuration du front-end **myFrontEnd**.
* À l’emplacement **eastus2**.
 
```azurecli-interactive
az network private-link-service create \
    --resource-group CreatePrivLinkService-rg \
    --name myPrivateLinkService \
    --vnet-name myVNet \
    --subnet mySubnet \
    --lb-name myLoadBalancer \
    --lb-frontend-ip-configs myFrontEnd \
    --location eastus2
```

Votre service de liaison privée est créé et peut recevoir du trafic. Si vous voulez voir les flux de trafic, configurez votre application derrière votre équilibreur de charge standard.


## <a name="create-private-endpoint"></a>Créer un point de terminaison privé

Dans cette section, vous allez mapper le service de liaison privée sur un point de terminaison privé. Un réseau virtuel contient le point de terminaison privé pour le service de liaison privée. Ce réseau virtuel contient les ressources qui auront accès à votre service de liaison privée.

### <a name="create-private-endpoint-virtual-network"></a>Créer un réseau virtuel de point de terminaison privé

Créez un réseau virtuel avec la commande [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) :

* Nommé **myVNetPE**.
* Préfixe d’adresse **11.1.0.0/16**.
* Sous-réseau nommé **mySubnetPE**.
* Préfixe du sous-réseau **11.1.0.0/24**.
* Dans le groupe de ressources **CreatePrivLinkService-rg**.
* Emplacement **eastus2**.

```azurecli-interactive
  az network vnet create \
    --resource-group CreatePrivLinkService-rg \
    --location eastus2 \
    --name myVNetPE \
    --address-prefixes 11.1.0.0/16 \
    --subnet-name mySubnetPE \
    --subnet-prefixes 11.1.0.0/24
```

Pour mettre à jour le sous-réseau afin de désactiver les stratégies réseau de point de terminaison privé, utilisez [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) :

```azurecli-interactive
az network vnet subnet update \
    --name mySubnetPE \
    --resource-group CreatePrivLinkService-rg \
    --vnet-name myVNetPE \
    --disable-private-endpoint-network-policies true
```

### <a name="create-endpoint-and-connection"></a>Créer un point de terminaison et une connexion

* Utilisez [az network private-link-service show](/cli/azure/network/private-link-service#az_network_private_link_service_show) pour obtenir l’ID de la ressource du service de liaison privée. La commande place l’ID de la ressource dans une variable à des fins d’utilisation ultérieure.

* Utilisez [az network private-endpoint create](/cli/azure/network/private-endpoint#az_network_private_endpoint_create) pour créer le point de terminaison privé dans le réseau virtuel que vous avez créé précédemment.

* Nommé **myPrivateEndpoint**.
* Dans le groupe de ressources **CreatePrivLinkService-rg**.
* Nom de la connexion **myPEconnectiontoPLS**.
* Emplacement **eastus2**.
* Dans le réseau virtuel **myVNetPE** et le sous-réseau **mySubnetPE**.

```azurecli-interactive
  export resourceid=$(az network private-link-service show \
    --name myPrivateLinkService \
    --resource-group CreatePrivLinkService-rg \
    --query id \
    --output tsv)

  az network private-endpoint create \
    --connection-name myPEconnectiontoPLS \
    --name myPrivateEndpoint \
    --private-connection-resource-id $resourceid \
    --resource-group CreatePrivLinkService-rg \
    --subnet mySubnetPE \
    --manual-request false \
    --vnet-name myVNetPE 

```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous n’en avez plus besoin, utilisez la commande [az group delete](/cli/azure/group#az-group-delete) pour supprimer le groupe de ressources, le service de liaison privée, l’équilibreur de charge et toutes les ressources associées.

```azurecli-interactive
  az group delete \
    --name CreatePrivLinkService-rg 
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous :

* Avez créé un réseau virtuel et un Azure Load Balancer interne.
* Avez créé un service de liaison privée.

Pour en savoir plus sur le point de terminaison privé Azure, passez à :
> [!div class="nextstepaction"]
> [Démarrage rapide : Créer un point de terminaison privé à l’aide d’Azure CLI](create-private-endpoint-cli.md)
