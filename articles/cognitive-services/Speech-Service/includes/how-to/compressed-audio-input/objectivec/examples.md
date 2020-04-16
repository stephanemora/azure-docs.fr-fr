---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: f0baac79bfbfc06b24d692caef87a20013c4654c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81421679"
---
Pour diffuser en streaming des données dans un format audio compressé sur le service Speech, créez un élément `SPXPullAudioInputStream` ou `SPXPushAudioInputStream`.

L’extrait de code suivant montre comment créer un `SPXAudioConfiguration` à partir d’une instance de `SPXPushAudioInputStream`, en spécifiant mp3 comme format de compression du flux.

[!code-objectivec[Set up the input stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=67-76&highlight=2-11)]

L’extrait de code suivant montre comment les données audio compressées peuvent être lues à partir d’un fichier pour alimenter `SPXPushAudioInputStream`.

[!code-objectivec[Push compressed audio data into the stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=106-150&highlight=19-44)]
