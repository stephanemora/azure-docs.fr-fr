---
title: 'Démarrage rapide : Créer un équilibreur de charge interne - Azure CLI'
titleSuffix: Azure Load Balancer
description: Ce démarrage rapide montre comment créer un équilibreur de charge interne en utilisant l’interface de ligne de commande Azure.
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/19/2020
ms.author: allensu
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: f728e1f1e2186188135666ed54e02c9ed3507509
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106056536"
---
# <a name="quickstart-create-an-internal-load-balancer-by-using-the-azure-cli"></a>Démarrage rapide : Créer un équilibreur de charge interne à l’aide de l’interface de ligne de commande Azure

Démarrez avec Azure Load Balancer en utilisant l’interface de ligne de commande Azure pour créer un équilibreur de charge interne et trois machines virtuelles.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)] 

Ce guide de démarrage rapide nécessite la version 2.0.28 ou ultérieure de l’interface Azure CLI. Si vous utilisez Azure Cloud Shell, sachez que la version la plus récente est déjà installée.

>[!NOTE]
>Azure Load Balancer Standard est l’option recommandée pour les charges de travail de production. Cet article contient des informations sur Azure Load Balancer Standard, ainsi que sur Azure Load Balancer De base. Pour plus d’informations sur les SKU, consultez [SKU d’Azure Load Balancer](skus.md).

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Un groupe de ressources Azure est un conteneur logique dans lequel vous déployez et gérez vos ressources Azure.

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#az_group_create). Nommez le groupe de ressources **CreateIntLBQS-rg**, puis spécifiez la région **eastus**.

```azurecli-interactive
  az group create \
    --name CreateIntLBQS-rg \
    --location eastus

```

## <a name="azure-load-balancer-standard"></a>Azure Load Balancer Standard

Dans cette section, vous créez un équilibreur de charge qui équilibre la charge des machines virtuelles. Quand vous créez un équilibreur de charge interne, un réseau virtuel est configuré comme réseau pour l’équilibreur de charge. Le diagramme suivant illustre les ressources créées dans ce guide de démarrage rapide :

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal.png" alt-text="Ressources Standard Load Balancer créées pour le guide de démarrage rapide." border="false":::

### <a name="configure-the-virtual-network"></a>Configurer le réseau virtuel

Avant de déployer des machines virtuelles et votre équilibreur de charge, créez les ressources de réseau virtuel associées.

#### <a name="create-a-virtual-network"></a>Créez un réseau virtuel

Créez un réseau virtuel à l’aide de la commande [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create). Spécifiez les éléments suivants :

* Nommé **myVNet**
* Préfixe d’adresse **10.1.0.0/16**
* Sous-réseau nommé **myBackendSubnet**
* Préfixe du sous-réseau **10.1.0.0/24**
* Dans le groupe de ressources **CreateIntLBQS-rg**
* Région **eastus**

```azurecli-interactive
  az network vnet create \
    --resource-group CreateIntLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

#### <a name="create-a-public-ip-address"></a>Créer une adresse IP publique

Utilisez [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create) pour créer une IP publique pour l’hôte Azure Bastion. Spécifiez les éléments suivants :

* Créez une IP publique redondante interzone standard nommée **myBastionIP**
* Dans **CreateIntLBQS-rg**

```azurecli-interactive
az network public-ip create \
    --resource-group CreateIntLBQS-rg  \
    --name myBastionIP \
    --sku Standard
```
#### <a name="create-an-azure-bastion-subnet"></a>Créer un sous-réseau Azure Bastion

Utilisez [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) pour créer un sous-réseau. Spécifiez les éléments suivants :

* Nommé **AzureBastionSubnet**
* Préfixe d’adresse **10.1.1.0/24**
* Dans le réseau virtuel **myVNet**
* Dans le groupe de ressources **CreateIntLBQS-rg**

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateIntLBQS-rg  \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

#### <a name="create-an-azure-bastion-host"></a>Créer un hôte Azure Bastion

Utilisez [az network bastion create](/cli/azure/network/bastion#az-network-bastion-create) pour créer un hôte. Spécifiez les éléments suivants :

* Nommé **myBastionHost**
* Dans **CreateIntLBQS-rg**
* Associé à l’IP publique **myBastionIP**
* Associé au réseau virtuel **myVNet**
* Dans la région **eastus**

```azurecli-interactive
az network bastion create \
    --resource-group CreateIntLBQS-rg  \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Le déploiement de l’hôte Azure Bastion peut prendre quelques minutes.

#### <a name="create-a-network-security-group"></a>Créer un groupe de sécurité réseau

