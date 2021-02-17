---
title: Mettre à jour ou supprimer un équilibreur de charge existant utilisé par des groupes de machines virtuelles identiques
titleSuffix: Update or delete an existing load balancer used by virtual machine scale sets
description: Grâce à ce guide pratique, familiarisez-vous avec Azure Standard Load Balancer et les groupes de machines virtuelles identiques.
services: load-balancer
documentationcenter: na
author: irenehua
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/30/2020
ms.author: irenehua
ms.openlocfilehash: 0c491275f793ce2cd5e830ca6a3014dc45d6d509
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594533"
---
# <a name="update-or-delete-a-load-balancer-used-by-virtual-machine-scale-sets"></a>Mettre à jour ou supprimer un équilibreur de charge utilisé par des groupes de machines virtuelles identiques

Lorsque vous travaillez avec des groupes de machines virtuelles identiques et une instance d’Azure Load Balancer, vous pouvez :

- Ajouter, mettre à jour et supprimer des règles.
- Ajouter des configurations.
- Supprimer l’équilibreur de charge.

## <a name="set-up-a-load-balancer-for-scaling-out-virtual-machine-scale-sets"></a>Configurer un équilibreur de charge pour effectuer un scale-out des groupes de machines virtuelles identiques

Assurez-vous que le [pool NAT de trafic entrant](/cli/azure/network/lb/inbound-nat-pool?view=azure-cli-latest) est configuré sur l’instance d’Azure Load Balancer et que le groupe de machines virtuelles identiques est placé dans le pool principal de l’équilibreur de charge. Load Balancer crée automatiquement des règles NAT de trafic entrant dans le pool NAT de trafic entrant lorsque de nouvelles instances de machines virtuelles sont ajoutées au groupe de machines virtuelles identiques.

Pour vérifier si le pool NAT de trafic entrant est correctement configuré :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Dans le menu de gauche, sélectionnez **Toutes les ressources**. Sélectionnez ensuite **MyLoadBalancer** dans la liste des ressources.
1. Sous **Paramètres**, sélectionnez **Règles NAT de trafic entrant**. Dans le volet droit, si vous voyez une liste de règles créées pour chaque instance individuelle dans le groupe de machines virtuelles identiques, vous êtes prêt pour effectuer un scale-up à tout moment.

## <a name="add-inbound-nat-rules"></a>Ajouter des règles NAT de trafic entrant

Il est impossible d’ajouter une seule règle NAT de trafic entrant. Toutefois, vous pouvez ajouter un ensemble de règles NAT de trafic entrant avec une plage de ports frontaux et un port principal définis pour toutes les instances du groupe de machines virtuelles identiques.

Pour ajouter un ensemble complet de règles NAT de trafic entrant pour les groupes de machines virtuelles identiques, commencez par créer un pool NAT de trafic entrant dans l’équilibreur de charge. Ensuite, référencez le pool NAT de trafic entrant à partir du profil réseau du groupe de machines virtuelles identiques. Vous trouverez ci-dessous un exemple complet utilisant l’interface CLI.

