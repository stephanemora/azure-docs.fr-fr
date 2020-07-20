---
title: À propos du routage de hub virtuel
titleSuffix: Azure Virtual WAN
description: Cet article décrit le routage de hub virtuel
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 368440976558730bd7bb9600a0bf45c56d1ee300
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86147390"
---
# <a name="about-virtual-hub-routing"></a>À propos du routage de hub virtuel

Les fonctionnalités de routage d’un hub virtuel sont fournies par un routeur qui gère tout le routage entre les passerelles à l’aide du protocole BGP (Border Gateway Protocol). Un hub virtuel peut contenir plusieurs passerelles, comme une passerelle VPN site à site, une passerelle ExpressRoute, une passerelle point à site ou un pare-feu Azure. Ce routeur fournit également une connectivité de transit entre les réseaux virtuels qui se connectent à un hub virtuel et peut prendre en charge un débit agrégé de 50 Gbits/s. Ces fonctionnalités de routage s’appliquent aux clients de réseau virtuel Standard.

Pour configurer le routage, consultez le [guide pratique pour configurer le routage de hub virtuel](how-to-virtual-hub-routing.md).

## <a name="routing-concepts"></a><a name="concepts"></a>Concepts de routage

Les sections suivantes décrivent les concepts clés du routage de hub virtuel.

> [!NOTE]
> Certains nouveaux concepts relatifs à la table de routage, à l’association, à la propagation et aux itinéraires statiques de hub dans une connexion de réseau virtuel peuvent toujours être en cours de déploiement et censés se terminer la semaine du 3 août.
>

### <a name="hub-route-table"></a><a name="hub-route"></a>Table de routes du hub

Une table de routage de hub virtuel peut contenir une ou plusieurs routes. Une route comprend un nom, une étiquette, un type de destination, une liste de préfixes de destination et des informations de tronçon suivant pour le routage d’un paquet. Une **connexion** a généralement une configuration de routage liée par association ou propagation à une table de routage.

### <a name="connection"></a><a name="connection"></a>Connexion

Les connexions sont des ressources Resource Manager dotées d’une configuration de routage. Il existe quatre types de connexions :

* **Connexion VPN** : connecte un site VPN à une passerelle VPN de hub virtuel.
* **Connexion ExpressRoute** : connecte un circuit ExpressRoute à une passerelle ExpressRoute de hub virtuel.
* **Connexion de configuration P2S** : connecte une configuration VPN utilisateur (point à site) à une passerelle VPN utilisateur de hub virtuel (point à site).
* **Connexion entre hub et réseau virtuel** : connecte des réseaux virtuels à un hub virtuel.

Vous pouvez configurer la configuration de routage pour une connexion de réseau virtuel durant l’installation. Par défaut, toutes les connexions sont liées par association ou propagation à la table de routage par défaut.

### <a name="association"></a><a name="association"></a>Association

Chaque connexion est associée à une table de routage. L’association d’une connexion à une table de routage permet d’envoyer le trafic à la destination indiquée sous forme de routes dans la table de routage. La configuration de routage de la connexion montre la table de routage associée.  Plusieurs connexions peuvent être associées à la même table de routage. Toutes les connexions VPN, ExpressRoute et VPN utilisateur sont associées à la même table de routage (celle par défaut).

Par défaut, toutes les connexions sont associées à une **table de routage par défaut** dans un hub virtuel. Chaque hub virtuel a sa propre table de routage par défaut que vous pouvez modifier pour ajouter une ou plusieurs routes statiques. Les routes ajoutées de manière statique ont priorité sur les routes apprises de manière dynamique pour les mêmes préfixes.

:::image type="content" source="./media/about-virtual-hub-routing/concepts-association.png" alt-text="Association":::

### <a name="propagation"></a><a name="propagation"></a>Propagation

Les connexions propagent dynamiquement des routes dans une table de routage. Avec une connexion VPN, une connexion ExpressRoute ou une connexion de configuration P2S, les routes sont propagées du hub virtuel dans le routeur local à l’aide du protocole BGP. Les routes peuvent être propagées dans une ou plusieurs tables de routage.

Une **table de routage None** est également disponible pour chaque hub virtuel. La propagation dans la table de routage None implique qu’aucune route ne doit être propagée à partir de la connexion. Les connexions VPN, ExpressRoute et VPN utilisateur propagent des routes dans le même ensemble de tables de routage.

:::image type="content" source="./media/about-virtual-hub-routing/concepts-propagation.png" alt-text="Propagation":::

### <a name="configuring-static-routes-in-a-virtual-network-connection"></a><a name="static"></a>Configuration de routes statiques dans une connexion de réseau virtuel

La configuration de routes statiques fournit un mécanisme pour diriger le trafic par le biais d’une adresse IP de tronçon suivant, qui peut être celle d’une appliance virtuelle réseau provisionnée dans un réseau virtuel spoke attaché à un hub virtuel. La route statique se compose d’un nom de route, d’une liste de préfixes de destination et d’une adresse IP de tronçon suivant.

## <a name="route-tables-in-basic-and-standard-virtual-wans-prior-to-the-feature-set-of-association-and-propagation"></a><a name="route"></a>Tables de routage dans les réseaux étendus virtuels De base et Standard avant l’ensemble des fonctionnalités d’association et de propagation

Les tables de routage disposent désormais de fonctionnalités d’association et de propagation. Une table de routage préexistante est une table de routage qui n’a pas ces fonctionnalités. Si le routage de hub comporte des routes préexistantes et que vous souhaitez utiliser les nouvelles fonctionnalités, tenez compte des éléments suivants :

* **Clients de réseau virtuel Standard avec des routes préexistantes dans le hub virtuel** :

Pour utiliser les nouvelles fonctionnalités de table de route, veuillez patienter jusqu’à la semaine du 3 août que le déploiement dans Azure soit terminé. Si vous avez des routes préexistantes dans la section Routage du hub dans le portail Azure, vous devez d’abord les supprimer, puis essayer de créer de nouvelles tables de route (disponible dans la section Tables de route du hub dans le portail Azure).

* **Clients de réseau virtuel De base avec des routes préexistantes dans le hub virtuel** : Pour utiliser les nouvelles fonctionnalités de table de route, veuillez patienter jusqu’à la semaine du 3 août que le déploiement dans Azure soit terminé. Si vous avez des routes préexistantes dans la section Routage du hub dans le portail Azure, vous devez d’abord les supprimer, puis **mettre à niveau** votre réseau étendu virtuel De base vers un réseau étendu virtuel Standard. Consultez [Mettre à niveau un réseau étendu virtuel De base vers le type Standard](upgrade-virtual-wan.md).

## <a name="next-steps"></a>Étapes suivantes

Pour configurer le routage, consultez le [guide pratique pour configurer le routage de hub virtuel](how-to-virtual-hub-routing.md).

Pour plus d’informations sur Virtual WAN, consultez la [FAQ](virtual-wan-faq.md).
