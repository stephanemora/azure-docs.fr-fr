---
title: Autres exigences réseau pour SAP HANA sur Azure (grandes instances) | Microsoft Docs
description: Apprenez-en davantage sur les exigences réseau supplémentaires pour SAP HANA sur Azure (grandes instances) que vous pouvez avoir.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 6/3/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a91f2116eb7eb2e6d7908e5c8c4e5171fff8c294
ms.sourcegitcommit: 47ac63339ca645096bd3a1ac96b5192852fc7fb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114361069"
---
# <a name="other-network-requirements-for-large-instances"></a>Autres exigences réseau supplémentaires pour les grandes instances

Dans cet article, nous allons examiner d’autres exigences réseau que vous pouvez rencontrer lors du déploiement de SAP HANA – Grandes instances sur Azure.

## <a name="prerequisites"></a>Configuration requise

Cet article suppose que vous avez accompli les étapes décrites dans :
- [Connexion de machines virtuelles Azure à de grandes instances HANA](hana-connect-azure-vm-large-instances.md)
- [Connecter un réseau virtuel à de grandes instances HANA](hana-connect-vnet-express-route.md)

## <a name="add-more-ip-addresses-or-subnets"></a>Ajouter des adresses IP ou des sous-réseaux supplémentaires

Vous devrez peut-être ajouter d’autres adresses IP ou sous-réseaux. Utilisez le portail Azure, PowerShell ou l’interface Azure CLI pour ajouter des adresses IP ou des sous-réseaux.

Ajoutez la nouvelle plage d’adresses IP en tant que nouvelle plage à l’espace d’adressage du réseau virtuel. Ne générez pas une nouvelle plage agrégée. Envoyez ce changement à Microsoft. Vous pouvez ainsi vous connecter à partir de cette nouvelle plage d’adresses IP aux grandes instances HANA dans votre client. Vous pouvez ouvrir une demande de support Azure pour que le nouvel espace d’adressage du réseau virtuel soit ajouté. Après avoir reçu confirmation, suivez les étapes décrites dans [Connexion de machines virtuelles Azure à de grandes instances HANA](hana-connect-azure-vm-large-instances.md). 

Pour créer un sous-réseau dans le portail Azure, consultez [Créer un réseau virtuel à l’aide du portail Azure](../../../virtual-network/manage-virtual-network.md#create-a-virtual-network). Pour en créer un à partir de PowerShell, consultez [Créer un réseau virtuel à l’aide de PowerShell](../../../virtual-network/manage-virtual-network.md#create-a-virtual-network).

## <a name="add-virtual-networks"></a>Ajouter des réseaux virtuels

Après avoir connecté un ou plusieurs réseaux virtuels Azure, vous voudrez peut-être en connecter d’autres qui accèdent à SAP HANA sur Azure (grandes instances). Commencez par envoyer une demande de support Azure. Dans cette demande, fournissez les informations spécifiques qui identifient le déploiement Azure en question. Spécifiez aussi la ou les plages d’espace d’adressage IP de l’espace d’adressage du réseau virtuel Azure. Le management des services SAP HANA sur Microsoft fournit alors les informations nécessaires pour connecter les réseaux virtuels ajoutés et Azure ExpressRoute. Pour chaque réseau virtuel, vous avez besoin d’une clé d’autorisation unique pour connecter le circuit ExpressRoute aux grandes instances HANA.

## <a name="increase-expressroute-circuit-bandwidth"></a>Augmenter la bande passante du circuit ExpressRoute

Consultez SAP HANA sur Microsoft Service Management. S’il vous est recommandé d’augmenter la bande passante du circuit ExpressRoute de SAP HANA sur Azure (grandes instances), créez une demande de support Azure. (Vous pouvez demander une augmentation maximale de 10 Gbits/s pour une seule bande passante de circuit.) Vous recevrez ensuite une notification une fois l’opération terminée ; vous n’avez rien d’autre à faire pour activer cette vitesse supérieure dans Azure.

## <a name="add-another-expressroute-circuit"></a>Ajouter un autre circuit ExpressRoute

Consultez SAP HANA sur Microsoft Service Management. S’il vous est conseillé d’ajouter un autre circuit ExpressRoute, créez une demande de support Azure (y compris une demande d’obtention des informations d’autorisation pour se connecter au nouveau circuit). Avant d’effectuer la demande, vous devez définir l’espace d’adressage utilisé sur les réseaux virtuels. SAP HANA sur Microsoft Service Management peut ensuite fournir l’autorisation.

Quand le nouveau circuit est créé et la configuration du management des services SAP HANA sur Microsoft terminée, vous recevez une notification avec les informations nécessaires pour continuer. Vous ne pouvez pas connecter des réseaux virtuels Azure à ce circuit ajouté s’ils sont déjà connectés à un autre circuit ExpressRoute SAP HANA sur Azure (grande instance) dans la même région Azure.

## <a name="delete-a-subnet"></a>Supprimer un sous-réseau

Pour supprimer un sous-réseau de réseau virtuel, vous pouvez utiliser le portail Azure, PowerShell ou Azure CLI. Si l’espace d’adressage ou la plage d’adresses IP de votre réseau virtuel Azure était une plage agrégée, vous n’avez rien à faire avec Microsoft. (Le réseau virtuel continue à propager l’espace d’adressage de l’itinéraire BGP qui inclut le sous-réseau supprimé.) 

Vous avez peut-être défini la plage d’adresses ou l’espace d’adressage du réseau virtuel Azure comme plusieurs plages d’adresses IP. Il se peut que l’une de ces plages ait été attribuée à votre sous-réseau supprimé. Veillez à la supprimer de l’espace d’adressage de votre réseau virtuel. Ensuite, demandez au management des services SAP HANA sur Microsoft de la supprimer des plages avec lesquelles SAP HANA sur Azure (grandes instances) est autorisé à communiquer.

Pour plus d’informations, consultez [Supprimer un sous-réseau](../../../virtual-network/virtual-network-manage-subnet.md#delete-a-subnet).

## <a name="delete-a-virtual-network"></a>Supprimer un réseau virtuel

Pour plus d’informations, consultez [Supprimer un réseau virtuel](../../../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

Le management des services SAP HANA sur Microsoft supprime les autorisations existantes sur le circuit ExpressRoute de SAP HANA sur Azure (grandes instances). Il supprime également l’espace d’adressage ou la plage d’adresses IP du réseau virtuel Azure qui communiquent avec les grandes instances HANA.

Une fois que vous avez supprimé le réseau virtuel, ouvrez une demande de support Azure pour fournir la ou les plages d’adresses IP à supprimer.

Veillez à tout supprimer. Supprimez les éléments suivants :
- Connexion ExpressRoute
- Passerelle de réseau virtuel
- Adresse IP publique de la passerelle de réseau virtuel
- Réseau virtuel

## <a name="delete-an-expressroute-circuit"></a>Supprimer un circuit ExpressRoute

Pour supprimer un circuit ExpressRoute supplémentaire de SAP HANA sur Azure (grandes instances), ouvrez une demande de support Azure avec le management des services SAP HANA sur Microsoft. Demandez de supprimer le circuit. Dans l’abonnement Azure, vous pouvez supprimer ou conserver le réseau virtuel en fonction des besoins. Toutefois, vous devez supprimer la connexion entre le circuit ExpressRoute des grandes instances HANA et la passerelle de réseau virtuel liée.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment installer et configurer SAP HANA (grandes instances) sur Azure.

> [!div class="nextstepaction"]
> [Installer et configurer SAP HANA (grandes instances)](hana-installation.md)
