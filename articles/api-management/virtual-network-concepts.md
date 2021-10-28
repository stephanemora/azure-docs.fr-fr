---
title: Utiliser la Gestion des API Azure avec un réseau virtuel Azure
description: En savoir plus sur les scénarios et les conditions requises pour connecter votre instance de Gestion des API à un réseau virtuel Azure.
author: dlepow
ms.service: api-management
ms.topic: conceptual
ms.date: 08/19/2021
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 364487d697aee69215e9ca9f080e4aa6a1a83468
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130253361"
---
# <a name="use-a-virtual-network-with-azure-api-management"></a>Utiliser un réseau virtuel avec la Gestion des API Azure

Avec les Réseaux virtuels Azure (VNETs), vous pouvez placer vos ressources Azure dans un réseau routable non-Internet dont vous contrôlez l’accès. Vous pouvez ensuite connecter ces réseaux à vos réseaux locaux à l’aide de différentes technologies VPN. Pour en savoir plus sur les réseaux virtuels Azure, commencez par consulter les informations fournies dans [Vue d’ensemble du réseau virtuel Azure](../virtual-network/virtual-networks-overview.md).

Cet article décrit les options de connectivité du réseau virtuel VNET, les spécifications et les considérations relatives à votre instance de Gestion des API. Vous pouvez utiliser les modèles du portail Azure, de l’interface CLI Azure CLI, du Gestionnaire de ressource Azure ou d’autres outils pour le déploiement. Vous contrôlez le trafic entrant et sortant dans le sous-réseau dans lequel la gestion des API est déployée à l’aide des [groupes de sécurité réseau][NetworkSecurityGroups].

Pour obtenir des instructions détaillées sur le déploiement et la configuration du réseau, consultez :

* [Se connecter à un réseau virtuel externe à l’aide de la Gestion des API Azure](./api-management-using-with-vnet.md).
* [Se connecter à un réseau virtuel interne à l’aide de la Gestion des API Azure](./api-management-using-with-internal-vnet.md).

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="access-options"></a>Options d’accès

Par défaut, une instance de Gestion des API doit être accessible à partir d’Internet. À l’aide d’un réseau virtuel, vous pouvez configurer le portail des développeurs, la passerelle d’API et d’autres points de terminaison de Gestion des API pour qu’ils soient accessibles à partir d’Internet (mode externe) ou uniquement au sein du réseau virtuel (mode interne). 

* **Externe** : les points de terminaison de la Gestion des API sont accessibles à partir de l’Internet public via un équilibreur de charge externe. La passerelle peut accéder aux ressources au sein du réseau virtuel.

    :::image type="content" source="media/virtual-network-concepts/api-management-vnet-external.png" alt-text="Se connecter à un réseau virtuel externe VNET":::

    Utilisez la Gestion des API en mode externe pour accéder aux services principaux déployés dans le réseau virtuel.

* **Interne** : les points de terminaison de la Gestion des API sont accessibles uniquement au sein du réseau virtuel VNET via un équilibreur de charge interne. La passerelle peut accéder aux ressources au sein du réseau virtuel.

    :::image type="content" source="media/virtual-network-concepts/api-management-vnet-internal.png" alt-text="Se connecter à un réseau virtuel interne VNET":::

    Utilisez la Gestion des API en mode interne pour :

    * Rendre les API hébergées dans votre centre de données privé accessibles en toute sécurité à des tiers à l’aide de connexions Azure VPN ou Azure ExpressRoute.
    * Activer les scénarios de cloud hybride en exposant vos API cloud et sur site par le biais d’une passerelle commune.
    * Gérer vos API hébergées dans plusieurs localisations géographiques à l’aide d’un seul point de terminaison de passerelle.


## <a name="network-resource-requirements"></a>Besoins en ressources réseau

Voici les exigences en matière de ressources de réseau virtuel pour la Gestion des API. Certaines exigences varient en fonction de la version (`stv2` ou `stv1`) de la [plateforme de calcul](compute-infrastructure.md) qui héberge votre instance de Gestion des API.

