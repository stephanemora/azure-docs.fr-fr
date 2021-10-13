---
title: Gérer une connexion de point de terminaison privé dans Azure
titleSuffix: Azure Private Link
description: Découvrez comment gérer une connexion de point de terminaison privé dans Azure
services: private-link
author: asudbring
ms.service: private-link
ms.topic: how-to
ms.date: 10/04/2021
ms.author: allensu
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7923e35165f756878ed52e807a1e0106e1660e59
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129535959"
---
# <a name="manage-a-private-endpoint-connection"></a>Gérer une connexion de point de terminaison privé

Azure Private Link fonctionne sur un modèle d’approbation, dans lequel l’utilisateur du service Private Link peut demander une connexion au fournisseur de services pour utiliser le service. 

Le fournisseur de services peut alors décider s’il faut autoriser l’utilisateur à se connecter. Azure Private Link permet aux fournisseurs de services de gérer la connexion des points de terminaison privés dans leurs ressources. 

Cet article fournit des instructions sur la gestion des connexions de points de terminaison privés.

![Gérer les points de terminaison privés](media/manage-private-endpoint/manage-private-endpoint.png)

L’utilisateur du service Private Link peut choisir entre deux méthodes d’approbation des connexions :

- **Automatique** : Si l’utilisateur du service a des autorisations du contrôle d’accès en fonction du rôle Azure sur la ressource du fournisseur de services, il peut choisir la méthode d’approbation automatique. Lorsque la demande atteint la ressource du fournisseur de services, celui-ci n’a rien à faire, car la connexion est automatiquement approuvée. 

- **Manuel** : si l’utilisateur du service ne dispose pas d’autorisations du Contrôle d’accès en fonction du rôle Azure (Azure RBAC) sur la ressource du fournisseur de services, il peut choisir la méthode d’approbation manuelle. La demande de connexion apparaît comme étant **En attente** dans les ressources du service. Le fournisseur de services doit approuver manuellement la demande avant de pouvoir établir des connexions. En cas d’approbation manuelle, l’utilisateur peut également ajouter un message à sa demande pour fournir davantage de contexte au fournisseur de services. Le fournisseur de services peut choisir parmi les options suivantes pour toutes les connexions de point de terminaison privés : **Approuver**, **Rejeter**, **Supprimer**.

Le tableau ci-dessous présente les différentes actions que peut effectuer le fournisseur de services, ainsi que les états de connexion résultants pour les points de terminaison privés. Le fournisseur de services peut modifier l’état de la connexion ultérieurement sans intervention de l’utilisateur. Chaque action met à jour l’état du point de terminaison côté client. 


| Action du fournisseur de services  | État du point de terminaison privé de l’utilisateur du service | Description |
|---------|---------|---------|
| None    |    Pending     |    La connexion est créée manuellement et est en attente de l’approbation du propriétaire de la ressource Private Link.       |
| Approbation    |  Approved       |  La connexion a été approuvée automatiquement ou manuellement et est prête à être utilisée.     |
| Rejeter     | Rejeté        | La connexion a été rejetée par le propriétaire de la ressource Private Link.        |
| Supprimer    |  Déconnecté       | La connexion a été supprimée par le propriétaire de la ressource Private Link, le point de terminaison privé devient donc informatif et doit être supprimé dans le cadre d’un nettoyage.        |

## <a name="manage-private-endpoint-connections-on-azure-paas-resources"></a>Gérer les connexions de point de terminaison privé sur des ressources Azure PaaS

Il est recommandé d’utiliser le portail Azure pour gérer les connexions de point de terminaison privé dans les ressources Azure PaaS. 

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Dans la zone de recherche située en haut du portail, entrez **Liaison privée**. Dans les résultats de la recherche, sélectionnez **Liaison privée**.

3. Dans le **Centre de liaisons privées**, sélectionnez **Points de terminaison privés** ou **Services de liaison privée**.

4. Pour chaque point de terminaison, vous pouvez afficher le nombre de connexions de point de terminaison privé qui lui sont associées. Vous pouvez filtrer les ressources selon vos besoins.

5. Sélectionnez le point de terminaison privé.  Dans la liste des connexions, sélectionnez la connexion que vous souhaitez gérer. 

