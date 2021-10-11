---
title: 'Tutoriel : Configurer la préférence de routage d’un service Azure Kubernetes – Azure CLI'
titlesuffix: Azure virtual network
description: Utilisez ce tutoriel pour apprendre à configurer la préférence de routage d’un service Azure Kubernetes.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: tutorial
ms.date: 10/01/2021
ms.custom: template-tutorial
ms.openlocfilehash: 277f861b0816b0c7eaf0267fc80f1b15d12509d5
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129368927"
---
# <a name="tutorial-configure-routing-preference-for-an-azure-kubernetes-service-using-the-azure-cli"></a>Tutoriel : Configurer la préférence de routage d’un service Azure Kubernetes à l’aide de l’interface Azure CLI

Cet article montre comment configurer une préférence de routage via un réseau de fournisseur de services Internet (option **Internet**) pour un cluster Kubernetes en utilisant Azure CLI. La préférence de routage est définie par la création d’une adresse IP publique du type de préférence de routage **Internet**, puis par son utilisation lors de la création du cluster AKS.

Dans ce tutoriel, vous apprenez à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer une adresse IP publique dotée de la préférence de routage **Internet**.
> * Créer un cluster Azure Kubernetes avec une IP publique dotée de la préférence de routage **Internet**.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Cet article demande la version 2.0.49 ou ultérieure d’Azure CLI. Si vous utilisez Azure Cloud Shell, la version la plus récente est déjà installée.

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#az_group_create). L’exemple suivant crée un groupe de ressources dans la région Azure **USA Est** :

```azurecli-interactive
  az group create \
    --name TutorAKSRP-rg \
    --location eastus

```

## <a name="create-public-ip-with-internet-routing-preference"></a>Créer une IP publique dotée d’une préférence de routage Internet

Créez une adresse IP publique dotée de la préférence de routage de type **Internet** au moyen de la commande [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create).

La commande suivante crée une adresse IP publique avec la préférence de routage **Internet** dans la région Azure **USA Est**.

```azurecli-interactive
  az network public-ip create \
    --resource-group TutorAKSRP-rg \
    --name myPublicIP-IR \
    --version IPv4 \
    --ip-tags 'RoutingPreference=Internet' \
    --sku Standard \
    --zone 1 2 3
```
> [!NOTE]
>  La préférence de routage ne prend actuellement en charge que les adresses IP publiques IPV4.

## <a name="create-kubernetes-cluster-with-public-ip"></a>Créer un cluster Kubernetes avec l’IP publique

Placez l’ID de l’IP publique créée précédemment dans une variable pour une utilisation ultérieure. Utilisez [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show) pour récupérer l’ID de l’IP publique.

La commande suivante récupère l’ID de l’adresse IP publique et le place dans une variable à utiliser dans la commande suivante.

```azurecli-interactive
  export resourceid=$(az network public-ip show \
    --resource-group TutorAKSRP-rg \
    --name myPublicIP-IR \
    --query id \
    --output tsv)
```

Utilisez [az aks create](/cli/azure/aks#az_aks_create) pour créer le cluster Kubernetes.

La commande suivante crée le cluster Kubernetes et utilise la variable pour l’IP publique créée à l’étape précédente.

```azurecli-interactive
  az aks create \
    --resource-group TutorAKSRP-rg \
    --name MyAKSCluster \
    --load-balancer-outbound-ips $resourceid \
    --generate-ssh-key
```

>[!NOTE]
>Le déploiement du cluster AKS ne prend que quelques minutes.

Pour la validation, recherchez l’adresse IP publique créée à l’étape précédente dans le portail Azure. L’IP publique est associée à l’équilibreur de charge. L’équilibreur de charge est associé au cluster Kubernetes, comme indiqué ci-dessous :

  :::image type="content" source="./media/routing-preference-azure-kubernetes-service-cli/verify-aks-ip.png" alt-text="Capture d’écran de l’adresse IP publique du cluster AKS.":::

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand vous n’en avez plus besoin, utilisez la commande [az group delete](/cli/azure/group#az_group_delete) pour supprimer le groupe de ressources, l’IP publique, le cluster AKS et toutes les ressources associées.

```azurecli-interactive
  az group delete \
    --name TutorAKSRP-rg
```

## <a name="next-steps"></a>Étapes suivantes

Passez à l’article suivant pour apprendre à créer une machine virtuelle avec une préférence de routage mixte :
> [!div class="nextstepaction"]
> [Configurer les deux options de préférence de routage pour une machine virtuelle](routing-preference-mixed-network-adapter-portal.md)