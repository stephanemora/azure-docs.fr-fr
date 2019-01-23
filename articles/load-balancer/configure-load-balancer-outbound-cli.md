---
title: Configurer des règles d’équilibrage de charge et des règles de trafic sortant à l’aide d’Azure CLI
titlesuffix: Azure Load Balancer
description: Cet article explique comment configurer des règles d’équilibrage de charge et des règles de trafic sortant dans Standard Load Balancer à l’aide d’Azure CLI.
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: kumud
ms.openlocfilehash: a1fbe541d9cb2f9b5a839d90fcfa9c7b017efce9
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2019
ms.locfileid: "54198506"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-using-azure-cli"></a>Configurer des règles d’équilibrage de charge et des règles de trafic sortant dans Standard Load Balancer à l’aide d’Azure CLI

Ce guide de démarrage rapide vous montre comment configurer des règles de trafic sortant dans Standard Load Balancer à l’aide d’Azure CLI.  

Lorsque vous avez terminé, la ressource Load Balancer contient deux frontends et les règles associées : une règle pour le trafic entrant et une règle pour le trafic sortant.  Chaque frontend référence une adresse IP publique. Ce scénario utilise une adresse IP publique différente pour le trafic entrant et le trafic sortant.   La règle d’équilibrage de charge gère uniquement l’équilibrage de charge du trafic entrant. La règle de trafic sortant contrôle la NAT de trafic sortant sur la machine virtuelle.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Si vous choisissez d’installer et d’utiliser l’interface CLI localement, vous devez exécuter Azure CLI version 2.0.28 ou ultérieure pour poursuivre la procédure décrite dans ce tutoriel. Pour connaître la version de l’interface, exécutez `az --version`. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="create-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec la commande [az group create](https://docs.microsoft.com/cli/azure/group#create). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.

L’exemple suivant crée un groupe de ressources nommé *myresourcegroupoutbound* à l’emplacement *eastus2* :

