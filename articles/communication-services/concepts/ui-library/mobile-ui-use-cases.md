---
title: Cas d’usage de la bibliothèque mobile d’interface utilisateur
titleSuffix: An Azure Communication Services - UI Mobile Library use cases scenarios
description: Ce document présente les fonctionnalités de la bibliothèque mobile d’interface utilisateur et explique comment vous en servir dans vos applications.
author: jorgegarc
ms.author: jorgegarc
ms.date: 09/14/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: cc849bb98faea5cbf7f3ec7828cc8318061dc5a5
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130181669"
---
# <a name="ui-library-ios-and-android-capabilities"></a>Fonctionnalités de la bibliothèque d’interface utilisateur (iOS et Android)

[!INCLUDE [Public Preview Notice](../../includes/private-preview-include.md)]

La bibliothèque d’interface utilisateur pour iOS et Android prend en charge les cas d’usage d’appel par le biais du **composite d’appel**.
Les composites permettent aux développeurs d’intégrer facilement une expérience d’appel complète à leur application avec seulement quelques lignes de code. Ces composites prennent en charge l’intégralité du cycle de vie de l’appel, de la configuration à la fin de l’appel.

## <a name="calling"></a>Appel

| Domaine                                                                                            | Cas d’usage                                              |
| ----------------------------------------------------------------------------------------------- | ------------------------------------------------------ |
| Call Types                                                                                      | Participer à une réunion Teams                                     |
|                                                                                                 | Participer à un appel Azure Communication Services avec un ID de groupe   |
| [Interopérabilité avec Teams](../../concepts/teams-interop.md) | Salle d’attente                                             |
|                                                                                                 | Bannière d’alerte de transcription et enregistrement               |
| Galerie des participants                                                                             | Les participants distants sont affichés sur la grille              |
|                                                                                                 | Aperçu vidéo disponible tout au long de l’appel pour l’utilisateur local |
|                                                                                                 | Avatars par défaut disponibles quand la vidéo est désactivée            |
|                                                                                                 | Contenu de l’écran partagé affiché dans la galerie des participants |
|                                                                                     | Liste des participants                                     |
| Configuration de l’appel                                                                              | Gestion des périphériques du microphone                           |
|                                                                                                 | Gestion des périphériques de la caméra                               |
|                                                                                                 | Gestion des périphériques des haut-parleurs                              |
|                                                                                                 | Aperçu local disponible pour que l’utilisateur puisse vérifier la vidéo        |
| Contrôles d’appel                                                                                   | Désactiver/réactiver le son lors d’un appel                                       |
|                                                                                                 | Activer/désactiver la vidéo lors d’un appel                                   |
|                                                                                                 | Terminer l’appel                                               |

## <a name="supported-identities"></a>Identités prises en charge

Une identité Azure Communication Services est nécessaire pour initialiser les composites et s’authentifier auprès du service.
Pour plus d’informations sur l’authentification, consultez [Authentification](../authentication.md) et [Jetons d’accès](../../quickstarts/access-tokens.md)

## <a name="teams-interop"></a>Interopérabilité avec Teams

![Modèle d’interopérabilité avec Teams pour les appels et les conversations](../media/mobile-ui/teams-interop-diagram.png)

Pour les scénarios d’[interopérabilité avec Teams](../teams-interop.md), les développeurs peuvent utiliser les composants de la bibliothèque mobile d’interface utilisateur pour rejoindre des réunions Teams par le biais d’Azure Communication Services.
Pour activer l’interopérabilité avec Teams, les développeurs peuvent utiliser le composite d’appel qui prend en charge le cycle de vie de la participation à un appel Teams.

:::image type="content" source="../media/mobile-ui/teams-meet.png" alt-text="Expérience préalable à la réunion":::

## <a name="theming"></a>Création de thèmes

Le composite d’appel de la bibliothèque d’interface utilisateur pour iOS et Android fournit des interfaces permettant aux développeurs de modifier le thème de l’expérience en passant une couleur primaire. Le composite utilise cette couleur primaire pour fournir des thèmes appropriés tout au long de l’expérience.

