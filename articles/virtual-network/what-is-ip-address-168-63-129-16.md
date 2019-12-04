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
ms.openlocfilehash: e061d503254ba7aa7735a97a060fc63f96b3fb61
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196665"
---
# <a name="what-is-ip-address-1686312916"></a>Quelle est l’adresse IP 168.63.129.16 ?

L’adresse IP 168.63.129.16 est une adresse IP publique virtuelle qui est utilisée pour établir un canal de communication vers les ressources de la plateforme Azure. Les clients peuvent définir n’importe quel espace d'adressage pour leur réseau virtuel privé dans Azure. Par conséquent, les ressources de la plateforme Azure doivent être présentées en tant qu’adresse IP publique unique. Cette adresse IP publique virtuelle facilite les choses suivantes :

- Permet à l’agent de machine virtuelle de communiquer avec la plateforme Azure pour signaler qu’il est dans un état « Prêt ».
- Permet la communication avec le serveur virtuel DNS pour fournir la résolution de nom filtré aux ressources (machine virtuelle, p. ex.) qui ne possèdent pas de serveur DNS personnalisé. Ce filtrage permet de s’assurer que les clients peuvent résoudre uniquement les noms d’hôte de leurs ressources.
- Donne les moyens aux [sondes d’intégrité de l’équilibreur de charge Azure](../load-balancer/load-balancer-custom-probe-overview.md) de déterminer l’intégrité des machines virtuelles.
- Permet à la machine virtuelle d’obtenir une adresse IP dynamique auprès du service DHCP dans Azure.
- Active les messages de pulsation de l’agent invité pour le rôle PaaS.

## <a name="scope-of-ip-address-1686312916"></a>Portée de l’adresse IP 168.63.129.16

L’adresse IP publique 168.63.129.16 est utilisée dans toutes les régions et tous les clouds nationaux. Cette adresse IP publique spéciale ne change pas car elle appartient à Microsoft. Elle est autorisée par la règle du groupe de sécurité réseau par défaut. Nous vous conseillons d’autoriser cette adresse IP dans toutes les stratégies de pare-feu local, en entrée et en sortie. La communication est sécurisée entre cette adresse IP spéciale et les ressources car seule la plateforme Azure interne peut envoyer un message à partir de cette adresse. Si cette adresse est bloquée, un comportement inattendu peut se produire dans différentes situations.
Les ports suivants doivent au moins être ouverts pour permettre la communication avec WireServer : 80, 443 et 32526.

Toutes les [sondes d’intégrité Azure Load Balancer](../load-balancer/load-balancer-custom-probe-overview.md) ont pour source cette adresse IP. Si vous bloquez cette adresse IP, l’exécution de vos sondes échouera.

Dans un scénario de réseau non virtuel (Classic), la sonde d’intégrité provient d'une adresse IP privée et 168.63.129.16 n’est pas utilisée.

## <a name="next-steps"></a>Étapes suivantes

- [Groupes de sécurité](security-overview.md)
- [Créer, changer ou supprimer un groupe de sécurité réseau](manage-network-security-group.md)
