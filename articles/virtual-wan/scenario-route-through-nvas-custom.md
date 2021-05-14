---
title: Acheminer le trafic via des appliances virtuelles réseau à l’aide de paramètres personnalisés
titleSuffix: Azure Virtual WAN
description: Découvrez les scénarios de routage Virtual WAN consistant à router le trafic à travers des appliances virtuelles réseau (NVA, Network Virtual Appliance). Une appliance NVA différente est utilisée pour le trafic Internet et les paramètres personnalisés.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 04/27/2021
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 153e3dd64546dfa713abefcff106652fc061a6a2
ms.sourcegitcommit: 62e800ec1306c45e2d8310c40da5873f7945c657
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108165818"
---
# <a name="scenario-route-traffic-through-nvas-by-using-custom-settings"></a>Scénario : Acheminer le trafic via des appliances virtuelles réseau à l’aide de paramètres personnalisés

Lorsque vous utilisez le routage de hub virtuel Azure Virtual WAN, vous disposez d’un certain nombre d’options. Cet article vous aide quand vous souhaitez acheminer le trafic via une appliance virtuelle réseau (NVA) pour la communication entre les réseaux virtuels et les branches et d’utiliser une NVA distincte pour le trafic Internet. Pour plus d’informations, consultez [À propos du routage de hub virtuel](about-virtual-hub-routing.md).

## <a name="design"></a>Conception

* Les **spokes** pour les réseaux virtuels connectés au hub virtuel. (Par exemple, VNet 1, VNet 2 et VNet 3 dans le diagramme plus loin dans cet article.)
* **Réseau virtuel de service** pour les réseaux virtuels où les utilisateurs ont déployé une NVA pour inspecter le trafic non-Internet, et éventuellement avec des services communs accédés par des spokes. (Par exemple, VNet 4 dans le diagramme plus loin dans cet article.)
* **Réseau virtuel de périmètre** pour les réseaux virtuels où les utilisateurs ont déployé une NVA pour inspecter le trafic Internet. (Par exemple, VNet 5 dans le diagramme plus loin dans cet article.)
* **Hubs** pour les hubs Virtual WAN gérés par Microsoft.

Le tableau suivant fournit un récapitulatif des connexions prises en charge dans ce scénario :

| À partir          | À|Spokes|Réseau virtuel de service|Branches|Internet|
|---|:---:|:---:|:---:|:---:|:---:|
| **Spokes**| ->| directement ; |directement ; | via le réseau virtuel de service |Via un réseau virtuel de périmètre |
| **Réseau virtuel de service**| ->| directement ; |n/a| directement ; | |
| **Branches** | ->| via le réseau virtuel de service |directement ;| directement ; |  |

Chacune des cellules de la matrice de connectivité indique si la connectivité circule directement via Virtual WAN ou sur l’un des réseaux virtuels avec une appliance virtuelle réseau.

Notez les points suivants :

* Spokes :
  * Les spokes atteindront d’autres spokes directement via des hubs Virtual WAN.
  * Les spokes obtiendront la connectivité aux branches via un itinéraire statique qui pointe vers le réseau virtuel de service. Ils n’apprennent pas les préfixes spécifiques des branches, car ces préfixes sont plus spécifiques et remplaceraient le résumé.
  * Les spokes enverront le trafic Internet vers le réseau virtuel de périmètre via un appairage direct de réseaux virtuels.
* Les branches atteindront les spokes via un routage statique pointant vers le réseau virtuel de service. Ils n’apprennent pas les préfixes spécifiques des réseaux virtuels qui remplacent l’itinéraire statique résumé.
* Le réseau virtuel de service sera similaire à un réseau virtuel de services partagés qui doit être accessible à partir de chaque réseau virtuel et de chaque branche.
* Le réseau virtuel de périmètre n’a pas besoin d’une connectivité sur Virtual WAN, car le seul trafic qu’il prendra en charge passe par des peerings de réseaux virtuels directs. Toutefois, afin de simplifier la configuration, nous allons utiliser le même modèle de connectivité que pour le réseau virtuel de périmètre.

Nous avons trois modèles de connectivité distincts, qui se traduisent par trois tables de routage. Les associations aux différents réseaux virtuels sont les suivantes :

* Spokes :
  * Table de routage associée : **RT_V2B**
  * Propagation aux tables de routage : **RT_V2B** et **RT_SHARED**
* Réseaux virtuels d’appliance virtuelle réseau (réseau virtuel de service et réseau virtuel DMZ) :
  * Table de routage associée : **RT_SHARED**
  * Propagation aux tables de routage : **RT_SHARED**
* Branches :
  * Table de routage associée : **Par défaut**
  * Propagation aux tables de routage : **RT_SHARED** et **Par défaut**

