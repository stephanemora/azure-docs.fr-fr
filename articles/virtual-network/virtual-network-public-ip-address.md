---
title: Créer, changer ou supprimer une adresse IP publique Azure
titleSuffix: Azure Virtual Network
description: Gérer les adresses IP publiques. Découvrez comment une adresse IP publique constitue une ressource disposant de paramètres configurables.
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/20/2021
ms.author: allensu
ms.openlocfilehash: bd8c98e51f942d8f8ae843049832d8c76e0f24a9
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114284127"
---
# <a name="create-change-or-delete-an-azure-public-ip-address"></a>Créer, changer ou supprimer une adresse IP publique Azure

Découvrez les adresses IP publiques et apprenez à en créer, modifier et supprimer une. Une adresse IP publique est une ressource disposant de paramètres configurables. L’attribution d’une adresse IP publique à une ressource Azure qui prend en charge les adresses IP publiques permet :

- Communication entrante d’Internet vers la ressource, par exemple les machines virtuelles Azure (VM), les passerelles Azure Application Gateway, les équilibreurs de charge Azure Load Balancer, les passerelles VPN Azure, etc.
- Connectivité sortante vers Internet à l’aide d’une adresse IP prédictible.

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="create-a-public-ip-address"></a>Créer une adresse IP publique

Pour obtenir des instructions sur la création d’adresses IP publiques à l’aide du portail, de PowerShell, de l’interface CLI ou de modèles Resource Manager, consultez les pages suivantes :

 * [Créer des adresses IP publiques – Portail](./create-public-ip-portal.md?tabs=option-create-public-ip-standard-zones)
 * [Créer des adresses IP publiques - PowerShell](./create-public-ip-powershell.md?tabs=option-create-public-ip-standard-zones)
 * [Créer des adresses IP publiques - Azure CLI](./create-public-ip-cli.md?tabs=option-create-public-ip-standard-zones)
 * [Créer des adresses IP publiques – Modèle](./create-public-ip-template.md)

>[!NOTE]
>Bien que le portail permette de créer deux ressources d’adresse IP publique (IPv4 et IPv6), les commandes PowerShell et CLI créent une ressource avec une adresse pour une version IP ou l’autre. Si vous souhaitez deux ressources d’adresse IP publique, une pour chaque version d’adresse IP, vous devez exécuter la commande à deux reprises, en spécifiant des versions d’adresse IP et des noms différents pour les ressources d’adresse IP publique.