```azurecli-interactive
  az group create \
    --name myresourcegroupoutbound \
    --location eastus2
```
## <a name="create-virtual-network"></a>Création d’un réseau virtuel
Créez un réseau virtuel nommé *myvnetoutbound* avec un sous-réseau nommé *mysubnetoutbound* dans *myresourcegroupoutbound* à l’aide de la commande [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet#create).

```azurecli-interactive
  az network vnet create \
    --resource-group myresourcegroupoutbound \
    --name myvnetoutbound \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mysubnetoutbound
    --subnet-prefix 192.168.0.0/24
```

## <a name="create-inbound-public-ip-address"></a>Créer une adresse IP publique entrante 

Pour accéder à votre application web sur Internet, vous avez besoin d’une adresse IP publique pour l’équilibreur de charge. L’équilibreur de charge standard prend uniquement en charge des adresses IP publiques standard. Utilisez la commande [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip#create) pour créer une adresse IP publique standard nommée *mypublicipinbound* dans *myresourcegroupoutbound*.

```azurecli-interactive
  az network public-ip create --resource-group myresourcegroupoutbound --name mypublicipinbound --sku standard
```

## <a name="create-outbound-public-ip-address"></a>Créer une adresse IP publique sortante 

À l’aide de la commande [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip#create), créez une adresse IP standard pour la configuration sortante frontend de Load Balancer.

```azurecli-interactive
  az network public-ip create --resource-group myresourcegroupoutbound --name mypublicipoutbound --sku standard
```


## <a name="create-azure-load-balancer"></a>Créer un équilibreur de charge Azure

Cette section explique en détail comment vous pouvez créer et configurer les composants suivants de l’équilibreur de charge :
  - Une IP frontend qui reçoit le trafic réseau entrant sur l’équilibreur de charge.
  - Un pool backend où l’IP frontend envoie le trafic réseau à charge équilibrée.
  - Une sonde d’intégrité qui détermine l’intégrité des instances de machine virtuelle backend.
  - Une règle de trafic entrant utilisée par l’équilibreur de charge pour distribuer le trafic à destination des machines virtuelles.
  - Une règle de trafic sortant utilisée par l’équilibreur de charge pour distribuer le trafic en provenance des machines virtuelles.

### <a name="create-load-balancer"></a>Créer un équilibreur de charge

À l’aide de la commande [az network lb create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest#create), créez un équilibreur de charge *lb* avec l’adresse IP entrante, qui inclut une configuration d’adresse IP frontend entrante et un pool backend qui est associé à l’adresse IP publique *mypublicipinbound* créée à l’étape précédente.

```azurecli-interactive
  az network lb create \
    --resource-group myresourcegroupoutbound \
    --name lb \
    --sku standard \
    --backend-pool-name bepool \
    --frontend-ip-name myfrontendinbound \
    --location eastus2 \
    --public-ip-address mypublicipinbound   
  ```

### <a name="create-outbound-frontend-ip"></a>Créer une IP frontend sortante
Avec la commande [az network lb frontend-ip create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest#create), créez la configuration IP frontend sortante pour l’équilibreur de charge, qui inclut une configuration IP frontend sortante nommée *myfrontendoutbound* qui est associée à l’adresse IP publique *mypublicipoutbound*

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group myresourcegroupoutbound \
    --name myfrontendoutbound \
    --lb-name lb \
    --public-ip-address mypublicipoutbound 
  ```

### <a name="create-health-probe"></a>Créer une sonde d’intégrité

Une sonde d’intégrité vérifie toutes les instances de machine virtuelle pour s’assurer qu’elles peuvent transmettre le trafic réseau. L’instance de machine virtuelle présentant des contrôles de sonde défaillants est supprimée de l’équilibrage de charge jusqu’à ce qu’elle revienne en ligne et que la sonde valide son intégrité. Créez une sonde d’intégrité à l’aide de la commande [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest#create) pour surveiller l’intégrité des machines virtuelles. 

```azurecli-interactive
  az network lb probe create \
    --resource-group myresourcegroupoutbound \
    --lb-name lb \
    --name http \
    --protocol http \
    --port 80 \
    --path /  
```

### <a name="create-load-balancing-rule"></a>Créer une règle d’équilibrage de charge

Une règle d’équilibreur de charge définit la configuration IP frontend pour le trafic entrant et le pool backend pour recevoir le trafic, ainsi que le port source et le port de destination requis. À l’aide de la commande [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#create), créez une règle d’équilibreur de charge nommée *myinboundlbrule* pour écouter le port 80 dans le pool frontend *myfrontendinbound* et envoyer le trafic réseau à charge équilibrée vers le pool d’adresses backend *bepool* sur le port 80 également. 

>[!NOTE]
>Cette règle d’équilibrage de charge désactive la (S)NAT de trafic sortant automatique, du fait de la définition du paramètre --disable-outbound-snat. La NAT de trafic sortant est uniquement fournie par la règle de trafic sortant.

```azurecli-interactive
az network lb rule create \
--resource-group myresourcegroupoutbound \
--lb-name lb \
--name inboundlbrule \
--protocol tcp \
--frontend-port 80 \
--backend-port 80 \
--probe http \
--frontend-ip-name myfrontendinbound \
--backend-pool-name bepool \
--disable-outbound-snat
```

### <a name="create-outbound-rule"></a>Créer une règle de trafic sortant

Une règle de trafic sortant définit l’adresse IP publique frontend, représentée par la configuration frontend *myfrontendoutbound*, utilisée pour l’ensemble du trafic NAT sortant, ainsi que le pool backend auquel cette règle s’applique.  Créez une règle de trafic sortant *myoutboundrule* pour la NAT de trafic sortant sur toutes les machines virtuelles (configurations IP de carte réseau) du pool backend *bepool*.  La commande ci-dessous change également le délai d’inactivité sortant de 4 à 15 minutes, et alloue 10 000 ports SNAT au lieu de 1 024.  Consultez [Règles de trafic sortant](https://aka.ms/lboutboundrules) pour plus de détails.

```azurecli-interactive
az network lb outbound-rule create \
 --resource-group myresourcegroupoutbound \
 --lb-name lb \
 --name outboundrule \
 --frontend-ip-configs myfrontendoutbound \
 --protocol All \
 --idle-timeout 15 \
 --outbound-ports 10000 \
 --address-pool bepool
```

Maintenant, vous pouvez ajouter votre machine virtuelle au pool backend *bepool* en mettant à jour la configuration IP des ressources de carte réseau respectives.

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous n’en avez plus besoin, vous pouvez utiliser la commande [az group delete](/cli/azure/group#az-group-delete) pour supprimer le groupe de ressources, l’équilibreur de charge et toutes les ressources associées.

```azurecli-interactive 
  az group delete --name myresourcegroupoutbound
```

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez créé un équilibreur de charge standard, configuré les règles de trafic entrant pour l’équilibreur de charge et configuré une sonde d’intégrité pour les machines virtuelles dans le pool backend. Pour en savoir plus sur Azure Load Balancer, consultez les didacticiels qui lui sont consacrés.

> [!div class="nextstepaction"]
> [Didacticiels Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)

