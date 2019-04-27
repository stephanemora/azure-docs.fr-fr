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
ms.date: 08/07/2018
ms.author: gracez
ms.openlocfilehash: 33a03b24de56ab1090cc8e07c9619eda17f33e27
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61293533"
---
# <a name="release-notes-of-cognitive-services-speech-devices-sdk"></a>Notes de publication du Kit de développement logiciel (SDK) Speech Devices de Cognitive Services

Les sections suivantes dressent la liste des modifications dans les versions les plus récentes.

## <a name="cognitive-services-speech-devices-sdk-140-2019-apr-release"></a>Appareils vocale de COGNITIVE Services SDK 1.4.0 : Mise en production avril 2019 

* Mise à jour le [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) composant vers la version 1.4.0. Pour plus d’informations, consultez ses [notes de publication](https://aka.ms/csspeech/whatsnew). 

## <a name="cognitive-services-speech-devices-sdk-131-2019-mar-release"></a>Appareils de reconnaissance vocale de COGNITIVE Services SDK 1.3.1 : Version de mars de 2019 

* Mise à jour le [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) composant vers la version 1.3.1. Pour plus d’informations, consultez ses [notes de publication](https://aka.ms/csspeech/whatsnew). 
*   Word de mise en éveil mis à jour gestion, voir les modifications avec rupture.
*   Exemple d’application ajoute le choix du langage pour la reconnaissance vocale et la traduction.

**Dernières modifications** 

*   [L’installation d’un mot de mise en éveil](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-qsg#run-a-sample-application) a été simplifié, il fait désormais partie de l’application et n’a pas besoin de l’installation distincte sur l’appareil.
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
