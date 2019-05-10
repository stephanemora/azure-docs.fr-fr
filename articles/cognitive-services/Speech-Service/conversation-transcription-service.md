---
title: Transcription de conversation - Services de reconnaissance vocale
titleSuffix: Azure Cognitive Services
description: Transcription de conversation est une fonctionnalité avancée des Services de reconnaissance vocale qui combine la reconnaissance vocale en temps réel, identification de l’orateur et diarization. Transcription de conversation est idéale pour la transcription des réunions en personne, avec la possibilité de faire la distinction des haut-parleurs, il vous permet de savoir qui a dit quoi et quand, permettant aux participants de vous concentrer sur la réunion et rapidement suivent les étapes suivantes. Cette fonctionnalité améliore également l’accessibilité. Avec la transcription, vous pouvez activement inciter les participants souffrant de TROUBLES AUDITIFS.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: erhopf
ms.openlocfilehash: eebe3ed15d21b08b208667a571bd59eac98a674d
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65190272"
---
# <a name="what-is-conversation-transcription"></a>Quelle est la Transcription de Conversation ?

Transcription de conversation est une fonctionnalité avancée des Services de reconnaissance vocale qui combine la reconnaissance vocale en temps réel, identification de l’orateur et diarization. Transcription de conversation est idéale pour la transcription des réunions en personne, avec la possibilité de faire la distinction des haut-parleurs, il vous permet de savoir qui a dit quoi et quand, permettant aux participants de vous concentrer sur la réunion et rapidement suivent les étapes suivantes. Cette fonctionnalité améliore également l’accessibilité. Avec la transcription, vous pouvez activement inciter les participants souffrant de TROUBLES AUDITIFS.   

Transcription de conversation offre reconnaissance précise avec des modèles vocaux personnalisables que vous pouvez adapter pour comprendre le vocabulaire propre à l’entreprise et du secteur. En outre, vous pouvez associer la Transcription de Conversation avec le SDK d’appareils vocale pour optimiser l’expérience pour les appareils multi microphone.

>[!NOTE]
> Actuellement, la Transcription de Conversation est recommandée pour les petites réunions. Si vous souhaitez étendre la Transcription de Conversation pour les réunions de grande taille à l’échelle, veuillez nous contacter.

Ce diagramme illustre le matériel, les logiciels et les services qui fonctionnent avec la Transcription de Conversation.

![Le diagramme de Transcription de Conversation importation](media/scenarios/conversation-transcription-service.png)

>[!IMPORTANT]
> Un tableau de sept microphone circulaire avec la configuration de la géométrie spécifique est requis. Pour plus d’informations de conception et de spécification, consultez [Microphone de SDK Microsoft Speech appareil](https://aka.ms/cts/microphone). Pour en savoir plus, ou achetez un kit de développement, consultez [obtenir Microsoft Speech Device SDK](https://aka.ms/cts/getsdk).

## <a name="get-started-with-conversation-transcription"></a>Prise en main la Transcription de Conversation

Il existe trois étapes que vous devez suivre pour bien démarrer avec la Transcription de Conversation.

1. Collecter des exemples de voix auprès des utilisateurs.
2. Générer des profils utilisateur à l’aide d’exemples de voix utilisateur
3. Le Speech SDK permet d’identifier les utilisateurs (intervenants) et transcription vocale

## <a name="collect-user-voice-samples"></a>Collecter des exemples de voix utilisateur

La première étape consiste à collecter des enregistrements audio à partir de chaque utilisateur. Vocale de l’utilisateur doit être enregistrée dans un endroit calme sans bruit de fond. La longueur minimale recommandée pour chaque échantillon audio est comprise entre 30 secondes et de deux minutes. Les échantillons audio plus long entraîne une meilleure précision lors de l’identification des haut-parleurs. Fichier audio doit être un canal mono avec un taux d’échantillonnage 16 KHz.

Après les instructions mentionnées précédemment, comment audio est enregistré et stocké vous revient : une base de données sécurisé est recommandé. Dans la section suivante, nous allons examiner comment cette audio sert à générer des profils utilisateur qui sont utilisés avec le Speech SDK pour reconnaître les haut-parleurs.

## <a name="generate-user-profiles"></a>Générer des profils utilisateur

Ensuite, vous devez envoyer les enregistrements audio que vous avez collecté pour le Service de génération de signatures pour valider l’audio et de générer des profils utilisateur. Le [Service de génération de signatures](https://aka.ms/cts/signaturegenservice) est un ensemble d’API REST, qui vous permettent de générez et récupérer les profils utilisateur.

Pour créer un profil utilisateur, vous devez utiliser le `GenerateVoiceSignature` API. Détails de la spécification et exemple de code sont disponibles :

> [!NOTE]
> Transcription de conversation n’est actuellement disponible dans « en-US » et « zh-CN » dans les régions suivantes : `centralus` et `eastasia`.

* [Spécification de REST](https://aka.ms/cts/signaturegenservice)
* [L’utilisation de Transcription de Conversation](https://aka.ms/cts/howto)

## <a name="transcribe-and-identify-speakers"></a>Transcrire et identifiez les orateurs

Transcription de conversation attend des flux audio multicanal et profils utilisateur en tant qu’entrées pour générer des transcriptions et identifier les haut-parleurs. Les données de profil utilisateur et d’audio sont envoyées au service de Transcription de Conversation à l’aide du SDK d’appareils vocale. Comme mentionné précédemment, un tableau de sept microphone circulaire et les appareils de Speech SDK doit utiliser la Transcription de Conversation.

>[!NOTE]
> Pour plus d’informations de conception et de spécification, consultez [Microphone de SDK Microsoft Speech appareil](https://aka.ms/cts/microphone). Pour en savoir plus, ou achetez un kit de développement, consultez [obtenir Microsoft Speech Device SDK](https://aka.ms/cts/getsdk).

Pour savoir comment utiliser la Transcription de Conversation avec le SDK d’appareils de reconnaissance vocale, consultez [l’utilisation de transcription de conversation](https://aka.ms/cts/howto).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur les appareils de Speech SDK](speech-devices-sdk.md)
