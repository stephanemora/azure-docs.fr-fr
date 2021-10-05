---
title: À propos du Peering BGP avec un hub virtuel
titleSuffix: Azure Virtual WAN
description: Découvrez le Peering BGP avec un hub virtuel Azure Virtual WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 08/06/2021
ms.author: cherylmc
ms.openlocfilehash: e570e5f06af814a6d0cbb581275d1c70ebf0df8a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124780791"
---
# <a name="scenario-bgp-peering-with-a-virtual-hub-preview"></a>Scénario : Peering BGP avec un hub virtuel (préversion)

> [!IMPORTANT]
> La fonctionnalité d’appairage BGP avec le hub WAN virtuel est actuellement en préversion publique contrôlée. Si vous souhaitez essayer cette fonctionnalité, envoyez un e-mail à **previewbgpwithvhub@microsoft.com** avec l’ID de ressource de votre ressource WAN virtuelle. Une fois que vous avez reçu la confirmation de l’activation des fonctionnalités, suivez la [page](create-bgp-peering-hub-portal.md) de documentation suivante pour connaître les principales considérations et accéder à un guide de configuration détaillé. 
>
> Pour localiser l’ID de ressource, ouvrez le portail Azure, naviguez jusqu’à votre ressource WAN virtuelle et sélectionnez **Paramètres > Propriétés > ID de ressource.**<br> Exemple : `/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Network/virtualWans/<virtualWANname>`
>


Le routeur de hub Azure Virtual WAN, également appelé routeur de hub virtuel, agit en tant que gestionnaire d’itinéraires et permet de simplifier l’opération de routage dans et entre hubs virtuels. En d’autres termes, un routeur de hub virtuel effectue les opérations suivantes :

* Simplifie la gestion du routage en étant le moteur de routage central qui communique avec les passerelles telles que VPN, ExpressRoute, P2S et les appliances virtuelles réseau (NVA). 
* Active les scénarios de routage avancés des tables de routage personnalisées, d’association et de propagation des itinéraires.
* Agit en tant que routeur pour le trafic qui transite entre et vers les réseaux virtuels connectés à un hub virtuel.

À présent, le routeur de hub virtuel offre également la possibilité d’appairer et d’échanger des informations de routage directement via le protocole de routage Border Gateway Protocol (BGP). Une NVA ou un point de terminaison BGP configuré dans un réseau virtuel connecté à un hub virtuel peut être appairé directement avec le routeur de hub virtuel s’il prend en charge le protocole de routage BGP et garantit que l’ASN sur la NVA est configuré pour être différent de l’ASN du hub virtuel.

## <a name="benefits-and-considerations"></a>Avantages et considérations

**Principaux avantages**

* Vous n’avez plus besoin de mettre à jour manuellement la table de routage sur votre NVA chaque fois que vos adresses de réseau virtuel sont mises à jour.
* Vous n’avez plus besoin de mettre à jour les itinéraires définis par l’utilisateur manuellement, ni de supprimer les anciennes, dès lors que votre NVA en annonce de nouveaux.
* Les NVA dans les réseaux virtuels connectés à un hub virtuel peuvent apprendre les itinéraires de passerelle de hub virtuel (VPN, ExpressRoute ou NVA managées).
* Vous pouvez appairer plusieurs instances de votre NVA avec un routeur de hub virtuel. Vous pouvez configurer des attributs BGP dans votre NVA et, en fonction de votre conception (actif/actif ou actif/passif), laissez le routeur de hub virtuel déterminer quelle instance de NVA est active ou passive.

**Considérations**

* Vous ne pouvez pas appairer un routeur de hub virtuel avec un Serveur de routes Azure approvisionné dans un réseau virtuel.
* Le routeur de hub virtuel prend en charge les ASN 16 bits (2 octets) uniquement.
* La connexion de réseau virtuel qui possède le point de terminaison de connexion BGP NVA doit toujours être associée et propagée vers defaultRouteTable. Les tables de routage personnalisées ne sont pas prises en charge pour le moment.
* Le routeur de hub virtuel prend en charge la connectivité de transit entre les réseaux virtuels connectés aux hubs virtuels. Cette fonctionnalité n’a rien à voir avec le Peering BGP, car Virtual WAN prend déjà en charge la connectivité de transit. Exemples :
  * VNET1 : NVA1 connectée au hub virtuel 1 -> (connectivité de transit) -> VNET2 : NVA2 connectée au hub virtuel 1.
  * VNET1 : NVA1 connectée au hub virtuel 1 -> (connectivité de transit) -> VNET2 : NVA2 connectée au hub virtuel 2.