La plage de ports frontaux du nouveau pool NAT de trafic entrant ne doit pas chevaucher les pools NAT de trafic entrant existants. Pour afficher les pools NAT de trafic entrant existants qui sont configurés, utilisez cette [commande CLI](/cli/azure/network/lb/inbound-nat-pool?view=azure-cli-latest#az_network_lb_inbound_nat_pool_list) :
  
```azurecli-interactive
  az network lb inbound-nat-pool create 
          -g MyResourceGroup 
          --lb-name MyLb
          -n MyNatPool 
          --protocol Tcp 
          --frontend-port-range-start 80 
          --frontend-port-range-end 89 
          --backend-port 80 
          --frontend-ip-name MyFrontendIp
  az vmss update 
          -g MyResourceGroup 
          -n myVMSS 
          --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools "{'id':'/subscriptions/mySubscriptionId/resourceGroups/MyResourceGroup/providers/Microsoft.Network/loadBalancers/MyLb/inboundNatPools/MyNatPool'}"
            
  az vmss update-instances
          -–instance-ids *
          --resource-group MyResourceGroup
          --name MyVMSS
```
## <a name="update-inbound-nat-rules"></a>Mettre à jour les règles NAT de trafic entrant

Il est impossible de mettre à jour une seule règle NAT de trafic entrant. Toutefois, vous pouvez mettre à jour un ensemble de règles NAT de trafic entrant avec une plage de ports frontaux et un port principal définis pour toutes les instances du groupe de machines virtuelles identiques.

Pour mettre à jour un ensemble complet de règles NAT de trafic entrant pour les groupes de machines virtuelles identiques, mettez à jour le pool NAT de trafic entrant dans l’équilibreur de charge.
    
```azurecli-interactive
az network lb inbound-nat-pool update 
        -g MyResourceGroup 
        --lb-name MyLb 
        -n MyNatPool
        --protocol Tcp 
        --backend-port 8080
```

## <a name="delete-inbound-nat-rules"></a>Supprimer les règles NAT de trafic entrant

Les règles NAT de trafic entrant individuelles ne peuvent pas être supprimées, mais vous pouvez supprimer l’ensemble des règles NAT de trafic entrant.

Pour supprimer l’ensemble des règles NAT de trafic entrant utilisées par le groupe identique, commencez par supprimer le pool NAT du groupe identique. Vous trouverez un exemple complet utilisant l’interface CLI ici :
    
```azurecli-interactive
    az vmss update
       --resource-group MyResourceGroup
       --name MyVMSS
     az vmss update-instances 
       --instance-ids "*" 
       --resource-group MyResourceGroup
       --name MyVMSS
    az network lb inbound-nat-pool delete
       --resource-group MyResourceGroup
       -–lb-name MyLoadBalancer
       --name MyNatPool
```

## <a name="add-multiple-ip-configurations"></a>Ajouter plusieurs configurations IP

Pour ajouter plusieurs configurations IP :

1. Dans le menu de gauche, sélectionnez **Toutes les ressources**. Sélectionnez ensuite **MyLoadBalancer** dans la liste des ressources.
1. Sous **Paramètres**, sélectionnez **Configuration d’adresses IP frontales**. Sélectionnez ensuite **Ajouter**.
1. Dans la page **Ajouter une adresse IP frontale**, entrez les valeurs et sélectionnez **OK**.
1. Suivez [l’étape 5](./load-balancer-multiple-ip.md#step-5-configure-the-health-probe) et [l’étape 6](./load-balancer-multiple-ip.md#step-5-configure-the-health-probe) dans ce tutoriel si de nouvelles règles d’équilibrage de charge sont nécessaires.
1. Créez un ensemble de règles NAT de trafic entrant en utilisant les configurations IP frontales nouvellement créées, le cas échéant. Vous trouverez un exemple dans la section précédente.

## <a name="delete-the-front-end-ip-configuration-used-by-the-virtual-machine-scale-set"></a>Supprimer la configuration IP frontale utilisée par le groupe de machines virtuelles identiques

Pour supprimer la configuration IP frontale utilisée par le groupe identique :

 1. Commencez par supprimer le pool NAT de trafic entrant (l’ensemble de règles NAT de trafic entrant) qui fait référence à la configuration IP frontale. Vous trouverez des instructions sur la suppression des règles de trafic entrant dans la section précédente.
 1. Supprimez la règle d’équilibrage de charge qui fait référence à la configuration IP frontale.
 1. Supprimez la configuration IP frontale.

## <a name="delete-a-load-balancer-used-by-a-virtual-machine-scale-set"></a>Supprimer un équilibreur de charge utilisé par un groupe de machines virtuelles identiques

Pour supprimer la configuration IP frontale utilisée par le groupe identique :

 1. Commencez par supprimer le pool NAT de trafic entrant (l’ensemble de règles NAT de trafic entrant) qui fait référence à la configuration IP frontale. Vous trouverez des instructions sur la suppression des règles de trafic entrant dans la section précédente.
 1. Supprimez la règle d’équilibrage de charge qui référence le pool principal qui contient le groupe de machines virtuelles identiques.
 1. Supprimez la référence `loadBalancerBackendAddressPool` du profil réseau du groupe de machines virtuelles identiques.
 
 Vous trouverez un exemple complet utilisant l’interface CLI ici :

```azurecli-interactive
    az vmss update
       --resource-group MyResourceGroup
       --name MyVMSS
       --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerBackendAddressPools
    az vmss update-instances 
       --instance-ids "*" 
       --resource-group MyResourceGroup
       --name MyVMSS
```
Enfin, supprimez la ressource d’équilibreur de charge.
 
## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur Azure Load Balancer et les groupes de machines virtuelles identiques, consultez les concepts.

> [Azure Load Balancer avec des groupes de machines virtuelles identiques](load-balancer-standard-virtual-machine-scale-sets.md)