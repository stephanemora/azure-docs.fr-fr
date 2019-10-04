---
title: Transcription de conversation – Service Speech
titleSuffix: Azure Cognitive Services
description: La transcription de conversation est une fonctionnalité avancée de Speech Services qui combine la reconnaissance vocale en temps réel, l’identification de l’orateur et la diarisation. La transcription de conversation est idéale pour transcrire les réunions en direct, avec la possibilité de faire la distinction entre les orateurs. Elle vous permet de savoir qui a dit quoi et quand, ce qui permet aux participants de se concentrer sur la réunion et de passer rapidement aux étapes suivantes. Cette fonctionnalité permet aussi d’améliorer l’accessibilité. Avec la transcription, vous pouvez faire participer activement les personnes présentant des troubles auditifs.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: acbf82335523ee226f4b899180f0f22cb94cca5e
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562875"
---
# <a name="what-is-conversation-transcription"></a>Qu’est-ce que la transcription de conversation ?

La transcription de conversation est une fonctionnalité avancée de Speech Services qui combine la reconnaissance vocale en temps réel, l’identification de l’orateur et la diarisation. La transcription de conversation est idéale pour transcrire les réunions en direct, avec la possibilité de faire la distinction entre les orateurs. Elle vous permet de savoir qui a dit quoi et quand, ce qui permet aux participants de se concentrer sur la réunion et de passer rapidement aux étapes suivantes. Cette fonctionnalité permet aussi d’améliorer l’accessibilité. Avec la transcription, vous pouvez faire participer activement les personnes présentant des troubles auditifs.   

La transcription de conversation assure une reconnaissance précise à l’aide de modèles vocaux personnalisables que vous pouvez adapter au vocabulaire propre à un secteur d’activité ou à une entreprise. De plus, vous pouvez associer la transcription de conversation au kit SDK Speech Devices pour optimiser l’expérience avec les appareils dotés de plusieurs micros.

>[!NOTE]
> Pour l’heure, la transcription de conversation est recommandée pour les réunions en petit comité. Si vous avez besoin d’une extension de la transcription de conversation pour prendre en charge des réunions de plus grande ampleur, contactez-nous.

Ce schéma illustre le matériel, les logiciels et les services qui fonctionnent avec la transcription de conversation.

![Schéma d’importation de la transcription de conversation](media/scenarios/conversation-transcription-service.png)

>[!IMPORTANT]
> Un ensemble de sept micros circulaires doté d’une configuration de géométrie spécifique est nécessaire. Pour obtenir des détails sur les spécifications et la conception, consultez [Microphones pour le SDK Microsoft Speech Devices](https://aka.ms/cts/microphone). Pour obtenir des informations complémentaires ou pour acheter un kit de développement, consultez [Obtenir le SDK Microsoft Speech Devices](https://aka.ms/cts/getsdk).

## <a name="get-started-with-conversation-transcription"></a>Commencer avec la transcription de conversation

Avant d’utiliser la transcription de conversation, vous devez effectuer trois étapes.

1. Collectez des échantillons vocaux d’utilisateurs.
2. Générez des profils utilisateur à l’aide des échantillons vocaux des utilisateurs.
3. Utilisez le SDK Speech pour identifier les utilisateurs (orateurs) et transcrire la parole.

## <a name="collect-user-voice-samples"></a>Collecter des échantillons vocaux d’utilisateurs

Dans un premier temps, collectez des enregistrements audio de chaque utilisateur. La voix des utilisateurs doit être enregistrée dans un environnement calme sans bruit de fond. La longueur recommandée de chaque échantillon audio se situe entre 30 secondes et deux minutes. Les échantillons audio plus longs offrent une meilleure précision en termes d’identification des orateurs. Le contenu audio doit être monocanal avec un taux d’échantillonnage de 16 KHz.

Au-delà des recommandations mentionnées ci-dessus, il vous appartient de choisir la façon dont vous enregistrez et stockez le contenu audio. Cependant, il est recommandé d’utiliser une base de données sécurisée. Dans la section suivante, nous allons examiner la façon dont le contenu audio est utilisé pour générer les profils utilisateur qui vont permettre au SDK Speech de reconnaître les orateurs.

## <a name="generate-user-profiles"></a>Générer des profils utilisateur

La prochaine étape consiste à envoyer les enregistrements audio que vous avez collectés au service de génération de signatures pour valider le contenu audio et générer des profils utilisateur. Le [service de génération de signatures](https://aka.ms/cts/signaturegenservice) est un ensemble d’API REST qui permettent de générer et de récupérer des profils utilisateur.

Pour créer un profil utilisateur, vous devez utiliser l’API `GenerateVoiceSignature`. Des détails de spécification et un exemple de code sont disponibles :

> [!NOTE]
> La transcription de conversation est actuellement disponible dans les langues « en-US » et « zh-CN » dans les régions suivantes : `centralus` et `eastasia`.

* [Spécification REST](https://aka.ms/cts/signaturegenservice)
* [Guide pratique pour utiliser la transcription de conversation](https://aka.ms/cts/howto)

## <a name="transcribe-and-identify-speakers"></a>Transcrire et identifier les orateurs

La transcription de conversation a besoin de flux audio multicanaux et de profils utilisateur en entrée pour générer des transcriptions et identifier les orateurs. Les données audio et de profil utilisateur sont envoyées au service de transcription de conversation à l’aide du SDK Speech Devices. Comme indiqué précédemment, la transcription de conversation nécessite un ensemble circulaire de sept micros et le SDK Speech Devices.

>[!NOTE]
> Pour obtenir des détails sur les spécifications et la conception, consultez [Microphones pour le SDK Microsoft Speech Devices](https://aka.ms/cts/microphone). Pour obtenir des informations complémentaires ou pour acheter un kit de développement, consultez [Obtenir le SDK Microsoft Speech Devices](https://aka.ms/cts/getsdk).

Pour savoir comment utiliser la transcription de conversation avec le SDK Speech Devices, consultez [Guide pratique pour utiliser la transcription de conversation](https://aka.ms/cts/howto).


## <a name="quick-start-with-a-sample-app"></a>Guide de démarrage rapide avec un exemple d’application

Le SDK Microsoft Speech Devices propose un guide de démarrage rapide avec un exemple d’application pour tous les appareils. La transcription de conversation en est un exemple. Vous pouvez la trouver dans le [guide de démarrage rapide du SDK Speech Devices pour Android](https://aka.ms/sdsdk-quickstart) avec l’exemple d’application et son code source pour référence.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur le SDK Speech Devices](speech-devices-sdk.md)