* Vous pouvez utiliser vos propres numéros ASN publics ou privés dans votre appliance virtuelle réseau. Vous ne pouvez pas utiliser les plages réservées par Azure ou l’IANA. Les ASN suivants sont réservés par Azure ou l’IANA :
   * Numéros ASN réservés par Azure :
     * ASN publics : 8074, 8075, 12076
     * ASN privés : 65515, 65517, 65518, 65519, 65520
   * ASN réservés par l’IANA : 23456, 64496-64511, 65535-65551
* Alors que le routeur de hub virtuel échange des itinéraires BGP avec votre NVA et les propage vers votre réseau virtuel, il facilite directement la propagation des itinéraires depuis un site local via les passerelles hébergées par le hub virtuel (passerelle VPN/passerelle ExpressRoute/passerelles NVA managées). 

   Le routeur de hub virtuel présente les limites suivantes :

   | Ressource | Limite |
   |---|---|
   |  Nombre d’itinéraires que chaque pair BGP peut publier sur le hub virtuel.| Le hub ne peut accepter qu’un nombre maximal de 10 000 itinéraires (total) à partir de ses ressources connectées. Par exemple, si un hub virtuel a un total de 6 000 itinéraires à partir des réseaux virtuels connectés, des branches, des hubs virtuels, etc., lorsqu’un nouveau Peering BGP est configuré avec une NVA, celle-ci ne peut publier que jusqu’à 4 000 itinéraires. |

## <a name="bgp-peering-scenarios"></a>Scénarios de Peering BGP

Cette section décrit les scénarios dans lesquels la fonctionnalité de Peering BGP peut être utilisée pour configurer le routage.

## <a name="transit-vnet-connectivity"></a><a name="vnet-vnet"></a>Connectivité de réseau virtuel de transit

:::image type="content" source="./media/scenario-bgp-peering-hub/vnet-vnet.png" alt-text="Graphique avec routage de réseau virtuel à réseau virtuel.":::

Dans ce scénario, le hub virtuel nommé « Hub 1 » est connecté à plusieurs réseaux virtuels. L’objectif est d’établir un routage entre les réseaux virtuels VNET1 et VNET5.

### <a name="configuration-steps-without-bgp-peering"></a>Étapes de configuration sans Peering BGP

Les étapes suivantes sont requises lorsque le Peering BGP n’est pas utilisé sur le hub virtuel :

Configuration du hub virtuel

* Dans la table defaultRouteTable du Hub 1, configurez l’itinéraire statique pour VNET5 (sous-réseau 10.2.1.0/24) pointant vers la connexion VNET2.
* Sur la connexion de réseau virtuel du Hub 1 pour VNET2, configurez l’itinéraire statique pour VNET5 pointant vers l’adresse IP de la NVA de VNET2 (sous-réseau 10.2.0.5).
* Sur Hub 1, propagez les itinéraires des connexions pour VNET1 et VNET2 vers defaultRouteTable, puis associez-les à defaultRouteTable.

Configuration du réseau virtuel

* Sur VNET5, configurez un itinéraire défini par l’utilisateur (UDR) pour qu’il pointe vers l’adresse IP de la NVA de VNET2.

### <a name="configuration-steps-with-bgp-peering"></a>Étapes de configuration avec Peering BGP

Dans la configuration précédente, la maintenance des itinéraires statiques et des UDR peut devenir complexe si la configuration de VNET5 change fréquemment. Pour résoudre ce problème, la fonctionnalité de Peering BGP avec un hub virtuel peut être utilisée et la configuration du routage doit être modifiée en suivant les étapes suivantes :

Configuration du hub virtuel

* Sur Hub 1, configurez la NVA de VNET2 en tant que pair BGP. Configurez également la NVA de VNET2 pour obtenir un Peering BGP avec Hub 1.
* Sur Hub 1, propagez les itinéraires des connexions pour VNET1 et VNET2 vers defaultRouteTable, puis associez-les à defaultRouteTable.

Configuration du réseau virtuel

* Sur VNET5, configurez un itinéraire défini par l’utilisateur (UDR) pour qu’il pointe vers l’adresse IP de la NVA de VNET2.

#### <a name="effective-routes"></a>Itinéraires effectifs

Le tableau ci-dessous montre quelques entrées des itinéraires effectifs de Hub 1 dans defaultRouteTable. Notez que l’itinéraire pour VNET5 (sous-réseau 10.2.1.0/24). Il confirme que VNET1 et VNET5 pourront communiquer entre eux.