> [!NOTE]
> Assurez-vous que les réseaux virtuels spoke ne se propagent pas à l’étiquette par défaut. Cela garantit que le trafic des branches vers les réseaux virtuels spoke est transféré aux appliances virtuelles réseau.

Ces itinéraires statiques garantissent que le trafic à destination et en provenance du réseau virtuel et de la branche parcourt l’appliance virtuelle réseau dans le réseau virtuel de service (VNet 4) :

| Description | Table de routage | Itinéraire statique              |
| ----------- | ----------- | ------------------------- |
| Branches    | RT_V2B      | 10.2.0.0/16 -> vnet4conn  |
| Spokes NVA  | Default     | 10.1.0.0/16 -> vnet4conn  |

Vous pouvez maintenant utiliser Virtual WAN pour sélectionner la connexion appropriée à laquelle envoyer les paquets. Vous devez également utiliser Virtual WAN pour sélectionner l’action appropriée à effectuer lors de la réception de ces paquets. Pour ce faire, vous utilisez les tables de routage de connexion, comme suit :

| Description | Connexion | Itinéraire statique            |
| ----------- | ---------- | ----------------------- |
| VNet2Branch | vnet4conn  | 10.2.0.0/16 -> 10.4.0.5 |
| Branch2VNet | vnet4conn  | 10.1.0.0/16 -> 10.4.0.5 |

Pour plus d’informations, consultez [À propos du routage de hub virtuel](about-virtual-hub-routing.md).

## <a name="architecture"></a>Architecture

Le diagramme suivant illustre l’architecture décrite précédemment dans cet article.

Le diagramme affiche un hub ; **Hub 1**.

* **Hub1** est connecté directement aux réseaux virtuels NVA **VNet 4** et **VNet 5**.

* Le trafic entre les réseaux virtuels 1, 2 et 3, et les branches devrait passer par l’**appliance virtuelle réseau du réseau virtuel 4** 10.4.0.5.

* Tout le trafic Internet en provenance des VNets 1, 2 et 3 devrait passer par la **NVA VNet 5** 10.5.0.5.

:::image type="content" source="./media/routing-scenarios/nva-custom/architecture.png" alt-text="Diagramme de l’architecture d’un réseau.":::

## <a name="workflow"></a><a name="workflow"></a>Flux de travail

:::image type="content" source="./media/routing-scenarios/nva-custom/workflow.png" alt-text="Diagramme du workflow." lightbox="./media/routing-scenarios/nva-custom/workflow.png":::

Pour configurer le routage via une appliance virtuelle réseau (NVA), procédez comme suit :

