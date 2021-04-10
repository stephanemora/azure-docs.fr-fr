---
title: Configurer la préférence de routage d’un service Azure Kubernetes à l’aide d’Azure CLI
titlesuffix: Azure Virtual Network
description: Découvrez comment configurer la préférence de routage d’un cluster AKS à l’aide de l’interface Azure CLI.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2021
ms.author: mnayak
ms.openlocfilehash: ac70f48a3c484f8865c54e09c59662a14a259e74
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101678429"
---
# <a name="configure-routing-preference-for-a-kubernetes-cluster-using-azure-cli"></a>Configurer la préférence de routage d’un cluster Kubernetes à l’aide d’Azure CLI

Cet article montre comment configurer une préférence de routage via un réseau de fournisseur de services Internet (option **Internet**) pour un cluster Kubernetes en utilisant Azure CLI. La préférence de routage est définie par la création d’une adresse IP publique de type préférence de routage **Internet****, puis par son utilisation lors de la création du cluster AKS.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Cet article demande la version 2.0.49 ou ultérieure d’Azure CLI. Si vous utilisez Azure Cloud Shell, la version la plus récente est déjà installée.

## <a name="create-a-resource-group"></a>Créer un groupe de ressources
Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#az-group-create). L’exemple suivant crée un groupe de ressources dans la région Azure **USA Est** :

```azurecli
  az group create --name myResourceGroup --location eastus
```
## <a name="create-a-public-ip-address"></a>Créer une adresse IP publique

Créez une adresse IP publique avec la préférence de routage de type **Internet** au moyen de la commande [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create).

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

## <a name="get-the-id-of-public-ip-address"></a>Obtenir l’ID d’une adresse IP publique

La commande suivante retourne l’ID de l’adresse IP publique créée à la section précédente :
```azurecli
az network public-ip show \
--resource-group myResourceGroup \
--name myRoutingPrefIP \
--query id
```
## <a name="create-kubernetes-cluster-with-the-public-ip"></a>Créer un cluster Kubernetes avec l’IP publique

La commande suivante crée le cluster AKS avec l’IP publique créée à la section précédente :

```azurecli
az aks create \
--resource-group MyResourceGroup \
--name MyAKSCluster \
--load-balancer-outbound-ips "Enter the public IP ID from previous step" --generate-ssh-key
```

>[!NOTE]
>Le déploiement du cluster AKS ne prend que quelques minutes.

Pour la validation, recherchez l’adresse IP publique créée à l’étape précédente dans le portail Azure ; vous constaterez que l’IP est associée à l’équilibreur de charge qui est lié au cluster Kubernetes, comme illustré ci-dessous :

 ![Adresse IP publique de la préférence de routage pour Kubernetes](./media/routing-preference-azure-kubernetes-service-cli/routing-preference-azure-kubernetes-service.png)


## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur la [préférence de routage dans les adresses IP publiques](routing-preference-overview.md). 
- [Configurer la préférence de routage pour une machine virtuelle à l’aide d’Azure CLI](configure-routing-preference-virtual-machine-cli.md).

