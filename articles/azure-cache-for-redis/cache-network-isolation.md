---
title: Options d’isolement réseau d’Azure Cache pour Redis
description: Dans cet article, vous allez apprendre à déterminer la meilleure solution d’isolement réseau pour vos besoins. Nous allons passer en revue les concepts de base d’Azure Private Link, l’injection Azure Virtual Network et des règles de pare-feu Azure avec leurs avantages et leurs limites.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: b47462a60e35200908592274a9e1c424eca5ab62
ms.sourcegitcommit: 1b698fb8ceb46e75c2ef9ef8fece697852c0356c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110655364"
---
# <a name="azure-cache-for-redis-network-isolation-options"></a>Options d’isolement réseau d’Azure Cache pour Redis

Dans cet article, vous allez apprendre à déterminer la meilleure solution d’isolement réseau pour vos besoins. Nous allons aborder les concepts de base d’Azure Private Link, de l’injection Azure Virtual Network et des règles de Pare-feu Azure. Nous allons aborder leurs avantages et leurs limites.  

## <a name="azure-private-link"></a>Azure Private Link

Azure Private Link fournit une connectivité privée entre un réseau virtuel et les services Azure PaaS. Il simplifie l’architecture réseau et sécurise la connexion entre points de terminaison dans Azure. Il sécurise la connexion en éliminant l’exposition des données à l’internet public.

### <a name="advantages"></a>Avantages

* Prise en charge des instances Azure Cache pour Redis De base, Standard et Premium.
* [Azure Private Link](../private-link/private-link-overview.md) vous permet de vous connecter à une instance de cache Azure à partir de votre réseau virtuel via un point de terminaison privé. Le point de terminaison est associé à une adresse IP privée dans un sous-réseau au sein du réseau virtuel. Avec cette liaison privée, les instances de cache sont disponibles tant à partir du réseau virtuel que publiquement.  
* Une fois qu’un point de terminaison privé est créé, l’accès au réseau public peut être limité à l’aide de l’indicateur `publicNetworkAccess`. Cet indicateur est défini sur `Disabled` par défaut, ce qui autorise uniquement l’accès aux liaisons privées. Vous pouvez définir la valeur sur `Enabled` ou `Disabled` avec une requête PATCH. Pour plus d’informations, consultez [Azure Cache pour Redis avec Azure Private Link (cache-private-link.md).
* Toutes les dépendances de cache externes seront sans effet sur les règles NSG du réseau virtuel.

### <a name="limitations"></a>Limites

* Les groupes de sécurité réseau (NSG) sont désactivés pour les points de terminaison privés. Toutefois, s’il existe d’autres ressources sur le sous-réseau, l’application du NSG s’appliquera à ces ressources.
* Pas encore pris en charge : géoréplication, règles de pare-feu, prise en charge de la console du portail, multiplicité des points de terminaison par cache en cluster, persistance au pare-feu et caches injectés sur le réseau virtuel.
* Pour vous connecter à un cache en cluster, `publicNetworkAccess` doit être défini sur `Disabled`, et il ne peut y avoir qu’une seule connexion de point de terminaison privé.

> [!NOTE]
> Lors de l’ajout d’un point de terminaison privé à une instance de cache, tout le trafic de Redis est acheminé vers le point de terminaison privé en raison du DNS.
> Vérifiez que les règles de pare-feu précédentes sont ajustées avant.  

## <a name="azure-virtual-network-injection"></a>Injection sur le réseau virtuel Azure

Le réseau virtuel est l’élément de base de votre réseau privé dans Azure. Il permet à de nombreuses ressources Azure de communiquer entre elles, avec Internet et avec les réseaux locaux en toute sécurité. Un réseau virtuel Azure est semblable à un réseau traditionnel que vous exploiteriez dans votre propre centre de données. Toutefois, le réseau virtuel présente également les avantages de l’infrastructure, de la mise à l’échelle, de la disponibilité et de l’isolation d’Azure.

### <a name="advantages"></a>Avantages

* Quand une instance Azure Cache pour Redis est configurée avec un réseau virtuel, elle n’est pas adressable publiquement. L’instance n’est accessible qu’à partir de machines virtuelles et d’applications présentes au sein du réseau virtuel.  
* Lorsque le réseau virtuel est combiné à des stratégies NSG limitées, il contribue à réduire le risque d’exfiltration de données.
* Un déploiement de réseau virtuel offre une sécurité améliorée et une isolation du trafic pour votre instance Azure Cache pour Redis. Les sous-réseaux, stratégies de contrôle d’accès et d’autres fonctionnalités restreignent davantage l’accès.
* La géoréplication est prise en charge.

### <a name="limitations"></a>Limites

* Les caches injectés sur le réseau virtuel sont uniquement disponibles pour Azure Cache pour Redis Premium.
* Lorsque vous utilisez un cache injecté sur le réseau virtuel, vous devez modifier votre réseau virtuel pour mettre en cache des dépendances telles que des listes de révocation de certificats, une infrastructure à clé publique (PKI), Azure Key Vault, Stockage Azure, Azure Monitor, et plus encore.  

## <a name="azure-firewall-rules"></a>Règles de Pare-feu Azure

[Pare-feu Azure](../firewall/overview.md) est un service de sécurité réseau informatique managé qui protège vos ressources de réseau virtuel Azure. Il s’agit d’un service de pare-feu avec état intégral, doté d’une haute disponibilité intégrée et d’une scalabilité illimitée dans le cloud. Vous pouvez créer, appliquer et consigner des stratégies de connectivité réseau et d’application de façon centralisée entre les abonnements et les réseaux virtuels.  

### <a name="advantages"></a>Avantages

* Lorsque des règles de pare-feu sont configurées, seules les connexions client à partir des plages d’adresses IP spécifiées peuvent se connecter au cache. Les connexions depuis les systèmes de surveillance de cache Azure pour Redis sont toujours autorisées, même si des règles de pare-feu sont configurées. Les règles NSG que vous définissez sont également autorisées.  

### <a name="limitations"></a>Limites

* Actuellement, les règles de pare-feu peuvent être utilisées avec des caches injectés sur le réseau virtuel, mais pas avec des points de terminaison privés.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment configurer un [cache injecté sur un réseau virtuel pour une instance Azure Cache pour Redis Premium](cache-how-to-premium-vnet.md).
* Découvrez comment configurer des [règles de pare-feu pour tous les niveaux d’Azure Cache pour Redis](cache-configure.md#firewall).
* Découvrez comment [configurer des points de terminaison privés pour tous les niveaux d’Azure Cache pour Redis](cache-private-link.md).
