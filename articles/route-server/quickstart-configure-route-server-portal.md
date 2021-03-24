---
title: 'Démarrage rapide : Créer et configurer Route Server en utilisant le portail Azure'
description: Dans ce guide de démarrage rapide, vous apprenez à créer et configurer un serveur de routage en utilisant le portail Azure.
services: route-server
author: duongau
ms.service: route-server
ms.topic: quickstart
ms.date: 03/03/2021
ms.author: duau
ms.openlocfilehash: f76c48af4f5ebc8013daad457f9973cf7792c7c6
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102547988"
---
# <a name="quickstart-create-and-configure-route-server-using-the-azure-portal"></a>Démarrage rapide : Créer et configurer Route Server en utilisant le portail Azure

Cet article vous aide à configurer Azure Route Server pour effectuer un appairage avec une appliance virtuelle réseau (NVA) dans votre réseau virtuel en utilisant le portail Azure. Azure Route Server va apprendre les routes provenant de l’appliance virtuelle réseau et il les programmera sur les machines virtuelles du réseau virtuel. Azure Route Server publiera également les routes du réseau virtuel menant à l’appliance virtuelle réseau. Pour plus d’informations, consultez [Azure Route Server](overview.md).

> [!IMPORTANT]
> Azure Route Server (préversion) est en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prérequis

* Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Vérifiez les [limites du service pour Azure Route Server](route-server-faq.md#limitations).

## <a name="create-a-route-server"></a>Créer un serveur de routage

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Vous connecter à votre compte Azure et sélectionner votre abonnement.

Dans un navigateur, accédez au [portail Azure](https://portal.azure.com) et connectez-vous avec votre compte Azure.

### <a name="create-a-route-server"></a>Créer un serveur de routage

1. Atteindre https://aka.ms/routeserver.

1. Sélectionnez **+ Créer un serveur de routage**.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/route-server-landing-page.png" alt-text="Capture d’écran de la page d’accueil de Route Server."::: 

1. Dans la page **Créer un serveur de routage** , entrez ou sélectionnez les informations nécessaires.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/create-route-server-page.png" alt-text="Capture d’écran de la page Créer un serveur de routage.":::     

    | Paramètres | Valeur |
    |----------|-------|
    | Abonnement | Sélectionnez l’abonnement Azure que vous voulez utiliser pour déployer le serveur de routage. |
    | Groupe de ressources | Sélectionnez un groupe de ressources où créer le serveur de routage. Si vous n’avez pas de groupe de ressources, vous pouvez en créer un. |
    | Nom | Entrez un nom pour le serveur de routage. |
    | Région | Sélectionnez la région où créer le serveur de routage. Sélectionnez la même région que le réseau virtuel que vous avez créé précédemment pour voir le réseau virtuel dans la liste déroulante. |
    | Réseau virtuel | Sélectionnez le réseau virtuel où le serveur de routage sera créé. Vous pouvez créer un réseau virtuel ou en utiliser un qui existe déjà. Si vous utilisez un réseau virtuel existant, vérifiez que le réseau virtuel existant dispose d’un espace suffisant pour au moins un sous-réseau /27 de façon à répondre aux spécifications de sous-réseau de serveur de routage. Si vous ne voyez pas votre réseau virtuel dans la liste déroulante, vérifiez que vous avez sélectionné la région ou le groupe de ressources approprié. |
    | Sous-réseau | Une fois que vous avez créé ou sélectionné un réseau virtuel, le champ Sous-réseau apparaît. Ce sous-réseau est un sous-réseau dédié exclusivement à Route Server. Sélectionnez **Gérer la configuration du sous-réseau**, puis créez le sous-réseau Azure Route Server. Sélectionnez **+ Sous-réseau**, puis créez un sous-réseau en utilisant les instructions suivantes :</br><br>- Le sous-réseau doit être nommé *RouteServerSubnet*.</br><br>- Le sous-réseau doit avoir une dimension minimale de /27 ou plus.</br> |

1. Sélectionnez **Vérifier + Créer**, vérifiez le récapitulatif, puis sélectionnez **Créer**. 

    > [!NOTE]
    > Le déploiement du serveur de routage va prendre environ 20 minutes.

## <a name="set-up-peering-with-nva"></a>Configurer l’appairage avec l’appliance virtuelle réseau

La section va vous aider à configurer l’appairage de BGP avec votre appliance virtuelle réseau.

1. Accédez à [Route Server](https://aka.ms/routeserver) dans le portail Azure et sélectionnez le serveur de routage à configurer.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/select-route-server.png" alt-text="Capture d’écran de la liste des serveurs de routage."::: 

1. Dans le volet de navigation gauche, sélectionnez **Pairs** sous *Paramètres*. Sélectionnez ensuite **+ Ajouter** pour ajouter un nouveau pair.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/peers-landing-page.png" alt-text="Capture d’écran de la page d’accueil des pairs."::: 

1. Entrez les informations suivantes sur le pair de votre appliance virtuelle réseau.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/add-peer-page.png" alt-text="Capture d’écran de la page Ajouter un pair.":::

    | Paramètres | Valeur |
    |----------|-------|
    | Nom | Donnez un nom à l’appairage entre votre serveur de routage et l’appliance virtuelle réseau. |
    | ASN |  Entrez le numéro ASN (Autonomous Systems Number) de votre appliance virtuelle réseau. |
    | Adresse IPv4 | Entrez l’adresse IP de l’appliance virtuelle réseau avec laquelle le serveur de routage va communiquer pour établir le protocole BGP. |

1. Sélectionnez **Ajouter** pour ajouter ce pair.

## <a name="complete-the-configuration-on-the-nva"></a>Terminer la configuration sur l’appliance virtuelle réseau

Vous aurez besoin des adresses IP et du numéro ASN du pair d’Azure Route Server pour terminer la configuration de votre appliance virtuelle réseau afin d’établir une session BGP. Vous pouvez obtenir ces informations à partir de la page Vue d’ensemble de votre serveur de routage.

:::image type="content" source="./media/quickstart-configure-route-server-portal/route-server-overview.png" alt-text="Capture d’écran de la page Vue d’ensemble du serveur de routage.":::

## <a name="configure-route-exchange"></a>Configurer l’échange de routage

Si vous disposez d’une passerelle ExpressRoute et/ou d’une passerelle VPN, et que vous voulez qu’elles échangent des routes avec le serveur de routage, vous pouvez activer l’échange de routes.

1. Accédez à [Route Server](https://aka.ms/routeserver) dans le portail Azure et sélectionnez le serveur de routage à configurer.

1. Dans le volet de navigation de gauche, sélectionnez **Configuration** sous *Paramètres*.

1. Sélectionnez **Activer** pour le paramètre **Branche à branche**, puis sélectionnez **Enregistrer**.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/enable-route-exchange.png" alt-text="Capture d’écran montrant comment activer l’échange de routes.":::

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous n’avez plus besoin du serveur de routage Azure, sélectionnez **Supprimer** dans la page Vue d’ensemble pour déprovisionner le serveur de routage.

:::image type="content" source="./media/quickstart-configure-route-server-portal/delete-route-server.png" alt-text="Capture d’écran montrant comment supprimer le serveur de routage.":::

## <a name="next-steps"></a>Étapes suivantes

Après avoir créé le serveur de routage Azure, découvrez à présent de quelle façon Azure Route Server interagit avec les passerelles VPN et ExpressRoute : 

> [!div class="nextstepaction"]
> [Prise en charge d’Azure ExpressRoute et de VPN Azure](expressroute-vpn-support.md)
