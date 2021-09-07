---
title: SDK Teams Embed
titleSuffix: An Azure Communication Services - Teams Embed SDK description
description: Ce document passe en revue les fonctionnalités de Teams Embed et explique comment elles fonctionnent dans vos applications
author: tophpalmer
ms.author: chpalm
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 49fca98d6bd8bc45cb7306dbc6ae9fae5047651b
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123256823"
---
# <a name="teams-embed"></a>Intégration des équipes

[!INCLUDE [Public Preview Notice](../../includes/private-preview-include.md)]

La fonctionnalité Intégration des équipes d’Azure Communication Services est axée sur les interactions courantes d’appels entre entreprises et clients d’une part, et entre entreprises d’autre part. Au cœur du système Teams Embed se trouvent les [appels vidéo et vocaux](../voice-video-calling/calling-sdk-features.md), et ce système repose sur les primitives d’appel d’Azure pour offrir une expérience utilisateur complète basée sur les réunions de Microsoft Teams.

Le SDK Teams Embed est à source fermée et met ces fonctionnalités à votre disposition dans un format composite clé en main. Vous déposez Teams Embed dans le canevas de votre application, et le SDK génère une expérience utilisateur complète. Étant donné que cette expérience utilisateur est très similaire aux réunions de Microsoft Teams, vous pouvez tirer parti des avantages suivants :

