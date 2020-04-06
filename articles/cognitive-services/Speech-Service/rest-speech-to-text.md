---
title: Informations de référence sur l’API de reconnaissance vocale (REST) - Service Speech
titleSuffix: Azure Cognitive Services
description: Découvrez comment utiliser l’API REST de reconnaissance vocale. Cet article vous présente les options d’autorisation, les options de requête, et vous explique comment structurer une demande et recevoir une réponse.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: dapine
ms.openlocfilehash: 759ea697e4093da5bfc1c082c886c6dfda636f42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79474796"
---
# <a name="speech-to-text-rest-api"></a>API REST de reconnaissance vocale

En guise d’alternative au [SDK Speech](speech-sdk.md), le service Speech vous permet de convertir la parole en texte à l’aide d’une API REST. Chaque point de terminaison accessible est associé à une région. Votre application nécessite une clé d’abonnement pour le point de terminaison que vous prévoyez d’utiliser. L’API REST est très limitée et ne doit être utilisée que dans les cas où le [kit de développement logiciel (SDK) Speech](speech-sdk.md) ne peut pas l’être.

Avant d’utiliser l’API REST de reconnaissance vocale, tenez compte des points suivants :

* Les demandes qui utilisent l’API REST et transmettent directement l’audio peuvent contenir jusqu’à 60 secondes d’audio.
* L’API REST de reconnaissance vocale retourne uniquement les résultats finaux. Les résultats partiels ne sont pas fournis.

