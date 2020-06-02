---
title: Assistants vocaux Windows - Directives en matière de confidentialité
titleSuffix: Azure Cognitive Services
description: Instructions relatives à l'activation vocale d'un agent vocal par défaut.
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: 436367ede4f4be323b5334a201b1c9fb8f7f28e8
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/09/2020
ms.locfileid: "82997324"
---
# <a name="privacy-guidelines-for-voice-assistants-on-windows"></a>Assistants vocaux Windows - Directives en matière de confidentialité

Il est important de fournir aux utilisateurs des informations claires sur la façon dont leurs données vocales sont collectées et utilisées, de même qu'il est important qu'ils aient le contrôle sur le mode de collecte de ces données. Ces facettes essentielles de la confidentialité (*divulgation* et *consentement*) sont particulièrement importantes pour les assistants vocaux intégrés à Windows compte tenu de la nature de leur utilisation, basée sur l'écoute permanente.

Les développeurs qui créent des assistants vocaux sous Windows doivent inclure dans leurs applications des éléments d'interface utilisateur clairs qui reflètent les capacités d'écoute de l'assistant.

> [!NOTE]
> En l'absence de divulgation et de consentement appropriés, y compris après les mises à jour de l'application, l'activation vocale de l'assistant peut devenir indisponible jusqu'à ce que les problèmes de confidentialité soient résolus. 

## <a name="minimum-requirements-for-feature-inclusion"></a>Exigences minimales en matière d'inclusion de fonctionnalités

Les utilisateurs de Windows peuvent voir et contrôler la disponibilité de leurs applications d'assistant sous **`Settings > Privacy > Voice activation`** .

 > [!div class="mx-imgBorder"]
 > [![privacy-app-listing](media/voice-assistants/windows_voice_assistant/privacy-app-listing.png "Entrée de paramètre de confidentialité d'activation vocale Windows pour une application d'assistant")](media/voice-assistants/windows_voice_assistant/privacy-app-listing.png#lightbox)

Pour être éligible à l'inclusion dans cette liste, une application doit :

1. indiquer clairement à ses utilisateurs qu'elle attend qu'un mot clé soit prononcé, même lorsqu'elle n'est pas en cours d'exécution, et préciser de quel mot clé il s'agit ;
1. inclure une description de la façon dont les données vocales d'un utilisateur seront utilisées, avec un lien ou une référence aux politiques de confidentialité pertinentes ;
1. informer les utilisateurs qu'en plus des paramètres de l'application, ils peuvent consulter et modifier leurs choix de confidentialité sous **`Settings > Privacy > Voice activation`** , en incluant éventuellement un lien de protocole vers `ms-settings:privacy-voiceactivation` pour un accès direct.

Une fois ces exigences satisfaites et l'approbation de Microsoft obtenue, l'application d'assistant apparaîtra dans la liste des applications à activation vocale, à condition qu'elle soit inscrite auprès des API `Windows.ApplicationModel.ConversationalAgent` et que les utilisateurs puissent accorder leur consentement à l'application pour l'activation par mot clé. Par défaut, ces deux paramètres sont `Off` et obligent l'utilisateur à se rendre manuellement sur la page Paramètres pour les activer.

> [!NOTE]
> Dans tous les cas, l'autorisation d'activation vocale nécessite une autorisation d'accès à un microphone. Si l'application d'assistant n'a pas accès à un microphone, elle n'est pas éligible à l'activation vocale et apparaît comme désactivée dans les paramètres de confidentialité de l'activation vocale.

## <a name="additional-requirements-for-inclusion-in-microphone-consent"></a>Exigences supplémentaires pour l'inclusion dans le consentement relatif au microphone

Les créateurs d'assistants qui souhaitent faciliter l'accès à l'activation vocale à leurs utilisateurs doivent satisfaire quelques exigences supplémentaires. Une fois ces exigences satisfaites, le paramètre d'activation vocale standard d'une application d'assistant, déverrouillé par l'appareil, sera `On` par défaut, à condition que l'accès au microphone ait été accordé à l'application. Il n'est ainsi plus nécessaire d'accéder à la section Paramètres avant d'activer vocalement un assistant.

En guise d'exigences supplémentaires, une application d'assistant doit :

1. **avant** de demander le consentement relatif au microphone (par exemple, à l'aide de l'API `AppCapability.RequestAccessAsync`), indiquer clairement à l'utilisateur que l'application d'assistant attend qu'il prononce un mot clé, même lorsque l'application n'est pas en cours d'exécution, et souhaite obtenir son consentement ;
2. inclure toutes les informations pertinentes en matière d'utilisation des données et de politiques de confidentialité **avant** de demander l'accès au microphone ou d'utiliser les API `Windows.ApplicationModel.ConversationalAgent` ;
3. éviter toute formulation directive ou suggestive (par exemple, « Cliquez sur Oui à l'invite suivante ») dans le flux d'expérience divulguant le comportement de capture audio et demandant l'autorisation.

Une fois ces conditions remplies, l'application d'assistant apparaît comme `enabled` dans la liste des applications éligibles à l'activation vocale, à condition que l'accès au microphone soit accordé.

> [!NOTE]
> L'activation vocale par-dessus le verrouillage n'est pas éligible à l'activation automatique avec accès au microphone et oblige toujours l'utilisateur à se rendre sur la page de confidentialité de l'activation vocale pour activer l'accès par-dessus le verrouillage d'un assistant.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur les meilleures pratiques relatives aux assistants vocaux Windows](windows-voice-assistants-best-practices.md)