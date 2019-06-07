---
title: Documentation du Kit de développement logiciel (SDK) Speech Devices
titleSuffix: Azure Cognitive Services
description: Notes de publication - ce qui a changé dans les versions les plus récentes
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: wellsi
ms.openlocfilehash: 1c91cde45a6a420376af36f70487adf7fe0ee83a
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/07/2019
ms.locfileid: "66751815"
---
# <a name="release-notes-of-cognitive-services-speech-devices-sdk"></a>Notes de publication du Kit de développement logiciel (SDK) Speech Devices de Cognitive Services
Les sections suivantes dressent la liste des modifications dans les versions les plus récentes.

## <a name="speech-devices-sdk-151"></a>Appareils de Speech SDK 1.5.1 :

*   Inclure [Transcription de Conversation](conversation-transcription-service.md) dans l’exemple d’application.
*   Mise à jour le [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) composant vers la version 1.5.1. Pour plus d’informations, consultez ses [notes de publication](https://aka.ms/csspeech/whatsnew).

## <a name="cognitive-services-speech-devices-sdk-150-2019-may-release"></a>Appareils de reconnaissance vocale de COGNITIVE Services SDK 1.5.0 : Mise en production mai 2019

*   Appareils de Speech SDK est désormais GA et n’est plus une préversion contrôlée.
*   Mise à jour le [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) composant vers la version 1.5.0. Pour plus d’informations, consultez ses [notes de publication](https://aka.ms/csspeech/whatsnew).
*   Nouvelle technologie de mise en éveil word apporte des améliorations de la qualité, consultez les modifications avec rupture.
*   Nouveau pipeline de traitement audio pour la reconnaissance lointain champ améliorée.

**Dernières modifications**

*   En raison de la nouvelle technologie de word de mise en éveil tous les mots de mise en éveil doivent être recréées sur notre portail de word améliorées de mise en éveil. Pour supprimer complètement les anciens mots clés à partir de l’appareil désinstaller l’ancienne application.
    - adb uninstall com.microsoft.coginitiveservices.speech.samples.sdsdkstarterapp

## <a name="cognitive-services-speech-devices-sdk-140-2019-apr-release"></a>Appareils vocale de COGNITIVE Services SDK 1.4.0 : Mise en production avril 2019 

* Mise à jour le [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) composant vers la version 1.4.0. Pour plus d’informations, consultez ses [notes de publication](https://aka.ms/csspeech/whatsnew). 

## <a name="cognitive-services-speech-devices-sdk-131-2019-mar-release"></a>Appareils de reconnaissance vocale de COGNITIVE Services SDK 1.3.1 : Version de mars de 2019 

* Mise à jour le [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) composant vers la version 1.3.1. Pour plus d’informations, consultez ses [notes de publication](https://aka.ms/csspeech/whatsnew). 
*   Word de mise en éveil mis à jour gestion, voir les modifications avec rupture.
*   Exemple d’application ajoute le choix du langage pour la reconnaissance vocale et la traduction.

**Dernières modifications** 

*   [L’installation d’un mot de mise en éveil](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws) a été simplifié, il fait désormais partie de l’application et n’a pas besoin de l’installation distincte sur l’appareil.
*   La reconnaissance de word de mise en éveil a changé, et deux événements sont pris en charge.
    - RecognizingKeyword, indique le résultat de reconnaissance vocale contient du texte de mot clé (non vérifié).
    - RecognizedKeyword, indique cette reconnaissance du mot clé terminée reconnaissant le mot-clé donné.


## <a name="cognitive-services-speech-devices-sdk-110-2018-nov-release"></a>Kit SDK Speech Devices de Cognitive Services version 1.1.0 : version de novembre 2018 

* Mise à jour du composant [Kit de développement logiciel (SDK) Speech](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) vers la version 1.1.0. Pour plus d’informations, consultez ses [notes de publication](https://aka.ms/csspeech/whatsnew). 
* Grâce à notre algorithme de traitement audio amélioré, la reconnaissance vocale en champ éloigné gagne en précision.
* Exemple d’application avec ajout de la prise en charge de la reconnaissance vocale en chinois.

## <a name="cognitive-services-speech-devices-sdk-101-2018-oct-release"></a>Kit SDK Speech Devices de Cognitive Services version 1.0.1 : version d’octobre 2018 

* Mise à jour du composant [Kit de développement logiciel (SDK) Speech](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) vers la version 1.0.1. Pour plus d’informations, consultez ses [notes de publication](https://aka.ms/csspeech/whatsnew). 
* Notre nouvel algorithme de traitement audio améliore la précision de la reconnaissance vocale  
* Un bogue de session audio de reconnaissance continue a été corrigé.

**Dernières modifications** 

* Cette version contient plusieurs changements cassants. Pour plus d’informations concernant les API, consultez [cette page](https://aka.ms/csspeech/breakingchanges_1_0_0). 
* Les fichiers de modèle KWS ne sont pas compatibles avec le Kit de développement logiciel (SDK) Speech Devices 1.0.1. Les fichiers Wake Word existants sont supprimés une fois les nouveaux fichiers Wake Word écrits sur l’appareil. 

## <a name="cognitive-services-speech-devices-sdk-050-2018-aug-release"></a>Kit SDK Speech Devices de Cognitive Services version 0.5.0 : Version d’août 2018

* Amélioration de la précision de la reconnaissance vocale grâce à la correction d’un bogue dans le code de traitement audio.
* Mise à jour du composant [Kit de développement logiciel (SDK) Speech](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) vers la version 0.5.0. Pour plus d’informations, consultez ses [notes de publication](releasenotes.md#cognitive-services-speech-sdk-050-2018-july-release).

## <a name="cognitive-services-speech-devices-sdk-0212733-2018-may-release"></a>Kit SDK Speech Devices de Cognitive Services version 0.2.12733 : version de mai 2018

La première préversion publique du Kit de développement logiciel (SDK) Speech Devices de Cognitive Services.
