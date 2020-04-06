---
title: Documentation du Kit de développement logiciel (SDK) Speech Devices
titleSuffix: Azure Cognitive Services
description: Les notes de publication constituent un journal des mises à jour, améliorations, correctifs de bogues et modifications apportées au SDK Speech Devices. Cet article est mis à jour avec chaque version du SDK Speech Devices.
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: wellsi
ms.openlocfilehash: 29dcb3c0e74482fd6670d1a0983e751043379d6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80371618"
---
# <a name="release-notes-speech-devices-sdk"></a>Notes de publication : Kit de développement logiciel (SDK) Speech Devices

Les sections suivantes dressent la liste des modifications dans les versions les plus récentes.

## <a name="speech-devices-sdk-190"></a>SDK Speech Devices 1.9.0 :

- Les fichiers binaires initiaux pour le [kit DDK Urbetter](https://aka.ms/sdsdk-download-urbetter) (Linux ARM64) sont fournis.
- Roobo v1 utilise désormais Maven pour le SDK Speech
- Mise à jour du composant [SDK Speech](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) vers la version 1.9.0. Pour plus d’informations, consultez ses [notes de publication](https://aka.ms/csspeech/whatsnew).

## <a name="speech-devices-sdk-170"></a>SDK Speech Devices 1.7.0 :

- Linux ARM est maintenant pris en charge.
- Les fichiers binaires initiaux pour le [kit DDK Roobo v2](https://aka.ms/sdsdk-download-roobov2) sont fournis (Linux ARM64).
- Les utilisateurs Windows peuvent utiliser `AudioConfig.fromDefaultMicrophoneInput()` ou `AudioConfig.fromMicrophoneInput(deviceName)` pour spécifier le microphone à utiliser.
- La taille de la bibliothèque a été optimisée.
- La reconnaissance multitours à l’aide du même objet de module de reconnaissance vocale/d’intention est prise en charge.
- Correction des blocages occasionnels qui se produisent pendant l’arrêt de la reconnaissance.
- Les exemples d’applications contiennent maintenant un exemple de fichier participants.properties pour illustrer le format du fichier.
- Mise à jour du composant [SDK Speech](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) vers la version 1.7.0. Pour plus d’informations, consultez ses [notes de publication](https://aka.ms/csspeech/whatsnew).

## <a name="speech-devices-sdk-160"></a>Kit de développement logiciel (SDK) Speech Devices 1.6.0 :

- Prise en charge de [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/) sur Windows et Linux avec un [exemple d’application](https://aka.ms/sdsdk-download) courant
- Mise à jour du composant [Kit de développement logiciel (SDK) Speech](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) vers la version 1.6.0. Pour plus d’informations, consultez ses [notes de publication](https://aka.ms/csspeech/whatsnew).

## <a name="speech-devices-sdk-151"></a>Kit de développement logiciel (SDK) Speech Devices 1.5.1 :

- Intégrez la [Transcription de conversation](conversation-transcription-service.md) dans l’exemple d’application.
- Mise à jour du composant [Kit de développement logiciel (SDK) Speech](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) vers la version 1.5.1. Pour plus d’informations, consultez ses [notes de publication](https://aka.ms/csspeech/whatsnew).

## <a name="speech-devices-sdk-150-2019-may-release"></a>SDK Speech Devices 1.5.0 : version de mai 2019

- Le SDK Speech Devices est désormais généralement disponible et n’est plus en préversion contrôlée.
- Mise à jour du composant [Kit de développement logiciel (SDK) Speech](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) vers la version 1.5.0. Pour plus d’informations, consultez ses [notes de publication](https://aka.ms/csspeech/whatsnew).
- La nouvelle technologie de mot clé apporte des améliorations de qualité notables, voir Dernières modifications.
- Un nouveau pipeline de traitement audio pour une meilleure reconnaissance en champ lointain.

**Dernières modifications**

- En raison de la nouvelle technologie de mot clé, tous les mots clés doivent être recréés sur notre portail de mots clés améliorés. Pour supprimer complètement les anciens mots-clés de l’appareil, désinstallez l’ancienne application.
  - adb uninstall com.microsoft.cognitiveservices.speech.samples.sdsdkstarterapp

## <a name="speech-devices-sdk-140-2019-apr-release"></a>SDK Speech Devices 1.4.0 : version d’avril 2019

- Mise à jour du composant [Kit de développement logiciel (SDK) Speech](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) vers la version 1.4.0. Pour plus d’informations, consultez ses [notes de publication](https://aka.ms/csspeech/whatsnew).

## <a name="speech-devices-sdk-131-2019-mar-release"></a>SDK Speech Devices 1.3.1 : version de mars 2019

- Mise à jour du composant [Kit de développement logiciel (SDK) Speech](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) vers la version 1.3.1. Pour plus d’informations, consultez ses [notes de publication](https://aka.ms/csspeech/whatsnew).
- Gestion des mots clés mise à jour, voir Dernières modifications.
- L’exemple d’application ajoute le choix de la langue à la fois pour la reconnaissance vocale et la traduction.

**Dernières modifications**

- [L’installation d’un mot clé](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws) a été simplifiée. Elle fait désormais partie de l’application et n’a pas besoin d’une installation distincte sur l’appareil.
- La reconnaissance des mots clés a changé, et deux événements sont pris en charge.
  - `RecognizingKeyword,` indique que le résultat de la reconnaissance vocale contient un mot clé (non vérifié).
  - `RecognizedKeyword` indique que la reconnaissance de mots clés a effectué la reconnaissance du mot clé donné.

## <a name="speech-devices-sdk-110-2018-nov-release"></a>SDK Speech Devices 1.1.0 : version de novembre 2018

- Mise à jour du composant [Kit de développement logiciel (SDK) Speech](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) vers la version 1.1.0. Pour plus d’informations, consultez ses [notes de publication](https://aka.ms/csspeech/whatsnew).
- Grâce à notre algorithme de traitement audio amélioré, la reconnaissance vocale en champ éloigné gagne en précision.
- Exemple d’application avec ajout de la prise en charge de la reconnaissance vocale en chinois.

## <a name="speech-devices-sdk-101-2018-oct-release"></a>SDK Speech Devices 1.0.1 : version d’octobre 2018

- Mise à jour du composant [Kit de développement logiciel (SDK) Speech](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) vers la version 1.0.1. Pour plus d’informations, consultez ses [notes de publication](https://aka.ms/csspeech/whatsnew).
- Notre nouvel algorithme de traitement audio améliore la précision de la reconnaissance vocale
- Un bogue de session audio de reconnaissance continue a été corrigé.

**Dernières modifications**

- Cette version contient plusieurs changements cassants. Pour plus d’informations concernant les API, consultez [cette page](https://aka.ms/csspeech/breakingchanges_1_0_0).
- Les fichiers de modèle KWS ne sont pas compatibles avec le Kit de développement logiciel (SDK) Speech Devices 1.0.1. Les fichiers de mots clés existants sont supprimés une fois les nouveaux fichiers de mots clés écrits sur l’appareil.

## <a name="speech-devices-sdk-050-2018-aug-release"></a>SDK Speech Devices 0.5.0 : Version d’août 2018

- Amélioration de la précision de la reconnaissance vocale grâce à la correction d’un bogue dans le code de traitement audio.
- Mise à jour du composant [Kit de développement logiciel (SDK) Speech](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) vers la version 0.5.0. Pour plus d’informations, consultez ses [notes de publication](releasenotes.md#cognitive-services-speech-sdk-050-2018-july-release).

## <a name="speech-devices-sdk-0212733-2018-may-release"></a>SDK Speech Devices 0.2.12733 : version de mai 2018

La première préversion publique du Kit de développement logiciel (SDK) Speech Devices de Cognitive Services.
