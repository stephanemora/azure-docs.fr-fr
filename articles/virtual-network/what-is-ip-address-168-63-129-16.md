---
title: Quelle est l’adresse IP 168.63.129.16 ? | Microsoft Docs
description: Apprenez-en davantage sur l'adresse IP 168.63.129.16 et sur la façon dont elle fonctionne avec vos ressources.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: v-jesits
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/21/2019
ms.author: genli
ms.openlocfilehash: 7f0539e7c2f7e5ae8847b35b47d3708c6c5d6a09
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62107991"
---
# <a name="what-is-ip-address-1686312916"></a>Quelle est l’adresse IP 168.63.129.16 ?

L’adresse IP 168.63.129.16 est une adresse IP publique virtuelle qui est utilisée pour établir un canal de communication vers les ressources de la plateforme Azure. Les clients peuvent définir n’importe quel espace d'adressage pour leur réseau virtuel privé dans Azure. Par conséquent, les ressources de la plateforme Azure doivent être présentées en tant qu’adresse IP publique unique. Cette adresse IP publique virtuelle facilite les choses suivantes :

- Permet à l’agent de machine virtuelle de communiquer avec la plateforme Azure pour signaler qu’il est dans un état « Prêt ».
- Permet la communication avec le serveur virtuel DNS pour fournir la résolution de nom filtré aux ressources (machine virtuelle, p. ex.) qui ne possèdent pas de serveur DNS personnalisé. Ce filtrage permet de s’assurer que les clients peuvent résoudre uniquement les noms d’hôte de leurs ressources.
- Donne les moyens aux sondes d’intégrité de l’équilibreur de charge de déterminer l’intégrité des machines virtuelles dans un ensemble dont la charge est équilibrée.
- Active les messages de pulsation de l’agent invité pour le rôle PaaS.

## <a name="scope-of-ip-address-1686312916"></a>Portée de l’adresse IP 168.63.129.16

L’adresse IP publique virtuelle 168.63.129.16 est utilisée dans toutes les régions et tous les clouds nationaux. Cette adresse IP publique spéciale ne change pas. Elle est autorisée par la règle du groupe de sécurité réseau par défaut. Nous vous conseillons d’autoriser cette adresse IP dans toutes les stratégies de pare-feu local. La communication est sécurisée entre cette adresse IP spéciale et les ressources car seule la plateforme Azure interne peut envoyer un message à partir de cette adresse. Si cette adresse est bloquée, un comportement inattendu peut se produire dans différentes situations.

De plus, vous pouvez vous attendre à ce que le trafic circule entre l’adresse IP publique virtuelle 168.63.129.16 vers le point de terminaison configuré pour une [sonde d’intégrité Load Balancer](../load-balancer/load-balancer-custom-probe-overview.md). Dans un scénario de réseau non virtuel, la sonde d’intégrité provient d'une adresse IP privée. 

## <a name="next-steps"></a>Étapes suivantes

- [Groupes de sécurité](security-overview.md)
- [Créer, changer ou supprimer un groupe de sécurité réseau](manage-network-security-group.md)
