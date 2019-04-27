---
title: Problèmes connus avec le plug-in Project Acoustics
titlesuffix: Azure Cognitive Services
description: Vous pourriez rencontrer les problèmes connus suivants lors de l’utilisation de Designer Preview for Project Acoustics.
services: cognitive-services
author: kylestorck
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: resources
ms.date: 03/20/2019
ms.author: kylestorck
ms.openlocfilehash: 50de4d983ed24440d655cf5b9ba3fb5e33d8d7cd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61335345"
---
# <a name="project-acoustics-known-issues"></a>Problèmes connus de projet acoustique
Vous pourriez rencontrer les problèmes connus suivants lors de l’utilisation de Designer Preview for Project Acoustics.

## <a name="acoustic-parameters-are-lost-when-you-rename-a-scene"></a>Les paramètres Acoustic sont perdus lorsque vous renommez une scène

Si vous renommez une scène, tous les paramètres acoustiques qui appartiennent à cette scène ne sont pas automatiquement transférer vers la nouvelle scène. Ils amèneront existent toujours dans l’ancien fichier de ressource toutefois. Recherchez le fichier **SceneName_AcousticParameters.asset** dans le répertoire **Editor** en regard de votre fichier de scène. Renommez votre fichier afin de refléter le nouveau nom de la scène.

## <a name="unity-crashes-when-closing-project"></a>Unity se bloque lors de la fermeture du projet

Dans les dernières versions d’Unity (2018.2+), il existe un bogue connu où Unity se bloque lorsque vous fermez votre projet. Cela est suivi par [ce problème Unity](https://issuetracker.unity3d.com/issues/crash-on-assetdatabase-getassetimporterversions-when-closing-a-specific-unity-project).

## <a name="deploying-to-android-from-some-unity-versions"></a>Déploiement sur Android à partir de certaines versions Unity

Certaines versions de Unity ont un bogue avec le déploiement de plug-ins audio pour Android. Assurez-vous que vous n’utilisez pas une version faisant l’objet [ce bogue](https://issuetracker.unity3d.com/issues/android-ios-audiosource-playing-through-google-resonance-audio-sdk-with-spatializer-enabled-does-not-play-on-built-player).

## <a name="i-get-an-error-that-could-not-find-metadata-file-systemsecuritydll"></a>J’obtiens une erreur « Impossible de trouver le fichier de métadonnées System.Security.dll »

Vérifiez que la version du Runtime de script dans les paramètres du lecteur est définie sur **.NET 4.x Equivalent** et redémarrez Unity.

## <a name="im-having-authentication-problems-when-connecting-to-azure"></a>Je rencontre des problèmes d’authentification lors de la connexion à Azure

Vérifiez que vous utilisez les informations d’identification correctes pour votre compte Azure, que votre compte prend en charge le type de nœud demandé dans la création et que votre horloge système est exacte.

## <a name="canceling-a-bake-leaves-the-bake-tab-in-deleting-state"></a>L’annulation d’un baking laisse l’onglet Baking en état de « suppression »
Projet acoustique nettoie toutes les ressources Azure pour un travail sur la réussite ou d’annulation. Cela peut prendre jusqu'à 5 minutes.

## <a name="next-steps"></a>Étapes suivantes
* Essayez le [Unity](unity-quickstart.md) ou [Unreal](unreal-quickstart.md) exemple de contenu

