---
title: Synthèse vocale référence de l’API (REST) - Services de reconnaissance vocale
titleSuffix: Azure Cognitive Services
description: Découvrez comment utiliser l’API REST de synthèse vocale. Cet article vous présente les options d’autorisation, les options de requête, et vous explique comment structurer une demande et recevoir une réponse.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: d67d8462c177d19dfa3cebbd0b4b000fbe3f41b8
ms.sourcegitcommit: b8f9200112cae265155b8877f7e1621c4bcc53fc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/14/2019
ms.locfileid: "57894940"
---
# <a name="text-to-speech-rest-api"></a>API REST de synthèse vocale

Les Services de reconnaissance vocale vous permettent de convertir la synthèse vocale à l’aide d’une API REST. Chaque point de terminaison accessible est associé à une région. Votre application nécessite une clé d’abonnement pour le point de terminaison que vous prévoyez d’utiliser. L’API REST de synthèse vocale prend en charge les voix de synthèse vocale neuronales et standard qui, à leur tour, prennent chacune en charge une langue et un dialecte spécifiques, identifiés par les paramètres régionaux.

* Pour obtenir la liste complète des voix, consultez [prise en charge linguistique](language-support.md#text-to-speech).
* Pour obtenir des informations sur la disponibilité régionale, consultez [régions](regions.md#text-to-speech).

> [!IMPORTANT]
> Les prix varient pour les voix standard, personnalisées et neurales. Pour plus d’informations, voir la [tarification](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

Avant d’utiliser cette API, comprendre :

* L’API REST de synthèse vocale nécessite un en-tête d’autorisation. Cela signifie que vous devez échanger un jeton pour accéder au service. Pour en savoir plus, consultez [Authentification](#authentication).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="request-headers"></a>En-têtes de requête

Ce tableau répertorie les en-têtes obligatoires et facultatifs pour les demandes de reconnaissance vocale.

| En-tête | Description | Obligatoire/facultatif |
|--------|-------------|---------------------|
| `Authorization` | Un jeton d’autorisation précédé du mot `Bearer`. Pour en savoir plus, consultez [Authentification](#authentication). | Obligatoire |
| `Content-Type` | Spécifie le type de contenu pour le texte fourni. Valeur acceptée : `application/ssml+xml`. | Obligatoire |
| `X-Microsoft-OutputFormat` | Spécifie le format de la sortie audio. Pour obtenir une liste complète des valeurs acceptées, consultez [Sorties audio](#audio-outputs). | Obligatoire |
| `User-Agent` | Nom de l'application. La valeur fournie doit être inférieure à 255 caractères. | Obligatoire |

## <a name="audio-outputs"></a>Sorties audio

Liste de formats audio pris en charge envoyés dans chaque demande en tant qu’en-tête `X-Microsoft-OutputFormat`. Chaque format indique la vitesse de transmission et le type d’encodage. Les Services de reconnaissance vocale prend en charge 24 KHz, 16 KHz, et sorties audio de 8 KHz.

|||
|-|-|
| `raw-16khz-16bit-mono-pcm` | `raw-8khz-8bit-mono-mulaw` |
| `riff-8khz-8bit-mono-alaw` | `riff-8khz-8bit-mono-mulaw` |
| `riff-16khz-16bit-mono-pcm` | `audio-16khz-128kbitrate-mono-mp3` |
| `audio-16khz-64kbitrate-mono-mp3` | `audio-16khz-32kbitrate-mono-mp3` |
| `raw-24khz-16bit-mono-pcm` | `riff-24khz-16bit-mono-pcm` |
| `audio-24khz-160kbitrate-mono-mp3` | `audio-24khz-96kbitrate-mono-mp3` |
| `audio-24khz-48kbitrate-mono-mp3` | |

> [!NOTE]
> Si la voix que vous avez sélectionnée ne présente pas la même vitesse de transmission que le format de sortie spécifié, les données audio sont rééchantillonnées si nécessaire. Toutefois, es voix à 24 kHz ne prennent pas en charge les formats de sortie `audio-16khz-16kbps-mono-siren` et `riff-16khz-16kbps-mono-siren`.

## <a name="request-body"></a>Corps de la demande

Le corps de chaque requête `POST` est envoyée en tant que [Speech Synthesis Markup Language (SSML)](speech-synthesis-markup.md). SSML vous permet de choisir la voix et la langue de la synthèse vocale renvoyée par le service de synthèse vocale. Pour obtenir la liste complète des voix prises en charge, consultez [prise en charge linguistique](language-support.md#text-to-speech).

> [!NOTE]
> Si vous utilisez une voix personnalisée, le corps d’une requête peut être envoyé sous forme de texte brut (ASCII ou UTF-8).

## <a name="sample-request"></a>Exemple de requête

Cette requête HTTP utilise SSML pour spécifier la voix et la langue. Le corps ne doit pas dépasser 1 000 caractères.

```http
POST /cognitiveservices/v1 HTTP/1.1

X-Microsoft-OutputFormat: raw-16khz-16bit-mono-pcm
Content-Type: application/ssml+xml
Host: westus.tts.speech.microsoft.com
Content-Length: 225
Authorization: Bearer [Base64 access_token]

<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Female'
    name='Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)'>
        Microsoft Speech Service Text-to-Speech API
</voice></speak>
```

Consultez nos guides de démarrage rapide pour obtenir des exemples spécifiques au langage :

* [.NET Core, C#](quickstart-dotnet-text-to-speech.md)
* [Python](quickstart-python-text-to-speech.md)
* [Node.JS](quickstart-nodejs-text-to-speech.md)

## <a name="http-status-codes"></a>Codes d’état HTTP

Le code d’état HTTP de chaque réponse indique la réussite ou des erreurs courantes.

| Code d'état HTTP | Description | Raison possible |
|------------------|-------------|-----------------|
| 200 | OK | La requête a réussi ; le corps de réponse est un fichier audio. |
| 400 | Demande incorrecte | Un paramètre obligatoire est manquant, vide ou présente une valeur Null. Il est également possible que la valeur transmise à un paramètre obligatoire ou facultatif ne soit pas valide. Ce problème est généralement dû à un en-tête trop long. |
| 401 | Non autorisé | La demande n’est pas autorisée. Vérifiez que votre clé d’abonnement ou votre jeton est valide et dans la région appropriée. |
| 413 | Entité de requête trop volumineuse | L’entrée SSML comporte plus de 1 024 caractères. |
| 429 | Trop de demandes | Vous avez dépassé le quota ou le taux de requêtes autorisé pour votre abonnement. |
| 502 | Passerelle incorrecte | Problème de réseau ou côté serveur. Cette erreur peut également signaler des en-têtes non valides. |

Si l’état HTTP est `200 OK`, le corps de la réponse contient un fichier audio au format demandé. Ce fichier peut être lu pendant son transfert ou pendant son enregistrement dans une mémoire tampon ou dans un fichier.

## <a name="next-steps"></a>Étapes suivantes

- [Obtenir votre abonnement d’essai gratuit au service Speech](https://azure.microsoft.com/try/cognitive-services/)
- [Personnaliser les modèles acoustiques](how-to-customize-acoustic-models.md)
- [Personnaliser les modèles de langage](how-to-customize-language-model.md)
