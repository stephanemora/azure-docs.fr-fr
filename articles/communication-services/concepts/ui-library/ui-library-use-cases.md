---
title: Cas d’usage de la bibliothèque d’interface utilisateur
titleSuffix: An Azure Communication Services concept document
description: Découvrir la bibliothèque d’interface utilisateur et comment elle peut vous aider à créer des expériences de communication
author: ddematheu2
manager: chrispalm
services: azure-communication-services
ms.author: dademath
ms.date: 05/11/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 143cdce2cd177c73fb4da76dca3be71f2730bbd9
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110468754"
---
# <a name="ui-library-use-cases"></a>Cas d’usage de la bibliothèque d’interface utilisateur

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

> [!NOTE]
> Pour obtenir une documentation détaillée sur la bibliothèque d’interface utilisateur, consultez le [Storybook de la bibliothèque d’interface utilisateur](https://azure.github.io/communication-ui-library). Vous y trouverez de la documentation conceptuelle, des guides de démarrage rapide et des exemples supplémentaires.


La bibliothèque d’interface utilisateur prend en charge de nombreux cas pour les expériences d’appel et de conversation.
Ces fonctionnalités sont disponibles par le biais des composants IU et des composites.
Dans le cas des composites, ces fonctionnalités sont générées et exposées directement quand le composite est intégré à une application.
Pour les composants IU, ces fonctionnalités sont exposées par le biais d’une combinaison de fonctionnalités d’interface utilisateur et de bibliothèques avec état sous-jacentes.
Pour tirer pleinement parti de ces fonctionnalités, nous vous recommandons d’utiliser les composants IU avec les bibliothèques de client d’appel et de conversation avec état.

## <a name="calling-use-cases"></a>Cas d’usage d’appel

| Domaine                | Cas d’usage                                              |
| ------------------- | ------------------------------------------------------ |
| Call Types          | Participer à une réunion Teams                                     |
|                     | Participer à un appel Azure Communication Services avec un ID de groupe   |
| [Interopérabilité avec Teams](../teams-interop.md)      | Salle d’attente                                             |
|                     | Bannière d’alerte de transcription et enregistrement               |
| Contrôles d’appel       | Désactiver/réactiver le son lors d’un appel                                       |
|                     | Activer/désactiver la vidéo lors d’un appel                                   |
|                     | Partage d’écran                                         |
|                     | Terminer l’appel                                               |
| Galerie des participants | Les participants distants sont affichés sur la grille              |
|                     | Aperçu vidéo disponible tout au long de l’appel pour l’utilisateur local |
|                     | Avatars par défaut disponibles quand la vidéo est désactivée            |
|                     | Contenu de l’écran partagé affiché dans la galerie des participants |
| Configuration de l’appel  | Gestion des périphériques du microphone                           |
|                     | Gestion des périphériques de la caméra                               |
|                     | Gestion des périphériques des haut-parleurs                              |
|                     | Aperçu local disponible pour que l’utilisateur puisse vérifier la vidéo        |
| Participants        | Liste des participants                                     |

## <a name="chat-use-cases"></a>Cas d’usage de conversation

| Domaine         | Cas d’usage                                        |
| ------------ | ------------------------------------------------ |
| Types de conversations   | Participer à une conversation de réunion Teams                        |
|              | Participer à un thread de conversation Azure Communication Services |
| Actions de conversation | Envoyer un message de conversation                                |
|              | Recevoir un message de conversation                             |
| Événements de conversation  | Indicateurs de saisie                                |
|              | Accusé de lecture                                     |
|              | Participant ajouté/supprimé                        |
|              | Titre de la conversation modifié                               |
| Participants | Liste des participants                               |

## <a name="supported-identities"></a>Identités prises en charge

Une identité Azure Communication Services est nécessaire pour initialiser les bibliothèques de client avec état et s’authentifier auprès du service.
Pour plus d’informations sur l’authentification, consultez [Authentification](../authentication.md) et [Jetons d’accès](../../quickstarts/access-tokens.md?pivots=programming-language-javascript)

## <a name="teams-interop-use-case"></a>Cas d’usage de l’interopérabilité avec Teams

Pour les scénarios [d’interopérabilité avec Teams](../teams-interop.md), les développeurs peuvent utiliser des composants de bibliothèque d’interface utilisateur pour accéder aux réunions Teams via Azure Communication Services.
Pour activer l’interopérabilité avec Teams, les développeurs peuvent soit utiliser des composites d’appel et de conversation directement, soit utiliser des composants IU pour créer une expérience personnalisée.
Quand vous activez les appels et les conversations sur des applications, n’oubliez pas que le client de conversation ne peut pas être initialisé tant que le participant n’a pas été admis dans l’appel.
Une fois qu’il est admis, le client de conversation peut être initialisé pour rejoindre le fil de conversation de la réunion.
Consultez le diagramme ci-dessous pour obtenir de l’aide :

:::image type="content" source="../media/teams-interop-pattern.png" alt-text="Modèle d’interopérabilité avec Teams pour les appels et les conversations":::

Quand vous utilisez des composants IU pour fournir des expériences d’interopérabilité avec Teams, la bibliothèque d’interface utilisateur fournit des exemples pour des éléments clés de l’expérience.
Par exemple :
- [Exemple de salle d’attente](https://azure.github.io/communication-ui-library/?path=/story/examples-teams-interop--lobby) : exemple de salle d’attente permettant à l’utilisateur de patienter jusqu’à ce qu’il soit admis.
- [Bannière de conformité](https://azure.github.io/communication-ui-library/?path=/story/examples-teams-interop--compliance-banner) : exemple de bannière qui indique à l’utilisateur si l’appel est en cours d’enregistrement ou non.
- [Thème Teams](https://azure.github.io/communication-ui-library/?path=/story/examples-themes--teams) : exemple de thème pour que la bibliothèque d’interface utilisateur ressemble à Microsoft Teams.


## <a name="customization"></a>Personnalisation

La bibliothèque d’interface utilisateur expose des modèles permettant aux développeurs de modifier les composants afin qu’ils correspondent à l’apparence de leur application.
Ces fonctionnalités sont un domaine clé de différenciation entre les composites et les composants IU. Les composites fournissent moins d’options de personnalisation pour favoriser une expérience d’intégration plus simple.

| Cas d’usage                                            | Composites | Composants IU |
| --------------------------------------------------- | ---------- | ------------- |
| Thèmes basés sur Fluent                                | X          | X             |
| La disposition de l’expérience est composable                     |            | X             |
| Les styles CSS peuvent être utilisés pour modifier les propriétés de style  |            | X             |
| Les icônes peuvent être remplacées                               |            | X             |
| La disposition de la galerie de participants peut être modifiée          |            | X             |
| La disposition du contrôle d’appel peut être modifiée                 | X          | X             |
| Des modèles de données peuvent être injectés pour modifier les métadonnées de l’utilisateur | X          | X             |

## <a name="observability"></a>Observabilité

Dans le cadre de l’architecture de gestion des états découplée de la bibliothèque d’interface utilisateur, les développeurs peuvent accéder directement aux clients de conversation et aux appels avec état.

Les développeurs peuvent se connecter au client avec état pour lire l’état, gérer les événements et remplacer le comportement à transmettre aux composants IU.

| Cas d’usage                                  | Composites | Composants IU |
| ----------------------------------------- | ---------- | ------------- |
| Il est possible d’avoir accès à l’état du client d’appel/de conversation    | X          | X             |
| Il est possible d’avoir accès aux événements clients et de les gérer | X          | X             |
| Il est possible d’avoir accès aux événements IU et de les gérer     | X          | X             |

## <a name="recommended-architecture"></a>Architecture recommandée

:::image type="content" source="../media/ui-library-architecture.png" alt-text="Architecture client-serveur recommandée pour la bibliothèque d’interface utilisateur":::

Les composants composites et de base sont initialisés à l’aide d’un jeton d’accès Azure Communication Services. Les jetons d’accès doivent être obtenus auprès d’Azure Communication Services via un service approuvé que vous gérez. Consultez [Démarrage rapide : Créer des jetons d’accès](../../quickstarts/access-tokens.md?pivots=programming-language-javascript) et [Tutoriel Service approuvé](../../tutorials/trusted-service-tutorial.md) pour plus d’informations.

Ces bibliothèques de client ont également besoin du contexte de l’appel ou de la conversation auxquels elles se joignent. À l’instar des jetons d’accès utilisateur, ce contexte doit être distribué aux clients par l’intermédiaire de votre propre service approuvé. La liste ci-dessous résume les fonctions d’initialisation et de gestion des ressources que vous devez rendre opérationnelles.

| Responsabilités de Contoso                                 | Responsabilités de la bibliothèque d’interface utilisateur                                     |
| -------------------------------------------------------- | --------------------------------------------------------------- |
| Fournir un jeton d’accès d’Azure                          | Transférer le jeton d’accès fourni pour initialiser les composants        |
| Fournir une fonction d’actualisation                                 | Actualiser le jeton d’accès à l’aide de la fonction fournie par les développeurs          |
| Récupérer/transmettre les informations permettant de se joindre à l’appel ou à la conversation          | Transmettre les informations d’appel et de conversation pour initialiser les composants |
| Récupérer/transmettre des informations utilisateur pour un modèle de données personnalisé | Transmettre le modèle de données personnalisé aux composants à afficher          |

## <a name="platform-support"></a>Plateforme prise en charge

| Kit SDK    | Windows            | macOS                | Ubuntu   | Linux    | Android  | iOS        |
| ------ | ------------------ | -------------------- | -------- | -------- | -------- | ---------- |
| SDK IU | Chrome\*, nouveau Edge | Chrome\*, Safari\*\* | Chrome\* | Chrome\* | Chrome\* | Safari\*\* |

\*Notez que la dernière version de Chrome est prise en charge en plus des deux versions précédentes.

\*\*Notez que les versions 13.1+ de Safari sont prises en charge. La vidéo sortante pour Safari macOS n’est pas encore prise en charge, mais elle est prise en charge sur iOS. Le partage d’écran sortant est pris en charge uniquement sur iOS pour ordinateur de bureau.

## <a name="accessibility"></a>Accessibilité

L’accessibilité dès la conception est un principe pour l’ensemble des produits Microsoft.
La bibliothèque d’interface utilisateur respecte ce principe pour garantir que tous les composants IU soient entièrement accessibles.
Pendant la préversion publique, la bibliothèque d’interface utilisateur continue de s’améliorer et d’ajouter des fonctionnalités d’accessibilité aux composants IU.
Nous prévoyons d’ajouter plus de détails concernant l’accessibilité d’ici la mise en disponibilité générale de la bibliothèque d’interface utilisateur.

## <a name="localization"></a>Localisation

La localisation est un élément clé pour offrir des produits pouvant être utilisés dans le monde entier par des personnes qui parlent différentes langues.
La bibliothèque IU fournit une prise en charge prête à l’emploi pour certaines langues et fonctionnalités comme DàG.
Les développeurs peuvent fournir leurs propres fichiers de localisation à utiliser pour la bibliothèque IU.
Ces fonctionnalités de localisation seront ajoutées d’ici la disponibilité générale.

> [!div class="nextstepaction"]
> [Consultez le Storybook de la bibliothèque d’interface utilisateur](https://azure.github.io/communication-ui-library)
