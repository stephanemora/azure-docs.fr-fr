---
title: Se connecter à un réseau pair dans Azure Lab Services | Microsoft Docs
description: Découvrez comment connecter votre réseau lab avec un autre réseau en tant que pair. Par exemple, connectez votre réseau local d’établissement scolaire/université avec le réseau virtuel Lab dans Azure.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: 5e013011f81542aa279ba9276a6a1aac01eb9e41
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/18/2020
ms.locfileid: "77443184"
---
# <a name="connect-your-labs-network-with-a-peer-virtual-network-in-azure-lab-services"></a>Connecter un réseau lab avec un réseau virtuel pair dans Azure Lab Services 
Cet article fournit des informations concernant le Peering de votre réseau lab avec un autre réseau. 

## <a name="overview"></a>Vue d’ensemble
Le Peering d’un réseau virtuel vous permet de connecter des réseaux virtuels Azure en toute transparence. Une fois homologués, les réseaux virtuels apparaissent comme un seul réseau à des fins de connectivité. Le trafic entre les machines virtuelles des réseaux virtuels appairés est acheminé via l’infrastructure principale de Microsoft de façon assez semblable au trafic entre des machines virtuelles d’un même réseau virtuel via des IP privées seulement. Pour en savoir plus, consultez [Peering de réseaux virtuels](../../virtual-network/virtual-network-peering-overview.md).

Il vous faudra peut-être connecter votre réseau lab avec un réseau virtuel pair dans certains scénarios, notamment dans les scénarios suivants :

- Les machines virtuelles du lab disposent d’un logiciel qui se connecte aux serveurs de licences locaux pour obtenir une licence.
- Les machines virtuelles du lab doivent accéder à des jeux de données (ou à d’autres fichiers) sur les partages réseau de l’université. 

Certains des réseaux locaux sont connectés au réseau virtuel Azure via [ExpressRoute](../../expressroute/expressroute-introduction.md) ou via la [Passerelle de réseau virtuel](../../vpn-gateway/vpn-gateway-about-vpngateways.md). Ces services doivent être configurés en dehors de Azure Lab Services. Pour en savoir plus sur la connexion d’un réseau local à Azure à l’aide d’ExpressRoute, consultez [Vue d’ensemble d’ExpressRoute](../../expressroute/expressroute-introduction.md). Pour la connectivité locale à l’aide d’une passerelle de réseau virtuel, la passerelle, le réseau virtuel spécifié et le compte lab doivent tous être situés dans la même région.

## <a name="configure-at-the-time-of-lab-account-creation"></a>Effectuer la configuration au moment de la création du compte lab
Lors de la création du nouveau compte lab, vous pouvez choisir un réseau virtuel existant qui s’affiche dans la liste déroulante **Appairer un réseau virtuel** dans l’onglet **Avancé**. Le réseau virtuel sélectionné est connecté aux labs créés sous le compte lab. Toutes les machines virtuelles des labs qui sont créées après la création de cette modification auront accès aux ressources sur le réseau virtuel pair. 

![Sélectionner le réseau virtuel à appairer](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer.png)

> [!NOTE]
> Pour obtenir des instructions pas à pas détaillées pour la création d’un compte lab, consultez [Configurer un compte lab](tutorial-setup-lab-account.md)


## <a name="configure-after-the-lab-is-created"></a>Effectuer la configuration une fois le compte lab créé
La même propriété peut être activée à partir de l’onglet **Configuration de Labs** de la page **Compte Lab** si vous n’avez pas configuré un réseau pair au moment de la création d’un compte lab. Les modifications apportées à ce paramètre s’appliquent uniquement aux labs créés après la modification. Comme vous pouvez le voir sur l’image, vous pouvez activer ou désactiver le champ **Appairer un réseau virtuel** pour les labs dans le compte lab. 

![Activer ou désactiver le Peering de réseaux virtuels une fois le lab créé](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer-existing-lab.png) 

Lorsque vous sélectionnez un réseau virtuel pour le champ **Appairer un réseau virtuel**, l’option **Autoriser le créateur du lab à choisir l’emplacement du lab** est désactivée. Cela s’explique par le fait que les laboratoires dans le compte de laboratoire doivent être dans la même région que le compte de laboratoire pour pouvoir se connecter aux ressources dans le réseau virtuel de pair. 

> [!IMPORTANT]
> La modification de ce paramètre s’applique uniquement aux labs créés une fois la modification effectuée, pas pour les labs existants. 


## <a name="next-steps"></a>Étapes suivantes
Voir les articles suivants :

- [Autoriser le créateur du lab à choisir l’emplacement du lab](allow-lab-creator-pick-lab-location.md)
- [Attacher une galerie d’images partagées à un lab](how-to-attach-detach-shared-image-gallery.md)
- [Ajouter un utilisateur comme propriétaire de lab](how-to-add-user-lab-owner.md)
- [Afficher les paramètres de pare-feu pour un lab](how-to-configure-firewall-settings.md)
- [Configurer les autres paramètres d’un lab](how-to-configure-lab-accounts.md)