Pour plus d’informations sur les attributs spécifiques d’une adresse IP publique lors de la création, consultez le tableau suivant :

   |Paramètre|Requis ?|Détails|
   |---|---|---|
   |Version de l’adresse IP|Oui| Sélectionnez IPv4 ou IPv6, ou Les deux. Si vous sélectionnez Les deux, 2 adresses IP publiques sont créées : 1 adresse IPv4 et 1 adresse IPv6. Apprenez-en davantage sur [IPv6 dans les réseaux virtuels Azure](../virtual-network/ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).|
   |SKU|Oui|Toutes les adresses IP publiques créées avant l’introduction des références SKU sont des adresses IP publiques **De base**. Une fois l’adresse IP publique créée, vous ne pouvez pas modifier la référence SKU. Les machines virtuelles autonomes, les machines virtuelles comprises dans un groupe à haute disponibilité et les groupes de machines virtuelles identiques peuvent utiliser les références SKU De Base ou Standard. La combinaison de références SKU entre machines virtuelles dans des groupes à haute disponibilité, des groupes identiques ou des machines virtuelles autonomes n’est pas autorisée. Référence SKU **De base** : si vous créez une adresse IP publique dans une région qui prend en charge les zones de disponibilité, le paramètre **Zone de disponibilité** est défini sur *Aucun* par défaut. Les adresses IP publiques de base ne prennent pas en charge les zones de disponibilité. Référence **Standard** : Une adresse IP publique de référence Standard peut être associée à une machine virtuelle ou à un équilibreur de charge front-end. Si vous créez une adresse IP publique dans une région qui prend en charge les zones de disponibilité, le paramètre **Zone de disponibilité** est défini sur *Redondant interzone* par défaut. Pour plus d’informations sur les zones de disponibilité, consultez **Zone de disponibilité**. La référence SKU Standard est nécessaire si vous associez l’adresse à un équilibreur de charge Standard. Pour plus d’informations sur les équilibreurs de charge Standard, consultez [Référence SKU d’Azure Load Balancer Standard](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Quand vous attribuez une adresse IP publique de référence SKU standard à l’interface réseau d’une machine virtuelle, vous devez explicitement autoriser le trafic prévu avec un [groupe de sécurité réseau](./network-security-groups-overview.md#network-security-groups). La communication avec la ressource est possible uniquement si vous créez et associez un groupe de sécurité réseau et que vous autorisez explicitement le trafic prévu.|
   |Niveau|Oui|Indique si l’adresse IP est associée à une région (**Régionale**) ou est « anycast » dans plusieurs régions (**Globale**). *Notez qu’une adresse IP « de niveau global » est une fonctionnalité d’évaluation pour les adresses IP standard et n’est actuellement utilisée que pour les équilibreurs de charge interrégions*.|
   |Nom|Oui|Le nom doit être unique au sein du groupe de ressources que vous avez sélectionné.|
   |Affectation d’adresses IP|Oui|**Dynamique :** les adresses dynamiques sont affectées une fois qu’une adresse IP publique est associée à une ressource Azure, et que la ressource est démarrée pour la première fois. Les adresses dynamiques peuvent changer si une ressource, telle qu’une machine virtuelle, est arrêtée (libérée), puis redémarrée. L’adresse reste la même si une machine virtuelle est redémarrée ou arrêtée. Lorsqu’une ressource d’adresse IP publique est supprimée d’une ressource, l’adresse dynamique est libérée.<br> **Statique :** les adresses statiques sont attribuées durant la création d’une adresse IP publique. Les adresses statiques ne sont pas libérées tant que la ressource d’adresse IP publique n’est pas supprimée. <br>Remarque : Si vous sélectionnez *IPv6* comme **Version IP**, la méthode d’affectation doit être *Dynamique* pour la référence SKU de base.  Les adresses de référence SKU standard sont *statiques* pour IPv4 et IPv6. |
  |Préférence de routage |Yes| Par défaut, la préférence de routage pour les adresses IP publiques est définie sur le « Réseau Microsoft », qui remet le trafic à l’utilisateur via le réseau étendu mondial de Microsoft.  La sélection de « Internet » réduit les déplacements sur le réseau de Microsoft, et utilise plutôt le réseau du fournisseur de services Internet pour acheminer le trafic à un coût optimisé.  Une préférence de routage des adresses IP publiques ne peut pas être modifiée une fois créée.  Pour plus d’informations sur la préférence de routage, consultez la section [Qu’est-ce qu’une préférence de routage (préversion) ?](./routing-preference-overview.md).   |
   |Délai d’inactivité (minutes)|Non|Durée (en minutes) de maintien d’une connexion TCP ou HTTP ouverte sans utiliser les clients pour envoyer des messages keep-alive. Si vous sélectionnez IPv6 comme **Version IP**, cette valeur est définie sur 4 minutes et ne peut pas être modifiée. |
   |Étiquette du nom DNS|Non|Doit être unique dans l’emplacement Azure où vous créez le nom (pour tous les abonnements et tous les clients). Azure inscrit automatiquement le nom et l’adresse IP dans son DNS pour que vous puissiez vous connecter à une ressource avec le nom. Azure ajoute un sous-réseau par défaut, tel que *location.cloudapp.azure.com*, au nom que vous indiquez pour créer le nom DNS complet. Si vous choisissez de créer les deux versions d’adresse, le même nom DNS est assigné aux adresses IPv4 et IPv6. Le DNS par défaut d’Azure contient à la fois les enregistrements de nom AAAA IPv4 et IPv6. Ce service renvoie les deux enregistrements lors de la recherche du nom DNS. Le client choisit l’adresse (IPv4 ou IPv6) avec laquelle communiquer. Vous pouvez utiliser le service Azure DNS pour configurer un nom DNS avec un suffixe personnalisé qui se résout en adresse IP publique. Pour plus d’informations, consultez [Utiliser Azure DNS avec une adresse IP publique Azure](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address).|
   |Nom (visible uniquement si vous sélectionnez la version d’adresse IP **Les deux**)|Oui, si vous sélectionnez la version d’adresse IP **Les deux**|Le nom doit être différent de celui que vous entrez pour le premier **Nom** dans cette liste. Si vous choisissez de créer une adresse IPv4 et une adresse IPv6, le portail crée deux ressources d’adresse IP publique distinctes : une pour chaque version d’adresse IP qui lui est assignée.|
   |Affectation d’adresses IP (visible uniquement si vous sélectionnez la version d’adresse IP **Les deux**)|Oui, si vous sélectionnez la version d’adresse IP **Les deux**| Mêmes restrictions que pour l’affectation d’adresses IP ci-dessus. |
   |Abonnement|Oui|Doit exister dans le même [abonnement](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) que la ressource à laquelle vous allez associer les adresses IP publiques.|
   |Resource group|Oui|Peut exister dans un [groupe de ressources](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) identique ou différent de celui de la ressource à laquelle vous allez associer les adresses IP publiques.|
   |Emplacement|Oui|Doit exister au même [emplacement](https://azure.microsoft.com/regions) (également appelé région) que la ressource à laquelle vous allez associer les adresses IP publiques.|
   |Zone de disponibilité| Non | Ce paramètre s’affiche uniquement si vous sélectionnez un type d’adresse IP et un emplacement pris en charge. Les adresses IP publiques de référence SKU **De base** et celles du niveau **Global** ne prennent pas en charge les zones de disponibilité. Vous pouvez sélectionner aucune zone (l’option par défaut), une zone spécifique ou redondant interzone. Le choix dépendra de vos exigences spécifiques en matière de défaillance de domaine. Pour obtenir la liste des emplacements pris en charge ainsi que des informations complémentaires sur les zones de disponibilité, consultez [Vue d’ensemble de zones de disponibilité](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="view-modify-settings-for-or-delete-a-public-ip-address"></a>Afficher une adresse IP publique, modifier ses paramètres ou la supprimer

   - **Afficher/Lister** : passez en revue les paramètres d’une adresse IP publique, notamment la référence SKU, l’adresse et toute association. Les associations peuvent être des front-ends de l’équilibreur de charge, des machines virtuelles et d’autres ressources Azure.
   - **Modifier** : modifiez les paramètres souhaités en utilisant les informations indiquées dans [Créer une adresse IP publique](#create-a-public-ip-address). Il s’agit de paramètres, tels que le délai d’inactivité, l’étiquette de nom DNS ou la méthode d’affectation. Pour prendre connaissance du processus complet de mise à niveau d’une référence SKU d’IP publique, et passer de De base à Standard, consultez [Mettre à niveau des adresses IP publiques Azure](./public-ip-upgrade-portal.md).
   
   >[!WARNING]
   >Supprimez l’adresse de toutes les configurations d’IP applicables (voir la section **Supprimer**) pour passer l’affectation d’une adresse IP publique, de statique à dynamique. Lorsque vous remplacez la méthode d’affectation statique par celle dynamique, vous perdez l’adresse IP qui a été affectée à la ressource IP publique. Si les serveurs DNS publics Azure conservent un mappage entre les adresses statiques ou dynamiques et une étiquette de nom DNS (si vous en avez défini une), une adresse IP dynamique peut être modifiée au démarrage de la machine virtuelle après qu’elle a été arrêtée (libérée). Pour empêcher toute modification de l’adresse, affectez une adresse IP statique.
   
|Opération|Portail Azure|Azure PowerShell|Azure CLI|
|---|---|---|---|
|Vue | Dans la section **Vue d’ensemble** d’une adresse IP publique |[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) pour récupérer un objet d’adresse IP publique et afficher ses paramètres| [az network public-IP show](/cli/azure/network/public-ip#az_network_public_ip_show) pour afficher les paramètres|
|List | Sous la catégorie **Adresses IP publiques** |[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) pour récupérer un ou plusieurs objets d’adresse IP publique et afficher ses paramètres|[az network public-ip list](/cli/azure/network/public-ip#az_network_public_ip_list) pour répertorier les adresses IP publiques|
|Modifier | En cas d’adresse IP dissociée, sélectionnez **Configuration** pour accéder aux options : </br> Modifier le délai d’inactivité. </br> Étiquette de nom DNS. </br> Changer l’affectation d’une adresse IP, de statique à dynamique. </br> Mettre à niveau une IP de base vers standard. |[Set-AzPublicIpAddress](/powershell/module/az.network/set-azpublicipaddress) pour mettre à jour les paramètres |[az network public-ip update](/cli/azure/network/public-ip#az_network_public_ip_update) pour mettre à jour |

   - **Supprimer** : la suppression des adresses IP publiques nécessite que l’objet IP publique ne soit pas associé à une configuration d’IP ou à une interface réseau de machine virtuelle. Pour plus d’informations, consultez le tableau suivant.

|Ressource|Portail Azure|Azure PowerShell|Azure CLI|
|---|---|---|---|
|[Machine virtuelle](./remove-public-ip-address-vm.md)|Sélectionnez **Dissocier** pour dissocier l’adresse IP de la configuration de la carte réseau, puis sélectionnez **Supprimer**.|[Set-AzPublicIpAddress](/powershell/module/az.network/set-azpublicipaddress) pour dissocier l’adresse IP de la configuration de la carte réseau ; [Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress) pour supprimer|[az network public-ip update avec le paramètre « --remove »](/cli/azure/network/public-ip#az_network_public_ip_update) pour supprimer l’adresse IP de la configuration de la carte réseau. Utilisez [az network public-ip delete](/cli/azure/network/public-ip#az_network_public_ip_delete) pour supprimer l’IP publique. |
|Front-end de l’équilibreur de charge | Accédez à une adresse IP publique inutilisée et sélectionnez **Associer**. Choisissez l’équilibreur de charge avec la configuration d’adresse IP front-end appropriée pour remplacer l’adresse IP. L’ancienne IP peut être supprimée à l’aide de la même méthode que celle d’une machine virtuelle.  | Utilisez [Set-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/set-azloadbalancerfrontendipconfig) pour associer une nouvelle configuration d’IP front-end à un équilibreur de charge public. Utilisez [Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress) pour supprimer une IP publique. Vous pouvez également utiliser [Remove-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/remove-azloadbalancerfrontendipconfig) pour supprimer une configuration d’IP front-end s’il en existe plusieurs. | Utilisez [az network lb frontend-ip update](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_update) pour associer une nouvelle configuration d’IP front-end à un équilibreur de charge public. Utilisez [Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress) pour supprimer une IP publique. Vous pouvez également utiliser [az network lb frontend-ip delete](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_delete) pour supprimer une configuration d’IP front-end s’il en existe plusieurs. |
|Pare-feu|N/A| [Libérer](../firewall/firewall-faq.yml#how-can-i-stop-and-start-azure-firewall) pour désallouer le pare-feu et supprimer toutes les configurations d’IP | Utilisez [az network firewall ip-config delete](/cli/azure/network/firewall/ip-config#az_network_firewall_ip_config_delete) pour supprimer l’IP. Utilisez PowerShell pour la libérer en premier. |

## <a name="virtual-machine-scale-sets"></a>Virtual Machine Scale Sets

Lorsque vous utilisez un groupe de machines virtuelles identiques avec des adresses IP publiques, il n’y a pas d’objets IP publics distincts associés à chaque instance de machine virtuelle. Toutefois, un objet de préfixe d’adresse IP publique [peut être utilisé pour générer les adresses IP d’instance](https://azure.microsoft.com/resources/templates/vmms-with-public-ip-prefix/).

Pour lister les adresses IP publiques sur un groupe de machines virtuelles identiques, vous pouvez utiliser PowerShell ([Get-AzPublicIpAddress -VirtualMachineScaleSetName](/powershell/module/az.network/get-azpublicipaddress)) ou l’interface CLI ([az virtual machine scale set list-instance-public-ips](/cli/azure/vmss#az_vmss_list_instance_public_ips)).

Pour plus d’informations, consultez [Réseau pour les groupes de machines virtuelles identiques Azure](../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#public-ipv4-per-virtual-machine).

## <a name="assign-a-public-ip-address"></a>Assigner une adresse IP publique

Découvrez comment attribuer une adresse IP publique aux ressources suivantes :

- Machine virtuelle [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) en cours de création. Ajout d’une IP à une [machine virtuelle existante](virtual-network-network-interface-addresses.md#add-ip-addresses).
- [Équilibreur de charge public](../load-balancer/quickstart-load-balancer-standard-public-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Application Gateway](../application-gateway/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Connexion site à site à l’aide d’une passerelle VPN](../vpn-gateway/tutorial-site-to-site-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Groupe de machines virtuelles identiques](../virtual-machine-scale-sets/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Passerelle NAT](./nat-gateway/tutorial-create-nat-gateway-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure Bastion](../bastion/quickstart-host-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Pare-feu Azure](../firewall/tutorial-firewall-deploy-portal-policy.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Équilibreur de charge inter-région](../load-balancer/tutorial-cross-region-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>Autorisations

Pour gérer les adresses IP publiques, votre compte doit être affecté au rôle de [contributeur de réseaux](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor). Un rôle [personnalisé](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) est également pris en charge. Le rôle personnalisé doit recevoir les actions appropriées, énumérées dans le tableau suivant :

| Action                                                             | Nom                                                           |
| ---------                                                          | -------------                                                  |
| Microsoft.Network/publicIPAddresses/read                           | Lire une adresse IP publique                                          |
| Microsoft.Network/publicIPAddresses/write                          | Créer ou mettre à jour une adresse IP publique                           |
| Microsoft.Network/publicIPAddresses/delete                         | Supprimer une adresse IP publique                                     |
| Microsoft.Network/publicIPAddresses/join/action                    | Associer une adresse IP publique à une ressource                    |

## <a name="next-steps"></a>Étapes suivantes

Le coût des adresses IP publiques est modique. Pour voir les prix, consultez la page [Tarification des adresses IP](https://azure.microsoft.com/pricing/details/ip-addresses).

- Créez une adresse IP publique avec les exemples de scripts [PowerShell](powershell-samples.md) ou [Azure CLI](cli-samples.md), ou à l’aide des [modèles Azure Resource Manager](template-samples.md)
- Créer et attribuer des [définitions Azure Policy](./policy-reference.md) pour les adresses IP publiques