Pour un équilibreur de charge standard, assurez-vous que vos machines virtuelles disposent d’interfaces réseau appartenant à un groupe de sécurité réseau. Créez un groupe de sécurité réseau à l’aide de la commande [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create). Spécifiez les éléments suivants :

* Nommé **myNSG**
* Dans le groupe de ressources **CreateIntLBQS-rg**

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

#### <a name="create-a-network-security-group-rule"></a>Créer une règle de groupe de sécurité réseau

Créez une règle de groupe de sécurité réseau à l’aide de la commande [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create). Spécifiez les éléments suivants :

* Nommée **myNSGRuleHTTP**
* Dans le groupe de sécurité réseau que vous avez créé à l’étape précédente, **myNSG**
* Dans le groupe de ressources **CreateIntLBQS-rg**
* Protocole **(*)**
* Direction **Inbound**
* Source **(*)**
* Destination **(*)**
* Port de destination **Port 80**
* Accès **Allow**
* Priorité **200**

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

### <a name="create-back-end-servers"></a>Créer des serveurs principaux

Dans cette section, vous allez créer :

* Trois interfaces réseau pour les machines virtuelles.
* Trois machines virtuelles à utiliser en tant que serveurs pour l’équilibreur de charge.

#### <a name="create-network-interfaces-for-the-virtual-machines"></a>Créer des interfaces réseau pour les machines virtuelles