| Android                            | iOS                                     |
| -------------------------------------------------------- | --------------------------------------------------------------- |
| :::image type="content" source="../media/mobile-ui/android-color.png" alt-text="Thèmes Android"::: | :::image type="content" source="../media/mobile-ui/ios-dark.png" alt-text="Thèmes iOS":::  |


## <a name="screen-size"></a>Taille de l'écran

Le composite d’appel s’adapte à n’importe quelle taille d’écran (des écrans 5 pouces aux tablettes), obtient la disposition de la liste des participants dynamiques, propose une vue claire et donne la priorité à la conversation.

|Mode fractionné | Mode Tablette|
|---------|---------|
| :::image type="content" source="../media/mobile-ui/meet-splitscreen.png" alt-text="Écran divisé"::: |  :::image type="content" source="../media/mobile-ui/tablet-landscape.png" alt-text="Mode tablette"::: |

## <a name="recommended-architecture"></a>Architecture recommandée

Les composites mobiles sont initialisés au moyen d’un jeton d’accès Azure Communication Services. Les jetons d’accès doivent être obtenus auprès d’Azure Communication Services via un service approuvé que vous gérez. Consultez [Démarrage rapide : Créer des jetons d’accès](../../quickstarts/access-tokens.md) et [Tutoriel Service approuvé](../../tutorials/trusted-service-tutorial.md) pour plus d’informations.

:::image type="content" source="../media/mobile-ui/ui-library-architecture.png" alt-text="Diagramme de l’architecture recommandée":::

Ces bibliothèques de client ont également besoin du contexte de l’appel auxquels elles se joignent. À l’instar des jetons d’accès utilisateur, ce contexte doit être distribué aux clients par l’intermédiaire de votre propre service approuvé. La liste ci-dessous résume les fonctions d’initialisation et de gestion des ressources que vous devez rendre opérationnelles.

| Responsabilités de Contoso                                 | Responsabilités de la bibliothèque d’interface utilisateur                                     |
| -------------------------------------------------------- | --------------------------------------------------------------- |
| Fournir un jeton d’accès d’Azure                          | Transférer le jeton d’accès fourni pour initialiser les composants        |
| Fournir une fonction d’actualisation                                 | Actualiser le jeton d’accès à l’aide de la fonction fournie par les développeurs          |
| Récupérer/transmettre les informations permettant de se joindre à l’appel ou à la conversation          | Transmettre les informations d’appel et de conversation pour initialiser les composants |
| Récupérer/transmettre des informations utilisateur pour un modèle de données personnalisé | Transmettre le modèle de données personnalisé aux composants à afficher          |

## <a name="platform-support"></a>Plateforme prise en charge

|Plateforme | Versions|
|---------|---------|
| iOS     | iOS 13+ |
| Android | v23+    |

## <a name="accessibility"></a>Accessibilité

- L’accessibilité dès la conception est un principe pour l’ensemble des produits Microsoft.
- La bibliothèque d’interface utilisateur respecte ce principe pour garantir que tous les composants IU soient entièrement accessibles.
- Pendant la préversion publique, la bibliothèque d’interface utilisateur continue de s’améliorer et d’ajouter des fonctionnalités d’accessibilité aux composants IU.

## <a name="localization"></a>Localisation

- La localisation est un élément clé pour offrir des produits pouvant être utilisés dans le monde entier par des personnes qui parlent différentes langues.
- La bibliothèque IU fournit une prise en charge prête à l’emploi pour certaines langues et fonctionnalités comme DàG.
- Les développeurs peuvent fournir leurs propres fichiers de localisation à utiliser pour la bibliothèque IU.

> [!div class="nextstepaction"]

Pour plus d’informations sur la façon de démarrer avec les composites de la bibliothèque mobile d’interface utilisateur, lisez notre [guide de démarrage rapide](../../quickstarts/ui-library/get-started-call.md).