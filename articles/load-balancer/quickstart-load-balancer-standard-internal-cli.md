---
title: 'Démarrage rapide : Créer un équilibreur de charge interne - Azure CLI'
titleSuffix: Azure Load Balancer
description: Ce guide de démarrage rapide montre comment créer un équilibreur de charge interne en utilisant Azure CLI.
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a load balancer so that I can load balance internal traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/19/2020
ms.author: allensu
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 15060a367bba2d50d7054730321f7f20d4c25e46
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97916675"
---
# <a name="quickstart-create-an-internal-load-balancer-to-load-balance-vms-using-azure-cli"></a>Démarrage rapide : Créer un équilibreur de charge interne pour équilibrer la charge de machines virtuelles avec Azure CLI

Démarrez avec Azure Load Balancer en utilisant Azure CLI pour créer un équilibreur de charge interne et trois machines virtuelles.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)] 

- Ce guide de démarrage rapide nécessite la version 2.0.28 ou ultérieure de l’interface Azure CLI. Si vous utilisez Azure Cloud Shell, la version la plus récente est déjà installée.

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#az_group_create) :

* Nommé **CreateIntLBQS-rg**. 
* Dans l’emplacement **eastus**.

```azurecli-interactive
  az group create \
    --name CreateIntLBQS-rg \
    --location eastus

```
---

# <a name="standard-sku"></a>[**Référence Standard**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>Il est recommandé de disposer d’un équilibreur de charge de référence SKU Standard pour les charges de travail de production. Pour plus d’informations sur les références (SKU), consultez **[Références SKU Azure Load Balancer](skus.md)** .

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal.png" alt-text="Ressources Standard Load Balancer créées pour le guide de démarrage rapide." border="false":::

## <a name="configure-virtual-network---standard"></a>Configurer le réseau virtuel - Standard

Avant de déployer des machines virtuelles et votre équilibreur de charge, créez les ressources de réseau virtuel associées.

### <a name="create-a-virtual-network"></a>Créez un réseau virtuel

Créez un réseau virtuel avec la commande [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) :

* Nommé **myVNet**.
* Préfixe d’adresse **10.1.0.0/16**.
* Sous-réseau nommé **myBackendSubnet**.
* Préfixe du sous-réseau **10.1.0.0/24**.
* Dans le groupe de ressources **CreateIntLBQS-rg**.
* Emplacement **eastus**.

```azurecli-interactive
  az network vnet create \
    --resource-group CreateIntLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

### <a name="create-a-public-ip-address"></a>Créer une adresse IP publique

Utilisez [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create) pour créer une adresse IP publique pour l’hôte bastion :

* Créez une adresse IP publique redondante de zone standard nommée **myBastionIP**.
* Dans **CreateIntLBQS-rg**.

```azurecli-interactive
az network public-ip create \
    --resource-group CreateIntLBQS-rg  \
    --name myBastionIP \
    --sku Standard
```
### <a name="create-a-bastion-subnet"></a>Créer un sous-réseau bastion

Utilisez [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) pour créer un sous-réseau bastion :

* Nommé **AzureBastionSubnet**.
* Préfixe d’adresse **10.1.1.0/24**.
* Dans le réseau virtuel **myVNet**.
* Dans le groupe de ressources **CreateIntLBQS-rg**.

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateIntLBQS-rg  \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

### <a name="create-bastion-host"></a>Créer un hôte bastion

Utilisez [az network bastion create](/cli/azure/network/bastion#az-network-bastion-create) pour créer un hôte bastion :

* Nom : **myBastionHost**
* Dans **CreateIntLBQS-rg**.
* Associé à l’adresse IP publique **myBastionIP**.
* Associé au réseau virtuel **myVNet**.
* À l’emplacement **eastus**.

```azurecli-interactive
az network bastion create \
    --resource-group CreateIntLBQS-rg  \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Le déploiement de l’hôte Azure Bastion peut prendre quelques minutes.


### <a name="create-a-network-security-group"></a>Créer un groupe de sécurité réseau

