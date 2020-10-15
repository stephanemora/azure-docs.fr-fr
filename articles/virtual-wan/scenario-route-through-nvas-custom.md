---
title: 'Scénario : Acheminer le trafic via des appliances virtuelles réseau à l’aide de paramètres personnalisés'
titleSuffix: Azure Virtual WAN
description: Ce scénario vous aide à acheminer le trafic via des appliances virtuelles réseau en utilisant une appliance virtuelle réseau différente pour le trafic lié à Internet.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: e1cf9faeab60264d491539256828151e496ade8f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91267497"
---
# <a name="scenario-route-traffic-through-nvas---custom-preview"></a>Scénario : Acheminer le trafic via des appliances virtuelles réseau – Personnalisé (préversion)

Lorsque vous utilisez le routage de hub virtuel Virtual WAN, un certain nombre de scénarios sont possibles. Dans ce scénario d’appliance virtuelle réseau (NVA), l’objectif est d’acheminer le trafic via une NVA pour la communication entre les réseaux virtuels et les branches et d’utiliser une NVA distincte pour le trafic Internet. Pour plus d’informations sur le routage de hub virtuel, consultez [À propos du routage de hub virtuel](about-virtual-hub-routing.md).

## <a name="design"></a><a name="design"></a>Conception

Dans ce scénario, nous allons utiliser la convention d’affectation de noms :

* « Réseau virtuel de service » pour les réseaux virtuels où les utilisateurs ont déployé une NVA (VNet 4 dans la **Figure 1**) pour inspecter le trafic non-Internet.
* « Réseau virtuel DMZ » pour les réseaux virtuels où les utilisateurs ont déployé une NVA (VNet 5 dans la **Figure 1**) pour inspecter le trafic Internet.
* « Spokes NVA » pour les réseaux virtuels connectés à un réseau virtuel NVA (VNet 1, VNet 2 et VNet 3 dans la **Figure 1**).
* « Hubs » pour les hubs Virtual WAN gérés par Microsoft.

La matrice de connectivité suivante résume les flux pris en charge dans ce scénario :

**Matrice de connectivité**

| Du          | Par :|*Spokes NVA*|*Réseau virtuel de service*|*Réseau virtuel DMZ*|*Branches statiques*|
|---|---|---|---|---|---|
| **Spokes NVA**| &#8594;|      X |            X |   Peering |    statique    |
| **Réseau virtuel de service**| &#8594;|    X |            X |      X    |      X       |
| **Réseau virtuel DMZ** | &#8594;|       X |            X |      X    |      X       |
| **Branches** | &#8594;|  statique |            X |      X    |      X       |

Chacune des cellules de la matrice de connectivité indique si une connexion de Virtual WAN (côté « De » du flux, les en-têtes de lignes) apprend un préfixe de destination (côté « À » du flux, en-têtes de colonne en italique) pour un flux de trafic spécifique. Un « X » signifie que la connectivité est fournie en mode natif par Virtual WAN, et « Statique » signifie que la connectivité est assurée par Virtual WAN par le biais de routes statiques. Passons en revue les différentes lignes :

* Spokes NVA :
  * Les spokes atteindront d’autres spokes directement via des hubs Virtual WAN.
  * Les spokes obtiendront la connectivité aux branches via un itinéraire statique qui pointe vers le réseau virtuel de service. Ils ne doivent pas apprendre les préfixes spécifiques des branches (sinon, ceux-ci seraient plus spécifiques et remplaceraient le résumé).
  * Les spokes enverront le trafic Internet vers le réseau virtuel DMZ via un appairage direct de réseaux virtuels.
* Branches :
  * Les branches atteindront les spokes via un routage statique pointant vers le réseau virtuel de service. Ils ne doivent pas apprendre les préfixes spécifiques des réseaux virtuels qui remplacent l’itinéraire statique résumé.
* Le réseau virtuel de service sera similaire à un réseau virtuel de services partagés qui doit être accessible à partir de chaque réseau virtuel et de chaque branche.
* Le réseau virtuel DMZ n’a pas vraiment besoin d’une connectivité sur Virtual WAN, puisque le seul trafic qu’il prendra en charge passera par les appairages directs de réseaux virtuels. Toutefois, nous allons utiliser le même modèle de connectivité que pour le réseau virtuel DMZ afin de simplifier la configuration.

Ainsi, notre matrice de connectivité nous offre trois modèles de connectivité distincts, qui se traduisent par trois tables de routage. Les associations aux différents réseaux virtuels sont les suivantes :

* Spokes NVA :
  * Table de routage associée : **RT_V2B**
  * Propagation aux tables de routage : **RT_V2B** et **RT_SHARED**
* Réseaux virtuels NVA (interne et Internet) :
  * Table de routage associée : **RT_SHARED**
  * Propagation aux tables de routage : **RT_SHARED**
* Branches :
  * Table de routage associée : **Par défaut**
  * Propagation aux tables de routage : **RT_SHARED** et **Par défaut**

Nous avons besoin de ces itinéraires statiques pour nous assurer que le trafic de réseau virtuel à branche et de branche à réseau virtuel passe par la NVA dans le réseau virtuel de service (VNet 4) :

| Description | Table de routage | Itinéraire statique              |
| ----------- | ----------- | ------------------------- |
| Branches    | RT_V2B      | 10.2.0.0/16 -> vnet4conn  |
| Spokes NVA  | Default     | 10.1.0.0/16 -> vnet4conn  |

Désormais, Virtual WAN sait à quelle connexion envoyer les paquets, mais la connexion doit savoir ce qu’il faut faire lors de la réception de ces paquets : c’est là que les tables de routage de connexion sont utilisées.