### <a name="stv2"></a>[stv2](#tab/stv2)

* Un réseau virtuel du Gestionnaire de ressource Azure est requis.
* Vous devez fournir une [adresse IPv4 publique](../virtual-network/ip-services/public-ip-addresses.md#standard) de référence SKU standard en plus de spécifier un réseau virtuel et un sous-réseau.
* Le sous-réseau utilisé pour se connecter à l’instance Gestion des API peut contenir d’autres types de ressources Azure.
* Le service de Gestion des API, le réseau virtuel et le sous-réseau, ainsi que la ressource de l’adresse IP publique doivent se trouver dans la même région et le même abonnement.
* Pour les déploiements de Gestion des API dans plusieurs régions, vous configurez les ressources de réseau virtuel séparément pour chaque emplacement.

### <a name="stv1"></a>[stv1](#tab/stv1)

* Un réseau virtuel du Gestionnaire de ressource Azure est requis.
* Le sous-réseau utilisé pour se connecter à l’instance de Gestion des API doit être dédié à la Gestion des API. Il ne peut pas contenir d’autres types de ressources Azure.
* Le service de Gestion des API, le réseau virtuel ainsi que les ressources du sous-réseau doivent se trouver dans la même région et le même abonnement.
* Pour les déploiements de Gestion des API dans plusieurs régions, vous configurez les ressources de réseau virtuel séparément pour chaque emplacement.

---

## <a name="subnet-size"></a>Taille du sous-réseau

La taille minimale du sous-réseau, dans lequel la Gestion des API peut être déployée, est de /29, ce qui donne trois adresses IP utilisables. Chaque unité d’échelle supplémentaire de Gestion des API requiert deux adresses IP supplémentaires. La taille minimale requise est basée sur les considérations suivantes :

* Azure réserve dans chaque sous-réseau des adresses IP qui ne peuvent pas être utilisées. La première et la dernière adresses IP des sous-réseaux sont réservées à des fins de conformité du protocole. Trois adresses supplémentaires sont utilisées pour les services Azure. Pour plus d’informations, consultez la section [L’utilisation des adresses IP au sein de ces sous-réseaux est-elle soumise à des restrictions ?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets).

* Outre les adresses IP utilisées par l’infrastructure de réseau virtuel Azure, chaque instance de Gestion des API du sous-réseau utilise :
    * deux adresses IP par unité de référence SKU Premium ou 
    * une adresse IP pour la référence SKU du développeur. 

* Chaque instance réserve une adresse IP supplémentaire pour l’équilibreur de charge externe. Lors du déploiement dans un [réseau virtuel interne VNET](./api-management-using-with-internal-vnet.md), l’instance requiert une adresse IP supplémentaire pour l’équilibreur de charge interne.

## <a name="routing"></a>Routage

Consultez le Guide de routage lors du déploiement de votre instance de Gestion des API dans un réseau virtuel [externe VNET](./api-management-using-with-vnet.md#routing) ou [interne VNET](./api-management-using-with-internal-vnet.md#routing).

En savoir plus sur les [adresses IP de Gestion des API](api-management-howto-ip-addresses.md).

## <a name="dns"></a>DNS

En mode externe, le réseau virtuel VNET active la [résolution de noms fournie par Azure](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) pour vos points de terminaison de la Gestion des API et autres ressources Azure. Il ne fournit pas de résolution de noms pour les ressources locales. 

En mode interne, vous devez fournir votre propre solution DNS pour garantir la résolution de noms pour les points de terminaison de la Gestion des API et autres ressources Azure requises. Nous vous recommandons de configurer une [zone DNS privée](../dns/private-dns-overview.md)Azure.

Pour plus d’informations, consultez : 
* [Résolution de noms des ressources dans les réseaux virtuels Azure](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)  
* [Créer une zone DNS privée Azure](../dns/private-dns-getstarted-portal.md)

> [!IMPORTANT]
> Si vous envisagez d’utiliser une solution DNS personnalisée pour le réseau virtuel VNET, configurez-la **avant** de déployer un service de Gestion des API dans celui-ci. Sinon, vous devez mettre à jour le service de Gestion des API à chaque fois que vous changez le(s) serveur(s) DNS en exécutant l’[Opération Appliquer une configuration réseau](/rest/api/apimanagement/2020-12-01/api-management-service/apply-network-configuration-updates).

## <a name="limitations"></a>Limites

Certaines limitations varient en fonction de la version (`stv2` ou `stv1`) de la [plateforme de calcul](compute-infrastructure.md) qui héberge votre instance de Gestion des API.

### <a name="stv2"></a>[stv2](#tab/stv2)

* Un sous-réseau contenant des instances du service de Gestion des API ne peut pas être déplacé entre des abonnements.
* Pour les déploiements de Gestion des API dans plusieurs régions configurés en mode de réseau virtuel interne, les utilisateurs possèdent le routage et sont chargés de gérer leur équilibrage de charge entre les différentes régions.
* Pour importer une API dans la Gestion des API à partir d’une [spécification OpenAPI](import-and-publish.md), l’URL de la spécification doit être hébergée sur une adresse Internet publiquement accessible.

### <a name="stv1"></a>[stv1](#tab/stv1)

* Un sous-réseau contenant des instances de la Gestion des API ne peut pas être déplacé entre des abonnements.
* Pour les déploiements de Gestion des API dans plusieurs régions configurés en mode de réseau virtuel interne, les utilisateurs possèdent le routage et sont chargés de gérer leur équilibrage de charge entre les différentes régions.
* Pour importer une API dans la Gestion des API à partir d’une [spécification OpenAPI](import-and-publish.md), l’URL de la spécification doit être hébergée sur une adresse Internet publiquement accessible.
* En raison de la limitation de la plateforme, la connectivité entre une ressource d’un réseau virtuel homologué à l’échelle mondiale dans une autre région et le service Gestion des API en mode interne ne fonctionnera pas. Pour plus d’informations, consultez la [documentation relative au réseau virtuel](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints).

---

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus :

* [Connexion d’un réseau virtuel à un back-end à l’aide de la passerelle VPN](../vpn-gateway/design.md#s2smulti)
* [Connexion d’un réseau virtuel à partir de modèles de déploiement différents](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [Questions fréquentes (FAQ) sur le réseau virtuel](../virtual-network/virtual-networks-faq.md)

Se connecter à un réseau virtuel :
* [Se connecter à un réseau virtuel externe à l’aide de la Gestion des API Azure](./api-management-using-with-vnet.md).
* [Se connecter à un réseau virtuel interne à l’aide de la Gestion des API Azure](./api-management-using-with-internal-vnet.md).

Passez en revue les rubriques suivantes

* [Connexion d’un réseau virtuel au serveur principal à l’aide de la passerelle VPN](../vpn-gateway/design.md#s2smulti)
* [Connexion d’un réseau virtuel utilisant des modèles de déploiement différents](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [Utilisation de l’inspecteur d’API pour le suivi des appels dans Gestion des API Azure](api-management-howto-api-inspector.md)
* [Questions fréquentes (FAQ) sur les réseaux virtuels](../virtual-network/virtual-networks-faq.md)
* [Balises de service](../virtual-network/network-security-groups-overview.md#service-tags)

[api-management-using-vnet-menu]: ./media/api-management-using-with-vnet/api-management-menu-vnet.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-using-with-vnet/api-management-using-vnet-add-api.png
[api-management-vnet-private]: ./media/virtual-network-concepts/api-management-vnet-internal.png
[api-management-vnet-public]: ./media/virtual-network-concepts/api-management-vnet-external.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[UDRs]: ../virtual-network/virtual-networks-udr-overview.md
[NetworkSecurityGroups]: ../virtual-network/network-security-groups-overview.md
[ServiceEndpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md
[ServiceTags]: ../virtual-network/network-security-groups-overview.md#service-tags