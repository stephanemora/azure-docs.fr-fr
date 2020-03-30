---
title: Problèmes connus avec le plug-in Project Acoustics
titlesuffix: Azure Cognitive Services
description: Vous pouvez rencontrer les problèmes connus suivants dans Project Acoustics.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: b71b93f271608d946d964f70dae9eefbef77e87b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "72243037"
---
# <a name="project-acoustics-known-issues"></a>Problèmes connus avec Project Acoustics
Cet article décrit les problèmes que vous pouvez rencontrer lorsque vous utilisez Project Acoustics.

## <a name="acoustic-parameters-are-lost-when-you-rename-a-scene"></a>Les paramètres Acoustic sont perdus lorsque vous renommez une scène

Si vous renommez une scène, tous les paramètres acoustiques associés ne sont pas transférés automatiquement vers la nouvelle scène. Toutefois, ils existent toujours dans l’ancien fichier de ressource. Recherchez le fichier *[SceneName]_AcousticParameters.asset* dans le répertoire *Editor* en regard de votre fichier de scène. Renommez le fichier afin de refléter le nouveau nom de la scène.

## <a name="deploy-to-android-bug-from-some-unity-versions"></a>Bogue de déploiement sur Android à partir de certaines versions d’Unity

Certaines versions d’Unity présentent un [bogue](https://issuetracker.unity3d.com/issues/android-ios-audiosource-playing-through-google-resonance-audio-sdk-with-spatializer-enabled-does-not-play-on-built-player) dans la manière de déployer les plug-ins audio sur Android. Vérifiez que vous n’utilisez pas une version affectée par ce bogue.

## <a name="could-not-find-metadata-file-systemsecuritydll-error"></a>Erreur « Impossible de trouver le fichier de métadonnées System.Security.dll »

Vérifiez que la **version du runtime de script** dans les paramètres du **lecteur** est *.NET 4.x Equivalent* et redémarrez Unity.

## <a name="authentication-problems-when-connecting-to-azure"></a>Problèmes d’authentification lors de la connexion à Azure

Vérifiez les éléments suivants :
- Vous avez utilisé les informations d’identification correctes pour votre compte Azure.
- Votre compte prend en charge le type de nœud que vous avez demandé dans le bake.
- L’horloge système est correctement définie.

## <a name="the-bake-tab-still-shows-deleting-after-you-cancel"></a>L’onglet Bake affiche toujours « suppression » après l’annulation
Project Acoustics nettoie toutes les ressources Azure pour un travail accompli ou annulé. Ce processus peut prendre jusqu’à 5 minutes.

## <a name="next-steps"></a>Étapes suivantes
* Tester l’exemple de contenu [Unity](unity-quickstart.md) ou [Unreal](unreal-quickstart.md).
