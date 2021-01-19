---
title: Résoudre les problèmes liés à Azure Load Balancer
description: Découvrez comment résoudre les problèmes connus liés à Azure Load Balancer.
services: load-balancer
documentationcenter: na
author: asudbring
manager: dcscontentpm
ms.custom: seodoc18
ms.service: load-balancer
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/28/2020
ms.author: allensu
ms.openlocfilehash: bfe2b21a86f2ce4b4630ba69cde87796fd367f4b
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98029135"
---
# <a name="troubleshoot-azure-load-balancer-backend-traffic-responses"></a>Résoudre les problèmes liés aux réponses du trafic des serveurs principaux Azure Load Balancer

Cette page contient des informations de résolution des problèmes liés aux questions relatives à Azure Load Balancer.

## <a name="vms-behind-load-balancer-are-not-responding-to-traffic-on-the-configured-data-port"></a>Les machines virtuelles situées derrière l’équilibreur de charge ne répondent pas au trafic sur le port de données configuré

Si une machine virtuelle d’un pool principal est répertoriée comme saine et répond aux sondes d’intégrité, mais ne participe toujours pas à l’équilibrage de charge ou ne répond pas au trafic de données, ce peut être dû à l’une des raisons suivantes :
* Une machine virtuelle du pool principal de l’équilibreur de charge n’écoute pas sur le port de données
* Un groupe de sécurité réseau bloque le port sur la machine virtuelle du pool principal de l’équilibreur de charge  
* Accès à l’équilibreur de charge à partir des mêmes machine virtuelle et carte d’interface réseau
* Accès au serveur frontal Load Balancer Internet à partir de la machine virtuelle du pool principal Load Balancer

## <a name="cause-1-load-balancer-backend-pool-vm-is-not-listening-on-the-data-port"></a>Cause 1 : Une machine virtuelle du pool principal de l’équilibreur de charge n’écoute pas sur le port de données

Si une machine virtuelle ne répond pas au trafic de données, il se peut que le port cible ne soit pas ouvert sur la machine virtuelle participant ou que la machine virtuelle n’écoute pas sur ce port. 

**Validation et résolution**

1. Connectez-vous à la machine virtuelle principale. 
2. Ouvrez une invite de commandes et exécutez la commande suivante pour vérifier qu’une application écoute sur le port de données :  
            netstat -an 
3. Si l’état du port indiqué n’est pas « ÉCOUTE », configurez le port d’écoute approprié. 
4. Si l’état du port est Écoute, recherchez dans l’application cible sur ce port des problèmes possibles.

## <a name="cause-2-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vm"></a>Cause 2 : Un groupe de sécurité réseau bloque le port sur la machine virtuelle du pool principal de l’équilibreur de charge  

Si un ou plusieurs groupes de sécurité réseau configurés sur le sous-réseau ou sur la machine virtuelle bloquent l’adresse IP source ou le port, alors la machine virtuelle ne peut pas répondre.

Pour l’équilibreur de charge public, l’adresse IP des clients Internet sera utilisée pour la communication entre les clients et les machines virtuelles principales de l’équilibreur de charge. Assurez-vous que l’adresse IP des clients est autorisée dans le groupe de sécurité réseau de la machine virtuelle principale.

1. Répertoriez les groupes de sécurité réseau configurés sur la machine virtuelle du pool principal. Pour plus d’informations, consultez [Créer, modifier ou supprimer un groupe de sécurité réseau](../virtual-network/manage-network-security-group.md).
1. Dans la liste des groupes de sécurité réseau, vérifiez si :
    - le trafic entrant ou sortant sur le port de données subit des interférences ; 
    - une règle **Refuser tout** des groupes de sécurité réseau sur la carte d’interface réseau de la machine virtuelle ou du sous-réseau a une priorité supérieure à celle de la règle par défaut qui autorise les sondes et le trafic de l’équilibreur de charge (les groupes de sécurité réseau doivent autoriser l’adresse IP 168.63.129.16 de l’équilibreur de charge qui correspond au port de la sonde).
1. Si l’une des règles bloque le trafic, supprimez et reconfigurez ces règles pour autoriser le trafic de données.  
1. Vérifiez si la machine virtuelle répond à présent aux sondes d’intégrité.

## <a name="cause-3-accessing-the-load-balancer-from-the-same-vm-and-network-interface"></a>Cause 3 : Accès à l’équilibreur de charge à partir des mêmes machine virtuelle et interface réseau 