| Préfixe de destination |  Tronçon suivant| Origine | Chemin ASN|
| --- | --- | --- | ---|
| 10.2.0.0/24 |eastusconn  | ID de connexion du réseau virtuel | - |
| 10.2.1.0/24 |ID de connexion de pair BGP pour NVA  | ID de connexion de pair BGP pour NVA |  65510|
|  10.4.1.0/24 | Hub 2 | Hub 2 | - |

Cette configuration du routage à l’aide de la fonctionnalité supprime la nécessité d’entrées d’itinéraires statiques sur le hub virtuel. Par conséquent, la configuration est plus simple et les tables de routage sont mises à jour de manière dynamique lorsque la configuration des réseaux virtuels connectés (comme VNET5) change.

## <a name="branch-vnet-connectivity"></a><a name="branch-vnet"></a>Connectivité de réseau virtuel de branche

:::image type="content" source="./media/scenario-bgp-peering-hub/branch-vnet.png" alt-text="Graphique avec routage de branche à réseau virtuel.":::

Dans ce scénario, le site local nommé « NVA Branch 1 » a un VPN configuré pour se terminer sur la NVA de VNET2. L’objectif est de configurer le routage entre la NVA branche 1 et le réseau virtuel VNET1.

### <a name="configuration-steps-without-bgp-peering"></a>Étapes de configuration sans Peering BGP

Les étapes suivantes sont requises lorsque le Peering BGP n’est pas utilisé sur le hub virtuel :

Configuration du hub virtuel

* Dans la table defaultRouteTable du Hub 1, configurez l’itinéraire statique pour NVA Branch 1 pointant vers la connexion VNET2.
* Sur la connexion de réseau virtuel du Hub 1 pour VNET2, configurez l’itinéraire statique pour NVA Branch 1 pointant vers l’adresse IP de la NVA de VNET2 (10.2.0.5).
* Sur Hub 1, propagez les itinéraires des connexions pour VNET1 et VNET2 vers defaultRouteTable, puis associez-les à defaultRouteTable.

Configuration du réseau virtuel

* Peering BGP entre NVA de VNET2 et NVA Branch 1, et publications d’itinéraires pour VNET1 de NVA de VNET2 sur NVA Branch 1.

### <a name="configuration-steps-with-bgp-peering"></a>Étapes de configuration avec Peering BGP

Au fil du temps, les préfixes de destination dans NVA Branch 1 peuvent changer. Il peut également y avoir de nombreux sites comme NVA Branch 1 qui ont besoin d’une connectivité à VNET1. Cela entraînerait la nécessité de mettre à jour les itinéraires statiques sur le Hub 1 et la connexion VNET2, ce qui peut devenir fastidieux. Dans ce cas, nous pouvons utiliser la fonctionnalité de Peering BGP avec un hub virtuel. Les étapes de configuration de la connectivité de routage seraient alors les suivantes.

Configuration du hub virtuel

* Sur Hub 1, configurez la NVA de VNET2 en tant que pair BGP. Configurez également la NVA de VNET2 pour obtenir un Peering BGP avec Hub 1.
* Sur Hub 1, propagez les itinéraires des connexions pour VNET1 et VNET2 vers defaultRouteTable, puis associez-les à defaultRouteTable.

Configuration du réseau virtuel

* Peering BGP entre NVA de VNET2 et NVA Branch 1, et publications d’itinéraires pour VNET1 de NVA de VNET2 sur NVA Branch 1.

#### <a name="effective-routes"></a>Itinéraires effectifs

Le tableau ci-dessous montre quelques entrées des itinéraires effectifs de Hub 1 dans defaultRouteTable. Notez que l’itinéraire pour NVA Branch 1 (sous-réseau 192.168.1.0/24) est appris via le Peering BGP avec la NVA.

 | Préfixe de destination |  Tronçon suivant| Origine | Chemin ASN|
| --- | --- | --- | ---|
| 10.2.0.0/24 | eastusconn  | ID de connexion du réseau virtuel | - |
| 192.168.1.0/24 | ID de connexion de pair BGP pour NVA  | ID de connexion de pair BGP pour NVA |  65510|

Pour gérer les changements de réseau dans NVA Branch 1 ou pour établir une connectivité entre de nouveaux sites comme NVA Branch 1, aucune configuration supplémentaire n’est requise sur Hub 1, car le Peering BGP entre Hub 1 et NVA met à jour les tables de routage de manière dynamique. La configuration et la maintenance sont donc simplifiées.

## <a name="next-steps"></a>Étapes suivantes

* [Configurer le Peering BGP avec un hub virtuel](create-bgp-peering-hub-portal.md).