Pour un équilibreur de charge standard, les machines virtuelles à l’adresse principale doivent disposer d’interfaces réseau appartenant à un groupe de sécurité réseau. 

Créez un Groupe de sécurité réseau avec la commande [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create) :

* Nommé **myNSG**.
* Dans le groupe de ressources **CreateIntLBQS-rg**.

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Créer une règle de groupe de sécurité réseau

Créez une règle de groupe de sécurité réseau avec la commande [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) :

* Nommé **myNSGRuleHTTP**.
* Dans le groupe de sécurité réseau que vous avez créé à l’étape précédente, **myNSG**.
* Dans le groupe de ressources **CreateIntLBQS-rg**.
* Protocole **(*)** .
* Direction **entrante**.
* Source **(*)** .
* Destination **(*)** .
* Port de destination **Port 80**.
* Accès **Autoriser**.
* Priorité **200**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreateIntLBQS-rg \
    --nsg-name myNSG \
    --name myNSGRuleHTTP \
    --protocol '*' \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```

## <a name="create-backend-servers---standard"></a>Créer des serveurs back-end - Standard

Dans cette section, vous allez créer :

* Trois interfaces réseau pour les machines virtuelles.
* Trois machines virtuelles à utiliser en tant que serveurs principaux pour l’équilibreur de charge.

### <a name="create-network-interfaces-for-the-virtual-machines"></a>Créer des interfaces réseau pour les machines virtuelles

Créez les interfaces réseau avec la commande [az network nic create](/cli/azure/network/nic#az-network-nic-create) :

* Nommées **myNicVM1**, **myNicVM2** et **myNicVM3**.
* Dans le groupe de ressources **CreateIntLBQS-rg**.
* Dans le réseau virtuel **myVNet**.
* Dans le sous-réseau **myBackendSubnet**.
* Dans le groupe de sécurité réseau **myNSG**.

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic create \
        --resource-group CreateIntLBQS-rg \
        --name $vmnic \
        --vnet-name myVNet \
        --subnet myBackEndSubnet \
        --network-security-group myNSG
  done
```

### <a name="create-virtual-machines"></a>Créer des machines virtuelles

