---
title: Configurer des règles d’équilibrage de charge et des règles de trafic sortant à l’aide d’Azure CLI
titleSuffix: Azure Load Balancer
description: Cet article explique comment configurer des règles d’équilibrage de charge et des règles de trafic sortant dans Standard Load Balancer à l’aide d’Azure CLI.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/01/2019
ms.author: allensu
ms.openlocfilehash: 2b326c6c58b4685b6e73d0f9a641a2f90807d705
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84803742"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-using-azure-cli"></a>Configurer des règles d’équilibrage de charge et des règles de trafic sortant dans Standard Load Balancer à l’aide d’Azure CLI

Ce guide de démarrage rapide vous montre comment configurer des règles de trafic sortant dans Standard Load Balancer à l’aide d’Azure CLI.  

Lorsque vous avez terminé, la ressource Load Balancer contient deux frontends et les règles associées : une règle pour le trafic entrant et une règle pour le trafic sortant.  Chaque frontend référence une adresse IP publique. Ce scénario utilise une adresse IP publique différente pour le trafic entrant et le trafic sortant.   La règle d’équilibrage de charge gère uniquement l’équilibrage de charge du trafic entrant. La règle de trafic sortant contrôle la NAT de trafic sortant sur la machine virtuelle.  Ce démarrage rapide utilise deux pools backend distincts, un pour trafic entrant et l’autre pour le trafic sortant, afin d’illustrer la fonctionnalité et permettre une grande flexibilité pour ce scénario.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Si vous choisissez d’installer et d’utiliser l’interface CLI localement, vous devez exécuter Azure CLI version 2.0.28 ou ultérieure pour poursuivre la procédure décrite dans ce tutoriel. Pour connaître la version de l’interface, exécutez `az --version`. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="create-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec la commande [az group create](https://docs.microsoft.com/cli/azure/group). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.

L’exemple suivant crée un groupe de ressources nommé *myresourcegroupoutbound* à l’emplacement *eastus2* :

```azurecli-interactive
  az group create \
    --name myresourcegroupoutbound \
    --location eastus2
```
## <a name="create-virtual-network"></a>Création d’un réseau virtuel
Créez un réseau virtuel nommé *myvnetoutbound* avec un sous-réseau nommé *mysubnetoutbound* dans *myresourcegroupoutbound* à l’aide de la commande [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet).

```azurecli-interactive
  az network vnet create \
    --resource-group myresourcegroupoutbound \
    --name myvnetoutbound \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mysubnetoutbound \
    --subnet-prefix 192.168.0.0/24
```

## <a name="create-inbound-public-ip-address"></a>Créer une adresse IP publique entrante 

Pour accéder à votre application web sur Internet, vous avez besoin d’une adresse IP publique pour l’équilibreur de charge. L’équilibreur de charge standard prend uniquement en charge des adresses IP publiques standard. Utilisez la commande [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip) pour créer une adresse IP publique standard nommée *mypublicipinbound* dans *myresourcegroupoutbound*.

```azurecli-interactive
  az network public-ip create --resource-group myresourcegroupoutbound --name mypublicipinbound --sku standard
```

## <a name="create-outbound-public-ip-address"></a>Créer une adresse IP publique sortante 

À l’aide de la commande [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip), créez une adresse IP standard pour la configuration sortante frontend de Load Balancer.

```azurecli-interactive
  az network public-ip create --resource-group myresourcegroupoutbound --name mypublicipoutbound --sku standard
```

## <a name="create-azure-load-balancer"></a>Créer un équilibreur de charge Azure

Cette section explique en détail comment vous pouvez créer et configurer les composants suivants de l’équilibreur de charge :
  - Une IP frontend qui reçoit le trafic réseau entrant sur l’équilibreur de charge.
  - Un pool backend où l’IP frontend envoie le trafic réseau à charge équilibrée.
  - Un pool backend pour la connexion sortante. 
  - Une sonde d’intégrité qui détermine l’intégrité des instances de machine virtuelle backend.
  - Une règle de trafic entrant utilisée par l’équilibreur de charge pour distribuer le trafic à destination des machines virtuelles.
  - Une règle de trafic sortant utilisée par l’équilibreur de charge pour distribuer le trafic en provenance des machines virtuelles.

