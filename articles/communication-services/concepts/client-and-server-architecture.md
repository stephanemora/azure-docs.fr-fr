---
title: Architecture client et serveur
titleSuffix: An Azure Communication Services concept document
description: Découvrez l’architecture de Communication Services.
author: mikben
manager: mikben
services: azure-communication-services
ms.author: mikben
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: a278a83f0a498baef991f75d4dd77a572c4c2470
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114288398"
---
# <a name="client-and-server-architecture"></a>Architecture client et serveur

Cette page illustre les composants architecturaux et flux de données typiques dans divers scénarios d’utilisation de la solution Azure Communication Services. Les composants concernés sont les suivants :

1. **Application cliente.** Les utilisateurs finaux utilisent ce site web ou cette application native pour communiquer. La solution Azure Communication Services fournit des [bibliothèques de client SDK](sdk-options.md) pour plusieurs navigateurs et plateformes d’application. En plus de nos kits de développement logiciel (SDK) principaux, une [boîte à outils d’interface utilisateur](https://aka.ms/acsstorybook) est disponible pour accélérer le développement d’applications de navigateur.
1. **Service de gestion des identités.**  Ce service vous permet de mapper des utilisateurs et d’autres concepts de votre logique métier à Azure Communication Services, ainsi que de créer des jetons pour ces utilisateurs si nécessaire.
1. **Service de gestion des appels.**  Ce service vous permet de gérer et surveiller les appels vocaux et vidéo.  Ce service peut créer des appels, inviter des utilisateurs, appeler des numéros de téléphone, lire des fichiers audio, écouter des tonalités DMTF et tirer parti de nombreuses autres fonctionnalités d’appel via le kit de développement logiciel (SDK) et les API REST d’automatisation des appels.


## <a name="user-access-management"></a>Gestion de l'accès utilisateur

Les clients Azure Communication Services doivent présenter des `user access tokens` pour accéder aux ressources de Communication Services en toute sécurité. Les `User access tokens` doivent être générés et gérés par un service approuvé en raison du caractère sensible du jeton, de la chaîne de connexion ou de l’identité managée nécessaire pour les générer. L’impossibilité de gérer correctement les jetons d’accès peut entraîner des frais supplémentaires en raison d’une mauvaise utilisation des ressources.

:::image type="content" source="../media/scenarios/architecture_v2_identity.svg" alt-text="Diagramme montrant l’architecture des jetons d’accès utilisateur.":::

### <a name="dataflows"></a>Dataflows
1. L’utilisateur démarre l’application cliente. La conception de cette application et du schéma d’authentification utilisateur sont sous votre contrôle.
2. L’application cliente contacte votre service de gestion des identités. Le service de gestion des identités gère un mappage entre vos utilisateurs et d’autres objets adressables (par exemple, des services ou des bots) et des identités Azure Communication Services.
3. Le service de gestion des identités crée un jeton d’accès utilisateur pour l’identité applicable. Si aucune identité Azure Communication Services n’a été allouée, une identité est créée.  

### <a name="resources"></a>Ressources
- **Concept :** [Identité de l’utilisateur](identity-model.md)
- **Démarrage rapide :** [Créer et gérer des jetons d’accès](../quickstarts/access-tokens.md)
- **Didacticiel :** [Créer un service d’accès des utilisateurs approuvés à l’aide d’Azure Functions](../tutorials/trusted-service-tutorial.md)

> [!IMPORTANT]
> Par souci de simplicité, nous ne montrons pas la gestion des accès utilisateur et la distribution des jetons dans les flux d’architecture suivants.


## <a name="calling-a-user-without-push-notifications"></a>Appel d’un utilisateur sans notifications Push
Les scénarios d’appel audio et vidéo les plus simples impliquent un utilisateur en appelant un autre, au premier plan sans notification Push.

:::image type="content" source="../media/scenarios/architecture_v2_calling_without_notifications.svg" alt-text="Diagramme montrant l’architecture Communication Services appelant sans notifications Push.":::

### <a name="dataflows"></a>Dataflows

1. L’utilisateur acceptant initialise le client d’appel, ce qui lui permet de recevoir des appels téléphoniques entrants.
2. L’utilisateur appelant a besoin de l’identité Azure Communication Services de la personne qu’il souhaite appeler. Une expérience classique peut avoir une *liste d’amis* gérée par le service de gestion des identités qui rassemble les amis de l’utilisateur et les identités Azure Communication Services associées.
3. L’utilisateur appelant initialise son client d’appel et appelle l’utilisateur distant.
4. L’utilisateur acceptant est informé de l’appel entrant par le biais du kit de développement logiciel (SDK) Appel.
5. Les utilisateurs communiquent dans un appel à la fois vocal et vidéo.

### <a name="resources"></a>Ressources
- **Concept:** [Vue d’ensemble de l’appel](voice-video-calling/calling-sdk-features.md)
- **Démarrage rapide :** [Ajouter l’appel vocal à votre application](../quickstarts/voice-video-calling/getting-started-with-calling.md)
- **Démarrage rapide :** [Ajouter l’appel vidéo à votre application](../quickstarts/voice-video-calling/get-started-with-video-calling.md)
- **Exemple de bannière :** [Appel de groupe pour Web, iOS et Android](../samples/calling-hero-sample.md)


## <a name="joining-a-user-created-group-call"></a>Participation à un appel de groupe créé par un utilisateur
Vous souhaiterez peut-être que des utilisateurs participent à un appel sans invitation explicite. Par exemple, vous pouvez avoir un *espace social* avec un appel associé auquel les utilisateurs participent quand il en ont le loisir. Dans ce premier flux de données, nous montrons un appel initialement créé par un client.

:::image type="content" source="../media/scenarios/architecture_v2_calling_join_client_driven.svg" alt-text="Diagramme montrant l’architecture Communication Services appelant des signaux hors bande.":::

### <a name="dataflows"></a>Dataflows
1. L’utilisateur appelant initialise son client d’appel et passe un appel de groupe.
2. L’utilisateur appelant partage l’ID d’appel de groupe avec un service de gestion des appels.
3. Le service de gestion des appels partage l’ID d’appel avec d’autres utilisateurs. Par exemple, si l’application est axée sur des événements planifiés, l’ID d’appel de groupe peut être un attribut du modèle de données de l’événement planifié.
4. Les autres utilisateurs participent à l’appel en utilisant l’ID d’appel de groupe.
5. Les utilisateurs communiquent dans un appel à la fois vocal et vidéo.


## <a name="joining-a-scheduled-teams-call"></a>Participation à un appel Teams planifié
Les applications Azure Communication Services peuvent rejoindre des appels Teams. Cela est idéal pour de nombreux scénarios entreprise-consommateur (B2C), où le consommateur utilise une application et une identité personnalisées, tandis que le côté professionnel utilise Teams.

:::image type="content" source="../media/scenarios/architecture_v2_calling_join_teams_driven.svg" alt-text="Diagramme montrant l’architecture Communication Services pour parciciper à une réunion Teams.":::


### <a name="dataflows"></a>Dataflows
1. Le service de gestion des appels crée un appel de groupe avec des [API Graph](/graph/api/resources/onlinemeeting?view=graph-rest-1.0). Un autre modèle des utilisateurs finaux créant l’appel de groupe à l’aide de [Bookings](https://www.microsoft.com/microsoft-365/business/scheduling-and-booking-app), d’Outlook, de Teams ou d’une autre expérience de planification de l’écosystème Microsoft 365.
2. Le service de gestion des appels partage les détails de l’appel Teams avec des clients Azure Communication Services.
3. En règle générale, un utilisateur de Teams doit rejoindre l’appel et autoriser des utilisateurs externes à participer via la salle d’attente. Toutefois, cette expérience est sensible à la configuration de locataire Teams et à des paramètres de réunion spécifiques.
4. Les utilisateurs d’Azure Communication Services initialisent leur client d’appel et rejoignent la réunion Teams en utilisant les détails reçus à l’étape 2.
5. Les utilisateurs communiquent dans un appel à la fois vocal et vidéo.

### <a name="resources"></a>Ressources
- **Concept :** [Interopérabilité de Teams](teams-interop.md)
- **Démarrage rapide** [Participer à une réunion Teams](../quickstarts/voice-video-calling/get-started-teams-interop.md)