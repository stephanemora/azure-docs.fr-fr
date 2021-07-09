---
author: alexeyo26
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 03/19/2021
ms.author: alexeyo
ms.openlocfilehash: db8ae4bba6717cb1c0f27befd0897b60c2760ed2
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/07/2021
ms.locfileid: "110059147"
---
## <a name="private-endpoints-and-vnet-service-endpoints"></a>Points de terminaison privés et points de terminaison de service de réseau virtuel

Azure propose des points de terminaison privés et des points de terminaison de service de réseau virtuel pour le tunneling du trafic à l’aide du [réseau principal Azure privé](https://azure.microsoft.com/global-infrastructure/global-network/). Ces types de points de terminaison sont similaires en ce qui concerne leur finalité et les technologies sur lesquelles ils sont basés. Toutefois, il existe des différences entre les deux technologies, et nous vous recommandons d’obtenir des informations complémentaires sur les avantages et les inconvénients de chacune avant de concevoir votre réseau.

Voici quelques éléments que vous devez prendre en compte quand vous faites un choix :
- Les deux technologies garantissent que le trafic entre le réseau virtuel et la ressource Speech ne passe *pas* par l’Internet public.
- Un point de terminaison privé fournit une adresse IP privée dédiée pour votre ressource Speech. Cette adresse IP est accessible uniquement au sein d’un réseau virtuel et d’un sous-réseau spécifiques. Vous avez un contrôle total de l’accès à cette adresse IP au sein de votre infrastructure réseau.
- Les points de terminaison de service de réseau virtuel ne fournissent *pas* d’adresse IP privée dédiée pour la ressource Speech. Mais au lieu de cela, ils encapsulent tous les paquets envoyés à la ressource Speech et les livrent directement par le biais du réseau principal Azure.
- Les deux technologies prennent en charge des scénarios locaux. Par défaut, quand vous utilisez des points de terminaison de service de réseau virtuel, les ressources de service Azure sécurisées sur les réseaux virtuels ne sont pas accessibles à partir de réseaux locaux, mais cela [peut être configuré](../../../virtual-network/virtual-network-service-endpoints-overview.md#secure-azure-service-access-from-on-premises).
- Les points de service de réseau virtuel sont souvent utilisés quand vous voulez limiter l’accès à votre ressource Speech en fonction du ou des réseaux virtuels d’où provient le trafic.
- Dans le cas de Cognitive Services, l’activation du point de terminaison de service de réseau virtuel force le trafic de **toutes** les ressources Cognitive Services à transiter par le réseau principal privé. Cela nécessite une configuration d’accès réseau explicite (voir les détails fournis [ici](../speech-service-vnet-service-endpoint.md#configure-vnets-and-the-speech-resource-networking-settings)). Les points de terminaison privés n’ont pas cette limitation et offrent plus de flexibilité pour votre configuration réseau : vous pouvez accéder à une ressource par le biais du réseau principal privé et à une autre par le biais de l’Internet public en utilisant le même sous-réseau du même réseau virtuel.
- Les points de terminaison privés entraînent des [coûts supplémentaires](https://azure.microsoft.com/pricing/details/private-link). Les points de terminaison de service de réseau virtuel sont gratuits.
- Les points de terminaison privés nécessitent une [configuration DNS supplémentaire](../speech-services-private-link.md#turn-on-private-endpoints).
- Une ressource Speech peut fonctionner simultanément avec des points de terminaison privés et des points de terminaison de service de réseau virtuel.

Nous vous recommandons d’essayer les deux types de points de terminaison avant de décider de votre conception de production. 

- [Documentation sur Private Link et sur les points de terminaison privés](../../../private-link/private-link-overview.md)
- [Documentation sur les points de terminaison de service de réseau virtuel](../../../virtual-network/virtual-network-service-endpoints-overview.md)
