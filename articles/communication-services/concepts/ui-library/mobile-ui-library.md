---
title: Bibliothèque mobile d’interface utilisateur
titleSuffix: An Azure Communication Services - UI Mobile Library
description: Ce document présente la bibliothèque mobile d’interface utilisateur.
author: jorgegarc
ms.author: jorgegarc
ms.date: 09/14/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: fdf84a2eb2cc57081a315bbf9dc41c14ee96ae8e
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130181663"
---
# <a name="ui-mobile-library"></a>Bibliothèque mobile d’interface utilisateur

[!INCLUDE [Public Preview Notice](../../includes/private-preview-include.md)]

La bibliothèque mobile d’interface utilisateur (« UI Mobile Library ») est une fonctionnalité d’Azure Communication Services axée sur la mise à disposition de composants d’interface utilisateur pour les interactions courantes que constituent les appels entre entreprises et clients d’une part et entre entreprises d’autre part. La bibliothèque mobile d’interface utilisateur fournit principalement des composants utilisés dans le cadre des [appels vidéo et vocaux](../voice-video-calling/calling-sdk-features.md). Elle s’appuie sur les fonctions Azure de base dans le domaine des appels afin d’offrir aux utilisateurs sur appareil mobile une expérience complète pour les appels et les réunions.

L’objectif de la bibliothèque mobile d’interface utilisateur est de vous fournir ces fonctionnalités dans un format composite clé en main. Vous déposez le SDK dans le canevas de votre application de développement mobile préférée, et le SDK génère une expérience utilisateur complète. Du fait de l’aspect léger de cette expérience utilisateur, vous pouvez tirer parti d’une réduction du temps de développement et de la complexité de l’ingénierie.

## <a name="composites"></a>Composites

Les composites sont des composants de niveau supérieur constitués de composants plus petits qui fournissent des solutions clés en main pour des scénarios de communication courants avec Azure Communication Services.

Les développeurs peuvent facilement instancier des composites en utilisant un jeton d’accès d’Azure Communication Services et les attributs de configuration requis pour l’appel.

| Composite                                                                   | Cas d’usage                                                                                                                                                                                                                                                                                                  |
| --------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| [CallComposite](../../quickstarts/ui-library/get-started-call.md)  | Appel de l’expérience qui permet aux utilisateurs de démarrer ou de rejoindre un appel. Dans l’expérience, les utilisateurs peuvent configurer leurs appareils, participer à l’appel avec la vidéo et voir les autres participants, notamment ceux qui ont activé la vidéo. Pour l’interopérabilité avec Teams, la fonctionnalité de salle d’attente est incluse de façon à ce que les utilisateurs puissent patienter jusqu’à ce qu’ils soient admis. |

## <a name="scenarios"></a>Scénarios

### <a name="joining-a-videoaudio-call"></a>Participation à un appel vidéo/audio

Les utilisateurs peuvent facilement rejoindre l’appel en utilisant l’*URL de réunion Teams* ou configurer un appel Azure Communication Services pour bénéficier d’une expérience plus simple et de haute qualité, tout comme avec l’application Teams. L’utilisateur peut également prendre part à un appel vidéo ou audio en direct complet, sans perdre l’expérience de la simplicité et en allant à l’essentiel.

### <a name="pre-call-experience"></a>Expérience préalable à l’appel

En tant que participant à un appel, entrez un nom et définissez une configuration par défaut pour les appareils audio et vidéo. Vous pouvez alors rejoindre l’appel.

:::image type="content" source="../media/mobile-ui/teams-meet.png" alt-text="Expérience préalable à la réunion.":::

### <a name="call-experience"></a>Expérience de l’appel

Le composite d’appel fournit une expérience de bout en bout qui met l’accent sur l’optimisation du temps de développement et une disposition simple.  

:::image type="content" source="../media/mobile-ui/calling-composite.png" alt-text="Expérience de réunion.":::

**L’expérience d’appel fournit toutes ces fonctionnalités dans un seul composant composite, ouvant la voie à un développement plus rapide sans code complexe.**

### <a name="quality-and-security"></a>Qualité et sécurité

Les composites mobiles sont initialisés au moyen de [jetons d’accès Azure Communication Services](../../quickstarts/access-tokens.md).

### <a name="more-details"></a>Détails supplémentaires

Pour plus d’informations sur les composites mobiles, visitez le [site des cas d’usage](mobile-ui-use-cases.md).

## <a name="what-ui-artifact-is-best-for-my-project"></a>Quel artefact d’interface utilisateur convient le mieux à mon projet ?

La compréhension de ces exigences vous aidera à choisir la bibliothèque de client appropriée :

- **Quel degré de personnalisation souhaitez-vous ?** Les principales bibliothèques de client Azure Communication ne disposent pas d’une expérience utilisateur. Elles sont conçues pour vous permettre d’en créer une qui répond à vos besoins. Les composants de bibliothèque d’interface utilisateur fournissent des ressources d’interface utilisateur au prix d’une personnalisation limitée.

- **Quelles plateformes ciblez-vous ?** Les capacités varient d’une plateforme à l’autre.

Des informations détaillées sur les fonctionnalités disponibles dans la [bibliothèque d’interface utilisateur sont présentées ici](mobile-ui-use-cases.md), mais les principaux compromis sont récapitulés ci-dessous.

| Bibliothèque de cliente/SDK  | Complexité de l’implémentation | Possibilité de personnalisation | Appel |  [Interopérabilité avec Teams](../../concepts/teams-interop.md) |
| --------------------- | ------------------------- | --------------------- |  ---- | ----------------------------------------------------------------------------------------------- |
| Composants composites  | Faible                       | Faible                   |         ✔    | ✔                                                                                               |
| Principales bibliothèques de client | Élevé                      | Élevé                  |         ✔    | ✔                                                                                               |

> [!div class="nextstepaction"]

Pour plus d’informations sur la façon de démarrer avec les composites de la bibliothèque mobile d’interface utilisateur, lisez notre [guide de démarrage rapide](../../quickstarts/ui-library/get-started-call.md).