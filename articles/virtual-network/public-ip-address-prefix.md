---
title: Préfixe d’adresse IP publique Azure
description: Découvrez ce qu’est un préfixe d’adresse IP publique Azure et comment il vous permet d’attribuer des adresses IP publiques prévisibles à vos ressources.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2020
ms.author: allensu
ms.openlocfilehash: 0f71f845ef3209146ead79cafae2f3aa5c8c6d7d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82144517"
---
# <a name="public-ip-address-prefix"></a>Préfixe d’adresse IP publique

Un préfixe d’adresse IP publique est une plage réservée d’adresses IP pour vos points de terminaison publics dans Azure. Azure alloue une plage contiguë d’adresses à votre abonnement en fonction du nombre que vous spécifiez. Si vous n’êtes pas familiarisé avec les adresses IP publique, consultez [Adresses IP publiques](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses).

Les adresses IP publiques sont attribuées à partir d’un pool d’adresses dans chaque région Azure. Vous pouvez [télécharger](https://www.microsoft.com/download/details.aspx?id=56519) la liste des plages qu’Azure utilise pour chaque région. Par exemple, 40.121.0.0/16 est une plage parmi plus de 100 plages qu’Azure utilise dans la région USA Est. La plage inclut les adresses utilisables comprises entre 40.121.0.1 et 40.121.255.254.

Vous créez un préfixe d’adresse IP publique dans une région et un abonnement Azure en spécifiant un nom et le nombre d’adresses que le préfixe doit inclure. Par exemple, si vous créez le préfixe d’adresse IP publique /28, Azure alloue automatiquement 16 adresses parmi l’une de ses plages. Vous ne savez pas quelle plage Azure attribue jusqu’à ce que vous créiez la plage, mais les adresses sont contiguës. Les préfixes d’adresses IP publiques entraînent des frais. Pour plus d’informations, consultez [Tarifs des adresses IP](https://azure.microsoft.com/pricing/details/ip-addresses).

## <a name="why-create-a-public-ip-address-prefix"></a>Pourquoi créer un préfixe d’adresse IP publique ?

Quand vous créez des ressources d’adresse IP publique, Azure attribue une adresse IP publique disponible à partir d’une des plages utilisées dans la région. Une fois qu’Azure attribue l’adresse, vous connaissez cette dernière, mais tant qu’Azure n’attribue pas l’adresse, vous ne savez pas quelle adresse peut être affectée. Cela peut se révéler problématique quand, par exemple, vous ou vos partenaires commerciaux configurez des règles de pare-feu qui autorisent des adresses IP spécifiques. Chaque fois que vous attribuez une nouvelle adresse IP publique à une ressource, l’adresse doit être ajoutée à la règle de pare-feu. Quand vous attribuez des adresses à vos ressources à partir d’un préfixe d’adresse IP publique, il n’est pas nécessaire de mettre à jour les règles de pare-feu chaque fois que vous attribuez l’une des adresses, car la totalité de la plage peut être ajoutée à une règle.

## <a name="benefits"></a>Avantages

- Vous pouvez créer des ressources d’adresse IP publique à partir d’une plage connue.
- Vous ou vos partenaires commerciaux pouvez créer des règles de pare-feu avec des plages qui incluent des adresses IP publiques que vous avez déjà attribuées, ainsi que des adresses que vous n’avez pas encore attribuées. Cela évite d’avoir à changer les règles de pare-feu quand vous attribuez des adresses IP à de nouvelles ressources.
- La taille par défaut d’une plage que vous pouvez créer est /28 ou 16 adresses IP.
- Le nombre de plages que vous pouvez créer n’est pas limité. Toutefois, il existe des limites sur le nombre maximal d’adresses IP publiques statiques que vous pouvez avoir dans un abonnement Azure. Par conséquent, le nombre de plages que vous créez ne peut pas comprendre plus d’adresses IP publiques que vous ne pouvez en avoir dans votre abonnement. Pour plus d'informations, consultez [Limites Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Les adresses que vous créez à l’aide d’adresses provenant du préfixe peuvent être attribuées à n’importe quelle ressource Azure à laquelle vous pouvez attribuer une adresse IP publique.
- Vous pouvez facilement voir quelles adresses IP de la plage sont allouées et lesquelles ne le sont pas encore.

## <a name="scenarios"></a>Scénarios
Vous pouvez associer les ressources suivantes à une adresse IP publique statique à partir d’un préfixe :

|Ressource|Scénario|Étapes|
|---|---|---|
|Virtual Machines| L’association d’adresses IP publiques à partir d’un préfixe à vos machines virtuelles dans Azure réduit la surcharge de gestion quand il s’agit de mettre sur liste verte des adresses IP dans un pare-feu. Vous pouvez simplement mettre sur liste verte la totalité d’un préfixe avec une seule règle de pare-feu. Quand vous effectuez une mise à l’échelle avec des machines virtuelles dans Azure, vous pouvez associer des adresses IP du même préfixe, ce qui réduit les coûts, fait gagner du temps et limite la surcharge de gestion.| Pour associer des adresses IP d’un préfixe à votre machine virtuelle : 1. [Créez un préfixe.](manage-public-ip-address-prefix.md) 2. [Créez une adresse IP à partir du préfixe.](manage-public-ip-address-prefix.md) 3. [Associez l’adresse IP à l’interface réseau de votre machine virtuelle.](virtual-network-network-interface-addresses.md#add-ip-addresses) Vous pouvez également [associer les adresses IP à un groupe de machines virtuelles identiques](https://azure.microsoft.com/resources/templates/101-vmms-with-public-ip-prefix/).
| Standard Load Balancer | L’association d’adresses IP publiques d’un préfixe à la configuration d’adresses IP de votre frontend ou à la règle de trafic sortant d’un équilibreur de charge garantit la simplification de votre espace d’adressage IP public Azure. Vous pouvez simplifier votre scénario en préparant les connexions sortantes de sorte qu’elles proviennent d’une plage d’adresses IP contiguës définie par un préfixe d’adresse IP publique. | Pour associer des adresses IP d’un préfixe à votre équilibreur de charge : 1. [Créez un préfixe.](manage-public-ip-address-prefix.md) 2. [Créez une adresse IP à partir du préfixe.](manage-public-ip-address-prefix.md) 3. Quand vous créez l’équilibreur de charge, sélectionnez ou mettez à jour l’adresse IP créée à l’étape 2 ci-dessus comme adresse IP de frontend de votre équilibreur de charge. |
| Pare-feu Azure | Vous pouvez utiliser une adresse IP publique d’un préfixe pour une NAT de trafic sortant. Cela signifie que tout le trafic réseau virtuel sortant est traduit en adresse IP publique de [pare-feu Azure](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Étant donné que cette adresse IP provient d’un préfixe prédéterminé, il est très facile de savoir à l’avance à quoi ressemblera votre empreinte d’adresse IP publique dans Azure. | 1. [Créez un préfixe.](manage-public-ip-address-prefix.md) 2. [Créez une adresse IP à partir du préfixe.](manage-public-ip-address-prefix.md) 3. Quand vous [déployez le pare-feu Azure](../firewall/tutorial-firewall-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-the-firewall), veillez à sélectionner l’adresse IP précédemment allouée à partir du préfixe.|
| Application Gateway v2 | Vous pouvez utiliser l’adresse IP publique d’un préfixe pour la mise à l’échelle automatique et la passerelle redondante interzone Application Gateway v2. Étant donné que cette adresse IP provient d’un préfixe prédéterminé, il est très facile de savoir à l’avance à quoi ressemblera votre empreinte d’adresse IP publique dans Azure. | 1. [Créez un préfixe.](manage-public-ip-address-prefix.md) 2. [Créez une adresse IP à partir du préfixe.](manage-public-ip-address-prefix.md) 3. Quand vous [déployez Application Gateway](../application-gateway/quick-create-portal.md#create-an-application-gateway), veillez à sélectionner l’adresse IP précédemment allouée à partir du préfixe.|

## <a name="constraints"></a>Contraintes

- Vous ne pouvez pas spécifier les adresses IP pour le préfixe. Azure alloue les adresses IP pour le préfixe, en fonction de la taille que vous spécifiez.
- Vous pouvez créer un préfixe pouvant inclure jusqu’à 16 adresses IP ou un /28 par défaut. Pour plus d’informations, consultez [Demandes d’augmentation de limites réseau](https://docs.microsoft.com/azure/azure-portal/supportability/networking-quota-requests) et [Limites d’Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Vous ne pouvez pas changer la plage une fois que vous avez créé le préfixe.
- Seules les adresses IP publiques créées avec la référence (SKU) standard peuvent être attribuées à partir de la plage du préfixe. Pour en savoir plus sur les références (SKU) d’adresses IP publiques, consultez [Adresse IP publique](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses).
- Les adresses de la plage ne peuvent être attribuées qu’à des ressources Azure Resource Manager. Il n’est pas possible d’attribuer des adresses aux ressources du modèle de déploiement classique.
- Toutes les adresses IP publiques créées à partir du préfixe doivent exister dans la même région et le même abonnement Azure que le préfixe, et doivent être attribuées à des ressources de la même région et du même abonnement.
- Vous ne pouvez pas supprimer un préfixe si des adresses qu’il contient sont attribuées à des ressources d’adresse P publique associées à une ressource. Dissociez d’abord toutes les ressources d’adresse IP publique qui sont des adresses IP attribuées du préfixe.


## <a name="next-steps"></a>Étapes suivantes

- [Créer](manage-public-ip-address-prefix.md) un préfixe d’adresse IP publique