| Description | Connexion | Itinéraire statique            |
| ----------- | ---------- | ----------------------- |
| VNet2Branch | vnet4conn  | 10.2.0.0/16 -> 10.4.0.5 |
| Branch2VNet | vnet4conn  | 10.1.0.0/16 -> 10.4.0.5 |

À ce stade, tout devrait être en place.

Pour plus d’informations sur le routage de hub virtuel, consultez [À propos du routage de hub virtuel](about-virtual-hub-routing.md).

## <a name="architecture"></a><a name="architecture"></a>Architecture

La **Figure 1** présente un hub, **Hub 1**.

* **Hub1** est connecté directement aux réseaux virtuels NVA **VNet 4** et **VNet 5**.

* Le trafic entre les VNets 1, 2 et 3 et les branches (VPN/ER/P2S) devrait passer par la **NVA VNet 4** 10.4.0.5.

* Tout le trafic Internet en provenance des VNets 1, 2 et 3 devrait passer par la **NVA VNet 5** 10.5.0.5.

**Figure 1**

:::image type="content" source="./media/routing-scenarios/nva-custom/figure-1.png" alt-text="Figure 1":::

## <a name="workflow"></a><a name="workflow"></a>Flux de travail

Pour configurer le routage via la NVA, voici les étapes à prendre en compte :

1. Pour que le trafic Internet transite via VNet 5, vous avez besoin que les VNets 1, 2 et 3 se connectent directement à VNet 5 via l’appairage de réseaux virtuels. Vous avez également besoin d’un itinéraire défini par l’utilisateur configuré dans le réseaux virtuels pour 0.0.0.0/0 et le tronçon suivant 10.5.0.5. Actuellement, Virtual WAN n’autorise pas une appliance virtuelle réseau de tronçon suivant dans le hub virtuel pour 0.0.0.0/0.

1. Dans le portail Azure, accédez à votre hub virtuel et créez une table de routage personnalisée **RT_Shared** qui apprendra les itinéraires via la propagation de toutes les connexions de réseaux virtuels et de branche. La **Figure 2** illustre cela sous la forme d’une table de routage personnalisé vide **RT_Shared**.

   * **Itinéraires :** vous n’avez pas besoin d’ajouter des itinéraires statiques.

   * **Association :** sélectionnez les VNets 4 et 5, ce qui signifie que leurs connexions s’associent à la table de routage **RT_Shared**.

   * **Propagation :** étant donné que vous souhaitez que l’ensemble des branches et des connexions de réseau virtuel propagent leurs itinéraires de manière dynamique vers cette table de routage, sélectionnez les branches et tous les réseaux virtuels.

1. Créez une table de routage personnalisée **RT_V2B** pour acheminer le trafic des VNets 1, 2 et 3 vers les branches.

   * **Itinéraires :** ajoutez une entrée d’itinéraire statique agrégé pour les branches (VPN/ER/P2S) (10.2.0.0/16 dans la **Figure 2**) avec un tronçon suivant en guise de connexion de VNet 4. Vous devez également configurer un itinéraire statique dans la connexion de VNet 4 pour les préfixes de branche et indiquer le tronçon suivant vers l’adresse IP spécifique de la NVA dans VNet 4.

   * **Association :** sélectionnez tous les VNets 1, 2 et 3. Cela implique que les connexions de réseau virtuel 1, 2 et 3 seront associées à cette table de routage et pourront apprendre des itinéraires statiques et dynamiques à partir de la propagation dans cette table de routage.

   * **Propagation :** les connexions propagent des itinéraires vers des tables de routage. La sélection des VNets 1, 2 et 3 permet de propager les itinéraires des VNets 1, 2 et 3 vers cette table de routage. Il n’est pas nécessaire de propager les itinéraires des connexions de branche vers RT_V2B, car le trafic de réseau virtuel de branche transite par la NVA dans VNet 4.
  
1. Modifiez la table de routage par défaut **DefaultRouteTable**.

   Toutes les connexions VPN, ExpressRoute et VPN utilisateur sont associées à la table de routage par défaut. Toutes les connexions VPN, ExpressRoute et VPN utilisateur propagent des itinéraires vers le même ensemble de tables de routage.

   * **Itinéraires :** ajoutez une entrée d’itinéraire statique agrégé pour les VNets 1, 2 et 3 (10.1.0.0/16 dans la **Figure 2**) avec un tronçon en guise de connexion de VNets 4. Vous devez également configurer un itinéraire statique dans la connexion de VNet 4 pour les préfixes agrégés des VNets 1, 2 et 3 et indiquer le tronçon suivant vers l’adresse IP spécifique de la NVA dans VNet 4.

   * **Association :** assurez-vous que l’option des branches (VPN/ER/P2S) est sélectionnée, en veillant à ce que les connexions de branches locales soient associées à la *table de routage par défaut*.

   * **Propagation à partir de :** assurez-vous que l’option des branches (VPN/ER/P2S) est sélectionnée, en veillant à ce que les connexions locales propagent des itinéraires vers la *table de routage par défaut*.

**Figure 2**

:::image type="content" source="./media/routing-scenarios/nva-custom/figure-2.png" alt-text="Figure 1" lightbox="./media/routing-scenarios/nva-custom/figure-2.png":::

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur Virtual WAN, consultez la [FAQ](virtual-wan-faq.md).
* Pour plus d’informations sur le routage de hub virtuel, consultez [À propos du routage de hub virtuel](about-virtual-hub-routing.md).
