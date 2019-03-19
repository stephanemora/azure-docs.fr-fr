---
title: Migrer à partir de la reconnaissance vocale Bing pour les Services Azure de reconnaissance vocale
titleSuffix: Azure Cognitive Services
description: Apprenez à migrer à partir d’un abonnement existant de la reconnaissance vocale Bing pour les Services de reconnaissance vocale de Azure.
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: gracez
ms.openlocfilehash: 6324da55c8af4934185fa39a106939844788adba
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57848964"
---
# <a name="migrate-from-bing-speech-to-the-speech-service"></a>Migrer de la Reconnaissance vocale Bing vers le Service Speech

Référez-vous à cet article pour migrer vos applications de l’API Reconnaissance vocale Bing vers le Service Speech.

Cet article décrit les différences entre les API de reconnaissance vocale Bing et les Services de reconnaissance vocale et suggère des stratégies pour migrer vos applications. Votre clé d’abonnement API reconnaissance vocale Bing ne fonctionne pas avec le Service de reconnaissance vocale ; vous aurez besoin d’un nouvel abonnement de Services de reconnaissance vocale.

Une clé d’abonnement Services de reconnaissance vocale unique accorde l’accès aux fonctionnalités suivantes. Elles sont mesurées séparément pour que seules les fonctions que vous utilisez vous soient facturées.