Si vous devez envoyer un contenu audio plus long pour votre application, vous pouvez utiliser le [SDK Speech](speech-sdk.md) ou une API REST basée sur un fichier, comme une [transcription par lot](batch-transcription.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="regions-and-endpoints"></a>Régions et points de terminaison

Le point de terminaison de l'API REST se présente sous le format suivant :

```
https://<REGION_IDENTIFIER>.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1
```

Remplacez `<REGION_IDENTIFIER>` par l'identificateur correspondant à la région de votre abonnement dans le tableau suivant :

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

> [!NOTE]
> Le paramètre de langue doit être ajouté à l'URL pour éviter de recevoir une erreur HTTP 4xx. Par exemple, la langue définie sur la valeur Anglais (États-Unis) à l’aide du point de terminaison USA Ouest est : `https://westus.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US`.

## <a name="query-parameters"></a>Paramètres de requête

Ces paramètres peuvent être inclus dans la chaîne de la requête REST.

| Paramètre | Description | Obligatoire/facultatif |
|-----------|-------------|---------------------|
| `language` | Identifie la langue parlée qui est reconnue. Voir [Langues prises en charge](language-support.md#speech-to-text). | Obligatoire |
| `format` | Spécifie le format du résultat. Les valeurs acceptées sont `simple` et `detailed`. Les résultats simples incluent `RecognitionStatus`, `DisplayText`, `Offset` et `Duration`. Les réponses détaillées incluent plusieurs résultats avec des valeurs de niveau de confiance et quatre différentes représentations. La valeur par défaut est `simple`. | Facultatif |
| `profanity` | Spécifie comment traiter la vulgarité dans les résultats de la reconnaissance. Les valeurs acceptées sont `masked`, qui remplace les vulgarités par des astérisques, `removed`, qui supprime les vulgarités du résultat, ou `raw`, qui inclut les vulgarités dans le résultat. La valeur par défaut est `masked`. | Facultatif |
| `cid` | Lorsque vous utilisez le [portail Custom Speech](how-to-custom-speech.md) pour créer des modèles personnalisés, vous pouvez utiliser des modèles personnalisés à l’aide de leur **ID de point de terminaison** figurant sur la page **Déploiement**. Utilisez l’**ID de point de terminaison** comme argument pour le paramètre de chaîne de requête `cid`. | Facultatif |

## <a name="request-headers"></a>En-têtes de requête

Ce tableau répertorie les en-têtes obligatoires et facultatifs pour les demandes de reconnaissance vocale.

|En-tête| Description | Obligatoire/facultatif |
|------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | Votre clé d’abonnement du service Speech. | Cet en-tête ou `Authorization` est requis. |
| `Authorization` | Un jeton d’autorisation précédé du mot `Bearer`. Pour en savoir plus, consultez [Authentification](#authentication). | Cet en-tête ou `Ocp-Apim-Subscription-Key` est requis. |
| `Content-type` | Décrit le format et le codec des données audio fournies. Les valeurs acceptées sont `audio/wav; codecs=audio/pcm; samplerate=16000` et `audio/ogg; codecs=opus`. | Obligatoire |
| `Transfer-Encoding` | Spécifie que les données audio sont envoyées en bloc plutôt que dans un seul fichier. Utilisez uniquement cet en-tête si vous envoyez les données audio en bloc. | Facultatif |
| `Expect` | Si vous utilisez le transfert en bloc, envoyez `Expect: 100-continue`. Le service Speech accuse réception de la requête initiale et attend des données supplémentaires.| Requis si vous envoyez les données audio en bloc. |
| `Accept` | Si cette valeur est fournie, elle doit être `application/json`. Le service Speech fournit les résultats au format JSON. Certains frameworks de demande fournissent une valeur par défaut incompatible. Il est recommandé de toujours inclure `Accept`. | Cette étape est facultative mais recommandée. |

## <a name="audio-formats"></a>Formats audio

L’audio est envoyé dans le corps de la requête HTTP `POST`. Il doit être dans l’un des formats de ce tableau :

| Format | Codec | Bitrate | Échantillonnage  |
|--------|-------|---------|--------------|
| WAV    | PCM   | 16 bits  | 16 kHz, mono |
| OGG    | OPUS  | 16 bits  | 16 kHz, mono |

>[!NOTE]
>Les formats ci-dessus sont pris en charge via l’API REST et le WebSocket du service Speech. Pour le moment, le [kit de développement logiciel (SDK) Speech](speech-sdk.md) prend en charge le format WAV avec codec PCM, ainsi que d'[autres formats](how-to-use-codec-compressed-audio-input-streams.md).

## <a name="sample-request"></a>Exemple de requête

L’exemple ci-dessous inclut le nom d’hôte et les en-têtes requis. Il est important de noter que le service attend également des données audio, ce qui n’est pas inclus dans cet exemple. Comme mentionné précédemment, l’envoi en bloc est recommandé, mais pas nécessaire.

```HTTP
POST speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codecs=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.stt.speech.microsoft.com
Transfer-Encoding: chunked
Expect: 100-continue
```

## <a name="http-status-codes"></a>Codes d’état HTTP

Le code d’état HTTP de chaque réponse indique la réussite ou des erreurs courantes.

| Code d'état HTTP | Description | Raison possible |
|------------------|-------------|-----------------|
| `100` | Continue | La requête initiale a été acceptée. Passez à l’envoi du reste des données. (Utilisé avec le transfert en bloc) |
| `200` | OK | La requête a réussi ; le corps de réponse est un objet JSON. |
| `400` | Demande incorrecte | Le code de langue n’est pas fourni, la langue n’est pas prise en charge, le fichier audio est non valide, etc. |
| `401` | Non autorisé | La clé d’abonnement ou le jeton d’autorisation n’est pas valide dans la région spécifiée, ou le point de terminaison n’est pas valide. |
| `403` | Interdit | Clé d’abonnement ou jeton d’autorisation manquant. |

## <a name="chunked-transfer"></a>Transfert en bloc

Le transfert en bloc (`Transfer-Encoding: chunked`) peut aider à réduire la latence de la reconnaissance. Il permet au service Speech de commencer à traiter le fichier audio pendant sa transmission. L’API REST ne fournit pas de résultats partiels ou intermédiaires.

Cet exemple de code montre comment envoyer l’audio en bloc. Seul le premier segment doit contenir l’en-tête du fichier audio. `request` est un objet `HttpWebRequest` connecté au point de terminaison REST approprié. `audioFile` est le chemin vers un fichier audio sur disque.

```csharp
var request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
request.SendChunked = true;
request.Accept = @"application/json;text/xml";
request.Method = "POST";
request.ProtocolVersion = HttpVersion.Version11;
request.Host = host;
request.ContentType = @"audio/wav; codecs=audio/pcm; samplerate=16000";
request.Headers["Ocp-Apim-Subscription-Key"] = "YOUR_SUBSCRIPTION_KEY";
request.AllowWriteStreamBuffering = false;

using (var fs = new FileStream(audioFile, FileMode.Open, FileAccess.Read))
{
    // Open a request stream and write 1024 byte chunks in the stream one at a time.
    byte[] buffer = null;
    int bytesRead = 0;
    using (var requestStream = request.GetRequestStream())
    {
        // Read 1024 raw bytes from the input audio file.
        buffer = new Byte[checked((uint)Math.Min(1024, (int)fs.Length))];
        while ((bytesRead = fs.Read(buffer, 0, buffer.Length)) != 0)
        {
            requestStream.Write(buffer, 0, bytesRead);
        }

        requestStream.Flush();
    }
}
```

## <a name="response-parameters"></a>Paramètres de réponse

Les résultats sont fournis au format JSON. Le format `simple` inclut ces champs de niveau supérieur.

| Paramètre | Description  |
|-----------|--------------|
|`RecognitionStatus`|État, tel que `Success` pour une reconnaissance ayant réussi. Voir le tableau suivant.|
|`DisplayText`|Le texte reconnu après mise en majuscules, ponctuation, normalisation du texte inversée (conversion de texte lu en formes plus courtes, par exemple 200 pour « deux cents » ou « Dr. Smith » pour « docteur smith ») et masquage des grossièretés. Présent uniquement en cas de réussite.|
|`Offset`|Moment (en unités de 100 nanosecondes) à partir duquel la voix identifiée commence dans le flux audio.|
|`Duration`|Durée (en unités de 100 nanosecondes) de la voix reconnue dans le flux audio.|

Le champ `RecognitionStatus` peut contenir ces valeurs :

| Statut | Description |
|--------|-------------|
| `Success` | La reconnaissance a réussi et le champ `DisplayText` est présent. |
| `NoMatch` | Des paroles ont été détectées dans le flux audio, mais aucun mot de la langue cible n’a été mis en correspondance. Signifie généralement que la langue de reconnaissance est différente de celle parlée par l’orateur. |
| `InitialSilenceTimeout` | Le début du flux audio contenait uniquement un silence, et le service a dépassé son délai d’attente de paroles. |
| `BabbleTimeout` | Le début du flux audio contenait uniquement du bruit, et le service a dépassé son délai d’attente de paroles. |
| `Error` | Le service de reconnaissance a rencontré une erreur interne et n’a pas pu continuer. Réessayez si possible. |

> [!NOTE]
> Si l’audio est composé uniquement de grossièretés et que le paramètre de requête `profanity` a la valeur `remove`, le service ne retourne pas de résultat de reconnaissance vocale.

Le format `detailed` inclut les mêmes données que le format `simple`, avec `NBest`, liste d’autres interprétations du même résultat de la reconnaissance. Ces résultats sont classés du plus probable au moins probable. La première entrée est identique au principal résultat de reconnaissance.  Lorsque vous utilisez le format `detailed`, `DisplayText` est fourni en tant que `Display` pour chaque résultat dans la liste `NBest`.

Chaque objet de la liste `NBest` inclut :

| Paramètre | Description |
|-----------|-------------|
| `Confidence` | Le score de confiance de l’entrée, compris entre 0,0 (aucun niveau de confiance) et 1,0 (confiance totale) |
| `Lexical` | La forme lexicale du texte reconnu : les mots reconnus. |
| `ITN` | La forme « normalisation du texte inversée » (canonique) du texte reconnu, avec numéros de téléphone, chiffres, abréviations (« docteur smith » en « dr smith ») et autres transformations appliquées. |
| `MaskedITN` | La forme « normalisation du texte inversée » avec masquage des grossièretés appliqué, si nécessaire. |
| `Display` | La forme d’affichage du texte reconnu, avec signes de ponctuation et mise en majuscules ajoutés. Ce paramètre est identique à la valeur `DisplayText` fournie lorsque le format est défini sur `simple`. |

## <a name="sample-responses"></a>Exemples de réponses

Réponse classique pour la reconnaissance `simple` :

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

Réponse classique pour la reconnaissance `detailed` :

```json
{
  "RecognitionStatus": "Success",
  "Offset": "1236645672289",
  "Duration": "1236645672289",
  "NBest": [
      {
        "Confidence" : "0.87",
        "Lexical" : "remind me to buy five pencils",
        "ITN" : "remind me to buy 5 pencils",
        "MaskedITN" : "remind me to buy 5 pencils",
        "Display" : "Remind me to buy 5 pencils.",
      },
      {
        "Confidence" : "0.54",
        "Lexical" : "rewind me to buy five pencils",
        "ITN" : "rewind me to buy 5 pencils",
        "MaskedITN" : "rewind me to buy 5 pencils",
        "Display" : "Rewind me to buy 5 pencils.",
      }
  ]
}
```

## <a name="next-steps"></a>Étapes suivantes

- [Obtenir votre abonnement d’essai gratuit au service Speech](https://azure.microsoft.com/try/cognitive-services/)
- [Personnaliser les modèles acoustiques](how-to-customize-acoustic-models.md)
- [Personnaliser les modèles de langage](how-to-customize-language-model.md)
