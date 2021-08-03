---
title: Vue d’ensemble de la bibliothèque d’interface utilisateur
titleSuffix: An Azure Communication Services concept document
description: Découvrez la bibliothèque d’interface utilisateur Azure Communication Services.
author: ddematheu2
manager: chrispalm
services: azure-communication-services
ms.author: dademath
ms.date: 05/11/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 4176bdf61cafdf50aef71e76b832a0e669f1535c
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110468801"
---
# <a name="ui-library-overview"></a>Vue d’ensemble de la bibliothèque d’interface utilisateur

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

> [!NOTE]
> Pour obtenir une documentation détaillée sur la bibliothèque d’interface utilisateur, consultez le [Storybook de la bibliothèque d’interface utilisateur](https://azure.github.io/communication-ui-library). Vous y trouverez de la documentation conceptuelle, des guides de démarrage rapide et des exemples supplémentaires.

La bibliothèque d’interface utilisateur Azure Communication Services vous permet de créer facilement des expériences utilisateur de communications modernes à l’aide d’Azure Communication Services. Elle vous donne accès à une bibliothèque de composants d’interface utilisateur prêts pour la production que vous pouvez placer dans vos applications :

- **Composites** : ces composants sont des solutions clés en main qui implémentent des scénarios de communication courants.
  Vous pouvez ajouter rapidement des expériences d’appel vidéo ou de conversation à vos applications.
  Les composants composites sont des composants d’ordre supérieur open source générés à l’aide de composants IU (interface utilisateur).

- **Composants IU** : ces composants sont open source et vous permettent de créer une expérience de communication personnalisée.
  Il existe des composants pour les appels et la conversation et peuvent être combinés pour créer des expériences.

Ces bibliothèques de client d’interface utilisateur utilisent toutes les ressources et le [langage de conception Fluent de Microsoft](https://developer.microsoft.com/fluentui/). Fluent UI fournit une couche de base pour la bibliothèque d’interface utilisateur et est utilisé activement sur les produits Microsoft.

Conjointement aux composants IU, la bibliothèque d’interface utilisateur expose une bibliothèque de client avec état pour les appels et les conversations.
Ce client est indépendant de toute infrastructure de gestion d’état spécifique et peut être intégré à des gestionnaires d’état courants comme Redux ou React Context.
Cette bibliothèque de client avec état peut être utilisée avec les composants IU pour transmettre des propriétés et des méthodes afin que les composants IU restituent les données. Pour plus d’informations, consultez [Vue d’ensemble du client avec état](https://azure.github.io/communication-ui-library/?path=/story/stateful-client-what-is-stateful--page).

## <a name="installing-ui-library"></a>Installation de la bibliothèque d’interface utilisateur

Les clients avec état font partie du package `@azure/communication-react`. 

```bash
npm i --save @azure/communication-react
```

## <a name="composites-overview"></a>Vue d’ensemble des composites

Les composites sont des composants de niveau supérieur composés de composants IU qui fournissent des solutions clés en main pour les scénarios de communication courants à l’aide d’Azure Communication Services.
Les développeurs peuvent facilement instancier le composite à l’aide d’un jeton d’accès d’Azure Communication Services et de la configuration requise attribuée à l’appel ou la conversation.

| Composite    | Cas d’usage  | 
| ------------ | ---------- |
| [CallComposite](https://azure.github.io/communication-ui-library/?path=/docs/composites-callcomposite--basic-example) | Appel de l’expérience qui permet aux utilisateurs de démarrer ou de rejoindre un appel. Dans l’expérience, les utilisateurs peuvent configurer leurs appareils, participer à l’appel avec la vidéo et voir les autres participants, notamment ceux qui ont activé la vidéo. Pour l’interopérabilité avec Teams, la fonctionnalité de salle d’attente est comprise, permettant à l’utilisateur de patienter jusqu’à ce qu’il soit admis. |
| [ChatComposite](https://azure.github.io/communication-ui-library/?path=/docs/composites-chatcomposite--basic-example)    | Expérience de conversation dans laquelle l’utilisateur peut envoyer et recevoir des messages. Les événements de fil de conversation, comme la saisie, les lectures, les participants qui entrent dans la conversation ou la quittent, sont affichés à l’utilisateur dans le cadre du fil de conversation.                                                                                                                          |

## <a name="ui-component-overview"></a>Vue d’ensemble des composants IU

Il s’agit de composants IU purs qui peuvent être utilisés par les développeurs pour composer des expériences de communication, comme assembler des vignettes vidéo dans une grille pour présenter les participants distants ou organiser les composants en fonction des spécifications de vos applications.
Les composants IU prennent en charge la personnalisation pour donner aux composants l’apparence correspondant au style et à la personnalisation d’une application.

| Domaine    | Composant    | Description       |
| ------- | ------------ | ----------------- |
| Appel | [Disposition de grille](https://azure.github.io/communication-ui-library/?path=/story/ui-components-gridlayout--grid-layout-component)                | Composant de grille pour organiser les vignettes vidéo dans une grille NxN                                            |
|         | [Vignette vidéo](https://azure.github.io/communication-ui-library/?path=/story/ui-components-videotile--video-tile-component)                   | Composant qui affiche le flux vidéo le cas échéant et un composant statique par défaut quand la vidéo n’est pas active        |
|         | [Barre de contrôle](https://azure.github.io/communication-ui-library/?path=/story/ui-components-controlbar--control-bar-component)                | Conteneur pour organiser DefaultButtons pour qu’ils se raccordent à des actions d’appel spécifiques, comme la désactivation du micro ou le partage de l’écran |
|         | [Galerie vidéo](https://azure.github.io/communication-ui-library/?path=/story/ui-components-video-gallery--video-gallery)                                           | Composant de la galerie vidéo clé en main qui change dynamiquement à mesure que des participants sont ajoutés               |
| Conversation    | [Fil de message](https://azure.github.io/communication-ui-library/?path=/story/ui-components-messagethread--message-thread-component)       | Conteneur permettant d’afficher les messages de conversation, les messages système et les messages personnalisés                          |
|         | [Boîte d’envoi](https://azure.github.io/communication-ui-library/?path=/story/ui-components-sendbox--send-box-component)                         | Composant d’entrée de texte avec un bouton Envoyer discret                                                   |
|         | [Indicateur d’état du message](https://azure.github.io/communication-ui-library/?path=/story/ui-components-message-status-indicator--message-status-indicator)        | Composant de confirmation de lecture à plusieurs états pour afficher l’état du message envoyé                                   |
|         | [Indicateur de saisie](https://azure.github.io/communication-ui-library/?path=/story/ui-components-typingindicator--typing-indicator-component) | Composant de texte permettant d’afficher les participants en train de taper activement un message sur un fil de conversation                      |
| Courant  | [Élément participant](https://azure.github.io/communication-ui-library/?path=/story/ui-components-participantitem--participant-item-component) | Composant commun permettant d’afficher un participant d’un appel ou d’une conversation, notamment son avatar et nom d’affichage            |
|         | [Liste des participants](https://azure.github.io/communication-ui-library/?path=/story/ui-components-participant-list--participant-list)                                 | Composant commun permettant d’afficher la liste des participants d’un appel ou d’une conversation, notamment les avatars et noms d’affichage       |


## <a name="what-ui-artifact-is-best-for-my-project"></a>Quel artefact d’interface utilisateur convient le mieux à mon projet ?

La compréhension de ces exigences vous aidera à choisir la bibliothèque de client appropriée :

- **Quel degré de personnalisation souhaitez-vous ?** Les principales bibliothèques de client Azure Communication ne disposent pas d’une expérience utilisateur. Elles sont conçues pour vous permettre d’en créer une qui répond à vos besoins. Les composants de bibliothèque d’interface utilisateur fournissent des ressources d’interface utilisateur au prix d’une personnalisation limitée.
- **Quelles plateformes ciblez-vous ?** Les capacités varient d’une plateforme à l’autre.

Des informations détaillées sur les fonctionnalités disponibles dans la [bibliothèque d’interface utilisateur sont présentées ici](https://azure.github.io/communication-ui-library/?path=/story/use-cases--page), mais les principaux compromis sont résumés ci-dessous.

| Bibliothèque de cliente/SDK  | Complexité de l’implémentation | Possibilité de personnalisation | Appel | Conversation | [Interopérabilité avec Teams](../teams-interop.md) |
| --------------------- | ------------------------- | --------------------- | ------- | ---- | ----------------------------------------------------------------------------------------------------- |
| Composants composites  | Faible                       | Faible                   | ✔       | ✔    | ✔                                                                                                     |
| Composants de base       | Moyenne                    | Moyenne                | ✔       | ✔    | ✔                                                                                                     |
| Principales bibliothèques de client | Élevé                      | Élevé                  | ✔       | ✔    | ✔                                                                                                     |

> [!div class="nextstepaction"]
> [Consultez le Storybook de la bibliothèque d’interface utilisateur](https://azure.github.io/communication-ui-library)
