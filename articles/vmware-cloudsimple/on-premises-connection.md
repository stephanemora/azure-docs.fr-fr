---
title: Azure VMware Solutions (AVS) - Connexion locale à l'aide d'ExpressRoute
description: Explique comment demander une connexion locale à l'aide d'ExpressRoute depuis un réseau de région AVS
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 10a21faf2790b4c7a26d80e46bf44c8bffabf27f
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77019619"
---
# <a name="connect-from-on-premises-to-avs-using-expressroute"></a>Connecter un réseau local à AVS à l'aide d'ExpressRoute

Si vous disposez déjà d'une connexion Azure ExpressRoute entre un emplacement externe (comme un emplacement local) et Azure, vous pouvez la connecter à votre environnement AVS. Vous pouvez le faire via une fonctionnalité d’Azure qui permet à deux circuits ExpressRoute de se connecter entre eux. Cette méthode établit une connexion sécurisée, privée, à bande passante élevée et à faible latence entre les deux environnements.

[![Connexion ExpressRoute locale - Global Reach](media/cloudsimple-global-reach-connection.png)](media/cloudsimple-global-reach-connection.png)

## <a name="before-you-begin"></a>Avant de commencer

Un bloc d’ adresses réseau **/29** est requis pour établir une connexion Global Reach à partir d’un emplacement local. L’espace d’adressage /29 est utilisé pour le réseau de transit entre les circuits ExpressRoute. Le réseau de transit ne doit pas chevaucher vos réseaux virtuels Azure, réseaux locaux ou réseaux de cloud privé AVS.

## <a name="prerequisites"></a>Conditions préalables requises

* Un circuit Azure ExpressRoute est requis avant de pouvoir établir la connexion entre le circuit et les réseaux de cloud privé AVS.
* Un utilisateur est requis, avec les privilèges pour créer des clés d’autorisation sur un circuit ExpressRoute.

## <a name="scenarios"></a>Scénarios

La connexion de votre réseau local à votre réseau de cloud privé AVS vous permet d'utiliser le cloud privé AVS de différentes façons, notamment pour :

* Accéder au réseau de votre cloud privé AVS sans créer de connexion VPN site à site
* Utiliser votre instance locale d'Active Directory en tant que source d'identité sur votre cloud privé AVS
* Migrer des machines virtuelles exécutées localement vers votre cloud privé AVS
* Utiliser votre cloud privé AVS dans le cadre d'une solution de récupération d'urgence
* Utiliser des ressources locales sur les machines virtuelles de charge de travail de votre cloud privé AVS

## <a name="connecting-expressroute-circuits"></a>Connexion de circuits ExpressRoute

Pour établir la connexion ExpressRoute, vous devez créer une autorisation sur votre circuit ExpressRoute et fournir les informations d'autorisation à AVS.


### <a name="create-expressroute-authorization"></a>Créer l’autorisation ExpressRoute

1. Connectez-vous au portail Azure.

2. Dans la barre de recherche supérieure, recherchez **Circuit ExpressRoute** et cliquez sur **Circuits ExpressRoute** sous **Services**.
    [![Circuits ExpressRoute](media/azure-expressroute-transit-search.png)](media/azure-expressroute-transit-search.png)

3. Sélectionnez le circuit ExpressRoute que vous souhaitez connecter à votre réseau AVS.

4. Dans la page ExpressRoute, cliquez sur **Autorisations**, entrez un nom pour l’autorisation, puis cliquez sur **Enregistrer**.
    [![Autorisation du circuit ExpressRoute](media/azure-expressroute-transit-authorizations.png)](media/azure-expressroute-transit-authorizations.png)

5. Copiez l’ID de ressource et la clé d’autorisation en cliquant sur l’icône de copie. Collez l’ID et la clé dans un fichier texte.
    [![Copier une autorisation du circuit ExpressRoute](media/azure-expressroute-transit-authorization-copy.png)](media/azure-expressroute-transit-authorization-copy.png)

    > [!IMPORTANT]
    > **L’ID de ressource** doit être copié à partir de l’interface utilisateur et doit être au format ```/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/expressRouteCircuits/<express-route-circuit-name>``` lorsque vous le fournissez pour qu’il soit pris en charge.

6. Configurez un ticket avec <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">Prise en charge</a> pour la connexion à créer.
    * Type de problème : **Technique**
    * Abonnement : **Abonnement dans lequel le service AVS est déployé**
    * Service : **VMware Solutions (AVS)**
    * Type de problème : **Demande de service**
    * Sous-type de problème : **Créer une connexion ExpressRoute au réseau local**
    * Indiquez l’ID de ressource et la clé d’autorisation que vous avez copiés et enregistrés dans le volet d’informations.
    * Fournissez un espace d’adressage réseau /29 pour le réseau de transit.
    * Envoyez-vous la route par défaut par le biais d’ExpressRoute ?
    * Le trafic du cloud privé AVS doit-il utiliser l'itinéraire par défaut envoyé par le biais d'ExpressRoute ?

    > [!IMPORTANT]
    > L'envoi de l'itinéraire par défaut vous permet d'acheminer tout le trafic Internet à partir du cloud privé AVS à l'aide de votre connexion Internet locale. Pour désactiver l'itinéraire par défaut configuré sur le cloud privé AVS et utiliser l'itinéraire par défaut de la connexion locale, fournissez des informations détaillées dans le ticket de support.

## <a name="next-steps"></a>Étapes suivantes

* [Apprenez-en davantage sur les connexions réseau Azure](cloudsimple-azure-network-connection.md)  
