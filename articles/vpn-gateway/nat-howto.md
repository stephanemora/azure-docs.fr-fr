---
title: Configurer NAT sur une passerelle VPN Azure
titleSuffix: Azure VPN Gateway
description: Découvrez comment configurer NAT sur une passerelle VPN Azure.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 06/24/2021
ms.author: yushwang
ms.openlocfilehash: 8b9d3d298986aa4aae6ead4e9733c9b0994b1f91
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2021
ms.locfileid: "113214062"
---
# <a name="how-to-configure-nat-on-azure-vpn-gateways-preview"></a>Comment configurer NAT sur des passerelles VPN Azure (préversion)

Cet article vous aide à configurer la traduction d’adresses réseau (NAT, Network Address Translation) sur une passerelle VPN Azure à l’aide du portail Azure.

## <a name="about-nat"></a><a name="about"></a>À propos de NAT

Le processus NAT définit les mécanismes de traduction d’une adresse IP en une autre dans un paquet IP. Il est couramment utilisé pour connecter des réseaux avec des plages d’adresses IP qui se chevauchent. Les règles ou stratégies NAT sur les périphériques de passerelle qui connectent les réseaux spécifient les mappages d’adresses pour la traduction d’adresses sur les réseaux.

Pour plus d’informations sur la prise en charge de NAT sur une passerelle VPN Azure, consultez [À propos de NAT sur les passerelles VPN Azure](nat-overview.md).

