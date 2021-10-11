---
title: 'Tutoriel : Configurer la préférence de routage pour une machine virtuelle – Azure CLI'
description: Dans ce tutoriel, apprenez à créer une machine virtuelle avec une adresse IP publique dotée d’un choix de préférence de routage, à l’aide de l’interface Azure CLI.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: tutorial
ms.date: 10/01/2021
ms.custom: template-tutorial
ms.openlocfilehash: 9428d28eabde40dec7ed2805217fb655d8b0f693
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129369054"
---
# <a name="tutorial-configure-routing-preference-for-a-vm-using-the-azure-cli"></a>Tutoriel : Configurer la préférence de routage pour une machine virtuelle à l’aide de l’interface Azure CLI
Ce tutoriel explique comment configurer la préférence de routage pour une machine virtuelle. Le trafic lié à Internet à partir de la machine virtuelle est routé via le réseau du fournisseur de services Internet lorsque vous choisissez l’option de préférence de routage **Internet**. Le routage par défaut passe par le réseau Microsoft mondial.

Dans ce tutoriel, vous apprenez à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer une adresse IP publique configurée pour la préférence de routage **Internet**.
> * Création d’une machine virtuelle
> * Vérifier que la préférence de routage de l’adresse IP publique est définie sur **Internet**.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Ce tutoriel nécessite l’interface Azure CLI version 2.0.28 ou ultérieure. Si vous utilisez Azure Cloud Shell, la version la plus récente est déjà installée.

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.

À l’aide de la commande [az group create](/cli/azure/group#az_group_create), créez un groupe de ressources nommé **TutorVMRoutePref-rg** dans l’emplacement **westus2**.

```azurecli-interactive
  az group create \
    --name TutorVMRoutePref-rg \
    --location westus2
```

## <a name="create-a-public-ip-address"></a>Créer une adresse IP publique

Utilisez [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) pour créer une adresse IPv4 publique redondante interzone standard, nommée **myPublicIP**, dans **TutorVMRoutePref-rg**. L’**étiquette** de **Internet** est appliquée à l’adresse IP publique sous forme de paramètre dans la commande CLI, ce qui active la préférence de routage **Internet**.

```azurecli-interactive
az network public-ip create \
    --resource-group TutorVMRoutePref-rg \
    --name myPublicIP \
    --version IPv4 \
    --ip-tags 'RoutingPreference=Internet' \
    --sku Standard \
    --zone 1 2 3
```

## <a name="create-virtual-machine"></a>Créer une machine virtuelle

Utilisez [az vm create](/cli/azure/vm#az_vm_create) pour créer une machine virtuelle. L’adresse IP publique créée à la section précédente est ajoutée à la commande CLI et attachée à la machine virtuelle lors de la création.

```azurecli-interactive
az vm create \
--name myVM \
--resource-group TutorVMRoutePref-rg \
--public-ip-address myPublicIP \
--size Standard_A2 \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest \
--admin-username azureuser
```

## <a name="verify-internet-routing-preference"></a>Vérifier la préférence de routage Internet

Utilisez [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show) afin de vérifier que la préférence de routage **Internet** est configurée pour l’adresse IP publique.

```azurecli-interactive
az network public-ip show \
    --resource-group TutorVMRoutePref-rg \
    --name myPublicIP \
    --query ipTags \
    --output tsv
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous n’avez plus besoin de la machines virtuelle ni de l’adresse IP publique, supprimez le groupe de ressources et toutes les ressources qu’il contient à l’aide de [az group delete](/cli/azure/group#az_group_delete).

```azurecli-interactive
  az group delete \
    --name TutorVMRoutePref-rg
```

## <a name="next-steps"></a>Étapes suivantes

Passez à l’article suivant pour apprendre à créer une machine virtuelle avec une préférence de routage mixte :
> [!div class="nextstepaction"]
> [Configurer les deux options de préférence de routage pour une machine virtuelle](routing-preference-mixed-network-adapter-portal.md)

