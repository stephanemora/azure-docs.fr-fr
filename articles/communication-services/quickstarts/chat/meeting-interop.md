---
title: Bien démarrer avec l’interopérabilité de Teams sur Azure Communication Services
titleSuffix: An Azure Communication Services quickstart
description: Dans ce guide de démarrage rapide, vous allez découvrir comment participer à une réunion Teams à l’aide du kit SDK Conversation Azure Communication Services.
author: askaur
ms.author: askaur
ms.date: 06/30/2021
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-web-ios
ms.openlocfilehash: c49f2a51dda269c8addcd32c2c82564455bc9743
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113113092"
---
# <a name="quickstart-join-your-chat-app-to-a-teams-meeting"></a>Démarrage rapide : Joindre votre application de conversation à une réunion Teams

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include-chat.md)]

> [!IMPORTANT]
> Pour activer/désactiver l’[interopérabilité des locataires Teams](../../concepts/teams-interop.md), remplissez [ce formulaire](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR21ouQM6BHtHiripswZoZsdURDQ5SUNQTElKR0VZU0VUU1hMOTBBMVhESS4u).

Démarrez avec Azure Communication Services en connectant votre solution de conversation à Microsoft Teams. 

::: zone pivot="platform-web"
[!INCLUDE [Teams interop with JavaScript SDK](./includes/meeting-interop-javascript.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [Teams interop with iOS SDK](./includes/meeting-interop-swift.md)]
::: zone-end

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous voulez nettoyer et supprimer un abonnement Communication Services, vous pouvez supprimer la ressource ou le groupe de ressources. La suppression du groupe de ressources efface également les autres ressources qui y sont associées. Apprenez-en davantage sur le [nettoyage des ressources](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les articles suivants :

- Consultez notre [exemple de bannière de conversation](../../samples/chat-hero-sample.md).
- Apprenez-en davantage sur le [fonctionnement de la conversation](../../concepts/chat/concepts.md).