---
author: alexeyo26
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 03/19/2021
ms.author: alexeyo
ms.openlocfilehash: 201aaae4089790bce6a697eccc7b131b3ad0d17e
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114453979"
---
## <a name="private-endpoints-and-virtual-network-service-endpoints"></a>Points de terminaison privés et points de terminaison de service du Réseau virtuel

Azure fournit des points de terminaison privés et des points de terminaison de service de Réseau virtuel pour le trafic qui transite par le [réseau principal Azure privé](https://azure.microsoft.com/global-infrastructure/global-network/). L’objectif et les technologies sous-jacentes de ces types de points de terminaison sont similaires. Toutefois, il existe des différences entre les deux technologies. Nous vous recommandons d’en savoir plus sur les avantages et les inconvénients des deux avant de concevoir votre réseau.

Voici quelques points à prendre en compte lorsque vous décidez de la technologie à utiliser :
- Les deux technologies garantissent que le trafic entre le réseau virtuel et la ressource Speech n’est pas transitant par l’Internet public.
- Un point de terminaison privé fournit une adresse IP privée dédiée pour votre ressource Speech. Cette adresse IP est accessible uniquement au sein d’un réseau virtuel et d’un sous-réseau spécifiques. Vous avez un contrôle total de l’accès à cette adresse IP au sein de votre infrastructure réseau.
- Les points de terminaison de service de réseau virtuel ne fournissent pas d’adresse IP privée dédiée pour la ressource Speech. Au lieu de cela, ils encapsulent tous les paquets envoyés à la ressource Speech et les fournissent directement sur le réseau principal Azure.
- Les deux technologies prennent en charge des scénarios locaux. Par défaut, lorsqu’ils utilisent des points de terminaison de service de réseau virtuel, les ressources de service Azure sécurisées pour les réseaux virtuels ne sont pas accessibles à partir des réseaux locaux. Mais vous pouvez [modifier ce comportement](../../../virtual-network/virtual-network-service-endpoints-overview.md#secure-azure-service-access-from-on-premises).
- Les points de terminaison de service de réseau virtuel sont souvent utilisés pour limiter l’accès à une ressource Speech en fonction des réseaux virtuels dont provient le trafic.
- Dans le cas de Services cognitifs, l’activation du point de terminaison de service du Réseau virtuel force le trafic de toutes les ressources de Services cognitifs à transiter par le réseau principal privé. Cela nécessite une configuration d’accès réseau explicite. (Pour plus d’informations, consultez [configurer les réseaux virtuels et les paramètres de mise en réseau des ressources Speech](../speech-service-vnet-service-endpoint.md#configure-virtual-networks-and-the-speech-resource-networking-settings).) Les points de terminaison privés n’ont pas cette limitation et offrent plus de flexibilité pour la configuration de votre réseau. Vous pouvez accéder à une ressource via le réseau principal privé et une autre via l’Internet public en utilisant le même sous-réseau du même réseau virtuel.
- Les points de terminaison privés entraînent des [coûts supplémentaires](https://azure.microsoft.com/pricing/details/private-link). Les points de terminaison de service du Réseau virtuel sont gratuits.
- Les points de terminaison privés nécessitent une [configuration DNS supplémentaire](../speech-services-private-link.md#turn-on-private-endpoints).
- Une ressource Speech peut fonctionner simultanément avec des points de terminaison privés et des points de terminaison de service du Réseau virtuel.

Nous vous recommandons d’essayer les deux types de points de terminaison avant de décider de votre conception de production. 

Pour plus d’informations, consultez ces ressources :

- [Documentation sur Private Link et sur les points de terminaison privés Azure](../../../private-link/private-link-overview.md)
- [Documentation sur les points de terminaison de service du Réseau virtuel](../../../virtual-network/virtual-network-service-endpoints-overview.md)
