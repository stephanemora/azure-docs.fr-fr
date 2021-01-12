---
title: Mettre à jour ou supprimer un équilibreur de charge Azure existant utilisé par un groupe de machines virtuelles identiques
titleSuffix: Update or delete existing Azure Load Balancer used by Virtual Machine Scale Set
description: Avec cet article pratique, familiarisez-vous avec Azure Standard Load Balancer et le service Virtual Machine Scale Sets.
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
ms.openlocfilehash: f8f664375e53a1cef28b0c7b95207770434f67fa
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97893236"
---
# <a name="how-to-updatedelete-azure-load-balancer-used-by-virtual-machine-scale-sets"></a>Comment mettre à jour/supprimer Azure Load Balancer utilisé par le service Virtual Machine Scale Sets

## <a name="how-to-set-up-azure-load-balancer-for-scaling-out-virtual-machine-scale-sets"></a>Comment configurer Azure Load Balancer pour effectuer un scale-out du service Virtual Machine Scale Sets
  * Assurez-vous que le [pool NAT de trafic entrant](https://docs.microsoft.com/cli/azure/network/lb/inbound-nat-pool?view=azure-cli-latest) est configuré sur l’équilibreur de charge et que le groupe de machines virtuelles identiques est placé dans le pool back-end de l’équilibreur de charge. Azure Load Balancer crée automatiquement des règles NAT de trafic entrant dans le pool NAT de trafic entrant lorsque de nouvelles instances de machines virtuelles sont ajoutées au groupe de machines virtuelles identiques. 
  * Pour vérifier si le pool NAT de trafic entrant est correctement configuré, 
  1. Connectez-vous au portail Azure sur https://portal.azure.com.
  
  1. Sélectionnez **Toutes les ressources** dans le menu de gauche, puis sélectionnez **MyLoadBalancer** dans la liste de ressources.
  
  1. Sous **Paramètres**, sélectionnez **Règles NAT de trafic entrant**.
Si vous voyez dans le volet droit une liste de règles créées pour chaque instance individuelle dans le groupe de machines virtuelles identiques, vous êtes prêt pour effectuer un scale-up à tout moment.

## <a name="how-to-add-inbound-nat-rules"></a>Comment ajouter des règles NAT de trafic entrant ? 
  * Il est impossible d’ajouter une seule règle NAT de trafic entrant. Toutefois, vous pouvez ajouter un ensemble de règles NAT de trafic entrant avec une plage de ports front-end et un port back-end définis pour toutes les instances du groupe de machines virtuelles identiques.
  * Pour ajouter un ensemble complet de règles NAT de trafic entrant pour le service Virtual Machine Scale Sets, vous devez d’abord créer un pool NAT de trafic entrant dans l’équilibreur de charge, puis faire référence au pool NAT de trafic entrant à partir du profil réseau du groupe de machines virtuelles identiques. Vous trouverez ci-dessous un exemple complet utilisant l’interface CLI.
  * La plage de ports front-end du nouveau pool NAT de trafic entrant ne doit pas chevaucher les pools NAT de trafic entrant existants. Pour afficher les pools NAT de trafic entrant existants configurés, vous pouvez utiliser cette [commande CLI](https://docs.microsoft.com/cli/azure/network/lb/inbound-nat-pool?view=azure-cli-latest#az_network_lb_inbound_nat_pool_list)
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
## <a name="how-to-update-inbound-nat-rules"></a>Comment mettre à jour les règles NAT de trafic entrant ? 
  * Il est impossible de mettre à jour une seule règle NAT de trafic entrant. Toutefois, vous pouvez mettre à jour un ensemble de règles NAT de trafic entrant avec une plage de ports front-end et un port back-end définis pour toutes les instances du groupe de machines virtuelles identiques.
  * Pour mettre à jour un ensemble complet de règles NAT de trafic entrant pour le service Virtual Machine Scale Sets, vous devez mettre à jour le pool NAT de trafic entrant dans l’équilibreur de charge. 
```azurecli-interactive
az network lb inbound-nat-pool update 
        -g MyResourceGroup 
        --lb-name MyLb 
        -n MyNatPool
        --protocol Tcp 
        --backend-port 8080
```

## <a name="how-to-delete-inbound-nat-rules"></a>Comment supprimer des règles NAT de trafic entrant ? 
* Il est impossible de supprimer une seule règle NAT de trafic entrant. Toutefois, vous pouvez supprimer l’ensemble des règles NAT de trafic entrant.
* Pour supprimer l’ensemble des règles NAT de trafic entrant utilisées par le groupe identique, vous devez d’abord supprimer le pool NAT du groupe identique. Vous trouverez ci-dessous un exemple complet utilisant l’interface CLI :
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

## <a name="how-to-add-multiple-ip-configurations"></a>Ajout de plusieurs configurations IP :
1. Sélectionnez **Toutes les ressources** dans le menu de gauche, puis sélectionnez **MyLoadBalancer** dans la liste de ressources.
   
1. Sous **Paramètres**, sélectionnez **Configuration d’adresses IP front-end**, puis **Ajouter**.
   
1. Dans la page **Ajouter une adresse IP front-end**, tapez les valeurs et sélectionnez **OK**

1. Suivez [l’étape 5](https://docs.microsoft.com/azure/load-balancer/load-balancer-multiple-ip#step-5-configure-the-health-probe) et [l’étape 6](https://docs.microsoft.com/azure/load-balancer/load-balancer-multiple-ip#step-5-configure-the-health-probe) dans ce tutoriel si de nouvelles règles d’équilibrage de charge sont nécessaires

1. Créez un ensemble de règles NAT de trafic entrant en utilisant les configurations IP front-end nouvellement créées, si nécessaire. Vous trouverez un exemple dans la [section précédente].

## <a name="how-to-delete-frontend-ip-configuration-used-by-virtual-machine-scale-set"></a>Suppression de la configuration IP front-end utilisée par le groupe de machines virtuelles identiques : 
 1. Pour supprimer la configuration IP front-end utilisée par le groupe identique, vous devez d’abord supprimer le pool NAT de trafic entrant (ensemble de règles NAT de trafic entrant) qui fait référence à la configuration IP front-end. Vous trouverez des instructions sur la suppression des règles de trafic entrant dans la section précédente.
 1. Supprimez la règle d’équilibrage de charge faisant référence à la configuration IP front-end. 
 1. Supprimez la configuration IP front-end.
 

## <a name="how-to-delete-azure-load-balancer-used-by-virtual-machine-scale-set"></a>Suppression d’Azure Load Balancer utilisé par le groupe de machines virtuelles identiques : 
 1. Pour supprimer la configuration IP front-end utilisée par le groupe identique, vous devez d’abord supprimer le pool NAT de trafic entrant (ensemble de règles NAT de trafic entrant) qui fait référence à la configuration IP front-end. Vous trouverez des instructions sur la suppression des règles de trafic entrant dans la section précédente.
 
 1. Supprimez la règle d’équilibrage de charge référençant le pool back-end contenant le groupe de machines virtuelles identiques.
 
 1. Supprimez la référence loadBalancerBackendAddressPool du profil réseau du groupe de machines virtuelles identiques. Vous trouverez ci-dessous un exemple complet utilisant l’interface CLI :
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

Pour en savoir plus sur Azure Load Balancer et le service Virtual Machine Scale Sets, consultez les concepts.

> [Azure Load Balancer avec des groupes de machines virtuelles identiques](load-balancer-standard-virtual-machine-scale-sets.md)
