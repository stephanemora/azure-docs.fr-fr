---
title: Connecter Azure Front Door Premium à une origine de compte de stockage avec Private Link
titleSuffix: Azure Private Link
description: Découvrez comment connecter votre Azure Front Door Premium à un compte de stockage de façon privée.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 03/04/2021
ms.author: tyao
ms.openlocfilehash: 885b4d132208ab6f8b470d147438e26a5fd4bab7
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102201666"
---
# <a name="connect-azure-front-door-premium-to-a-storage-account-origin-with-private-link"></a>Connecter Azure Front Door Premium à une origine de compte de stockage avec Private Link

Cet article vous guide tout au long de la procédure de configuration de la référence SKU Azure Front Door Premium pour que celle-ci se connecte à votre compte de stockage de façon privée à l’aide du service Azure Private Link.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au [portail Azure](https://portal.azure.com).

## <a name="enable-private-link-to-a-storage-account"></a>Activer Private Link sur un compte de stockage
 
Dans cette section, vous allez mapper le service Private Link à un point de terminaison privé créé dans le réseau privé d’Azure Front Door. 

1. Dans votre profil Azure Front Door Premium, sous *Paramètres*, sélectionnez **Groupes d’origine**.

1. Sélectionnez le groupe d’origine contenant le compte de stockage pour lequel vous souhaitez activer Private Link.

1. Sélectionnez **+ Ajouter une origine** pour ajouter une nouvelle origine de compte de stockage ou sélectionnez une origine de compte de stockage précédemment créée dans la liste.

    :::image type="content" source="../media/how-to-enable-private-link-storage-account/private-endpoint-storage-account.png" alt-text="Capture d’écran de l’activation d’une liaison privée à un compte de stockage.":::

1. Pour **Sélectionner une ressource Azure**, sélectionnez **Dans mon annuaire**. Sélectionnez ou entrez les paramètres suivants afin de configurer le site auquel vous souhaitez qu’Azure Front Door Premium se connecte de façon privée.

    | Paramètre | Value |
    | ------- | ----- |
    | Région | Sélectionnez la région qui est la même que votre origine ou la plus proche de celle-ci. |
    | Type de ressource | Sélectionnez **Microsoft.Storage/storageAccounts**. |
    | Ressource | Sélectionnez votre compte de stockage. |
    | Sous-ressource cible | Vous pouvez sélectionner *BLOB* ou *Web*. |
    | Message de requête | Personnalisez le message ou choisissez le message par défaut. |

1. Sélectionnez ensuite **Ajouter** pour enregistrer votre configuration.

## <a name="approve-private-endpoint-connection-from-the-storage-account"></a>Approuver la connexion par point de terminaison privé pour le compte de stockage

1. Accédez au compte de stockage pour lequel vous configurez Private Link dans la dernière section. Sous **Paramètres**, sélectionnez **Mise en réseau**.

1. Dans **Mise en réseau**, sélectionnez **Connexions de points de terminaison privés**. 

    :::image type="content" source="../media/how-to-enable-private-link-storage-account/storage-account-configure-endpoint.png" alt-text="Capture d’écran des paramètres de mise en réseau dans une application web.":::

1. Sélectionnez la requête de point de terminaison privé *en attente* d’Azure Front Door Premium, puis sélectionnez **Approuver**.

    :::image type="content" source="../media/how-to-enable-private-link-storage-account/private-endpoint-pending-approval.png" alt-text="Capture d’écran de la requête de point de terminaison privé de stockage en attente.":::

1. Une fois la requête approuvée, elle devrait ressembler à la capture d’écran ci-dessous. L’établissement complet de la connexion peut prendre quelques minutes. Vous pouvez à présent accéder à votre compte de stockage à partir d’Azure Front Door Premium.

    :::image type="content" source="../media/how-to-enable-private-link-storage-account/private-endpoint-approved.png" alt-text="Capture d’écran d’une requête de point de terminaison de stockage approuvée.":::

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur le [service Private Link avec un compte de stockage](../../storage/common/storage-private-endpoints.md).
