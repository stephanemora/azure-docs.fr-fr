---
title: Intégration des équipes d’infrastructure d’interface utilisateur
titleSuffix: An Azure Communication Services quickstart
description: Dans ce document, vous allez découvrir comment la fonctionnalité Intégration des équipes d’infrastructure d’interface utilisateur d’Azure Communication Services peut permettre de créer des expériences d’appel clé en main.
author: tophpalmer
ms.author: chpalm
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 8301f81b21db50814df1bc764cc1fae38b4f14de
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107307637"
---
# <a name="teams-embed"></a>Intégration des équipes

[!INCLUDE [Public Preview Notice](../../includes/private-preview-include.md)]


La fonctionnalité Intégration des équipes d’Azure Communication Services est axée sur les interactions courantes d’appels entre entreprises et clients d’une part, et entre entreprises d’autre part. Au cœur du système Intégration des équipes se trouvent les [appels vidéo et vocaux](../voice-video-calling/calling-sdk-features.md), mais le système Intégration des équipes repose sur les primitives d’appel d’Azure pour offrir une expérience utilisateur complète basée sur les réunions de Microsoft Teams.

Les kits SDK Intégration des équipes sont à source fermée et mettent ces fonctionnalités à votre disposition dans un format composite clé en main. Vous déposez Intégration des équipes dans le canevas de votre application, et le kit SDK génère une expérience utilisateur complète. Étant donné que cette expérience utilisateur est très similaire aux réunions de Microsoft Teams, vous pouvez tirer parti des avantages suivants :

- Réduction du temps de développement et de la complexité de l’ingénierie
- L’utilisateur final connaît Teams
- Possibilité de réutiliser le [contenu de formation des utilisateurs finaux de Teams](https://support.microsoft.com/office/meetings-in-teams-e0b0ae21-53ee-4462-a50d-ca9b9e217b67)

Intégration des équipes offre la plupart des fonctionnalités prises en charge dans les réunions de Teams, notamment :

- Expérience préalable à la réunion dans laquelle un utilisateur configure ses périphériques audio et vidéo
- Expérience de la configuration des périphériques audio et vidéo en cours de réunion
- [Arrière-plans vidéo](https://support.microsoft.com/office/change-your-background-for-a-teams-meeting-f77a2381-443a-499d-825e-509a140f4780) : participants autorisés à flouter ou à remplacer leur arrière-plan
- [Plusieurs options pour la galerie vidéo](https://support.microsoft.com/office/using-video-in-microsoft-teams-3647fc29-7b92-4c26-8c2d-8a596904cdae) : grande galerie, mode ensemble, focus, épinglage et à la une
- [Partage de contenu](https://support.microsoft.com/en-us/office/share-content-in-a-meeting-in-teams-fcc2bf59-aecd-4481-8f99-ce55dd836ce8) : participants autorisés à partager leur écran

Pour plus d’informations sur cette interface utilisateur comparée à d’autres kits de développement logiciel (SDK) Azure Communication, consultez [Présentation du concept du Kit de développement logiciel (SDK) d’interface utilisateur](ui-sdk-overview.md). 