* [Reconnaissance vocale](speech-to-text.md)
* [Reconnaissance vocale personnalisée](https://cris.ai)
* [Synthèse vocale](text-to-speech.md)
* [Voix de synthèse vocale personnalisées](how-to-customize-voice-font.md)
* [Traduction vocale](speech-translation.md) (n’inclut pas la [traduction de texte](../translator/translator-info-overview.md))

Le [Kit de développement logiciel (SDK) Speech](speech-sdk.md) constitue un remplacement fonctionnel des bibliothèques clientes de Reconnaissance vocale Bing, mais utilise une API différente.

## <a name="comparison-of-features"></a>Comparaison des fonctionnalités

Les Services de reconnaissance vocale sont en grande partie similaires à la reconnaissance vocale Bing, avec les différences suivantes.

Fonctionnalité | Reconnaissance vocale Bing | Services Speech | Détails
-|-|-|-
KIT DE DÉVELOPPEMENT LOGICIEL C++ | :heavy_minus_sign: | :heavy_check_mark: | Services de reconnaissance vocale prend en charge Windows et Linux.
Kit de développement logiciel (SDK) Java | :heavy_check_mark: | :heavy_check_mark: | Services de reconnaissance vocale prend en charge les appareils Android et vocale.
Kit de développement logiciel (SDK) C# | :heavy_check_mark: | :heavy_check_mark: | Services de reconnaissance vocale prend en charge Windows 10, Universal Windows Platform (UWP) et .NET Standard 2.0.
Reconnaissance vocale continue | 10 minutes | Illimitée (avec le Kit de développement logiciel) | Les protocoles de WebSockets de Services de reconnaissance vocale et de reconnaissance vocale Bing prend en charge jusqu'à 10 minutes par appel. Toutefois, le Kit de développement logiciel (SDK) Speech reconnecte ou déconnecte à l’issue du délai d’expiration.
Résultats partiels ou intermédiaires | :heavy_check_mark: | :heavy_check_mark: | Avec le protocole WebSockets ou le Kit de développement logiciel (SDK).
Modèles vocaux personnalisés | :heavy_check_mark: | :heavy_check_mark: | La Reconnaissance vocale Bing nécessite un abonnement Custom Speech séparé.
Polices de la voix personnalisées | :heavy_check_mark: | :heavy_check_mark: | La Reconnaissance vocale Bing nécessite un abonnement Custom Voice séparé.
Voix 24-KHz | :heavy_minus_sign: | :heavy_check_mark:
Reconnaissance de l’intention vocale | Nécessite un appel d’API LUIS séparé | Intégrée (avec Kit de développement logiciel (SDK)) |  Une clé LUIS peut être utilisée avec le service Speech Service.
Reconnaissance de l’intention simple | :heavy_minus_sign: | :heavy_check_mark:
Transcription par lots de fichiers audio longs | :heavy_minus_sign: | :heavy_check_mark:
Mode de reconnaissance | Manuel via URI de point de terminaison | Automatique | Le mode de reconnaissance n’est pas disponible dans Speech Service.
Lieu du point de terminaison | Globale | Zones géographiques | Les points de terminaison régionaux améliorent la latence.
API REST | :heavy_check_mark: | :heavy_check_mark: | Les API REST des Services vocale sont compatibles avec reconnaissance vocale de Bing (point de terminaison différent). Les API REST prennent en charge les fonctionnalités de synthèse vocale et de reconnaissance vocale limitée.
Protocoles WebSocket | :heavy_check_mark: | :heavy_check_mark: | L’API de WebSockets de Services de reconnaissance vocale est compatible avec la reconnaissance vocale de Bing (point de terminaison différent). Migrez vers le Kit de développement logiciel (SDK) Speech si possible afin de simplifier votre code.
Appels d’API de service à service | :heavy_check_mark: | :heavy_minus_sign: | Fournis dans la Reconnaissance vocale Bing via la bibliothèque de services C#.
Kit de développement logiciel (SDK) open source | :heavy_check_mark: | :heavy_minus_sign: |

Les Services de reconnaissance vocale utilisent un modèle de tarification basé sur le temps (plutôt qu’un modèle basé sur des transactions). Consultez [tarification des Services de reconnaissance vocale](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) pour plus d’informations.

## <a name="migration-strategies"></a>Stratégies de migration

Si vous ou votre organisation ont des applications en développement ou de production qui utilisent une API de reconnaissance vocale Bing, vous devez les mettre à jour pour utiliser les Services de reconnaissance vocale dès que possible. Consultez le [documentation des Services de reconnaissance vocale](index.yml) pour les kits de développement logiciel, des exemples de code et des didacticiels disponibles.

Les Services de reconnaissance vocale [API REST](rest-apis.md) sont compatibles avec les API de reconnaissance vocale Bing. Si vous utilisez actuellement l’API REST de reconnaissance vocale Bing, vous devez uniquement modifier le point de terminaison REST, puis basculez vers une clé d’abonnement Services de reconnaissance vocale.

Les protocoles WebSocket de Services de reconnaissance vocale sont également compatibles avec ceux utilisés par la reconnaissance vocale Bing. Pour tout nouveau développement, nous vous recommandons d’utiliser le kit de développement logiciel (SDK) Speech plutôt que WebSockets. Il est judicieux d’également migrer le code existant vers le kit de développement logiciel (SDK). Cependant, comme avec les API REST, le code existant utilisant la Reconnaissance vocale Bing via des WebSockets nécessite uniquement une modification du point de terminaison et une clé mise à jour.

Si vous utilisez une bibliothèque de client Reconnaissance vocale Bing pour un langage de programmation spécifique, la migration vers le [Kit de développement logiciel (SDK) Speech](speech-sdk.md) nécessite l’apport de modifications à votre application, car l’API est différente. Le kit de développement logiciel (SDK) Speech peut simplifier votre code tout en vous donnant accès à de nouvelles fonctionnalités.

Actuellement, le Kit de développement logiciel (SDK) Speech prend en charge C# (Windows 10, UWP, .NET Standard), Java (Android et appareils personnalisés), Objective C (iOS), C++ (Windows et Linux) et JavaScript. Les API sont similaires sur toutes les plateformes, et facilitent le développement multi-plateforme.

Les Services de reconnaissance vocale n’offrent pas un point de terminaison global. Vous devez déterminer si votre application fonctionne efficacement lorsqu’elle utilise un point de terminaison régional unique pour tout son trafic. Autrement, utilisez la géolocalisation pour déterminer le point de terminaison plus efficace. Vous avez besoin d’un abonnement distinct de Services de reconnaissance vocale dans chaque région que vous utilisez.

Si votre application utilise des connexions de longue durée et ne peut pas utiliser un kit SDK disponible, vous pouvez utiliser une connexion WebSockets. Gérez la limite de délai d’expiration de 10 minutes en vous reconnectant aux moments appropriés.

Pour prendre en main du kit de développement logiciel (SDK) Speech :

1. Téléchargez le [Kit de développement logiciel (SDK) Speech](speech-sdk.md).
1. Travail via les Services de reconnaissance vocale [guides de démarrage rapide](quickstart-csharp-dotnet-windows.md) et [didacticiels](how-to-recognize-intents-from-speech-csharp.md). Consultez également les [exemples de code](samples.md) pour vous familiariser avec les nouvelles API.
1. Mettre à jour votre application pour utiliser les Services de reconnaissance vocale.

## <a name="support"></a>Support

Les clients de Reconnaissance vocale Bing doivent contacter le service clientèle en ouvrant un [ticket de support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Vous pouvez également nous contacter si vous avez besoin d’un [plan de support technique](https://azure.microsoft.com/support/plans/).

Pour la prise en charge du Service de reconnaissance vocale, SDK et API, visitez les Services de reconnaissance vocale [page de prise en charge](support.md).

## <a name="next-steps"></a>Étapes suivantes

* [Essayez gratuitement les Services de reconnaissance vocale](get-started.md)
* [Démarrage rapide : Reconnaissance vocale dans une application UWP à l’aide du kit SDK Speech](quickstart-csharp-uwp.md)

## <a name="see-also"></a>Voir aussi
* [Notes de publication de Services de reconnaissance vocale](releasenotes.md)
* [Qu’est-ce que le service Speech Service](overview.md)
* [Documentation des Services de reconnaissance vocale et de Speech SDK](speech-sdk.md#get-the-sdk)
