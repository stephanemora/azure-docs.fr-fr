---
title: Plateforme de calcul de la Gestion des API Azure
description: En savoir plus sur la plateforme de calcul utilisée pour héberger votre instance de service de Gestion des API
author: dlepow
ms.service: api-management
ms.topic: conceptual
ms.date: 08/23/2021
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 23d54c2725e48b1d587fb641c593be0c8d63958f
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130248637"
---
# <a name="compute-platform-for-azure-api-management"></a>Plateforme de calcul pour la Gestion des API Azure

En tant que Cloud Platform-as-a-service (PaaS), la Gestion des API Azure fait abstraction de nombreux détails de l’infrastructure utilisée pour héberger et exécuter votre service. Vous pouvez créer, gérer et mettre à l’échelle la plupart des aspects de votre instance de Gestion des API sans avoir à connaître les ressources sous-jacentes.

Pour améliorer les fonctionnalités de service, nous mettons à niveau la version de la plateforme de calcul de la Gestion des API (les ressources de calcul Azure qui hébergent le service) pour les instances de plusieurs [niveaux de service](api-management-features.md). Cet article vous donne un contexte sur la mise à niveau et les versions majeures de la plateforme de calcul de la Gestion des API : `stv1` et `stv2` .

Nous avons réduit les impacts de cette mise à niveau sur le fonctionnement de votre instance de Gestion des API. Toutefois, si votre instance est connectée à un [réseau virtuel Azure](virtual-network-concepts.md), vous devrez modifier certains paramètres de configuration réseau lorsque l’instance sera mise à niveau vers la version de la plateforme `stv2`.

## <a name="compute-platform-versions"></a>Versions de la plateforme de calcul

| Version | Description | Architecture | Niveaux de Gestion des API |
| -------| ----------| ----------- | ------- |
| `stv2` | V2 à un seul locataire | [Groupes de machines virtuelles identiques](../virtual-machine-scale-sets/overview.md) | Développeur, De base, Standard et Premium |
| `stv1` |  V1 à un seul locataire | [Service cloud (classique)](../cloud-services/cloud-services-choose-me.md) | Développeur, De base, Standard et Premium |
| `mtv1` | V1 multi-locataire |  [App Service](../app-service/overview.md) | Consommation |


## <a name="how-do-i-know-which-platform-hosts-my-api-management-instance"></a>Comment savoir quelle plateforme héberge mon instance de Gestion des API ?

### <a name="developer-basic-standard-and-premium-tiers"></a>Niveaux Développeur, De base, Standard et Premium

* Les instances avec des connexions de réseau virtuel créées ou mises à jour à l’aide du portail Azure après le **avril 2021**, ou à l’aide de l’API REST de Gestion des API version **01/01/2021-Préversion** ou ultérieure, sont hébergées sur la plateforme`stv2`
* Si vous avez activé la [redondance de zone](zone-redundancy.md) dans votre instance de niveau Premium, elle est hébergée sur la plateforme `stv2`
* Dans le cas contraire, l’instance est hébergée sur la plateforme `stv1`

> [!TIP]
> À partir de la version `2021-04-01-preview` de l’API, l’instance de Gestion des API a une propriété en lecture seule `PlatformVersion` qui affiche ces informations sur la plateforme. 

### <a name="consumption-tier"></a>Niveau de consommation

* Toutes les instances sont hébergées sur la plateforme `mtv1`

## <a name="how-do-i-upgrade-to-the-stv2-platform"></a>Comment faire la mise à niveau vers la plateforme `stv2` ? 

La mise à jour est possible uniquement pour une instance de niveau Développeur, De base, Standard ou Premium. 

Créez ou mettez à jour la connexion du réseau virtuel ou la configuration de la zone de disponibilité dans une instance de Gestion des API à l’aide de :

* [Azure portal](https://portal.azure.com)
* API REST Azure, ou modèle ARM, spécification de la version d’API **01/01/2021-Préversion** ou version ultérieure

> [!IMPORTANT]
> Lorsque vous mettez à jour la version de la plateforme de calcul d’une instance connectée à un [réseau virtuel](virtual-network-concepts.md)Azure :
> * Vous devez fournir une ressource d'[adresse IPv4 publique](../virtual-network/ip-services/public-ip-addresses.md#standard) de référence SKU standard
> * L’adresse ou les adresses IP virtuelle(s) de votre instance de Gestion des API seront modifiées.

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur l’utilisation d’un [réseau virtuel](virtual-network-concepts.md) avec la Gestion des API.
* En savoir plus sur la [redondance de zone](zone-redundancy.md).