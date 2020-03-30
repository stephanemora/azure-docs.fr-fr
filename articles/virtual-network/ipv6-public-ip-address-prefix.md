---
title: Préfixe d’adresses IPv6 publiques dans Réseau virtuel Azure
titlesuffix: Azure Virtual Network
description: Apprenez-en davantage sur le préfixe d’adresses IPv6 publiques dans Réseau virtuel Azure.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/15/2019
ms.author: kumud
ms.openlocfilehash: 8254a7d86d5cadc2ddc03940f4ab2d08de74bd86
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72965175"
---
# <a name="reserved-public-ipv6-address-prefix-preview"></a>Préfixe d’adresses IPv6 publiques réservées (préversion)

Dans Azure, les machines virtuelles et les réseaux virtuels (VNet) à double pile (IPv4+IPv6) sont sécurisés par défaut, puisqu’ils n’ont pas de connectivité Internet. Vous pouvez facilement ajouter une connectivité Internet IPv6 à vos équilibreurs de charge Azure et à vos machines virtuelles avec des adresses IPv6 publiques que vous obtenez à partir d’Azure.

Toutes les adresses IP publiques que vous réservez sont associées à une région Azure de votre choix et à votre abonnement Azure. Vous pouvez déplacer une adresse IP publique IPv6 réservée (statique) entre les équilibreurs de charge Azure ou les machines virtuelles de votre abonnement. Vous pouvez dissocier entièrement l’adresse IP publique IPv6, et celle-ci sera conservée pour votre utilisation quand vous serez prêt.

> [!WARNING]
> Faites attention à ne pas supprimer accidentellement vos adresses IP publiques. La suppression d’une adresse IP publique entraîne sa suppression de votre abonnement, et vous ne pourrez pas la récupérer (même avec l’aide du support Azure).

En plus de réserver des adresses IPv6 individuelles, vous pouvez réserver des plages contiguës d’adresses IPv6 Azure (appelées préfixes IP) pour votre utilisation.  À l’instar des adresses IP individuelles, les préfixes réservés sont associés à une région Azure de votre choix et à votre abonnement Azure. La réservation d’une plage d’adresses contiguë et prévisible a de nombreuses utilisations. Par exemple, vous pouvez grandement simplifier la *mise en liste verte*, par votre entreprise et vos clients, des adresses IP de vos applications hébergées par Azure, car vos plages d’adresses IP statiques peuvent être facilement programmées dans des pare-feu locaux.  Vous pouvez créer des adresses IP publiques à partir de votre préfixe IP en fonction des besoins et, quand vous supprimez ces adresses IP publiques, elles sont *retournées* à votre plage réservée afin que vous puissiez les réutiliser ultérieurement. Toutes les adresses IP de votre préfixe IP sont réservées à votre usage exclusif jusqu’à ce que vous supprimiez votre préfixe.

> [!Important]
> Le protocole IPv6 pour réseau virtuel Azure est actuellement en préversion publique. Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Consultez les [Conditions d’utilisation supplémentaires des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="ipv6-prefix-sizes"></a>Tailles de préfixe IPv6
Les tailles de préfixe IP public suivantes sont disponibles :

-  Taille minimale de préfixe IPv6 :  /127 = 2 adresses
-  Taille maximale de préfixe IPv6 :  /124 = 16 adresses

La taille de préfixe est spécifiée en tant que taille de masque CIDR (Classless Inter-Domain Routing). Par exemple, un masque de /128 représente une adresse IPv6 individuelle, car les adresses IPv6 sont composées de 128 bits.

## <a name="pricing"></a>Tarifs
 
Pour connaître les coûts associés à l’utilisation des adresses IP publiques Azure (adresses IP individuelles et plages d’adresses IP), consultez [Tarification des adresses IP](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="limitations"></a>Limites
IPv6 est pris en charge sur les adresses IP publiques de base uniquement avec l’allocation « dynamique », ce qui signifie que l’adresse IPv6 changera si vous supprimez et redéployez votre application (machines virtuelles ou équilibreurs de charge) dans Azure. La prise en charge de l’allocation statique (réservée) par l’adresse IP publique standard IPv6 via les équilibrages de charge internes standard peut également prendre en charge l’allocation dynamique à partir du sous-réseau auquel elles sont affectées.  

Nous vous recommandons d’utiliser des adresses IP publiques standard et des équilibreurs de charge standard pour vos applications IPv6.

## <a name="next-steps"></a>Étapes suivantes
- Réserver un [préfixe d’adresses IPv6](ipv6-reserve-public-ip-address-prefix.md) publiques.
- En savoir plus sur les [adresses IPv6](ipv6-overview.md).
- En savoir plus sur la façon de [créer et utiliser des adresses IP publiques](virtual-network-public-ip-address.md) (IPv4 et IPv6) dans Azure.
