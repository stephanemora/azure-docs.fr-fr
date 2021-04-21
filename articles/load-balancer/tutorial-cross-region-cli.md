---
title: 'Tutoriel : Créer un équilibreur de charge interrégional à l’aide d’Azure CLI'
titleSuffix: Azure Load Balancer
description: Ce tutoriel explique comment déployer un équilibreur de charge Azure interrégional à l’aide d’Azure CLI.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 03/04/2021
ms.openlocfilehash: ca4134ff25dc9915f256b5a7bdd9404021b60a8e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791910"
---
# <a name="tutorial-create-a-cross-region-azure-load-balancer-using-azure-cli"></a>Tutoriel : Créer un équilibreur de charge Azure interrégional à l’aide d’Azure CLI

Un équilibreur de charge interrégional garantit la disponibilité mondiale du service dans plusieurs régions Azure. En cas de défaillance d'une région, le trafic est acheminé vers l'équilibreur de charge régional sain le plus proche.  

Dans ce tutoriel, vous apprenez à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un équilibreur de charge interrégional.
> * Créez une règle d’équilibreur de charge.
> * Créer un pool de back-ends contenant deux équilibreurs de charge régionaux.
> * Tester l’équilibreur de charge.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure.
- Deux équilibreurs de charge Azure de SKU **standard** avec des pools de back-ends déployés dans deux régions Azure différentes.
    - Pour plus d’informations sur la création d’un équilibreur de charge standard régional et de machines virtuelles pour les pools back-end, consultez [Démarrage rapide : Créer un équilibreur de charge public pour équilibrer la charge des machines virtuelles à l’aide d’Azure CLI](quickstart-load-balancer-standard-public-cli.md).
        - Ajoutez **-R1** et **-R2** au nom des équilibreurs de charge et des machines virtuelles dans chaque région. 
- Azure CLI installé localement ou Azure Cloud Shell.

Si vous choisissez d’installer et d’utiliser l’interface CLI en local, ce guide de démarrage nécessite Azure CLI version 2.0.28 ou ultérieure. Pour connaître la version de l’interface, exécutez `az --version`. Si vous devez effectuer une installation ou une mise à niveau, consultez [Installer Azure CLI]( /cli/azure/install-azure-cli).

## <a name="sign-in-to-azure-cli"></a>Connectez-vous à Azure CLI

Connectez-vous à Azure CLI :

```azurecli-interactive
az login
```

## <a name="create-cross-region-load-balancer"></a>Créer un équilibreur de charge interrégional

Dans cette section, vous allez créer un équilibreur de charge interrégional, une IP publique et un règle d’équilibrage de charge.

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#az_group_create) :

* Nommé **myResourceGroupLB-CR**.
* Dans la région **westus**.

```azurecli-interactive
  az group create \
    --name myResourceGroupLB-CR \
    --location westus
```

### <a name="create-the-load-balancer-resource"></a>Créer la ressource d’équilibreur de charge