- Réduction du temps de développement et de la complexité de l’ingénierie
- Connaissance de l’expérience Teams par l’utilisateur final
- Possibilité de réutiliser le [contenu de formation des utilisateurs finaux de Teams](https://support.microsoft.com/office/meetings-in-teams-e0b0ae21-53ee-4462-a50d-ca9b9e217b67)

Le SDK Teams Embed offre la plupart des fonctionnalités prises en charge dans les réunions de Teams, notamment :

## <a name="joining-a-meeting"></a>Participer à une réunion

Les utilisateurs peuvent participer facilement à la réunion en utilisant l’URL de réunion Teams pour une expérience plus simple et de haute qualité, tout comme avec l’application Teams. Ils ont ainsi la possibilité de participer à des réunions en direct en disposant de fonctionnalités avancées et sans perdre la simplicité de l’application Teams.

## <a name="pre-meeting-experience"></a>Expérience préalable à la réunion

Avant de participer à n’importe quelle réunion, vous pouvez définir une configuration par défaut pour les périphériques audio et vidéo. Vous pouvez également ajouter votre nom et votre propre avatar.

## <a name="meeting-experience"></a>Expérience de réunion

Personnalisez l’expérience utilisateur et ajustez les fonctionnalités en fonction de vos besoins. Vous contrôlerez l’expérience globale pendant les réunions.

- [**Partage de contenu**](https://support.microsoft.com/office/share-content-in-a-meeting-in-teams-fcc2bf59-aecd-4481-8f99-ce55dd836ce8) : l’utilisateur peut partager une vidéo, une photo ou la totalité de l’écran. Les autres participants voient le contenu partagé.

- [**Plusieurs options de disposition pour la galerie de vidéos**](https://support.microsoft.com/office/using-video-in-microsoft-teams-3647fc29-7b92-4c26-8c2d-8a596904cdae) : offrez la possibilité de sélectionner les options de disposition par défaut pendant la réunion (grande galerie, mode Ensemble, focus, épinglage et mise en évidence). Vous pouvez également adapter la disposition en fonction de la résolution de l’appareil.

- [**Activation/désactivation de la vidéo**](https://support.microsoft.com/office/using-video-in-microsoft-teams-3647fc29-7b92-4c26-8c2d-8a596904cdae#bkmk_turnvideoonoff) : offrez aux utilisateurs la possibilité de gérer leur vidéo pendant la réunion.

- **Actions du participant** : l’utilisateur peut [« lever la main »](https://support.microsoft.com/en-us/office/raise-your-hand-in-a-teams-meeting-bb2dd8e1-e6bd-43a6-85cf-30822667b372), activer et désactiver son micro, changer la configuration vidéo ou audio, raccrocher et effectuer bien d’autres actions.

- [**Prise en charge multilingue**](https://support.microsoft.com/topic/languages-supported-in-microsoft-teams-for-education-293792c3-352e-4b24-9fc2-4c28b5de2db8) : la solution assure la prise en charge de 56 langues pendant toute l’expérience des équipes.

## <a name="quality-and-security"></a>Qualité et sécurité

Le SDK Teams Embed s’appuie sur les normes de qualité de Teams. Pour tout ce qui concerne la qualité vidéo, vous pouvez donc vous reporter aux [exigences en bande passante](/microsoftteams/prepare-network#bandwidth-requirements).

Vous pouvez utiliser un jeton d’accès Azure Communication Services. Pour plus d’informations, consultez [Créer et gérer des jetons d’accès](../../quickstarts/access-tokens.md).

## <a name="capabilities"></a>Fonctionnalités

| Fonctionnalités du SDK                                                        | Disponibilité |
|---------------------------------------------------------------------|--------------|
| *Actions de réunion*                                                   |              |
| Participation à un appel                                                         | Oui          |
| Gestion de l’*état de l’appel*, notamment des états de connexion, du nombre de participants et des modalités comme l’état du micro ou de la caméra                                           | Oui          |
| Déclenchement des événements utilisateur Main levée et Son désactivé et de l’événement d’envoi de vidéo                                                                 | Oui          |
| Prise en charge du traitement de réseaux non fiables                                      | Oui          |
| Suppression de participants de la réunion                                    | Oui          |
| Prise en charge de 56 langues                                               | Oui          |
| Conversation pendant la réunion et conversation privée                                    | Non           |
| Appels RTPC                                                        | Non           |
| Enregistrement et transcription                                            | Non           |
| Partage du tableau blanc                                                  | Non           |
| Intervention dans des salles                                                 | Non           |
| *Participation à l’expérience de réunion*                                        |              |
| Participation à un appel de groupe avec un GUID et un jeton ACS                             | Oui          |
| Participation à une réunion avec une URL de réunion en direct et un jeton ACS                    | Oui          |
| Participation à une réunion avec une URL de réunion et un jeton ACS                         | Oui          |
| Participation par le biais de la salle d’attente                                           | Oui          |

| Personnalisation de l’expérience utilisateur                                       | Disponibilité |
|---------------------------------------------------------------------|--------------|
| *Actions de réunion*                                                   |              |
| Affichage de la liste d’appels                                             | Oui          |
| Personnalisation de la disposition : couleurs, icônes, boutons                        | Partiellement    |
| Personnalisation des icônes de l’écran d’appel                                     | Oui          |
| Modification de la disposition des vues de la réunion                                         | Oui          |
| Modification dynamique de la disposition NxN de l’appel                                    | Oui          |
| Lever/baisser la main                                                     | Oui          |
| Désactivation/réactivation du son                                                        | Oui          |
| Suspension                                                         | Oui          |
| Sélection du routage audio                                                | Oui          |
| Sélection de la caméra                                                       | Oui          |
| Partage de photos, d’écran et de vidéos                                       | Oui          |
| Démarrage/arrêt d’une vidéo                                                    | Oui          |
| Événement d’appui sur une vignette d’un utilisateur                                               | Oui          |
| Événement d’appui sur une plaque nominative                                              | Oui          |
| Flou d’arrière-plan                                                     | Oui          |
| Personnalisation de la couleur d’arrière-plan de l’écran                               | Non           |
| Personnalisation de la couleur de la barre supérieure/inférieure                                  | Non           |
| Partage du tableau blanc                                                  | Non           |
| *Expérience préalable à la réunion*                                            |              |
| Possibilité pour le participant de configurer le nom d’affichage et d’activer le partage de photos            | Oui          |
| Possibilité pour le participant de configurer l’affichage de la vue intermédiaire des appels (écran de pré-appel)   | Oui          |
| Possibilité pour le participant de configurer l’affichage de la plaque nominative sur l’écran d’appel             | Oui          |
| Personnalisation de l’écran de la salle d’attente                                          | Partiellement    |

Pour plus d’informations sur la façon de démarrer avec le SDK Teams Embed, consultez ce [guide de démarrage rapide](../../quickstarts/meeting/getting-started-with-teams-embed.md). Si vous souhaitez en savoir plus sur les fonctionnalités du SDK, consultez le [guide d’exemples](../../quickstarts/meeting/samples-for-teams-embed.md).