---
title: Quelle est l’adresse IP 168.63.129.16 ? | Microsoft Docs
description: Apprenez-en davantage sur l'adresse IP 168.63.129.16 et sur la façon dont elle fonctionne avec vos ressources.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: v-jesits
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/15/2019
ms.author: genli
ms.openlocfilehash: 287f881fb17dd84357f540ee562e21c66c11ab95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77114363"
---
# <a name="what-is-ip-address-1686312916"></a>Quelle est l’adresse IP 168.63.129.16 ?

L’adresse IP 168.63.129.16 est une adresse IP publique virtuelle qui est utilisée pour établir un canal de communication vers les ressources de la plateforme Azure. Les clients peuvent définir n’importe quel espace d'adressage pour leur réseau virtuel privé dans Azure. Par conséquent, les ressources de la plateforme Azure doivent être présentées en tant qu’adresse IP publique unique. Cette adresse IP publique virtuelle facilite les choses suivantes :

- Permet à l’agent de machine virtuelle de communiquer avec la plateforme Azure pour signaler qu’il est dans un état « Prêt ».
- Permet la communication avec le serveur virtuel DNS pour fournir la résolution de nom filtré aux ressources (machine virtuelle, p. ex.) qui ne possèdent pas de serveur DNS personnalisé. Ce filtrage permet de s’assurer que les clients peuvent résoudre uniquement les noms d’hôte de leurs ressources.
- Donne les moyens aux [sondes d’intégrité de l’équilibreur de charge Azure](../load-balancer/load-balancer-custom-probe-overview.md) de déterminer l’intégrité des machines virtuelles.
- Permet à la machine virtuelle d’obtenir une adresse IP dynamique auprès du service DHCP dans Azure.
- Active les messages de pulsation de l’agent invité pour le rôle PaaS.

## <a name="scope-of-ip-address-1686312916"></a>Portée de l’adresse IP 168.63.129.16

L’adresse IP publique 168.63.129.16 est utilisée dans toutes les régions et tous les clouds nationaux. Cette adresse IP publique spéciale ne change pas car elle appartient à Microsoft. Nous vous conseillons d’autoriser cette adresse IP dans toutes les stratégies de pare-feu local (dans la machine virtuelle) dans le sens sortant. La communication est sécurisée entre cette adresse IP spéciale et les ressources car seule la plateforme Azure interne peut envoyer un message à partir de cette adresse. Si cette adresse est bloquée, un comportement inattendu peut se produire dans différentes situations. L’adresse IP 168.63.129.16 est une [adresse IP virtuelle du nœud hôte](../virtual-network/security-overview.md#azure-platform-considerations). Les routes définies par l’utilisateur n’affectent donc pas cette adresse.

- L’agent de machine virtuelle a besoin d’une communication sortante avec WireServer (168.63.129.16) sur les ports 80, 443 et 32526. Ils doivent être ouverts dans le pare-feu local sur la machine virtuelle. Les groupes de sécurité réseau configurés n’affectent pas la communication sur ces ports avec l’adresse 168.63.129.16.
- La machine virtuelle peut obtenir des services DNS auprès de l’adresse 168.63.129.16. Si cette situation n’est pas souhaitable, ce trafic peut être bloqué dans le pare-feu local sur la machine virtuelle. Par défaut, les groupes de sécurité réseau configurés n’affectent pas la communication DNS, sauf si elle est spécifiquement ciblée par le biais de la balise de service [AzurePlatformDNS](../virtual-network/service-tags-overview.md#available-service-tags).
- Quand la machine virtuelle fait partie d’un pool de back-ends d’équilibreur de charge, la communication avec la [sonde d’intégrité](../load-balancer/load-balancer-custom-probe-overview.md) doit être autorisée à partir de l’adresse 168.63.129.16. La configuration du groupe de sécurité réseau par défaut inclut une règle qui autorise cette communication. Cette règle exploite la balise de service [AzureLoadBalancer](../virtual-network/service-tags-overview.md#available-service-tags). Si vous le souhaitez, vous pouvez bloquer ce trafic en configurant le groupe de sécurité réseau, mais cela entraîne l’échec des sondes.

Dans un scénario de réseau non virtuel (Classic), la sonde d’intégrité provient d'une adresse IP privée et 168.63.129.16 n’est pas utilisée.

## <a name="next-steps"></a>Étapes suivantes

- [Groupes de sécurité](security-overview.md)
- [Créer, changer ou supprimer un groupe de sécurité réseau](manage-network-security-group.md)
