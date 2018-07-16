---
title: Documentation du Kit de développement logiciel (SDK) Speech de Cognitive Services | Microsoft Docs
description: Notes de publication - ce qui a changé dans les versions les plus récentes
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: 0b1559d288380cf3d0c180a225278cc13d22a5d0
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/07/2018
ms.locfileid: "35378997"
---
# <a name="release-notes"></a>Notes de publication

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>Kit de développement logiciel (SDK) Speech de Cognitive Services version 0.4.0 : juin 2018

**Modifications fonctionnelles**

- AudioInputStream

  Un module de reconnaissance peut désormais consommer un flux comme la source audio. Pour obtenir des informations détaillées, consultez le [guide pratique](how-to-use-audio-input-streams.md).

- Format de sortie détaillé

  Lors de la création d’un `SpeechRecognizer`, vous pouvez demander le format de sortie `Detailed` ou `Simple`. Le `DetailedSpeechRecognitionResult` contient un score de confiance, le texte reconnu, la forme lexicale brute, la forme normalisée et la forme normalisée avec les blasphèmes masqués.

**Modification critique**

- Remplacez `SpeechRecognitionResult.Text` par `SpeechRecognitionResult.RecognizedText` en C#.

**Résolution des bogues**

- Résolvez un problème de rappel possible dans la couche USP lors de l’arrêt.

- Si un module de reconnaissance a utilisé un fichier d’entrée audio, il a été placé sur le descripteur de fichier plus longtemps que nécessaire.

- Plusieurs blocages supprimés entre la pompe de messages et le module de reconnaissance.

- Expiration du délai de déclenchement d’un résultat `NoMatch` lors de la réponse du service.

- Les bibliothèques Media Foundation sur Windows sont chargées en différé. Cette bibliothèque n’est requise que pour l’entrée du microphone.

- La vitesse de chargement de données audio est limitée à environ deux fois la vitesse audio d’origine.

- Sous Windows, les noms d’assemblys C# .NET sont maintenant forts.

- Correction de la documentation : `Region` est obligatoire pour créer un module de reconnaissance.

D’autres exemples ont été ajoutés et sont constamment mis à jour. Pour obtenir la dernière série d’exemples, consultez le [dépôt GitHub d’exemples pour le Kit de développement logiciel (SDK) Speech](https://aka.ms/csspeech/samples).

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>Kit de développement logiciel (SDK) Speech de Cognitive Services version 0.2.12733 : mai 2018

La première préversion publique du Kit de développement logiciel (SDK) Speech de Cognitive Services.
