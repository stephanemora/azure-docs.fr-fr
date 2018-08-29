---
title: Problèmes connus avec le plug-in Acoustics - Cognitive Services
description: Vous pourriez rencontrer les problèmes connus suivants lors de l’utilisation de Designer Preview for Project Acoustics.
services: cognitive-services
author: kylestorck
manager: noelc
ms.service: cognitive-services
ms.component: acoustics
ms.topic: article
ms.date: 08/17/2018
ms.author: kylestorck
ms.openlocfilehash: b0dd5e0c365c65fc7b29752f7885acb71bdb3df8
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/17/2018
ms.locfileid: "40181279"
---
# <a name="known-issues"></a>Problèmes connus
Vous pourriez rencontrer les problèmes connus suivants lors de l’utilisation de Designer Preview for Project Acoustics.

## <a name="acoustic-parameters-are-lost-when-you-rename-a-scene"></a>Les paramètres Acoustic sont perdus lorsque vous renommez une scène

Si vous renommez une scène, tous les paramètres Acoustic qui lui appartiennent ne sont pas transférés automatiquement vers la nouvelle scène. Toutefois, ils existent toujours dans l’ancien fichier de ressource. Recherchez le fichier **SceneName_AcousticParameters.asset** dans le répertoire **Editor** en regard de votre fichier de scène. Renommez votre fichier afin de refléter le nouveau nom de la scène.

## <a name="the-default-path-for-the-acousticsdata-folder-in-probes-tab-is-an-absolute-path"></a>Le chemin d’accès par défaut pour le dossier AcousticsData dans l’onglet Probes est un chemin d’accès absolu

La valeur par défaut est un chemin d’accès relatif pour faciliter le partage des projets entre les collaborateurs. Pour résoudre ce problème, modifiez le chemin d’accès en chemin d’accès relatif vers le répertoire du projet.

## <a name="runtime-voxels-are-a-different-size-than-design-time-voxels"></a>Les voxels Runtime sont de taille différente des voxels au moment de la conception

Si vous effectuez une opération **Calculate** dans l’onglet **Probes** et affichez les voxels, puis effectuez une création et affichez les voxels au moment de l’exécution pour la même scène, les voxels sont de tailles différentes. Les voxels affichés avant la création sont les voxels utilisés lors de la simulation. Les voxels affichés lors de l’exécution sont utilisés pour l’interpolation entre les points de la sonde. Cela peut entraîner une incohérence dans laquelle les portails semblent ouverts lors de l’exécution alors qu’ils ne le sont pas réellement.

## <a name="uwp-builds-not-working"></a>Les générations UWP ne fonctionnent pas

Dans les dernières versions d’Unity (2018.2+), les générations UWP échouent. La phase d’exécution de la génération se bloque et vous obtenez des erreurs « Les extensions Unity ne sont pas encore initialisées ». Cela est suivi par [ce problème Unity](https://fogbugz.unity3d.com/default.asp?1070491_1rgf14bakv5u779d).

## <a name="unity-crashes-when-closing-project"></a>Unity se bloque lors de la fermeture du projet

Dans les dernières versions d’Unity (2018.2+), il existe un bogue connu où Unity se bloque lorsque vous fermez votre projet. Cela est suivi par [ce problème Unity](https://issuetracker.unity3d.com/issues/crash-on-assetdatabase-getassetimporterversions-when-closing-a-specific-unity-project).

## <a name="trouble-deploying-to-android"></a>Problème de déploiement sur Android
Pour utiliser Project Acoustics sur Android, remplacez votre cible de génération par Android. Certaines versions d’Unity contiennent un bogue avec le déploiement de plug-ins audio -- Vérifiez que vous n’utilisez pas une version affectée par [ce bogue](https://issuetracker.unity3d.com/issues/android-ios-audiosource-playing-through-google-resonance-audio-sdk-with-spatializer-enabled-does-not-play-on-built-player).

## <a name="i-get-an-error-that-could-not-find-metadata-file-systemsecuritydll"></a>J’obtiens une erreur « Impossible de trouver le fichier de métadonnées System.Security.dll »

Vérifiez que la version du Runtime de script dans les paramètres du lecteur est définie sur **.NET 4.x Equivalent** et redémarrez Unity.

## <a name="im-having-authentication-problems-when-connecting-to-azure"></a>Je rencontre des problèmes d’authentification lors de la connexion à Azure

Vérifiez que vous utilisez les informations d’identification correctes pour votre compte Azure, que votre compte prend en charge le type de nœud demandé dans la création et que votre horloge système est exacte.

## <a name="next-steps"></a>Étapes suivantes
* Bien démarrer avec [l’intégration Acoustics dans votre projet Unity](getting-started.md)

