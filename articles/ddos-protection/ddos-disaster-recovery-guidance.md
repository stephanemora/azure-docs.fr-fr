---
title: Continuité de l’activité Azure DDoS Protection Standard | Microsoft Docs
description: Découvrez la procédure à suivre si une interruption du service Azure affecte Azure DDoS Protection Standard.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/16/2021
ms.author: yitoh
ms.topic: article
ms.openlocfilehash: 5085f1584a737e75adccf4ec23bf709bede28a4b
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107730273"
---
# <a name="azure-ddos-protection-standard--business-continuity"></a>Azure DDoS Protection Standard – Continuité de l’activité

Dans Azure DDoS Protection Standard, la continuité de l’activité et la reprise d’activité après sinistre permettent à votre entreprise de continuer à fonctionner en cas de perturbation. Cet article traite de la disponibilité (intra-région) et de la récupération d’urgence.

## <a name="overview"></a>Vue d’ensemble
Azure DDoS Protection Standard protège les adresses IP publiques au sein des réseaux virtuels. La protection est simple à activer sur n’importe quel réseau virtuel, nouveau ou existant, et ne nécessite aucun changement au niveau de l’application ou des ressources.

Un réseau virtuel (VNet) est une représentation de votre réseau dans le cloud. Les réseaux virtuels servent de limite d’approbation pour héberger vos ressources, telles qu’Azure Application Gateway, Pare-feu Azure et Machines virtuelles Azure. Ils sont créés dans l’étendue d’une région. Vous pouvez *créer* des réseaux virtuels avec le même espace d’adressage dans deux régions différentes (par exemple, USA Est et USA Ouest), mais vous ne pourrez pas les connecter les uns aux autres en raison de leur espace d’adressage en commun. 

## <a name="business-continuity"></a>Continuité de l’activité

Peut-être que votre application peut être interrompue de différentes manières. Une région peut être coupée complètement des autres en raison d’une catastrophe naturelle ou d’une catastrophe partielle en raison d’une panne de plusieurs appareils ou services. L’impact sur votre réseaux virtuels protégés est différent dans chacune de ces situations.

**Q : si une panne se produit pour une région entière, que faire ? Par exemple, si une région est entièrement coupée en raison d’une catastrophe naturelle ? Que se passe-t-il pour les réseaux virtuels hébergés dans la région ?**

A : le réseau virtuel et les ressources dans la région affectée restent inaccessibles pendant la durée de l’interruption de service.

![Diagramme de réseau virtuel simple.](../virtual-network/media/virtual-network-disaster-recovery-guidance/vnet.png)

**Q : que faire pour recréer le même réseau virtuel dans une autre région ?**

A : les réseaux virtuels sont des ressources relativement légères. Vous pouvez appeler des API Azure pour créer un réseau virtuel avec le même espace d’adressage dans une autre région. Pour recréer le même environnement que celui qui était présent dans la région affectée, vous effectuez des appels d’API afin de redéployer les ressources dans les réseaux virtuels que vous aviez. Vous devrez également créer une passerelle VPN et vous connecter à votre réseau local si vous aviez une connectivité locale (comme dans un déploiement hybride).

Pour créer un réseau virtuel, consultez [Création d’un réseau virtuel](../virtual-network/manage-virtual-network.md#create-a-virtual-network).

**Q : un réplica d’un réseau virtuel dans une région donnée peut-il être recréé dans une autre région à l’avance ?**

A : oui, vous pouvez créer deux réseaux virtuels utilisant le même espace d’adressage IP privé et des ressources identiques dans deux régions différentes à l’avance. Si vous hébergez des services Internet dans le réseau virtuel, vous pourriez configurer Traffic Manager de manière à router géographiquement le trafic vers la région active. Toutefois, vous ne pouvez pas connecter deux réseaux virtuels avec le même espace d’adressage de réseau local sous peine de causer des problèmes de routage. En cas d’incident et de perte d’un réseau virtuel dans une région, vous pouvez connecter à votre réseau local l’autre réseau virtuel dans la région disponible avec l’espace d’adressage correspondant.

Pour créer un réseau virtuel, consultez [Création d’un réseau virtuel](../virtual-network/manage-virtual-network.md#create-a-virtual-network).

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [créer un plan de protection DDoS](manage-ddos-protection.md).