### <a name="create-load-balancer"></a>Créer un équilibreur de charge

À l’aide de la commande [az network lb create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest), créez un équilibreur de charge *lb* avec l’adresse IP entrante, qui inclut une configuration d’adresse IP frontend entrante et un pool backend *bepoolinbound* qui est associé à l’adresse IP publique *mypublicipinbound* créée à l’étape précédente.

```azurecli-interactive
  az network lb create \
    --resource-group myresourcegroupoutbound \
    --name lb \
    --sku standard \
    --backend-pool-name bepoolinbound \
    --frontend-ip-name myfrontendinbound \
    --location eastus2 \
    --public-ip-address mypublicipinbound   
  ```

### <a name="create-outbound-pool"></a>Créer un pool sortant

Créez un pool d’adresses backend supplémentaires pour définir la connectivité sortante pour un pool de machines virtuelles avec [az network lb address-pool create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) portant le nom *bepooloutbound*.  La création d’un pool sortant distinct offre une flexibilité maximale. Toutefois, vous pouvez omettre cette étape et utiliser uniquement le trafic entrant *bepoolinbound* également.

```azurecli-interactive
  az network lb address-pool create \
    --resource-group myresourcegroupoutbound \
    --lb-name lb \
    --name bepooloutbound
```

### <a name="create-outbound-frontend-ip"></a>Créer une IP frontend sortante
Avec la commande [az network lb frontend-ip create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest), créez la configuration IP frontend sortante pour l’équilibreur de charge, qui inclut une configuration IP frontend sortante nommée *myfrontendoutbound* qui est associée à l’adresse IP publique *mypublicipoutbound*

```azurecli-interactive
  az network lb frontend-ip create \
    --resource-group myresourcegroupoutbound \
    --name myfrontendoutbound \
    --lb-name lb \
    --public-ip-address mypublicipoutbound 
  ```

### <a name="create-health-probe"></a>Créer une sonde d’intégrité

Une sonde d’intégrité vérifie toutes les instances de machine virtuelle pour s’assurer qu’elles peuvent transmettre le trafic réseau. L’instance de machine virtuelle présentant des contrôles de sonde défaillants est supprimée de l’équilibrage de charge jusqu’à ce qu’elle revienne en ligne et que la sonde valide son intégrité. Créez une sonde d’intégrité à l’aide de la commande [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest) pour surveiller l’intégrité des machines virtuelles. 

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

Une règle d’équilibreur de charge définit la configuration IP frontend pour le trafic entrant et le pool backend pour recevoir le trafic, ainsi que le port source et le port de destination requis. À l’aide de la commande [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest), créez une règle d’équilibreur de charge nommée *myinboundlbrule* pour écouter le port 80 dans le pool frontend *myfrontendinbound* et envoyer le trafic réseau à charge équilibrée vers le pool d’adresses backend *bepool* sur le port 80 également. 

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
--backend-pool-name bepoolinbound \
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
 --address-pool bepooloutbound
```

Si vous ne souhaitez pas utiliser de pool distinct sortant, vous pouvez modifier l’argument de pool d’adresses dans la commande précédente pour spécifier *bepoolinbound* à la place.  Nous vous recommandons d’utiliser des pools distincts pour des questions de flexibilité et de lisibilité de la configuration qui en résulte.

Vous pouvez maintenant ajouter votre machine virtuelle au pool back-end *bepoolinbound* __et__ *bepooloutbound* en mettant à jour la configuration IP des ressources de carte réseau respectives avec la commande [az network nic ip-config address-pool add](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest).

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous n’en avez plus besoin, vous pouvez utiliser la commande [az group delete](/cli/azure/group#az-group-delete) pour supprimer le groupe de ressources, l’équilibreur de charge et toutes les ressources associées.

```azurecli-interactive 
  az group delete --name myresourcegroupoutbound
```

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez créé un équilibreur de charge standard, configuré les règles de trafic entrant pour l’équilibreur de charge et configuré une sonde d’intégrité pour les machines virtuelles dans le pool backend. Pour en savoir plus sur Azure Load Balancer, consultez les didacticiels qui lui sont consacrés.

> [!div class="nextstepaction"]
> [Didacticiels Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
