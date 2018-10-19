---
title: Conditions requises supplémentaires du réseau pour SAP HANA sur Azure (grandes instances) | Microsoft Docs
description: Conditions requises supplémentaires du réseau pour SAP HANA sur Azure (grandes instances).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8ee89553846309bd96f3cad3648e337488cf07d3
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44167598"
---
# <a name="additional-network-requirements"></a>Conditions requises supplémentaires du réseau

Vous pouvez avoir des conditions requises supplémentaires du réseau dans le cadre du déploiement de grandes Instances HANA. Cet article illustre les conditions requises du réseau suivantes :
- [Ajout d’adresses IP ou de sous-réseau ](#adding-more-ip-addresses-or-subnets)
- [Ajout de réseaux virtuels](#adding-vnets)
- [Augmentation de la bande passante d’un circuit ExpressRoute](#increasing-expressroute-circuit-bandwidth)
- [Ajout d’un circuit ExpressRoute](#adding-an-additional-expressroute-circuit)
- [Suppression d’un sous-réseau](#deleting-a-subnet)
- [Suppression d’un réseau virtuel](#deleting-a-vnet)
- [Suppression d’un circuit de routeur express](#deleting-an-expressroute-circuit)


## <a name="adding-more-ip-addresses-or-subnets"></a>Ajout d’adresses IP ou sous-réseaux supplémentaires

Utilisez le portail Azure, PowerShell ou l’interface de ligne de commande pour ajouter des adresses IP ou des sous-réseaux.

Dans ce cas, nous vous recommandons d’ajouter la nouvelle plage d’adresses IP en tant que nouvelle plage à l’espace d’adressage du réseau virtuel, au lieu de générer une nouvelle plage agrégée. Dans les deux cas, vous devez envoyer cette modification à Microsoft pour autoriser la connectivité depuis cette plage d’adresses IP vers les unités de grandes instances HANA dans votre client. Vous pouvez ouvrir une demande de support Azure pour obtenir l’ajout du nouvel espace d’adressage de réseau virtuel. Après avoir reçu la confirmation, effectuez les étapes suivantes.

Pour créer un sous-réseau supplémentaire à partir du portail Azure, consultez l’article [Créer un réseau virtuel à l’aide du portail Azure](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network), et pour le créer à partir de PowerShell, consultez [Créer un réseau virtuel à l’aide de PowerShell](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network).

## <a name="adding-vnets"></a>Ajout de réseaux virtuels

Après la connexion initiale d’un ou plusieurs réseaux virtuels Azure, vous voudrez peut-être ajouter d’autres réseaux virtuels qui accèdent à SAP HANA sur Azure (grandes instances). Tout d’abord, envoyez une demande de support Azure, dans laquelle vous incluez à la fois les informations propres au déploiement Azure et la ou les plages d’espace d’adressage IP de l’espace d’adressage du réseau virtuel Azure. SAP HANA sur Azure Service Management fournit alors les informations nécessaires à la connexion des réseaux virtuels supplémentaires et d’ExpressRoute. Pour chaque réseau virtuel, vous avez besoin d’une clé d’autorisation unique pour connecter le circuit ExpressRoute à de grandes instances HANA.

Procédure d’ajout d’un nouveau réseau virtuel Azure :

1. Effectuez la première étape du processus d’intégration ; voir la section _Création d’un réseau virtuel Azure_.
2. Effectuez la deuxième étape du processus d’intégration ; voir la section _Création d’un sous-réseau de passerelle_.
3. Pour connecter vos réseaux virtuels supplémentaires au circuit ExpressRoute de grande instance HANA, ouvrez une demande de support Azure contenant les informations sur le nouveau réseau virtuel, et demandez une nouvelle clé d’autorisation.
4. Une fois informé que l’autorisation est accordée, utilisez les informations d’autorisation fournies par Microsoft pour finaliser la troisième étape du processus d’intégration, voir la section _Liaison de réseaux virtuels_.

## <a name="increasing-expressroute-circuit-bandwidth"></a>Augmentation de la bande passante d’un circuit ExpressRoute

Consultez SAP HANA sur Azure Service Management. S’il vous est recommandé d’augmenter la bande passante du circuit ExpressRoute de SAP HANA sur Azure (grandes instances), créez une demande de support Azure. (Vous pouvez demander une augmentation maximale de 10 Gbits/s pour une seule bande passante de circuit.) Vous recevrez ensuite une notification une fois l’opération terminée ; aucune action supplémentaire n’est nécessaire pour activer cette vitesse supérieure dans Azure.

## <a name="adding-an-additional-expressroute-circuit"></a>Ajout d’un circuit ExpressRoute supplémentaire

Consultez SAP HANA sur Azure Service Management. S’il vous est recommandé d’ajouter un circuit ExpressRoute supplémentaire, envoyez une demande de support Azure pour créer un nouveau circuit ExpressRoute (et pour obtenir les informations d’autorisation permettant de s’y connecter). L’espace d’adressage qui va être utilisé sur les réseaux virtuels doit être défini avant d’effectuer la demande, afin que SAP HANA sur Azure Service Management puisse fournir l’autorisation.

Une fois le nouveau circuit créé et la configuration de SAP HANA sur Azure Service Management terminée, vous allez recevoir une notification avec les informations nécessaires pour continuer. Suivez les étapes ci-dessus pour créer et connecter le nouveau réseau virtuel à ce circuit supplémentaire. Vous ne pouvez pas connecter des réseaux virtuels Azure à ce circuit supplémentaire s’ils sont déjà connectés à un autre circuit ExpressRoute SAP HANA sur Azure (grande instance) dans la même région Azure.

## <a name="deleting-a-subnet"></a>Suppression d’un sous-réseau

Pour supprimer un sous-réseau de réseau virtuel, vous pouvez utiliser le portail Azure, PowerShell ou l’interface de ligne de commande. Si votre plage d’adresses IP de réseau virtuel Azure/espace d’adressage de réseau virtuel Azure était une plage agrégée, vous ne recevrez aucun suivi de la part de Microsoft. Toutefois, le réseau virtuel continue à propager l’espace d’adressage de l’itinéraire BGP qui inclut le sous-réseau supprimé. Si vous avez défini la plage d’adresses IP de réseau virtuel Azure/l’espace d’adressage de réseau virtuel Azure sous la forme de plusieurs plages d’adresses IP, dont l’une a été affectée à votre sous-réseau supprimé, vous devrez la supprimer de votre espace d’adressage de réseau virtuel et en informer l’équipe de gestion des services SAP HANA sur Azure pour qu’elle la supprime des plages avec lesquelles les grandes instances SAP HANA sur Azure sont autorisées à communiquer.

Pour supprimer un sous-réseau, consultez [Supprimer un sous-réseau](../../../virtual-network/virtual-network-manage-subnet.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-subnet) pour plus d’informations sur la création de sous-réseaux.

## <a name="deleting-a-vnet"></a>Suppression d’un réseau virtuel

Pour supprimer un réseau virtuel, consultez [Supprimer un réseau virtuel](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#delete-a-virtual-network). L’équipe de gestion des services SAP HANA sur Azure supprime les autorisations existantes sur le circuit ExpressRoute de SAP HANA sur Azure (grandes instances) et supprime la plage d’adresses IP du réseau virtuel Azure/l’espace d’adressage du réseau virtuel Azure pour la communication avec les grandes instances HANA.

Une fois que le réseau virtuel a été supprimé, ouvrez une demande de support Azure pour fournir la ou les plages d’adresses IP à supprimer.

Pour vous assurer que tout est supprimé, supprimez les éléments suivants :

- La connexion ExpressRoute, la passerelle de réseau virtuel, l’adresse IP publique de la passerelle de réseau virtuel et le réseau virtuel

## <a name="deleting-an-expressroute-circuit"></a>Suppression d’un circuit ExpressRoute

Pour supprimer un circuit supplémentaire ExpressRoute de SAP HANA sur Azure (grandes instances), ouvrez une demande de support Azure avec SAP HANA sur Azure Service Management et demandez à ce que le circuit soit supprimé. Dans l’abonnement Azure, vous pouvez supprimer ou conserver le réseau virtuel, en fonction des besoins. Toutefois, vous devez supprimer la connexion entre le circuit ExpressRoute des grandes instances HANA et la passerelle de réseau virtuel liée.

Si vous souhaitez également supprimer un réseau virtuel, suivez les instructions du paragraphe Suppression d’un réseau virtuel dans la section précédente.

**Étapes suivantes**

- Voir [Guide pratique d’installation et de configuration de SAP HANA (grandes instances) sur Azure](hana-installation.md).