> [!IMPORTANT] 
> Azure NAT pour passerelle VPN est actuellement en préversion. 
> * Ne manquez pas de lire la section **[Limitations de la préversion](#limits)** de cet article.
> * NAT sur une passerelle VPN Azure prend uniquement en charge les règles NAT 1:1 statiques. Les règles de NAT dynamique ne sont pas prises en charge.
> * NAT est pris en charge sur les références (SKU) suivantes : VpnGw2~5, VpnGw2AZ~5AZ.

## <a name="getting-started"></a>Bien démarrer

Chaque partie de cet article vous aide à constituer un bloc de construction pour la configuration de NAT dans votre connectivité réseau. Si vous terminez ces trois parties, vous générez la topologie illustrée dans le Schéma 1.

### <a name="diagram-1"></a><a name="diagram"></a>Diagramme 1

:::image type="content" source="./media/nat-overview/vpn-nat.png" alt-text="Capture d’écran du diagramme 1." lightbox="./media/nat-overview/vpn-nat.png" border="false":::

### <a name="prerequisites"></a>Prérequis

* Assurez-vous de disposer d’un abonnement Azure. Si vous ne disposez pas déjà d’un abonnement Azure, vous pouvez activer vos [avantages abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou créer un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).
* Consultez la section [Limitations de la préversion](#limits).

## <a name="part-1-create-vnet-and-gateways"></a><a name ="vnet"></a>Partie 1 : Créer un réseau virtuel et des passerelles

Dans cette section, vous allez créer un réseau virtuel, une passerelle VPN et les ressources de passerelle de réseau local correspondant aux ressources présentées dans le [Diagramme 1](#diagram).

Pour créer ces ressources, suivez les étapes décrites dans l’article [Didacticiel site à site](tutorial-site-to-site-portal.md). Complétez les sections suivantes de l’article, mais ne créez pas de connexion.

* [Réseau virtuel](tutorial-site-to-site-portal.md#CreatVNet)
* [Passerelle VPN](tutorial-site-to-site-portal.md#VNetGateway)
* [Passerelle de réseau local](tutorial-site-to-site-portal.md#LocalNetworkGateway)
* [Configurer votre périphérique VPN](tutorial-site-to-site-portal.md#VPNDevice)


>[!IMPORTANT]
>  Lorsque vous suivez les étapes décrites dans les articles suivants, ne créez pas les ressources de **connexion** évoquées dans les articles. Cela échouera parce que les espaces d’adressage IP sont les mêmes pour le réseau virtuel, la branche 1 et la branche 2. Suivez les étapes de la section suivante pour créer les règles NAT, puis créez les connexions avec les règles NAT.
>


Les captures d’écran suivantes présentent des exemples de ressources à créer.

* **Réseau virtuel**

   :::image type="content" source="./media/nat-howto/vnet.png" alt-text="Capture d’écran montrant l’espace d’adressage de réseau virtuel." lightbox="./media/nat-howto/vnet.png":::
* **Passerelle VPN**

   :::image type="content" source="./media/nat-howto/vpn-gateway.png" alt-text="Capture d’écran montrant la passerelle." lightbox="./media/nat-howto/vpn-gateway.png":::
* **Passerelle de réseau local de la Branche 1**

   :::image type="content" source="./media/nat-howto/branch-1.png" alt-text="Capture d’écran montrant la passerelle de réseau local de la Branche 1." lightbox="./media/nat-howto/branch-1.png" :::

* **Passerelle de réseau local de la Branche 2**

   :::image type="content" source="./media/nat-howto/branch-2.png" alt-text="Capture d’écran montrant la passerelle de réseau local de la Branche 2." lightbox="./media/nat-howto/branch-2.png":::

   > [!IMPORTANT] 
   > Pendant la période de préversion, si l’espace d’adressage de la passerelle de réseau local est le même ou plus petit que l’espace d’adressage du réseau virtuel, utilisez **BGP** et laissez le champ de l’espace d’adressage de la passerelle de réseau local **vide**. Un routage statique (non-BGP) n’est pas pris en charge dans ce scénario pendant la période de préversion.
   >

## <a name="part-2-create-nat-rules"></a><a name ="nat-rules"></a>Partie 2 : Créer des règles NAT

Avant de créer des connexions, vous devez créer et enregistrer des règles NAT sur la passerelle VPN. Le tableau suivant présente les règles NAT obligatoires. Reportez-vous au [Diagramme 1](#diagram) pour la topologie.

**Tableau des règles NAT**

| Name     | Type   | Mode        | Interne    | Externe     | Connexion          |
| ---      | ---    | ---         | ---         | ---          | ---                 |
| Réseau virtuel     | statique | EgressSNAT  | 10.0.1.0/24 | 100.0.1.0/24 | Les deux connexions    | 
| Branch_1 | statique | IngressSNAT | 10.0.1.0/24 | 100.0.2.0/24 | Connexion à la Branche 1 |
| Branch_2 | statique | IngressSNAT | 10.0.1.0/24 | 100.0.3.0/24 | Connexion à la Branche 2 |

Pour créer toutes les règles NAT sur la passerelle VPN, procédez comme suit.

1. Dans le portail Azure, accédez à la page de ressource **Passerelle de réseau virtuel**, puis sélectionnez **Règles NAT (préversion)** .
1. À l’aide du **Tableau des règles NAT** ci-dessus, renseignez les valeurs.

   :::image type="content" source="./media/nat-howto/nat-rules.png" alt-text="Capture d’écran montrant les règles NAT." lightbox="./media/nat-howto/nat-rules.png":::
1. Cliquez sur **Enregistrer** pour enregistrer les règles NAT dans la ressource de passerelle VPN. L’opération peut prendre jusqu’à 10 minutes.

## <a name="part-3-create-connections-and-link-nat-rules"></a><a name ="connections"></a>Partie 3 : Créer des connexions et lier des règles NAT

Dans cette section, vous allez créer les connexions, puis y associer les règles NAT afin d’implémenter l’exemple de topologie illustré dans le [Diagramme 1](#diagram).

### <a name="1-create-connections"></a>1. Créer des connexions

Suivez les étapes décrites dans l’article [Créer une connexion site à site](tutorial-site-to-site-portal.md) afin de créer les deux connexions comme indiqué ci-dessous :

   :::image type="content" source="./media/nat-howto/connections.png" alt-text="Capture d’écran montrant la page Connexions." lightbox="./media/nat-howto/connections.png":::

### <a name="2-associate-nat-rules-with-the-connections"></a>2. Associer les règles NAT aux connexions

Dans cette étape, vous allez associer les règles NAT à chaque ressource de connexion.

1. Dans le portail Azure, accédez aux ressources de connexion, puis sélectionnez **Configuration**.

1. Sous règles NAT d’entrée, sélectionnez les règles NAT créées précédemment.

   :::image type="content" source="./media/nat-howto/config-nat.png" alt-text="Capture d’écran montrant les règles NAT configurées." lightbox="./media/nat-howto/config-nat.png":::

1. Cliquez sur **Enregistrer** pour appliquer les configurations à la ressource de connexion.

1. Répétez les étapes pour appliquer les règles NAT à d’autres ressources de connexion.

1. Si vous utilisez le protocole BGP, dans la page des règles NAT, sélectionnez **Activer la traduction d’itinéraire BGP**, puis cliquez sur **Enregistrer**. Notez que la table affiche maintenant les connexions liées à chaque règle NAT.

   :::image type="content" source="./media/nat-howto/nat-rules-linked.png" alt-text="Capture d’écran montrant Activer BGP." lightbox="./media/nat-howto/nat-rules-linked.png":::

Une fois ces étapes accomplies, vous aurez une configuration correspondant à la topologie illustrée dans le [Diagramme 1](#diagram).

## <a name="preview-limitations"></a><a name ="limits"></a>Limitations de la préversion

> [!IMPORTANT] 
> Quelques contraintes s’appliquent pendant la période de la préversion de la fonctionnalité NAT. Certaines d’entre elles seront levées avant la disponibilité générale.
>

* NAT sur une passerelle VPN Azure prend uniquement en charge les règles NAT 1:1 statiques. Les règles NAT dynamiques ne sont pas prises en charge.
* NAT est pris en charge sur les références (SKU) suivantes : VpnGw2~5, VpnGw2AZ~5AZ.
* NAT est pris en charge pour les connexions intersites IPsec/IKE uniquement. Les connexions de réseau virtuel à réseau virtuel et les connexions P2S (point à site) ne sont pas prises en charge.
* Des règles NAT ne peuvent pas être associées à des ressources de connexion pendant le processus de création de connexion. Commencez par créer la ressource de connexion, puis associez les règles NAT dans la page Configuration de la connexion.
* Pour la préversion, utilisez **BGP**, puis laissez l’espace d’adressage de la passerelle de réseau local **vide** s’il est identique à l’espace d'adressage du réseau virtuel ou à une partie de celui-ci. Le routage statique (non-BGP) n’est **pas** pris en charge avec le conflit d’adresses entre passerelles de réseau local et réseau virtuel.
* Les espaces d’adressage pour différentes passerelles de réseau local (réseaux locaux ou branches) peuvent être identiques avec les règles *IngressSNAT* pour mapper à des préfixes ne se chevauchant pas comme illustré dans le [diagramme 1](#diagram).
* Les règles NAT ne sont pas prises en charge sur des connexions pour lesquelles l’option *Utiliser des sélecteurs de trafic basés sur des stratégies* est activée.

## <a name="next-steps"></a>Étapes suivantes

Une fois la connexion achevée, vous pouvez ajouter des machines virtuelles à vos réseaux virtuels. Consultez [Création d’une machine virtuelle](../virtual-machines/windows/quick-create-portal.md) pour connaître les différentes étapes.
