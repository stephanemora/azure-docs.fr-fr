---
title: Connecter Azure Front Door Premium à une origine d’application web avec Private Link
titleSuffix: Azure Private Link
description: Découvrez comment connecter votre Azure Front Door Premium à une application web de façon privée.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: d30b73d1e80273d766f4f94224798ddb04763195
ms.sourcegitcommit: ba8f0365b192f6f708eb8ce7aadb134ef8eda326
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2021
ms.locfileid: "109633247"
---
# <a name="connect-azure-front-door-premium-to-a-web-app-origin-with-private-link"></a>Connecter Azure Front Door Premium à une origine d’application web avec Private Link

Cet article vous guide tout au long de la procédure de configuration de la référence (SKU) Azure Front Door Premium pour que celle-ci se connecte à votre application web de façon privée à l’aide du service Azure Private Link.

## <a name="prerequisites"></a>Prérequis

* Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Créez un service [Private Link](../../private-link/create-private-link-service-portal.md) pour vos serveurs web d’origine.

> [!Note]
> Private Endpoint est disponible dans les régions publiques pour les applications web Windows de niveau Premium V2, Premium V3, les applications Linux et le plan Azure Functions Premium (parfois appelé plan Élastique Premium).

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au [portail Azure](https://portal.azure.com).

## <a name="enable-private-link-to-a-web-app-in-azure-front-door-premium"></a>Activer une liaison privée à une application web dans Azure Front Door Premium
 
Dans cette section, vous allez mapper le service Private Link à un point de terminaison privé créé dans le réseau privé d’Azure Front Door. 

1. Dans votre profil Azure Front Door Premium, sous *Paramètres*, sélectionnez **Groupes d’origine**.

1. Sélectionnez le groupe d’origine contenant l’origine d’application web pour laquelle vous souhaitez activer Private Link.

1. Sélectionnez **+ Ajouter une origine** pour ajouter une nouvelle origine d’application web, ou sélectionnez une origine d’application web créée précédemment dans la liste.

    :::image type="content" source="../media/how-to-enable-private-link-web-app/private-endpoint-web-app.png" alt-text="Capture d’écran de l’activation d’une liaison privée à une application web.":::

1. Pour **Sélectionner une ressource Azure**, sélectionnez **Dans mon annuaire**. Sélectionnez ou entrez les paramètres suivants afin de configurer le site auquel vous souhaitez qu’Azure Front Door Premium se connecte de façon privée.

    | Paramètre | Value |
    | ------- | ----- |
    | Région | Sélectionnez la région qui est la même que votre origine ou la plus proche de celle-ci. |
    | Type de ressource | Sélectionnez **Microsoft.Web/sites**. |
    | Ressource | Sélectionnez **myPrivateLinkService**. |
    | Sous-ressource cible | sites |
    | Message de requête | Personnalisez le message ou choisissez le message par défaut. |

1. Sélectionnez ensuite **Ajouter** pour enregistrer votre configuration.

## <a name="approve-azure-front-door-premium-private-endpoint-connection-from-web-app"></a>Approuver une connexion de point de terminaison privé Azure Front Door Premium à partir d’une application web

1. Accédez à l’application web pour laquelle vous configurez Private Link dans la dernière section. Sous **Paramètres**, sélectionnez **Mise en réseau**.

1. Dans **Réseau**, sélectionnez **Configurer vos connexions de point de terminaison privé**.

    :::image type="content" source="../media/how-to-enable-private-link-web-app/web-app-configure-endpoint.png" alt-text="Capture d’écran des paramètres de mise en réseau dans une application web.":::

1. Sélectionnez la demande de point de terminaison privé *en attente* d’Azure Front Door Premium, puis sélectionnez **Approuver**.

    :::image type="content" source="../media/how-to-enable-private-link-web-app/private-endpoint-pending-approval.png" alt-text="Capture d’écran de la demande de point de terminaison privé en attente.":::

1. Une fois la demande approuvée, elle devrait ressembler à la capture d’écran ci-dessous. L’établissement complet de la connexion peut prendre quelques minutes. Vous pouvez à présent accéder à votre application web à partir d’Azure Front Door Premium. L’accès direct à l’application web à partir de l’Internet public est désactivé une fois le point de terminaison privé activé.

    :::image type="content" source="../media/how-to-enable-private-link-web-app/private-endpoint-approved.png" alt-text="Capture d’écran d’une demande de point de terminaison approuvée.":::

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur le [Service Private Link avec une application web Azure](../../app-service/networking/private-endpoint.md).
