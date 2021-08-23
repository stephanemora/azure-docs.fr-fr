---
title: Créer un point de terminaison personnalisé Teams
titleSuffix: An Azure Communication Services concept document
description: Cet article explique comment créer un point de terminaison de Teams personnalisé.
author: tomaschladek
manager: nmurav
services: azure-communication-services
ms.author: tchladek
ms.date: 06/30/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 3b0c1a7bbd2069ce828c0db4b80f047c1cc9faf7
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/27/2021
ms.locfileid: "114709196"
---
# <a name="build-a-custom-teams-endpoint"></a>Créer un point de terminaison personnalisé Teams

> [!IMPORTANT]
> Pour activer ou désactiver l’expérience de point de terminaison personnalisé Teams, [complétez et envoyez ce formulaire](https://forms.office.com/r/B8p5KqCH19).

Azure Communication Services permet de créer des points de terminaison personnalisés Teams afin de communiquer avec le client Microsoft Teams ou d’autres points de terminaison personnalisés Teams. Avec le point de terminaison personnalisé Teams, vous pouvez personnaliser la voix, la vidéo, la conversation et l’expérience de partage d’écran pour les utilisateurs Teams.

Vous pouvez utiliser le kit de développement logiciel (SDK) Azure Communication Services Identity pour échanger des jetons utilisateur Azure Active Directory (Azure ASD) contre des jetons d’accès Teams. Les diagrammes des sections suivantes illustrent les cas d’utilisation multi-locataire, où la société fictive Fabrikam est le client de la société fictive Contoso.

## <a name="calling"></a>Appel 

Les fonctionnalités de voix, vidéo et partage d’écran sont fournies via les kits de développement logiciel (SDK) Azure Communication Services Calling. Le diagramme suivant présente une vue d’ensemble du processus à suivre lorsque vous intégrez vos expériences d’appel à des points de terminaison personnalisés Teams.

![Diagramme du processus d’activation de la fonctionnalité d’appel pour une expérience de point de terminaison de Teams personnalisé.](./media/teams-identities/teams-identity-calling-overview.png)

## <a name="chat"></a>Conversation

Vous pouvez également utiliser des points de terminaison personnalisés Teams pour intégrer des fonctionnalités de conversation à l’aide d’API Graph. Pour plus d’informations sur l’API Graph, consultez la documentation sur le [type de ressource de conversation](/graph/api/channel-post-messages). 

![Diagramme du processus d’activation de la fonctionnalité de conversation pour une expérience de point de terminaison de Teams personnalisé.](./media/teams-identities/teams-identity-chat-overview.png)

## <a name="azure-communication-services-permissions"></a>Autorisations Azure Communication Services

### <a name="delegated-permissions"></a>Autorisations déléguées

|   Autorisation    |  Chaîne d'affichage   |  Description | Consentement de l'administrateur requis | Compte Microsoft pris en charge |
|:--- |:--- |:--- |:--- |:--- |
| _`https://auth.msft.communication.azure.com/VoIP`_ | Gérer les appels dans Teams | Démarrez, joignez, transférez ou quittez des appels Teams et mettez à jour les propriétés d’appel. | Non | Non |

### <a name="application-permissions"></a>Autorisations de l’application

Aucune.

### <a name="roles-for-granting-consent-on-behalf-of-a-company"></a>Rôles pour l’octroi du consentement pour le compte d’une société

- Administrateur général
- Administrateur d’application (uniquement en version préliminaire privée)
- Administrateur d’application cloud (uniquement en version préliminaire privée)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Émettre un jeton d’accès Teams](../quickstarts/manage-teams-identity.md)

En savoir plus sur [l’interopérabilité Teams](./teams-interop.md).