6. Vous pouvez modifier l’état de la connexion en sélectionnant l’une des options situées en haut.

## <a name="manage-private-endpoint-connections-on-a-customerpartner-owned-private-link-service"></a>Gérer les connexions de point de terminaison privé dans un service Private Link détenu par un client ou un partenaire

Azure PowerShell et Azure CLI sont recommandés pour la gestion des connexions de point de terminaison privé dans les services des partenaires Microsoft ou ceux appartenant aux clients. 
 
### <a name="powershell"></a>PowerShell 
  
Utilisez les commandes PowerShell suivantes pour gérer les connexions de point de terminaison privé.  

#### <a name="get-private-link-connection-states"></a>Obtenir les état des connexions Private Link 

Utilisez [Get-AzPrivateEndpointConnection](/powershell/module/az.network/get-azprivateendpointconnection) pour obtenir les connexions des points de terminaison privés et leurs états.  

```azurepowershell
Get-AzPrivateEndpointConnection -Name myPrivateLinkService -ResourceGroupName myResourceGroup 
```
 
#### <a name="approve-a-private-endpoint-connection"></a>Approuver une connexion de point de terminaison privé 
 
Utilisez la cmdlet [Approve-AzPrivateEndpointConnection](/powershell/module/az.network/approve-azprivateendpointconnection) pour approuver une connexion de point de terminaison privé. 
 
```azurepowershell
Approve-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService
```
 
#### <a name="deny-private-endpoint-connection"></a>Refuser une connexion de point de terminaison privé 
 
Utilisez la cmdlet [Deny-AzPrivateEndpointConnection](/powershell/module/az.network/deny-azprivateendpointconnection) pour rejeter une connexion de point de terminaison privé. 

```azurepowershell
Deny-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService 
```

#### <a name="remove-private-endpoint-connection"></a>Supprimer une connexion de point de terminaison privé 
 
Utilisez le cmdlet [Remove-AzPrivateEndpointConnection](/powershell/module/az.network/remove-azprivateendpointconnection) pour supprimer une connexion de point de terminaison privé. 

```azurepowershell
Remove-AzPrivateEndpointConnection -Name myPrivateEndpointConnection -ResourceGroupName myResourceGroup -ServiceName myPrivateLinkService
```
 
### <a name="azure-cli"></a>Azure CLI 
 
#### <a name="get-private-link-connection-states"></a>Obtenir les état des connexions Private Link 

Utilisez [az network private-endpoint-connection show](/cli/azure/network/private-endpoint-connection#az_network_private_endpoint_connection_show) pour obtenir les connexions des points de terminaison privés et leurs états.  

```azurecli
  az network private-endpoint-connection show \
    --name myPrivateEndpointConnection \
    --resource-group myResourceGroup
```
 
#### <a name="approve-a-private-endpoint-connection"></a>Approuver une connexion de point de terminaison privé 
 
Utilisez la cmdlet [az network private-endpoint-connection approve](/cli/azure/network/private-endpoint-connection#az_network_private_endpoint_connection_approve) pour approuver une connexion de point de terminaison privé. 
 
```azurecli
  az network private-endpoint-connection approve \
    --name myPrivateEndpointConnection  \
    --resource-group myResourceGroup
```
 
#### <a name="deny-private-endpoint-connection"></a>Refuser une connexion de point de terminaison privé 
 
Utilisez la cmdlet [az network private-endpoint-connection reject](/cli/azure/network/private-endpoint-connection#az_network_private_endpoint_connection_reject) pour refuser une connexion de point de terminaison privé. 

```azurecli
  az network private-endpoint-connection reject \
    --name myPrivateEndpointConnection  \
    --resource-group myResourceGroup
```

#### <a name="remove-private-endpoint-connection"></a>Supprimer une connexion de point de terminaison privé 
 
Utilisez la cmdlet [az network private-endpoint-connection delete](/cli/azure/network/private-endpoint-connection#az_network_private_endpoint_connection_delete) pour supprimer une connexion de point de terminaison privé. 

```azurecli
  az network private-endpoint-connection delete \
    --name myPrivateEndpointConnection \
    --resource-group myResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes
- [En savoir plus sur les points de terminaison privés](private-endpoint-overview.md)
 