Si l’application hébergée sur la machine virtuelle principale d’un équilibreur de charge essaie d’accéder à une autre application hébergée dans la même machine virtuelle principale via la même interface réseau, ce scénario n’est pas pris en charge et échoue. 

**Résolution** : vous pouvez résoudre ce problème via l’une des méthodes suivantes :
* Configurez des machines virtuelles du pool principal distinctes par application. 
* Configurez l’application dans les machines virtuelles à double carte d’interface réseau afin que chaque application utilise sa propre interface réseau et adresse IP. 

## <a name="cause-4-accessing-the-internal-load-balancer-frontend-from-the-participating-load-balancer-backend-pool-vm"></a>Cause 4 : Accès au serveur frontal Load Balancer interne à partir de la machine virtuelle du pool principal Load Balancer

Si un serveur Load Balancer interne est configuré au sein d’un réseau virtuel, et si l’une des machines virtuelles principales participantes essaie d’accéder au serveur frontal Load Balancer interne, des défaillances peuvent se produire lorsque le flux est mappé à la machine virtuelle d’origine. Ce scénario n'est pas pris en charge.

**Résolution** Il existe plusieurs façons pour débloquer ce scénario, notamment l’utilisation d’un proxy. Évaluez Application Gateway ou d’autres proxies tiers (par exemple, nginx ou haproxy). Pour plus d’informations sur Application Gateway, consultez la page [Vue d’ensemble de la passerelle Application Gateway](../application-gateway/overview.md).

**Détails** Les équilibreurs de charge internes ne traduisent pas les connexions sortantes vers le front-end d’un équilibreur de charge interne, car ils se trouvent tous deux dans un espace d’adressage IP privé. Les Load Balancer publics offrent des [connexions sortantes](load-balancer-outbound-connections.md) d’adresses IP privées à l’intérieur du réseau virtuel vers des adresses IP publiques. Pour les Load Balancer internes, cette approche évite le risque d’épuisement du port SNAT à l’intérieur de l’espace d’adressage IP interne unique lorsque la traduction n’est pas requise.

Un effet secondaire est que, si un flux sortant d’une machine virtuelle dans le pool principal tente un flux vers le serveur frontal du Load Balancer interne dans son pool _et_ s’il est mappé à lui-même, les deux aspects du flux ne correspondent pas. Étant donné qu’ils ne correspondent pas, le flux échoue. Le flux réussit s’il n’a pas été mappé avec la même machine virtuelle du pool principal que celle qui a créé le flux vers le serveur frontal.

Lorsque le flux est mappé avec lui-même, le flux sortant apparaît comme provenant de la machine virtuelle vers le serveur frontal et le flux entrant correspondant apparaît comme étant issu de la machine virtuelle vers elle-même. Du point de vue du système d’exploitation invité, les parties entrante et sortante d’un même flux ne correspondent pas à l’intérieur de la machine virtuelle. La pile TCP ne reconnaît pas ces deux moitiés de flux comme faisant partie du même flux. La source et la destination ne correspondent pas. Lorsque le flux est mappé avec une autre machine virtuelle dans le pool principal, les parties du flux correspondent et la machine virtuelle peut répondre au flux.

Le symptôme pour repérer ce scénario est la présence de délais d’expiration de connexion intermittents lorsque le flux retourne au back-end qui est à l’origine du flux. Les solutions de contournement courantes incluent l’insertion d’une couche de proxy derrière le Load Balancer interne et l’utilisation de règles de style de retour direct du serveur (DSR). Pour plus d’informations, consultez [Serveurs frontaux multiples dans Azure Load Balancer](load-balancer-multivip-overview.md).

Vous pouvez combiner un Load Balancer interne avec un proxy tiers ou utiliser une [Application Gateway](../application-gateway/overview.md) interne pour les scénarios de proxy limités à HTTP/HTTPS. Vous pouvez utiliser un Load Balancer public pour résoudre ce problème, mais le scénario qui en résulte est sujet aux [épuisements SNAT](load-balancer-outbound-connections.md). Évitez cette deuxième approche, sauf si elle est soigneusement gérée.

## <a name="next-steps"></a>Étapes suivantes

Si les étapes précédentes ne vous permettent pas de résoudre le problème, ouvrez un [ticket d’incident](https://azure.microsoft.com/support/options/).