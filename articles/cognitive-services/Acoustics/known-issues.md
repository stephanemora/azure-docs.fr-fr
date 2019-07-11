---
title: Problèmes connus avec le plug-in Project Acoustics
titlesuffix: Azure Cognitive Services
description: Vous pourriez rencontrer les problèmes connus suivants lors de l’utilisation de Designer Preview for Project Acoustics.
services: cognitive-services
author: kylestorck
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: kylestorck
ms.openlocfilehash: 02156f6e39c213764c35e67d8af028489d265835
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67616345"
---
# <a name="project-acoustics-known-issues"></a>Problèmes connus avec Project Acoustics
Vous pourriez rencontrer les problèmes connus suivants lors de l’utilisation de Designer Preview for Project Acoustics.

## <a name="acoustic-parameters-are-lost-when-you-rename-a-scene"></a>Les paramètres Acoustic sont perdus lorsque vous renommez une scène

Si vous renommez une scène, tous les paramètres Acoustic qui lui appartiennent ne seront pas transférés automatiquement vers la nouvelle scène. Toutefois, ils existeront toujours dans l’ancien fichier de ressource. Recherchez le fichier **SceneName_AcousticParameters.asset** dans le répertoire **Editor** en regard de votre fichier de scène. Renommez votre fichier afin de refléter le nouveau nom de la scène.

## <a name="unity-crashes-when-closing-project"></a>Unity se bloque lors de la fermeture du projet

Dans les dernières versions d’Unity (2018.2+), il existe un bogue connu où Unity se bloque lorsque vous fermez votre projet. Cela est suivi par [ce problème Unity](https://issuetracker.unity3d.com/issues/crash-on-assetdatabase-getassetimporterversions-when-closing-a-specific-unity-project).

## <a name="deploying-to-android-from-some-unity-versions"></a>Déploiement vers Android à partir de certaines versions d'Unity

Certaines versions d’Unity contiennent un bogue avec le déploiement de plug-ins audio sur Android. Vérifiez que vous n’utilisez pas une version affectée par [ce bogue](https://issuetracker.unity3d.com/issues/android-ios-audiosource-playing-through-google-resonance-audio-sdk-with-spatializer-enabled-does-not-play-on-built-player).

## <a name="i-get-an-error-that-could-not-find-metadata-file-systemsecuritydll"></a>J’obtiens une erreur « Impossible de trouver le fichier de métadonnées System.Security.dll »

Vérifiez que la version du Runtime de script dans les paramètres du lecteur est définie sur **.NET 4.x Equivalent** et redémarrez Unity.

## <a name="im-having-authentication-problems-when-connecting-to-azure"></a>Je rencontre des problèmes d’authentification lors de la connexion à Azure

Vérifiez que vous utilisez les informations d’identification correctes pour votre compte Azure, que votre compte prend en charge le type de nœud demandé dans la création et que votre horloge système est exacte.

## <a name="canceling-a-bake-leaves-the-bake-tab-in-deleting-state"></a>L’annulation d’un baking laisse l’onglet Baking en état de « suppression »
Project Acoustics nettoie toutes les ressources Azure pour un travail accompli ou annulé. Cette opération peut durer jusqu’à 5 minutes.

## <a name="next-steps"></a>Étapes suivantes
* Tester l’exemple de contenu [Unity](unity-quickstart.md) ou [Unreal](unreal-quickstart.md)

