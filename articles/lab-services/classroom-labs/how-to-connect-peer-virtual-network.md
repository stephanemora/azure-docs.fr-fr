---
title: Se connecter à un réseau pair à pair dans Azure Lab Services | Microsoft Docs
description: Découvrez comment connecter votre réseau de laboratoire avec un autre réseau en tant qu’homologue. Par exemple, connecter votre réseau d’établissement scolaire/université en local avec un réseau virtuel du laboratoire dans Azure.
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
ms.date: 05/07/2019
ms.author: spelluru
ms.openlocfilehash: c9b305beae1b385d4714e3a80e6843c7e76a4f60
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65410988"
---
# <a name="connect-your-labs-network-with-a-peer-virtual-network-in-azure-lab-services"></a>Connecter le réseau de votre laboratoire avec un réseau virtuel homologue dans Azure Lab Services 
Cet article fournit des informations sur l’homologation de votre réseau de laboratoires avec un autre réseau. 

## <a name="overview"></a>Présentation
Homologation de réseaux virtuels vous permet de connecter des réseaux virtuels Azure de façon transparente. Une fois homologués, les réseaux virtuels apparaissent comme un seul réseau à des fins de connectivité. Le trafic entre les machines virtuelles dans les réseaux virtuels homologués est acheminé via l’infrastructure principale de Microsoft, comme le trafic est acheminé entre les machines virtuelles dans le même réseau virtuel, via des adresses IP privées uniquement. Pour en savoir plus, consultez [Homologation de réseaux virtuels](../../virtual-network/virtual-network-peering-overview.md).

Vous devrez peut-être connecter le réseau de votre laboratoire avec un réseau virtuel homologue dans certains scénarios, y compris celles qui suivent :

- Les machines virtuelles dans le laboratoire ont des logiciels qui se connecte aux serveurs de licence en local pour acquérir une licence
- Les machines virtuelles dans le laboratoire doivent accéder à des jeux de données (ou tout autre fichier) sur les partages réseau de l’université. 

Certaines des réseaux locaux connectés au réseau virtuel Azure soit via [ExpressRoute](../../expressroute/expressroute-introduction.md) ou [passerelle de réseau virtuel](../../vpn-gateway/vpn-gateway-about-vpngateways.md). Ces services doivent être configurés en dehors d’Azure Lab Services. Pour en savoir plus sur la connexion d’un réseau local à Azure à l’aide d’ExpressRoute, consultez [présentation d’ExpressRoute]) (.. /expressroute/expressroute-introduction.MD). Pour la connectivité locale à l’aide d’une passerelle de réseau virtuel, la passerelle, spécifié de réseau virtuel, et le compte de laboratoire doit tous être dans la même région.

## <a name="configure-at-the-time-of-lab-account-creation"></a>Configurer au moment de la création de comptes de laboratoire
Lors de la création de compte nouveau laboratoire, vous pouvez choisir un réseau virtuel existant qui affiche dans le **réseau virtuel homologue** liste déroulante. Le réseau virtuel sélectionné est connected(peered) pour les laboratoires créés sous le compte de laboratoire. Toutes les machines virtuelles dans les laboratoires sont créées après la mise à cette modification aurait accès aux ressources sur le réseau virtuel homologué. 

![Sélectionnez le réseau virtuel à homologuer](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer.png)

> [!NOTE]
> Pour obtenir des instructions détaillées pour la création d’un compte de laboratoire, consultez [configurer un compte de laboratoire](tutorial-setup-lab-account.md)


## <a name="configure-after-the-lab-is-created"></a>Configurer une fois créé le laboratoire
La même propriété peut être activée à partir de la **configuration Labs** onglet de la **compte Lab** page si vous n’avez pas configuré un réseau pair à pair au moment de la création de comptes de laboratoire. Les modifications apportées à ce paramètre s’applique uniquement aux laboratoires créés après la modification. Comme vous pouvez le voir dans l’image, vous pouvez activer ou désactiver **réseau virtuel homologue** Labs dans le compte de laboratoire. 

![Activer ou désactiver l’homologation de réseaux après avoir créé le laboratoire](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer-existing-lab.png) 

Lorsque vous sélectionnez un réseau virtuel pour le **réseau virtuel homologue** champ, le **créateur de laboratoire autoriser pour choisir un emplacement de laboratoire** option est désactivée. Cela signifie que les laboratoires dans le compte de laboratoire doivent être dans la même région que le compte de laboratoire pour qu’ils puissent se connecter aux ressources dans le réseau virtuel homologue. 

> [!IMPORTANT]
> Modification de ce paramètre s’applique uniquement aux laboratoires qui sont créés après que la modification est apportée, pas pour les laboratoires existants. 


## <a name="next-steps"></a>Étapes suivantes
Consultez les articles suivants :

- [En tant qu’administrateur, créer et gérer des comptes de labo](how-to-manage-lab-accounts.md)
- [En tant que propriétaire de labo, créer et gérer des labos](how-to-manage-classroom-labs.md)
- [En tant que propriétaire de labo, configurer et publier des modèles](how-to-create-manage-template.md)
- [En tant qu’utilisateur du laboratoire, accéder aux laboratoires de classe](how-to-use-classroom-lab.md)

