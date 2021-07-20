---
title: Point de terminaison personnalisé Teams
titleSuffix: An Azure Communication Services concept document
description: Création d’un point de terminaison personnalisé Teams
author: tomaschladek
manager: nmurav
services: azure-communication-services
ms.author: tchladek
ms.date: 06/30/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: acbd6a332d74a9b244f34230ac4b4eb591a1ab2d
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113108060"
---
# <a name="custom-teams-endpoint"></a>Point de terminaison personnalisé Teams

> [!IMPORTANT]
> Pour activer/désactiver l’expérience de point de terminaison personnalisé Teams, complétez [ce formulaire](https://forms.office.com/r/B8p5KqCH19).

Azure Communication Services permet de créer des points de terminaison personnalisés Teams afin de communiquer avec le client Microsoft Teams ou d’autres points de terminaison personnalisés Teams. Avec le point de terminaison personnalisé Teams, vous pouvez personnaliser la voix, la vidéo, la conversation et l’expérience de partage d’écran pour les utilisateurs Teams.

Vous pouvez utiliser le kit de développement logiciel (SDK) Azure Communication Services Identity pour échanger des jetons utilisateur AAD contre des jetons d’accès Teams. Dans les diagrammes suivants, cela est illustré par le cas d’usage mutualisé, où Fabrikam est client de la société Contoso.

## <a name="calling"></a>Appel 

Les fonctionnalités de voix, vidéo et partage d’écran sont fournies via les kits de développement logiciel (SDK) Azure Communication Services Calling. Le diagramme suivant présente une vue d’ensemble du processus à suivre lorsque vous intégrez vos expériences d’appel à des points de terminaison personnalisés Teams.

![Processus d’activation de la fonctionnalité d’appel pour une expérience de point de terminaison personnalisé Teams](./media/teams-identities/teams-identity-calling-overview.png)

## <a name="chat"></a>Conversation

Vous pouvez également utiliser des points de terminaison personnalisés Teams pour intégrer des fonctionnalités de conversation à l’aide d’API Graph. Apprenez-en davantage sur l’API Graph dans [cette documentation](https://docs.microsoft.com/graph/api/channel-post-messages). 


![Processus d’activation de la fonctionnalité de conversation une expérience de point de terminaison personnalisé Teams](./media/teams-identities/teams-identity-chat-overview.png)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Émettre un jeton d’accès Teams](../quickstarts/manage-teams-identity.md)

Les documents suivants peuvent vous intéresser :

- En savoir plus sur [l’interopérabilité Teams](./teams-interop.md)