Créez trois interfaces réseau à l’aide de la commande [az network nic create](/cli/azure/network/nic#az-network-nic-create). Spécifiez les éléments suivants :

* Nommées **myNicVM1**, **myNicVM2** et **myNicVM3**
* Dans le groupe de ressources **CreateIntLBQS-rg**
* Dans le réseau virtuel **myVNet**
* Dans le sous-réseau **myBackendSubnet**
* Dans le groupe de sécurité réseau **myNSG**

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

#### <a name="create-the-virtual-machines"></a>Créer les machines virtuelles

Créez les machines virtuelles avec la commande [az vm create](/cli/azure/vm#az-vm-create). Spécifiez les éléments suivants :

* Nommées **myVM1**, **myVM2** et **myVM3**
* Dans le groupe de ressources **CreateIntLBQS-rg**
* Attachées à l’interface réseau **myNicVM1**, **myNicVM2** et **myNicVM3**
* Image de machine virtuelle **win2019datacenter**
* Dans **Zone 1**, **Zone 2** et **Zone 3**

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

### <a name="create-the-load-balancer"></a>Créer l’équilibreur de charge

Cette section explique en détail comment vous pouvez créer et configurer les composants suivants de l’équilibreur de charge :

* Un pool d’adresses IP qui reçoit le trafic entrant sur l’équilibreur de charge.
* Un second pool d’adresses IP auquel le premier pool envoie le trafic dont la charge a été équilibrée.
* Une sonde d’intégrité qui détermine l’intégrité des instances de machine virtuelle.
* Une règle d’équilibreur de charge qui définit la distribution du trafic aux machines virtuelles.

#### <a name="create-the-load-balancer-resource"></a>Créer la ressource d’équilibreur de charge

Créez un équilibreur de charge public avec la commande [az network lb create](/cli/azure/network/lb#az-network-lb-create). Spécifiez les éléments suivants :

* Nommé **myLoadBalancer**.
* Un pool nommé **myFrontEnd**
* Un pool nommé **myBackEndPool**
* Associé au réseau virtuel **myVNet**
* Associé au sous-réseau **myBackendSubnet**

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

#### <a name="create-the-health-probe"></a>Créer la sonde d’intégrité

Une sonde d’intégrité vérifie toutes les instances de machine virtuelle pour s’assurer qu’elles peuvent transmettre du trafic réseau. Une machine virtuelle dont le contrôle de sonde a échoué est supprimée de l’équilibreur de charge. La machine virtuelle est rajoutée à l’équilibreur de charge une fois l’échec résolu.

Créez la sonde d’intégrité avec la commande [az network lb probe create](/cli/azure/network/lb/probe#az-network-lb-probe-create). Spécifiez les éléments suivants :

* Analyse l’intégrité des machines virtuelles
* Nommée **myHealthProbe**
* Protocole **TCP**
* Surveillant le **port 80**

```azurecli-interactive
  az network lb probe create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

#### <a name="create-a-load-balancer-rule"></a>Créer une règle d’équilibreur de charge

Une règle d’équilibreur de charge définit les éléments suivants :

* La configuration IP du trafic entrant.
* Le pool d’adresses IP qui reçoit le trafic.
* Les ports source et de destination requis. 

Utilisez [az network lb rule create](/cli/azure/network/lb/rule#az-network-lb-rule-create) pour créer une règle d’équilibrage de charge. Spécifiez les éléments suivants :

* Nommée **myHTTPRule**.
* Écoutant le **port 80** dans le pool **myFrontEnd**
* Envoyant le trafic dont la charge est équilibrée au pool d’adresses **myBackEndPool** à l’aide du **port 80** 
* Utilisant la sonde d’intégrité **myHealthProbe**
* Protocole **TCP**
* Délai d’inactivité de **15 minutes**
* Activez la réinitialisation TCP

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
    --idle-timeout 15 \
    --enable-tcp-reset true
```

#### <a name="add-vms-to-the-load-balancer-pool"></a>Ajouter des machines virtuelles au pool de l’équilibreur de charge

Ajoutez les machines virtuelles au pool back-end avec la commande [az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool#az-network-nic-ip-config-address-pool-add). Spécifiez les éléments suivants :

* Dans le pool d’adresses **myBackEndPool**
* Dans le groupe de ressources **CreateIntLBQS-rg**
* Associées à l’interface réseau **myNicVM1**, **myNicVM2** et **myNicVM3**
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

## <a name="azure-load-balancer-basic"></a>Azure Load Balancer De base

Dans cette section, vous créez un équilibreur de charge qui équilibre la charge des machines virtuelles. Quand vous créez un équilibreur de charge interne, un réseau virtuel est configuré comme réseau pour l’équilibreur de charge. Le diagramme suivant illustre les ressources créées dans ce guide de démarrage rapide :

:::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/resources-diagram-internal-basic.png" alt-text="Ressources de l’équilibreur de charge De base créées pour le démarrage rapide." border="false":::

### <a name="configure-the-virtual-network"></a>Configurer le réseau virtuel

Avant de déployer des machines virtuelles et votre équilibreur de charge, créez les ressources de réseau virtuel associées.

#### <a name="create-a-virtual-network"></a>Créez un réseau virtuel

Créez un réseau virtuel à l’aide de la commande [az network vnet create](/cli/azure/network/vnet#az-network-vnet-createt). Spécifiez les éléments suivants :

* Nommé **myVNet**
* Préfixe d’adresse **10.1.0.0/16**
* Sous-réseau nommé **myBackendSubnet**
* Préfixe du sous-réseau **10.1.0.0/24**
* Dans le groupe de ressources **CreateIntLBQS-rg**
* Région **eastus**

```azurecli-interactive
  az network vnet create \
    --resource-group CreateIntLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```

#### <a name="create-a-public-ip-address"></a>Créer une adresse IP publique

Utilisez [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create) pour créer une IP publique pour l’hôte Azure Bastion. Spécifiez les éléments suivants :

* Créez une IP publique redondante interzone standard nommée **myBastionIP**
* Dans **CreateIntLBQS-rg**

```azurecli-interactive
az network public-ip create \
    --resource-group CreateIntLBQS-rg \
    --name myBastionIP \
    --sku Standard
```
#### <a name="create-an-azure-bastion-subnet"></a>Créer un sous-réseau Azure Bastion

Utilisez [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) pour créer un sous-réseau. Spécifiez les éléments suivants :

* Nommé **AzureBastionSubnet**
* Préfixe d’adresse **10.1.1.0/24**
* Dans le réseau virtuel **myVNet**
* Dans le groupe de ressources **CreateIntLBQS-rg**

```azurecli-interactive
az network vnet subnet create \
    --resource-group CreateIntLBQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

#### <a name="create-an-azure-bastion-host"></a>Créer un hôte Azure Bastion

Utilisez [az network bastion create](/cli/azure/network/bastion#az-network-bastion-create) pour créer un hôte. Spécifiez les éléments suivants :

* Nommé **myBastionHost**
* Dans **CreateIntLBQS-rg**
* Associé à l’IP publique **myBastionIP**
* Associé au réseau virtuel **myVNet**
* Dans la région **eastus**

```azurecli-interactive
az network bastion create \
    --resource-group CreateIntLBQS-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```

Le déploiement de l’hôte Azure Bastion peut prendre quelques minutes.

#### <a name="create-a-network-security-group"></a>Créer un groupe de sécurité réseau

Pour un équilibreur de charge standard, assurez-vous que vos machines virtuelles disposent d’interfaces réseau appartenant à un groupe de sécurité réseau. Créez un groupe de sécurité réseau à l’aide de la commande [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create). Spécifiez les éléments suivants :

* Nommé **myNSG**
* Dans le groupe de ressources **CreateIntLBQS-rg**

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

#### <a name="create-a-network-security-group-rule"></a>Créer une règle de groupe de sécurité réseau

Créez une règle de groupe de sécurité réseau à l’aide de la commande [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create). Spécifiez les éléments suivants :

* Nommée **myNSGRuleHTTP**
* Dans le groupe de sécurité réseau que vous avez créé à l’étape précédente, **myNSG**
* Dans le groupe de ressources **CreateIntLBQS-rg**
* Protocole **(*)**
* Direction **Inbound**
* Source **(*)**
* Destination **(*)**
* Port de destination **Port 80**
* Accès **Allow**
* Priorité **200**

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

### <a name="create-back-end-servers"></a>Créer des serveurs principaux

Dans cette section, vous allez créer :

* Trois interfaces réseau pour les machines virtuelles.
* Le groupe à haute disponibilité pour les machines virtuelles.
* Trois machines virtuelles à utiliser en tant que serveurs pour l’équilibreur de charge.

#### <a name="create-network-interfaces-for-the-virtual-machines"></a>Créer des interfaces réseau pour les machines virtuelles

Créez trois interfaces réseau à l’aide de la commande [az network nic create](/cli/azure/network/nic#az-network-nic-create). Spécifiez les éléments suivants :

* Nommées **myNicVM1**, **myNicVM2** et **myNicVM3**
* Dans le groupe de ressources **CreateIntLBQS-rg**
* Dans le réseau virtuel **myVNet**
* Dans le sous-réseau **myBackendSubnet**
* Dans le groupe de sécurité réseau **myNSG**

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

#### <a name="create-the-availability-set-for-the-virtual-machines"></a>Créer le groupe à haute disponibilité pour les machines virtuelles

Créez le groupe à haute disponibilité avec la commande [az vm availability-set create](/cli/azure/vm/availability-set#az-vm-availability-set-create). Spécifiez les éléments suivants :

* Nommé **myAvailabilitySet**
* Dans le groupe de ressources **CreateIntLBQS-rg**
* Région **eastus**

```azurecli-interactive
  az vm availability-set create \
    --name myAvailabilitySet \
    --resource-group CreateIntLBQS-rg \
    --location eastus 
    
```

#### <a name="create-the-virtual-machines"></a>Créer les machines virtuelles

Créez les machines virtuelles avec la commande [az vm create](/cli/azure/vm#az-vm-create). Spécifiez les éléments suivants :

* Nommées **myVM1**, **myVM2** et **myVM3**
* Dans le groupe de ressources **CreateIntLBQS-rg**
* Attachées à l’interface réseau **myNicVM1**, **myNicVM2** et **myNicVM3**
* Image de machine virtuelle **win2019datacenter**
* Dans **myAvailabilitySet**


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

### <a name="create-the-load-balancer"></a>Créer l’équilibreur de charge

Cette section explique en détail comment vous pouvez créer et configurer les composants suivants de l’équilibreur de charge :

* Un pool d’adresses IP qui reçoit le trafic entrant sur l’équilibreur de charge.
* Un second pool d’adresses IP auquel le premier pool envoie le trafic dont la charge a été équilibrée.
* Une sonde d’intégrité qui détermine l’intégrité des instances de machine virtuelle.
* Une règle d’équilibreur de charge qui définit la distribution du trafic aux machines virtuelles.

#### <a name="create-the-load-balancer-resource"></a>Créer la ressource d’équilibreur de charge

Créez un équilibreur de charge public avec la commande [az network lb create](/cli/azure/network/lb#az-network-lb-create). Spécifiez les éléments suivants :

* Nommé **myLoadBalancer**.
* Un pool nommé **myFrontEnd**
* Un pool nommé **myBackEndPool**
* Associé au réseau virtuel **myVNet**
* Associé au sous-réseau **myBackendSubnet**

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

#### <a name="create-the-health-probe"></a>Créer la sonde d’intégrité

Une sonde d’intégrité vérifie toutes les instances de machine virtuelle pour s’assurer qu’elles peuvent transmettre du trafic réseau. Une machine virtuelle dont le contrôle de sonde a échoué est supprimée de l’équilibreur de charge. La machine virtuelle est rajoutée à l’équilibreur de charge une fois l’échec résolu.

Créez la sonde d’intégrité avec la commande [az network lb probe create](/cli/azure/network/lb/probe#az-network-lb-probe-create). Spécifiez les éléments suivants :

* Analyse l’intégrité des machines virtuelles
* Nommée **myHealthProbe**
* Protocole **TCP**
* Surveillant le **port 80**

```azurecli-interactive
  az network lb probe create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

#### <a name="create-a-load-balancer-rule"></a>Créer une règle d’équilibreur de charge

Une règle d’équilibreur de charge définit les éléments suivants :

* La configuration IP du trafic entrant.
* Le pool d’adresses IP qui reçoit le trafic.
* Les ports source et de destination requis. 

Utilisez [az network lb rule create](/cli/azure/network/lb/rule#az-network-lb-rule-create) pour créer une règle d’équilibrage de charge. Spécifiez les éléments suivants :

* Nommée **myHTTPRule**.
* Écoutant le **port 80** dans le pool **myFrontEnd**
* Envoyant le trafic dont la charge est équilibrée au pool d’adresses **myBackEndPool** à l’aide du **port 80** 
* Utilisant la sonde d’intégrité **myHealthProbe**
* Protocole **TCP**
* Délai d’inactivité de **15 minutes**

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
#### <a name="add-vms-to-the-load-balancer-pool"></a>Ajouter des machines virtuelles au pool de l’équilibreur de charge

Ajoutez les machines virtuelles au pool back-end avec la commande [az network nic ip-config address-pool add](/cli/azure/network/nic/ip-config/address-pool#az-network-nic-ip-config-address-pool-add). Spécifiez les éléments suivants :

* Dans le pool d’adresses **myBackEndPool**
* Dans le groupe de ressources **CreateIntLBQS-rg**
* Associées à l’interface réseau **myNicVM1**, **myNicVM2** et **myNicVM3**
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

## <a name="test-the-load-balancer"></a>Tester l’équilibreur de charge

Créez l’interface réseau avec la commande [az network nic create](/cli/azure/network/nic#az-network-nic-create). Spécifiez les éléments suivants :

* Nommée **myNicTestVM**
* Dans le groupe de ressources **CreateIntLBQS-rg**
* Dans le réseau virtuel **myVNet**
* Dans le sous-réseau **myBackendSubnet**
* Dans le groupe de sécurité réseau **myNSG**

```azurecli-interactive
  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicTestVM \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
Créez la machine virtuelle avec la commande [az vm create](/cli/azure/vm#az-vm-create). Spécifiez les éléments suivants :

* Nommée **myTestVM**
* Dans le groupe de ressources **CreateIntLBQS-rg**
* Attachée à l’interface réseau **myNicTestVM**
* Image de machine virtuelle **Win2019Datacenter**

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

2. Sur la page **Vue d’ensemble**, recherchez l’adresse IP privée de l’équilibreur de charge. Dans le menu de gauche, sélectionnez **Tous les services** > **Toutes les ressources** > **myLoadBalancer**.

3. Dans la vue d’ensemble de **myLoadBalancer**, copiez l’adresse à côté d’**Adresse IP privée**.

4. Dans le menu de gauche, sélectionnez **Tous les services** > **Toutes les ressources**. Dans la liste des ressources, dans le groupe de ressources **CreateIntLBQS-rg**, sélectionnez **myTestVM**.

5. Sur la page **Vue d’ensemble**, sélectionnez **Se connecter** > **Bastion**.

6. Entrez le nom d’utilisateur et le mot de passe que vous avez saisis lors de la création de la machine virtuelle.

7. Sur **myTestVM**, ouvrez **Internet Explorer**.

8. Entrez l’adresse IP de l’étape précédente dans la barre d’adresse du navigateur. La page par défaut du serveur web IIS s’affiche sur le navigateur.

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/load-balancer-test.png" alt-text="Capture d’écran de l’adresse IP dans la barre d’adresse du navigateur." border="true":::
   
Pour voir l’équilibreur de charge répartir le trafic entre les trois machines virtuelles, vous pouvez personnaliser la page par défaut du serveur web IIS de chaque machine virtuelle. Ensuite, actualisez manuellement votre navigateur web à partir de la machine cliente.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous n’avez plus besoin des ressources, utilisez la commande [az group delete](/cli/azure/group#az-group-delete) pour supprimer le groupe de ressources, l’équilibreur de charge et toutes les ressources associées.

```azurecli-interactive
  az group delete \
    --name CreateIntLBQS-rg
```

## <a name="next-steps"></a>Étapes suivantes

Découvrez une vue d’ensemble d’Azure Load Balancer.
> [!div class="nextstepaction"]
> [Qu’est-ce que Azure Load Balancer ?](load-balancer-overview.md)
