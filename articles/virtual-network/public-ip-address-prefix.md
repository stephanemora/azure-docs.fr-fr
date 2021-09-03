---
title: Préfixe d’adresse IP publique Azure
titleSuffix: Azure Virtual Network
description: Découvrez ce qu’est un préfixe d’adresse IP publique Azure et comment il vous permet d’attribuer des adresses IP publiques à vos ressources.
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: conceptual
ms.date: 05/20/2021
ms.author: allensu
ms.openlocfilehash: 32f5cbc7be833b823259052152be57c2d18cec25
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122531705"
---
# <a name="public-ip-address-prefix"></a>Préfixe d’adresse IP publique

Un préfixe d’IP publique est une plage réservée d’[adresses IP publiques](./public-ip-addresses.md#public-ip-addresses) dans Azure. Les préfixes IP publics sont attribués à partir d’un pool d’adresses dans chaque région Azure. Vous créez un préfixe d’adresse IP publique dans une région et un abonnement Azure en spécifiant un nom et une taille de préfixe. La taille du préfixe correspond au nombre d’adresses disponibles. Les préfixes d’adresses IP publiques sont constitués d’adresses IPv4 ou IPv6.  Dans les régions avec des zones de disponibilité, les préfixes d’adresses IP publiques peuvent être créés comme redondants dans une zone ou associés à une zone de disponibilité spécifique.  Une fois le préfixe d’adresse IP publique créé, vous pouvez créer des adresses IP publiques.

## <a name="benefits"></a>Avantages

- Création de ressources d’IP publique statique à partir d’une plage connue. Les adresses que vous créez à l’aide du préfixe peuvent être attribuées à n’importe quelle ressource Azure à laquelle vous pouvez attribuer une adresse IP publique de SKU standard.
- Lorsque vous supprimer des adresses IP publiques, elles sont *retournées* dans votre plage réservée pour une utilisation future. Les adresses IP de votre préfixe d’adresse IP publique sont réservées à votre utilisation jusqu’à ce que vous supprimiez votre préfixe.
- Vous pouvez voir quelles adresses IP de la plage sont allouées et quelles adresses sont disponibles dans la plage de préfixes.

## <a name="prefix-sizes"></a>Tailles de préfixe

Les tailles de préfixe IP public suivantes sont disponibles :

-  /28 (IPv4) ou /124 (IPv6) = 16 adresses
-  /29 (IPv4) ou /125 (IPv6) = 8 adresses
-  /30 (IPv4) ou /126 (IPv6) = 4 adresses
-  /31 (IPv4) ou /127 (IPv6) = 2 adresses

La taille de préfixe est spécifiée en tant que taille de masque CIDR (Classless Inter-Domain Routing).

Il n’existe pas de limites quant au nombre de préfixes créés dans un abonnement. Par conséquent, le nombre de plages que vous créez ne peut pas dépasser le nombre d’adresses IP publiques statiques autorisées dans votre abonnement. Pour plus d'informations, consultez [Limites Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="scenarios"></a>Scénarios
Vous pouvez associer les ressources suivantes à une adresse IP publique statique à partir d’un préfixe :

|Ressource|Scénario|Étapes|
|---|---|---|
|Machines virtuelles| L’association d’IP publiques à partir d’un préfixe à vos machines virtuelles dans Azure réduit la surcharge de gestion lors de l’ajout d’adresses IP à une liste verte dans le pare-feu. Vous pouvez ajouter la totalité d’un préfixe avec une seule règle de pare-feu. Quand vous effectuez une mise à l’échelle avec des machines virtuelles dans Azure, vous pouvez associer des adresses IP du même préfixe, ce qui réduit les coûts, fait gagner du temps et limite la surcharge de gestion.| Pour associer des adresses IP d’un préfixe à votre machine virtuelle : </br> 1. [Créez un préfixe.](manage-public-ip-address-prefix.md) </br> 2. [Créez une adresse IP à partir du préfixe.](manage-public-ip-address-prefix.md) </br> 3. [Associez l’adresse IP à l’interface réseau de votre machine virtuelle.](virtual-network-network-interface-addresses.md#add-ip-addresses) </br> Vous pouvez également [associer les adresses IP à un groupe de machines virtuelles identiques](https://azure.microsoft.com/resources/templates/vmms-with-public-ip-prefix/).
| Équilibreurs de charge Standard | L’association d’IP publiques d’un préfixe à la configuration d’adresses IP frontale ou à la règle de trafic sortant d’un équilibreur de charge garantit la simplification de votre espace d’adressage IP public Azure. Simplifiez votre scénario en préparant les connexions sortantes de sorte à partir d’une plage d’adresses IP contiguës. | Pour associer des adresses IP d’un préfixe à votre équilibreur de charge : </br> 1. [Créez un préfixe.](manage-public-ip-address-prefix.md) </br> 2. [Créez une adresse IP à partir du préfixe.](manage-public-ip-address-prefix.md) </br> 3. Quand vous créez l’équilibreur de charge, sélectionnez ou mettez à jour l’adresse IP créée à l’étape 2 ci-dessus comme adresse IP frontale de votre équilibreur de charge. |
| Pare-feu Azure | Vous pouvez utiliser une adresse IP publique d’un préfixe pour une NAT de trafic sortant. Tout le trafic virtuel sortant est traduit en IP publique de [pare-feu Azure](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). | Pour associer une adresse IP d’un préfixe à votre pare-feu : </br> 1. [Créez un préfixe.](manage-public-ip-address-prefix.md) </br> 2. [Créez une adresse IP à partir du préfixe.](manage-public-ip-address-prefix.md) </br> 3. Quand vous [déployez le pare-feu Azure](../firewall/tutorial-firewall-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-the-firewall), veillez à sélectionner l’adresse IP précédemment allouée à partir du préfixe.|
| Passerelle VPN (AZ SKU), Application Gateway v2, NAT Gateway | Vous pouvez utiliser une adresse IP publique d’un préfixe pour votre passerelle. | Pour associer une adresse IP d’un préfixe à votre passerelle : </br> 1. [Créez un préfixe.](manage-public-ip-address-prefix.md) </br> 2. [Créez une adresse IP à partir du préfixe.](manage-public-ip-address-prefix.md) </br> 3. Quand vous déployez la [passerelle VPN](../vpn-gateway/tutorial-create-gateway-portal.md), la passerelle [Application Gateway](../application-gateway/quick-create-portal.md#create-an-application-gateway) ou la [NAT Gateway](./nat-gateway/tutorial-create-nat-gateway-portal.md), veillez à sélectionner l’adresse IP précédemment allouée à partir du préfixe.|

En outre, la ressource de préfixe d’adresse IP publique peut être utilisée directement par certaines ressources :

Ressource|Scénario|Étapes|
|---|---|---|
|Groupes identiques de machines virtuelles | Vous pouvez utiliser un préfixe d’adresse IP publique pour générer des adresses IP au niveau de l’instance dans un groupe de machines virtuelles identiques, même si les ressources des IP publiques individuelles ne sont pas créées. | Utilisez un [modèle](https://azure.microsoft.com/resources/templates/vmms-with-public-ip-prefix/) avec des instructions pour utiliser ce préfixe pour la configuration d’une IP publique dans le cadre de la création du groupe de machines virtuelles identiques. |
| Équilibreurs de charge Standard | Un préfixe d’adresse IP publique peut être utilisé pour mettre à l’échelle un équilibreur de charge à [l’aide de toutes les adresses IP de la plage pour les connexions sortantes](../load-balancer/outbound-rules.md#scale). | Pour associer un préfixe à votre équilibreur de charge : </br> 1. [Créez un préfixe.](manage-public-ip-address-prefix.md) </br> 2. Lorsque vous créez l’équilibreur de charge, sélectionnez un préfixe IP associé au front-end de votre équilibreur de charge. |
| NAT Gateway | Un préfixe d’adresse IP publique peut être utilisé pour mettre à l’échelle une passerelle NAT en utilisant les adresses IP publiques dans le préfixe des connexions sortantes. | Pour associer un préfixe à votre NAT Gateway : </br> 1. [Créez un préfixe.](manage-public-ip-address-prefix.md) </br> 2. Lors de la création de la NAT Gateway, sélectionnez le préfixe IP comme adresse IP sortante. |
| Passerelle VPN (SKU AZ) ou Application Gateway v2 | Vous pouvez utiliser l’adresse IP publique d’un préfixe pour votre passerelle VPN ou Application Gateway v2 redondante interzone. | Pour associer une adresse IP d’un préfixe à votre passerelle : </br> 1. [Créez un préfixe.](manage-public-ip-address-prefix.md) </br> 2. [Créez une adresse IP à partir du préfixe.](manage-public-ip-address-prefix.md) </br> 3. Quand vous déployez la [passerelle VPN](../vpn-gateway/tutorial-create-gateway-portal.md) ou la passerelle [Application Gateway](../application-gateway/quick-create-portal.md#create-an-application-gateway), veillez à sélectionner l’adresse IP précédemment allouée à partir du préfixe.|

## <a name="limitations"></a>Limites

- Vous ne pouvez pas spécifier les adresses IP pour le préfixe. Azure alloue les adresses IP pour le préfixe, en fonction de la taille que vous spécifiez.  En outre, toutes les IP publiques créées à partir du préfixe doivent résider dans la même région et le même abonnement Azure que le préfixe. Les adresses doivent être attribuées à des ressources de la même région et du même abonnement.
- Vous pouvez créer un préfixe pouvant inclure jusqu’à 16 adresses IP. Pour plus d’informations, consultez [Demandes d’augmentation de limites réseau](../azure-portal/supportability/networking-quota-requests.md) et [Limites d’Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- La taille de la plage ne peut pas être modifiée après la création du préfixe.
- Seules les adresses IP publiques créées avec la référence SKU standard peuvent être attribuées à partir de la plage du préfixe. Pour en savoir plus sur les références (SKU) d’adresses IP publiques, consultez [Adresse IP publique](./public-ip-addresses.md#public-ip-addresses).
- Les adresses de la plage ne peuvent être attribuées qu’à des ressources Azure Resource Manager. Il n’est pas possible d’attribuer des adresses aux ressources du modèle de déploiement classique.
- Vous ne pouvez pas supprimer un préfixe si des adresses qu’il contient sont attribuées à des ressources d’adresse P publique associées à une ressource. Dissociez d’abord toutes les ressources d’adresse IP publique qui sont des adresses IP attribuées du préfixe. Pour plus d’informations sur la dissociation des adresses IP publiques, consultez [Gérer les adresses IP publiques](./virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address).
- Les préfixes d’adresses IP publiques ne sont actuellement pas compatibles avec *Préférence de routage* **Internet** ou *Niveau* **global** (pour l’équilibrage de charge entre régions).
- Le format IPv6 est pris en charge sur les adresses IP publiques avec allocation **dynamique** uniquement. L’allocation dynamique signifie que l’adresse IPv6 sera modifiée si vous supprimez et que vous redéployez votre ressource dans Azure. 
- Les adresses IP publiques standard IPv6 prennent en charge l’allocation statique (réservée). 
- Les équilibreurs de charge internes standard prennent en charge l’allocation dynamique à partir du sous-réseau auquel ils sont affectés.  

## <a name="pricing"></a>Tarifs
 
Pour connaître les coûts associés à l’utilisation des adresses IP publiques Azure (adresses IP individuelles et plages d’adresses IP), consultez [Tarification des adresses IP](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="next-steps"></a>Étapes suivantes

- [Créer](manage-public-ip-address-prefix.md) un préfixe d’adresse IP publique
