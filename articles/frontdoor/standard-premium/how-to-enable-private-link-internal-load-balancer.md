---
title: Connecter Azure Front Door Premium à une origine d’équilibreur de charge avec Private Link
titleSuffix: Azure Private Link
description: Découvrez comment connecter Azure Front Door Premium à un équilibreur de charge interne.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 03/16/2021
ms.author: duau
ms.openlocfilehash: 80013ca5d4d20366672eda417862ff7bb1309b0c
ms.sourcegitcommit: ba8f0365b192f6f708eb8ce7aadb134ef8eda326
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2021
ms.locfileid: "109633156"
---
# <a name="connect-azure-front-door-premium-to-an-internal-load-balancer-origin-with-private-link"></a>Connecter Azure Front Door Premium à une origine d’équilibreur de charge avec Private Link

Cet article vous guide tout au long de la procédure de configuration de la référence SKU Azure Front Door Premium pour que celle-ci se connecte à l’origine de votre équilibreur de charge interne à l’aide du service Azure Private Link.

## <a name="prerequisites"></a>Prérequis

Créer un [service Private Link](../../private-link/create-private-link-service-portal.md)

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au [portail Azure](https://portal.azure.com).

## <a name="enable-private-link-to-an-internal-load-balancer"></a>Activer Private Link vers un équilibreur de charge interne
 
Dans cette section, vous allez mapper le service Private Link à un point de terminaison privé créé dans le réseau privé d’Azure Front Door. 

1. Dans votre profil Azure Front Door Premium, sous *Paramètres*, sélectionnez **Groupes d’origine**.

1. Sélectionnez le groupe d’origine pour lequel vous souhaitez activer Private Link sur l’équilibreur de charge interne.

1. Sélectionnez **+ Ajouter une origine** pour ajouter une origine à l’équilibreur de charge interne.

    :::image type="content" source="../media/how-to-enable-private-link-internal-load-balancer/private-endpoint-internal-load-balancer.png" alt-text="Capture d’écran de l’activation d’une liaison privée vers un équilibreur de charge interne.":::

1. Pour **Sélectionner une ressource Azure**, sélectionnez **Dans mon annuaire**. Sélectionnez ou entrez les paramètres suivants afin de configurer le site auquel vous souhaitez qu’Azure Front Door Premium se connecte de façon privée.

    | Paramètre | Value |
    | ------- | ----- |
    | Région | Sélectionnez la région qui est la même que votre origine ou la plus proche de celle-ci. |
    | Type de ressource | Sélectionnez **Microsoft.Network/privateLinkServices**. |
    | Ressource | Sélectionnez votre liaison privée associée à l’équilibreur de charge interne. |
    | Sous-ressource cible | Laisser vide. |
    | Message de requête | Personnalisez le message ou choisissez le message par défaut. |

1. Ensuite, sélectionnez **Ajouter**, puis **Mettre à jour** pour enregistrer votre configuration.

## <a name="approve-private-endpoint-connection-from-the-storage-account"></a>Approuver la connexion par point de terminaison privé pour le compte de stockage

1. Accédez au Centre Private Link et sélectionnez **Services de liaison privée**. Sélectionnez ensuite le nom de votre liaison privée.

    :::image type="content" source="../media/how-to-enable-private-link-internal-load-balancer/list.png" alt-text="Capture d’écran de la liste des liaisons privées.":::

1. Sous **Paramètres**, sélectionnez *Connexions des points de terminaison privés*.

    :::image type="content" source="../media/how-to-enable-private-link-internal-load-balancer/overview.png" alt-text="Capture d’écran de la page d’aperçu des liaisons privées.":::

1. Sélectionnez la requête de point de terminaison privé *en attente* d’Azure Front Door Premium, puis sélectionnez **Approuver**.

    :::image type="content" source="../media/how-to-enable-private-link-internal-load-balancer/private-endpoint-pending-approval.png" alt-text="Capture d’écran de l’approbation en attente pour la liaison privée.":::

1. Une fois la requête approuvée, elle devrait ressembler à la capture d’écran ci-dessous. L’établissement complet de la connexion peut prendre quelques minutes. Vous pouvez à présent accéder à votre équilibreur de charge à partir d’Azure Front Door Premium.

    :::image type="content" source="../media/how-to-enable-private-link-storage-account/private-endpoint-approved.png" alt-text="Capture d’écran de la demande de liaison privée approuvée.":::

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur le [service Private Link](../../private-link/private-link-service-overview.md).