Créez un équilibreur de charge interrégional à l’aide de la commande [az network cross-region-lb create](/cli/azure/network/cross-region-lb#az_network_cross_region_lb_create) :

* Nommé **myLoadBalancer-CR**.
* Un pool frontal nommé **myFrontEnd-CR**.
* Un pool back-end nommé **myBackEndPool-CR**.

```azurecli-interactive
  az network cross-region-lb create \
    --name myLoadBalancer-CR \
    --resource-group myResourceGroupLB-CR \
    --frontend-ip-name myFrontEnd-CR \
    --backend-pool-name myBackEndPool-CR     
```

### <a name="create-the-load-balancer-rule"></a>Créer la règle d’équilibreur de charge

Une règle d’équilibreur de charge définit les éléments suivants :

* La configuration IP frontale pour le trafic entrant.
* Le pool d’adresses IP principal qui reçoit le trafic.
* Les ports source et de destination requis. 

Créez une règle d’équilibreur de charge à l’aide de la commande [az network cross-region-lb rule create](/cli/azure/network/cross-region-lb/rule#az_network_cross_region_lb_rule_create) :

* Nommée **myHTTPRule-CR**.
* Écoutant le **port 80** dans le pool frontal **myFrontEnd-CR**.
* Envoyant le trafic dont la charge est équilibrée au pool d’adresses back-end **myBackEndPool-CR** en utilisant le **port 80**. 
* Protocole **TCP**.

```azurecli-interactive
  az network cross-region-lb rule create \
    --backend-port 80 \
    --frontend-port 80 \
    --lb-name myLoadBalancer-CR \
    --name myHTTPRule-CR \
    --protocol tcp \
    --resource-group myResourceGroupLB-CR \
    --backend-pool-name myBackEndPool-CR \
    --frontend-ip-name myFrontEnd-CR
```

## <a name="create-backend-pool"></a>Créer le pool principal

Dans cette section, vous allez ajouter deux équilibreurs de charge standard régionaux au pool de back-ends de l’équilibreur de charge interrégional.

> [!IMPORTANT]
> Pour effectuer ces étapes, vérifiez que deux équilibreurs de charge régionaux avec des pools de back-ends ont été déployés dans votre abonnement.  Pour plus d’informations, consultez **[Démarrage rapide : Créer un équilibreur de charge public pour équilibrer la charge des machines virtuelles à l’aide d’Azure CLI](quickstart-load-balancer-standard-public-cli.md)** .

### <a name="add-the-regional-frontends-to-load-balancer"></a>Ajouter les serveurs frontaux régionaux à l’équilibreur de charge

Dans cette section, vous allez placer les ID de ressource de deux serveurs frontaux d’équilibreurs de charge régionaux dans des variables.  Vous utiliserez ensuite les variables pour ajouter les serveurs frontaux au pool d’adresses back-end de l’équilibreur de charge interrégional.

Récupérez les ID de ressource à l’aide de la commande [az network lb frontend-ip show](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_show).

Utilisez la commande [az network cross-region-lb address-pool address add](/cli/azure/network/cross-region-lb/address-pool/address#az_network_cross_region_lb_address_pool_address_add) pour ajouter les serveurs frontaux que vous avez placés dans des variables du pool back-end de l’équilibreur de charge interrégional :

```azurecli-interactive
  region1id=$(az network lb frontend-ip show \
    --lb-name myLoadBalancer-R1 \
    --name myFrontEnd-R1 \
    --resource-group CreatePubLBQS-rg-r1 \
    --query id \
    --output tsv)

  az network cross-region-lb address-pool address add \
    --frontend-ip-address $region1id \
    --lb-name myLoadBalancer-CR \
    --name myFrontEnd-R1 \
    --pool-name myBackEndPool-CR \
    --resource-group myResourceGroupLB-CR

  region2id=$(az network lb frontend-ip show \
    --lb-name myLoadBalancer-R2 \
    --name myFrontEnd-R2 \
    --resource-group CreatePubLBQS-rg-r2 \
    --query id \
    --output tsv)
  
  az network cross-region-lb address-pool address add \
    --frontend-ip-address $region2id \
    --lb-name myLoadBalancer-CR \
    --name myFrontEnd-R2 \
    --pool-name myBackEndPool-CR \
    --resource-group myResourceGroupLB-CR
```

## <a name="test-the-load-balancer"></a>Tester l’équilibreur de charge

Dans cette section, vous allez tester l’équilibreur de charge interrégional. Vous allez vous connecter à l’adresse IP publique dans un navigateur web.  Vous allez arrêter les machines virtuelles dans l’un des pools de back-ends de l’équilibreur de charge régional et observer le basculement.

1. Pour obtenir l’IP publique de l’équilibreur de charge, utilisez la commande [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show) :

    ```azurecli-interactive
      az network public-ip show \
        --resource-group myResourceGroupLB-CR \
        --name PublicIPmyLoadBalancer-CR \
        --query ipAddress \
        --output tsv
    ```
2. Copiez l’adresse IP publique, puis collez-la dans la barre d’adresses de votre navigateur. La page par défaut du serveur Web IIS s’affiche sur le navigateur.

3. Arrêtez les machines virtuelles dans le pool de back-ends de l’un des équilibreurs de charge régionaux.

4. Actualisez le navigateur web et observez le basculement de la connexion vers l’autre équilibreur de charge régional.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous n’en avez plus besoin, utilisez la commande [az group delete](/cli/azure/group#az_group_delete) pour supprimer le groupe de ressources, l’équilibreur de charge et toutes les ressources associées.

```azurecli-interactive
  az group delete \
    --name myResourceGroupLB-CR
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous allez :

* Vous avez créé un équilibreur de charge interrégional.
* Vous avez créé une règle d’équilibrage de charge.
* Vous avez ajouté des équilibreurs de charge régionaux au pool de back-ends de l’équilibreur de charge interrégional.
* Vous avez testé l’équilibreur de charge.

Passez à l’article suivant pour savoir comment…
> [!div class="nextstepaction"]
> [Équilibrer la charge de machines virtuelles entre des zones de disponibilité](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
