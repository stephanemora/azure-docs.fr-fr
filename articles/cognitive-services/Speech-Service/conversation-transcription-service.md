---
title: Service de Transcription de conversation - Services de reconnaissance vocale
titleSuffix: Azure Cognitive Services
description: Le Service de Transcription de Conversation est une fonctionnalité avancée des Services de reconnaissance vocale qui combine la reconnaissance vocale en temps réel, identification de l’orateur et diarization. Service de Transcription de conversation est idéale pour la transcription des réunions en personne, avec la possibilité de faire la distinction des haut-parleurs, il vous permet de savoir qui a dit quoi et quand, permettant aux participants de vous concentrer sur la réunion et rapidement suivent les étapes suivantes. Cette fonctionnalité améliore également l’accessibilité. Avec la transcription, vous pouvez activement inciter les participants souffrant de TROUBLES AUDITIFS.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 2c3269a9e57c0392e152fedfb640b9fae91a4989
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025934"
---
# <a name="what-is-the-conversation-transcription-service"></a>Qu’est le Service de Transcription de Conversation ?

Le Service de Transcription de Conversation est une fonctionnalité avancée des Services de reconnaissance vocale qui combine la reconnaissance vocale en temps réel, identification de l’orateur et diarization. Service de Transcription de conversation est idéale pour la transcription des réunions en personne, avec la possibilité de faire la distinction des haut-parleurs, il vous permet de savoir qui a dit quoi et quand, permettant aux participants de vous concentrer sur la réunion et rapidement suivent les étapes suivantes. Cette fonctionnalité améliore également l’accessibilité. Avec la transcription, vous pouvez activement inciter les participants souffrant de TROUBLES AUDITIFS.   

Service de Transcription de conversation offre reconnaissance précise avec des modèles vocaux personnalisables que vous pouvez adapter pour comprendre le vocabulaire propre à l’entreprise et du secteur. En outre, vous pouvez associer des Service de Transcription de Conversation avec le SDK d’appareils vocale pour optimiser l’expérience pour les appareils multi microphone.

>[!NOTE]
> Actuellement, Service de Transcription de Conversation est recommandé pour les petites réunions. Si vous souhaitez étendre le Service de Transcription de Conversation pour les réunions de grande taille à l’échelle, veuillez nous contacter.

Ce diagramme illustre le matériel, les logiciels et les services qui fonctionnent avec le Service de Transcription de Conversation.

![Le diagramme du Service importation Conversation Transcription](media/scenarios/conversation-transcription-service.png)

>[!IMPORTANT]
> Un tableau de sept microphone circulaire avec la configuration de la géométrie spécifique est requis. Pour plus d’informations de conception et de spécification, consultez [Microphone de SDK Microsoft Speech appareil](https://aka.ms/cts/microphone). Pour en savoir plus, ou achetez un kit de développement, consultez [obtenir Microsoft Speech Device SDK](https://aka.ms/cts/getsdk).

## <a name="get-started-with-conversation-transcription"></a>Prise en main la Transcription de Conversation

Il existe trois étapes que vous devez suivre pour bien démarrer avec le Service de Transcription de Conversation.

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
> Le Service de Transcription de Conversation est actuellement disponible dans les régions suivantes : `centralus` et `eastasia`.

* [Spécification de REST](https://aka.ms/cts/signaturegenservice)
* [Comment utiliser le Service de Transcription de Conversation](https://aka.ms/cts/howto)

## <a name="transcribe-and-identify-speakers"></a>Transcrire et identifiez les orateurs

Le Service de Transcription de Conversation attend des flux audio multicanal et profils utilisateur en tant qu’entrées pour générer des transcriptions et identifier les haut-parleurs. Les données de profil utilisateur et d’audio sont envoyées au Service de Transcription de Conversation à l’aide du SDK d’appareils vocale. Comme mentionné précédemment, un tableau de sept microphone circulaire et les appareils de Speech SDK doit utiliser le Service de Transcription de Conversation.

>[!NOTE]
> Pour plus d’informations de conception et de spécification, consultez [Microphone de SDK Microsoft Speech appareil](https://aka.ms/cts/microphone). Pour en savoir plus, ou achetez un kit de développement, consultez [obtenir Microsoft Speech Device SDK](https://aka.ms/cts/getsdk).

Pour savoir comment utiliser le Service de Transcription de Conversation avec les appareils de Speech SDK, consultez [comment utiliser le service de transcription de conversation](https://aka.ms/cts/howto).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur les appareils de Speech SDK](speech-devices-sdk.md)
