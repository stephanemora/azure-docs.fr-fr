---
title: À propos du réseau à double hébergement avec le Serveur de routes Azure (préversion)
description: Découvrez-en plus sur le fonctionnement du Serveur de routes Azure (préversion) dans un réseau à double hébergement.
services: route-server
author: duongau
ms.service: route-server
ms.topic: conceptual
ms.date: 05/04/2021
ms.author: duau
ms.openlocfilehash: b786c58efd193eebbd43a4bd40f29201347c1309
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108776727"
---
# <a name="about-dual-homed-network-with-azure-route-server-preview"></a>À propos du réseau à double hébergement avec le Serveur de routes Azure (préversion)

Le Serveur de routes Azure prend en charge la topologie standard de votre réseau hub-and-spoke. Cette configuration intervient quand le Serveur de routes et l’appliance virtuelle réseau se trouvent tous les deux dans le réseau virtuel hub. Le serveur routeur vous permet également de configurer une topologie différente appelée « réseau à double hébergement ». Cette configuration intervient quand vous disposez d’un réseau virtuel spoke appairé avec deux réseaux virtuels hub, ou plus. Les machines virtuelles du réseau virtuel spoke peuvent communiquer via un réseau virtuel hub avec votre réseau local ou Internet.

## <a name="how-to-set-it-up"></a>Comment effectuer la configuration

Comme vous pouvez le voir dans le diagramme suivant, vous devez :

* Déployer une appliance virtuelle réseau dans chaque réseau virtuel hub et le serveur de routes dans le réseau virtuel spoke.
* Activer le peering de réseaux virtuels entre les réseaux virtuels hub et spoke.
* Configurer le peering BGP entre le Serveur de routes et chaque appliance virtuelle réseau déployée.

:::image type="content" source="./media/about-dual-homed-network/dual-homed-topology.png" alt-text="Diagramme du Serveur de routes dans une topologie à double hébergement.":::

### <a name="how-does-it-work"></a>Comment cela fonctionne-t-il ?

Dans le plan de contrôle, l’appliance virtuelle réseau et le Serveur de routes échangent des routes comme s’ils étaient déployés dans le même réseau virtuel. L’appliance virtuelle réseau découvre des informations relatives aux adresses de réseau virtuel spoke à partir du Serveur de routes. Le Serveur de routes découvre les routes à partir de chacune des appliances virtuelles réseau. Le Serveur de routes programme ensuite toutes les machines virtuelles du réseau virtuel spoke avec les routes qu’il a découvertes. 

Dans le plan de données, les machines virtuelles du réseau virtuel spoke voient l’appliance virtuelle réseau de sécurité ou l’appliance virtuelle réseau de réseau privé virtuel dans le hub comme tronçon suivant. Le trafic destiné au trafic lié à Internet ou au trafic intersite hybride est désormais routé par le biais des appliances virtuelles réseau dans le réseau virtuel hub. Vous pouvez configurer les deux hubs comme actif/actif ou actif/passif. En cas de défaillance du hub actif, le trafic à destination et en provenance des machines virtuelles bascule vers l’autre hub. Ces défaillances incluent notamment les défaillances d’appliance virtuelle réseau et les échecs de connectivité de service. Cette configuration garantit que votre réseau est configuré pour la haute disponibilité.

## <a name="integration-with-expressroute"></a>Intégration à ExpressRoute

Vous pouvez créer un réseau à double hébergement qui implique deux connexions ExpressRoute, ou plus. En plus des étapes décrites ci-dessus, vous devez :

* Créer un autre Serveur de routes dans chaque réseau virtuel hub disposant d’une passerelle ExpressRoute.
* Configurer le peering BGP entre l’appliance virtuelle réseau et le Serveur de routes dans le réseau virtuel hub.
* [Activer l’échange de routes](quickstart-configure-route-server-portal.md#configure-route-exchange) entre la passerelle ExpressRoute et le Serveur de routes dans le réseau virtuel hub.
* Vérifiez que l’option « Utiliser la passerelle distante ou utiliser le Serveur de routes distant » est **désactivée** dans la configuration de peering de réseaux virtuels (VNet) spoke.

:::image type="content" source="./media/about-dual-homed-network/dual-homed-topology-expressroute.png" alt-text="Diagramme du Serveur de routes dans une topologie à double hébergement avec ExpressRoute.":::

### <a name="how-does-it-work"></a>Comment cela fonctionne-t-il ?

Dans le plan de contrôle, les appliances virtuelles réseau du réseau virtuel hub découvrent les routes locales de la passerelle ExpressRoute par le biais de l’[échange de routes](quickstart-configure-route-server-portal.md#configure-route-exchange) avec le Serveur de routes dans le hub. En retour, l’appliance virtuelle réseau envoie les adresses de réseau virtuel spoke à la passerelle ExpressRoute à l’aide du même Serveur de routes. Le Serveur de routes dans le réseau virtuel spoke et dans le réseau virtuel hub programme alors les adresses réseau locales sur les machines virtuelles de leur réseau virtuel respectif.

> [!IMPORTANT]
> BGP empêche une boucle en vérifiant le numéro AS dans le chemin AS. Si le routeur de réception voit son propre numéro AS sous la forme du chemin AS d’un paquet BGP reçu, il supprime le paquet. Dans cet exemple, les deux Serveurs de routes ont le même numéro AS, 65515. Pour éviter que chaque Serveur de routes supprime les routes de l’autre Serveur de routes, l’appliance virtuelle réseau doit appliquer la stratégie BGP **as-override** lors du peering avec chaque Serveur de routes. 
>

Dans le plan de données, les machines virtuelles du réseau virtuel spoke envoient d’abord tout le trafic destiné au réseau local à l’appliance virtuelle réseau dans le réseau virtuel hub. L’appliance virtuelle réseau transfère ensuite le trafic vers le réseau local par le biais d’ExpressRoute. Le trafic en provenance d’un site local transite par le même chemin de données en sens inverse. Vous remarquerez qu’aucun des Serveurs de routes ne se trouve dans le chemin de données.

## <a name="next-steps"></a>Étapes suivantes

* [Découvrir comment le Serveur de routes Azure fonctionne avec ExpressRoute](expressroute-vpn-support.md)
* [Découvrir comment le Serveur de routes Azure fonctionne avec une appliance virtuelle réseau](resource-manager-template-samples.md)