Créez les machines virtuelles avec la commande [az vm create](/cli/azure/vm#az-vm-create) :

* Nommées **myVM1**, **myVM2** et **myVM3**.
* Dans le groupe de ressources **CreateIntLBQS-rg**.
* Attachées à l’interface réseau **myNicVM1**, **myNicVM2** et **myNicVM3**.
* Image de machine virtuelle **win2019datacenter**.
* Dans **Zone 1**, **Zone 2** et **Zone 3**.

```azurecli-interactive
  array=(1 2 3)
  for n in "${array[@]}"
  do
    az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM$n \
    --nics myNicVM$n \
    --image win2019datacenter \
    --admin-username azureuser \
    --zone $n \
    --no-wait
  done
```

Le déploiement des machines virtuelles peut prendre quelques minutes.

## <a name="create-standard-load-balancer"></a>Créer un équilibreur de charge standard

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
* Associé au sous-réseau de back-end **myBackendSubnet**.

```azurecli-interactive
  az network lb create \
    --resource-group CreateIntLBQS-rg \
    --name myLoadBalancer \
    --sku Standard \
    --vnet-name myVnet \
    --subnet myBackendSubnet \
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
    --resource-group CreateIntLBQS-rg \
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
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --disable-outbound-snat true \
    --idle-timeout 15 \
    --enable-tcp-reset true
```
>[!NOTE]
>Avec cette configuration, les machines virtuelles du pool principal ne disposent pas d’une connectivité Internet sortante. </br> Pour plus d’informations sur la façon de fournir une connectivité sortante, consultez : </br> **[Connexions sortantes dans Azure](load-balancer-outbound-connections.md)**</br> Options pour fournir la connectivité : </br> **[Configuration de l’équilibreur de charge en sortie uniquement](egress-only.md)** </br> **[Qu’est-ce que le service NAT de Réseau virtuel ?](../virtual-network/nat-overview.md)**

### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>Ajouter des machines virtuelles au pool de back-ends de l’équilibreur de charge

Ajoutez les machines virtuelles au pool de back-ends avec la commande [az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool#az-network-nic-ip-config-address-pool-add) :

* Dans le pool d’adresses de back-end **myBackEndPool**.
* Dans le groupe de ressources **CreateIntLBQS-rg**.
* Associées à l’interface réseau **myNicVM1**, **myNicVM2** et **myNicVM3**.
* Associée à l’équilibreur de charge **myLoadBalancer**.

```azurecli-interactive
  array=(VM1 VM2 VM3)
  for vm in "${array[@]}"
  do
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNic$vm \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
  done

```

# <a name="basic-sku"></a>[**Référence De base**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>Il est recommandé de disposer d’un équilibreur de charge de référence SKU Standard pour les charges de travail de production. Pour plus d’informations sur les références (SKU), consultez **[Références SKU d’Azure Load Balancer](skus.md)** .

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal-basic.png" alt-text="Ressources de l’équilibreur de charge de base créées dans le guide de démarrage rapide." border="false":::

## <a name="configure-virtual-network---basic"></a>Configurer un réseau virtuel - De base

Avant de déployer des machines virtuelles et votre équilibreur de charge, créez les ressources de réseau virtuel associées.

### <a name="create-a-virtual-network"></a>Créez un réseau virtuel

Créez un réseau virtuel avec la commande [az network vnet create](/cli/azure/network/vnet#az-network-vnet-createt) :

* Nommé **myVNet**.
* Préfixe d’adresse **10.1.0.0/16**.
* Sous-réseau nommé **myBackendSubnet**.
* Préfixe du sous-réseau **10.1.0.0/24**.
* Dans le groupe de ressources **CreateIntLBQS-rg**.
* Emplacement **eastus**.

```azurecli-interactive
  az network vnet create \
    --resource-group CreateIntLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

### <a name="create-a-public-ip-address"></a>Créer une adresse IP publique

Utilisez [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create) pour créer une adresse IP publique pour l’hôte bastion :

* Créez une adresse IP publique redondante de zone standard nommée **myBastionIP**.
* Dans **CreateIntLBQS-rg**.

```azurecli-interactive
az network public-ip create \
    --resource-group CreateIntLBQS-rg \
    --name myBastionIP \
    --sku Standard
```
### <a name="create-a-bastion-subnet"></a>Créer un sous-réseau bastion

Utilisez [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) pour créer un sous-réseau bastion :

* Nommé **AzureBastionSubnet**.
* Préfixe d’adresse **10.1.1.0/24**.
* Dans le réseau virtuel **myVNet**.
* Dans le groupe de ressources **CreateIntLBQS-rg**.

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateIntLBQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

### <a name="create-bastion-host"></a>Créer un hôte bastion

Utilisez [az network bastion create](/cli/azure/network/bastion#az-network-bastion-create) pour créer un hôte bastion :

* Nom : **myBastionHost**
* Dans **CreateIntLBQS-rg**.
* Associé à l’adresse IP publique **myBastionIP**.
* Associé au réseau virtuel **myVNet**.
* À l’emplacement **eastus**.

```azurecli-interactive
az network bastion create \
    --resource-group CreateIntLBQS-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Le déploiement de l’hôte Azure Bastion peut prendre quelques minutes.

### <a name="create-a-network-security-group"></a>Créer un groupe de sécurité réseau

Pour un équilibreur de charge standard, les machines virtuelles à l’adresse principale doivent disposer d’interfaces réseau appartenant à un groupe de sécurité réseau. 

Créez un Groupe de sécurité réseau avec la commande [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create) :

* Nommé **myNSG**.
* Dans le groupe de ressources **CreateIntLBQS-rg**.

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Créer une règle de groupe de sécurité réseau

Créez une règle de groupe de sécurité réseau avec la commande [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) :

* Nommé **myNSGRuleHTTP**.
* Dans le groupe de sécurité réseau que vous avez créé à l’étape précédente, **myNSG**.
* Dans le groupe de ressources **CreateIntLBQS-rg**.
* Protocole **(*)** .
* Direction **entrante**.
* Source **(*)** .
* Destination **(*)** .
* Port de destination **Port 80**.
* Accès **Autoriser**.
* Priorité **200**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreateIntLBQS-rg \
    --nsg-name myNSG \
    --name myNSGRuleHTTP \
    --protocol '*' \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```

## <a name="create-backend-servers---basic"></a>Créer des serveurs back-end - De base

Dans cette section, vous allez créer :

* Trois interfaces réseau pour les machines virtuelles.
* Un groupe à haute disponibilité pour les machines virtuelles.
* Trois machines virtuelles à utiliser en tant que serveurs principaux pour l’équilibreur de charge.

### <a name="create-network-interfaces-for-the-virtual-machines"></a>Créer des interfaces réseau pour les machines virtuelles

Créez les interfaces réseau avec la commande [az network nic create](/cli/azure/network/nic#az-network-nic-create) :

* Nommées **myNicVM1**, **myNicVM2** et **myNicVM3**.
* Dans le groupe de ressources **CreateIntLBQS-rg**.
* Dans le réseau virtuel **myVNet**.
* Dans le sous-réseau **myBackendSubnet**.
* Dans le groupe de sécurité réseau **myNSG**.

```azurecli-interactive
  array=(myNicVM1 myNicVM2 myNicVM3)
  for vmnic in "${array[@]}"
  do
    az network nic create \
        --resource-group CreateIntLBQS-rg \
        --name $vmnic \
        --vnet-name myVNet \
        --subnet myBackEndSubnet \
        --network-security-group myNSG
  done
```

### <a name="create-availability-set-for-virtual-machines"></a>Créer un groupe à haute disponibilité pour des machines virtuelles

Créez le groupe à haute disponibilité avec la commande [az vm availability-set create](/cli/azure/vm/availability-set#az-vm-availability-set-create) :

* Nommé **myAvailabilitySet**.
* Dans le groupe de ressources **CreateIntLBQS-rg**.
* Emplacement **eastus**.

```azurecli-interactive
  az vm availability-set create \
    --name myAvailabilitySet \
    --resource-group CreateIntLBQS-rg \
    --location eastus 
    
```

### <a name="create-virtual-machines"></a>Créer des machines virtuelles

Créez les machines virtuelles avec la commande [az vm create](/cli/azure/vm#az-vm-create) :

* Nommées **myVM1**, **myVM2** et **myVM3**.
* Dans le groupe de ressources **CreateIntLBQS-rg**.
* Attachées à l’interface réseau **myNicVM1**, **myNicVM2** et **myNicVM3**.
* Image de machine virtuelle **win2019datacenter**.
* Dans **myAvailabilitySet**.


```azurecli-interactive
  array=(1 2 3)
  for n in "${array[@]}"
  do
    az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM$n \
    --nics myNicVM$n \
    --image win2019datacenter \
    --admin-username azureuser \
    --availability-set myAvailabilitySet \
    --no-wait
  done
```
Le déploiement des machines virtuelles peut prendre quelques minutes.

## <a name="create-basic-load-balancer"></a>Créer un équilibreur de charge de base

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
* Associé au sous-réseau de back-end **myBackendSubnet**.

```azurecli-interactive
  az network lb create \
    --resource-group CreateIntLBQS-rg \
    --name myLoadBalancer \
    --sku Basic \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
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
    --resource-group CreateIntLBQS-rg \
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

```azurecli-interactive
  az network lb rule create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --idle-timeout 15 
```
### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>Ajouter des machines virtuelles au pool de back-ends de l’équilibreur de charge

Ajoutez les machines virtuelles au pool de back-ends avec la commande [az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool#az-network-nic-ip-config-address-pool-add) :

* Dans le pool d’adresses de back-end **myBackEndPool**.
* Dans le groupe de ressources **CreateIntLBQS-rg**.
* Associées à l’interface réseau **myNicVM1**, **myNicVM2** et **myNicVM3**.
* Associée à l’équilibreur de charge **myLoadBalancer**.

```azurecli-interactive
  array=(VM1 VM2 VM3)
  for vm in "${array[@]}"
  do
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNic$vm \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
  done

```
---

## <a name="test-the-load-balancer"></a>Tester l’équilibreur de charge

### <a name="create-test-virtual-machine"></a>Créer une machine virtuelle de test

Créez l’interface réseau avec la commande [az network nic create](/cli/azure/network/nic#az-network-nic-create) :

* Nommée **myNicTestVM**.
* Dans le groupe de ressources **CreateIntLBQS-rg**.
* Dans le réseau virtuel **myVNet**.
* Dans le sous-réseau **myBackendSubnet**.
* Dans le groupe de sécurité réseau **myNSG**.

```azurecli-interactive
  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicTestVM \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
Créez la machine virtuelle avec la commande [az vm create](/cli/azure/vm#az-vm-create) :

* Nommé **myTestVM**.
* Dans le groupe de ressources **CreateIntLBQS-rg**.
* Attachée à l’interface réseau **myNicTestVM**.
* Image de machine virtuelle **Win2019Datacenter**.

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myTestVM \
    --nics myNicTestVM \
    --image Win2019Datacenter \
    --admin-username azureuser \
    --no-wait
```
Le déploiement de la machine virtuelle peut prendre quelques minutes.

## <a name="install-iis"></a>Installer IIS

Utilisez [AZ VM Extension Set](/cli/azure/vm/extension#az_vm_extension_set) pour installer IIS sur les machines virtuelles et définir le nom de l’ordinateur comme site web par défaut.

```azurecli-interactive
  array=(myVM1 myVM2 myVM3)
    for vm in "${array[@]}"
    do
     az vm extension set \
       --publisher Microsoft.Compute \
       --version 1.8 \
       --name CustomScriptExtension \
       --vm-name $vm \
       --resource-group CreateIntLBQS-rg \
       --settings '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}'
  done

```

### <a name="test"></a>Test

1. [Connectez-vous](https://portal.azure.com) au portail Azure.

2. Recherchez l’adresse IP privée de l’équilibreur de charge sur l’écran **Vue d’ensemble**. Sélectionnez **Tous les services** dans le menu de gauche, sélectionnez **Toutes les ressources**, puis sélectionnez **myLoadBalancer**.

3. Notez ou copiez l’adresse en regard de **Adresse IP privée** dans la **Vue d’ensemble** de **myLoadBalancer**.

4. Sélectionnez **Tous les services** dans le menu de gauche, sélectionnez **Toutes les ressources** puis, dans la liste des ressources, sélectionnez **myTestVM** qui se trouve dans le groupe de ressources **CreateIntLBQS-rg**.

5. Dans la page **Vue d’ensemble**, sélectionnez **Se connecter**, puis **Bastion**.

6. Entrez le nom d’utilisateur et le mot de passe saisis pendant la création de la machine virtuelle.

7. Ouvrez **Internet Explorer** sur **myTestVM**.

8. Entrez l’adresse IP de l’étape précédente dans la barre d’adresse du navigateur. La page par défaut du serveur Web IIS s’affiche sur le navigateur.

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/load-balancer-test.png" alt-text="Créer un équilibreur de charge interne standard" border="true":::
   
Pour voir l’équilibreur de charge répartir le trafic entre les trois machines virtuelles, vous pouvez personnaliser la page par défaut du serveur web IIS de chaque machine virtuelle, puis forcer votre navigateur à s’actualiser à partir de la machine cliente.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous n’en avez plus besoin, utilisez la commande [az group delete](/cli/azure/group#az-group-delete) pour supprimer le groupe de ressources, l’équilibreur de charge et toutes les ressources associées.

```azurecli-interactive
  az group delete \
    --name CreateIntLBQS-rg
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide :

* Vous avez créé un équilibreur de charge standard ou public
* Attaché des machines virtuelles. 
* Vous avez configuré la règle de trafic et la sonde d’intégrité de l’équilibreur de charge.
* Vous avez testé l’équilibreur de charge.

Pour en savoir plus sur Azure Load Balancer, consultez :
> [!div class="nextstepaction"]
> [Qu’est-ce que Azure Load Balancer ?](load-balancer-overview.md)