1. Pour que le trafic Internet transite via VNet 5, vous avez besoin que les VNets 1, 2 et 3 se connectent directement à VNet 5 via l’appairage de réseaux virtuels. Vous avez également besoin d’un itinéraire défini par l’utilisateur dans les réseaux virtuels pour 0.0.0.0/0 et le tronçon suivant 10.5.0.5.

   Si vous ne souhaitez pas connecter les réseaux virtuels 1, 2 et 3 au réseau virtuel 5 et que vous utilisez simplement l’appliance virtuelle réseau (NVA) dans le réseau virtuel 4 pour acheminer le trafic 0.0.0.0/0 à partir des branches (connexions VPN locales ou ExpressRoute), accédez à l’[autre workflow](#alternate).

   Mais si vous souhaitez que le trafic réseau virtuel à réseau virtuel transite par l’appliance virtuelle réseau, vous devez déconnecter les réseaux virtuels 1, 2 et 3 du hub virtuel et les connecter ou les empiler au-dessus du réseau virtuel 4 Spoke de l’appliance virtuelle réseau. Dans un WAN virtuel, le trafic réseau virtuel à réseau virtuel transite via le hub du WAN virtuel ou le pare-feu Azure du hub d’un WAN virtuel (hub sécurisé). En cas de peering direct des réseaux virtuels, ceux-ci peuvent communiquer directement en ignorant le transit via le hub virtuel.

1. Dans le portail Azure, accédez à votre hub virtuel et créez une table de routage personnalisée appelée **RT_Shared**. Ce tableau apprend les itinéraires via la propagation à partir de toutes connexions de réseau virtuel et de branche. Vous pouvez voir cette table vide dans le diagramme suivant.

   * **Itinéraires :** Vous n’avez pas besoin d’ajouter des itinéraires statiques.

   * **Association :** Sélectionnez les réseaux virtuels 4 et 5, ce qui signifie que les connexions de ces réseaux virtuels sont associées à la table de routage **RT_Shared**.

   * **Propagation :** Étant donné que vous souhaitez que l’ensemble des branches et des connexions de réseau virtuel propagent leurs itinéraires de manière dynamique vers cette table de routage, sélectionnez les branches et tous les réseaux virtuels.

1. Créez une table de routage personnalisée appelée **RT_V2B** pour acheminer le trafic des VNets 1, 2 et 3 vers les branches.

   * **Itinéraires :** ajoutez une entrée d’itinéraire statique agrégé pour les branches, avec un tronçon suivant en guise de connexion de VNet 4. Configurez un itinéraire statique dans la connexion du réseau virtuel 4 pour les préfixes de branche. Indiquez le tronçon suivant comme adresse IP spécifique de l’appliance virtuelle réseau dans le réseau virtuel 4.

   * **Association :** sélectionnez tous les **réseaux virtuels 1, 2 et 3**. Cela implique que les connexions de réseau virtuel 1, 2 et 3 seront associées à cette table de routage et pourront apprendre des itinéraires statiques et dynamiques à partir de la propagation dans cette table de routage.

   * **Propagation :** les connexions propagent des itinéraires vers des tables de routage. La sélection des réseaux virtuels 1, 2 et 3 permet de propager les itinéraires des réseaux virtuels 1, 2 et 3 vers cette table de routage. Il n’est pas nécessaire de propager les itinéraires des connexions de branche vers **RT_V2B**, car le trafic de réseau virtuel de branche transite par la NVA dans VNet 4.
  
1. Modifiez la table de routage par défaut **DefaultRouteTable**.

   Toutes les connexions VPN, Azure ExpressRoute et VPN utilisateur sont associées à la table de routage par défaut. Toutes les connexions VPN, ExpressRoute et VPN utilisateur propagent des itinéraires vers le même ensemble de tables de routage.

   * **Itinéraires :** ajoutez une entrée d’itinéraire statique agrégé pour les VNets 1, 2 et 3, avec un tronçon en guise de connexion de VNets 4. Configurez un itinéraire statique dans la connexion du réseau virtuel 4 pour les préfixes agrégés des réseaux virtuels 1, 2 et 3. Indiquez le tronçon suivant comme adresse IP spécifique de l’appliance virtuelle réseau dans le réseau virtuel 4.

   * **Association :** Assurez-vous que l’option des branches (VPN/ER/P2S) est sélectionnée, en veillant à ce que les connexions de branches locales soient associées à la table de routage par défaut.

   * **Propagation à partir de :** Assurez-vous que l’option des branches (VPN/ER/P2S) est sélectionnée, en veillant à ce que les connexions locales soient des routes propagées à la table de routage par défaut.

## <a name="alternate-workflow"></a><a name="alternate"></a>Autre workflow

Dans ce workflow, vous ne connectez pas les réseaux virtuels 1, 2 et 3 au réseau virtuel 5. Vous utilisez plutôt l’appliance virtuelle réseau dans le réseau virtuel 4 pour acheminer le trafic 0.0.0.0/0 des branches (connexions VPN locales ou ExpressRoute).

:::image type="content" source="./media/routing-scenarios/nva-custom/alternate.png" alt-text="Diagramme de l’autre workflow." lightbox="./media/routing-scenarios/nva-custom/alternate.png":::

Pour configurer le routage via une appliance virtuelle réseau (NVA), procédez comme suit :

1. Dans le portail Azure, accédez à votre hub virtuel et créez une table de routage personnalisée appelée  **RT_NVA** pour diriger le trafic via l’appliance virtuelle réseau 10.4.0.5  

   * **Itinéraires :**  aucune action requise.

   * **Association :**  sélectionnez **VNet4**. Cela implique que les connexions du réseau virtuel 4 seront associées à cette table de routage et pourront apprendre des itinéraires statiques et dynamiques à partir de la propagation dans cette table de routage.

   * **Propagation :**   les connexions propagent des itinéraires vers des tables de routage. La sélection des réseaux virtuels 1, 2 et 3 permet de propager les itinéraires des réseaux virtuels 1, 2 et 3 vers cette table de routage. La sélection des branches (VPN/ER/P2S) permet de propager des itinéraires à partir de branches/connexions locales vers cette table de routage. Toutes les connexions VPN, ExpressRoute et VPN utilisateur propagent des itinéraires vers le même ensemble de tables de routage.

1. Créez une table de routage personnalisée appelée  **RT_VNET** pour diriger le trafic des réseaux virtuels 1, 2 et 3 vers des branches ou Internet (0.0.0.0/0) via l’appliance virtuelle réseau du réseau virtuel 4. Le trafic réseau virtuel à réseau virtuel est direct et ne transite pas par l’appliance virtuelle réseau du réseau virtuel 4. Si vous souhaitez que le trafic transite par l’appliance virtuelle réseau, déconnectez les réseaux virtuels 1, 2 et 3 et connectez-les au réseau virtuel 4 à l’aide du peering Vnet.

   * **Itinéraires :**  

     * Ajoutez un itinéraire agrégé « 10.2.0.0/16 » avec le tronçon suivant comme connexion au réseau virtuel 4 pour le trafic entre les réseaux virtuels 1, 2 et 3 et les branches. Dans la connexion au réseau virtuel 4, configurez un itinéraire pour « 10.2.0.0/16 » et indiquez le tronçon suivant comme adresse IP spécifique de l’appliance virtuelle réseau dans le réseau virtuel 4.

     * Ajoutez un itinéraire « 0.0.0.0/0 » avec le tronçon suivant comme connexion au réseau virtuel 4. « 0.0.0.0/0 » est ajouté pour impliquer l’envoi du trafic vers Internet. Elle n’implique pas de préfixes d’adresse spécifiques appartenant à réseaux virtuels ou à des branches. Dans la connexion au réseau virtuel 4, configurez un itinéraire pour « 0.0.0.0/0 » et indiquez le tronçon suivant comme adresse IP spécifique de l’appliance virtuelle réseau dans le réseau virtuel 4.

   * **Association :**  sélectionnez tous les **réseaux virtuels 1, 2 et 3**. Cela implique que les connexions de réseau virtuel 1, 2 et 3 seront associées à cette table de routage et pourront apprendre des itinéraires statiques et dynamiques à partir de la propagation dans cette table de routage.

   * **Propagation :**   les connexions propagent des itinéraires vers des tables de routage. La sélection des VNets 1, 2 et 3 permet de propager les itinéraires des VNets 1, 2 et 3 vers cette table de routage. Assurez-vous que l’option pour les branches (VPN/ER/P2S) n’est pas sélectionnée. Cela garantit que les connexions locales ne peuvent pas accéder directement aux réseaux virtuels 1, 2 et 3.

1. Modifiez la table de routage par défaut,  **DefaultRouteTable**.

   Toutes les connexions VPN, Azure ExpressRoute et VPN utilisateur sont associées à la table de routage par défaut. Toutes les connexions VPN, ExpressRoute et VPN utilisateur propagent des itinéraires vers le même ensemble de tables de routage.

   * **Itinéraires :**  

     * Ajoutez un itinéraire agrégé « 10.1.0.0/16 » pour les **réseaux virtuels 1, 2 et 3** avec la **connexion au réseau virtuel 4** comme tronçon suivant.

     * Ajoutez un itinéraire « 0.0.0.0/0 » avec le tronçon suivant comme **connexion au réseau virtuel 4**. « 0.0.0.0/0 » est ajouté pour impliquer l’envoi du trafic vers Internet. Elle n’implique pas de préfixes d’adresse spécifiques appartenant à réseaux virtuels ou à des branches. Dans l’étape précédente de la connexion au réseau virtuel 4, vous avez déjà configuré un itinéraire pour « 0.0.0.0/0 », avec le tronçon suivant comme adresse IP spécifique de l’appliance virtuelle réseau dans le réseau virtuel 4.

   * **Association :**  Assurez-vous que l’option pour les branches **(VPN/ER/P2S)** est sélectionnée. Cela garantit que les connexions de branche locales sont associées à la table de routage par défaut. Toutes les connexions VPN, Azure ExpressRoute et VPN utilisateur sont associées uniquement à la table de routage par défaut.

   * **Propagation à partir de :**  Assurez-vius que l’option pour les branches **(VPN/ER/P2S)** est sélectionnée. Cela garantit que les connexions locales propagent les itinéraires vers la table de routage par défaut. Toutes les connexions VPN, ExpressRoute et VPN utilisateur propagent des itinéraires vers le « même ensemble de tables de routage ».

   >[!Note]
   >
   > * Les utilisateurs du portail doivent activer la « propagation vers l’itinéraire par défaut » sur les connexions (VPN/ER/P2S/VNet) pour que l’itinéraire 0.0.0.0/0 prenne effet.
   > * Les utilisateurs PS/CLI/REST doivent affecter la valeur true à l’indicateur « enableinternetsecurity » pour que l’itinéraire 0.0.0.0/0 prenne effet.
   > * La connexion de réseau virtuel ne prend pas en charge l’adresse IP de tronçon suivant « multiple/unique » pour l’appliance virtuelle de réseau « same » dans un réseau virtuel SPOKE « si » l’un des itinéraires avec l’adresse IP de tronçon suivant est indiqué comme adresse IP publique ou 0.0.0.0/0 (Internet)

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur Virtual WAN, consultez la [FAQ](virtual-wan-faq.md).
* Pour plus d’informations sur le routage de hub virtuel, consultez [À propos du routage de hub virtuel](about-virtual-hub-routing.md).
