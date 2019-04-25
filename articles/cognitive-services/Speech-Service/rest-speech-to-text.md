---
title: Référence de l’API reconnaissance vocale (REST) - Services de reconnaissance vocale
titleSuffix: Azure Cognitive Services
description: Découvrez comment utiliser l’API REST de reconnaissance vocale. Cet article vous présente les options d’autorisation, les options de requête, et vous explique comment structurer une demande et recevoir une réponse.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: baaa7b1068e13863293e0968cb0bf1ffb198882b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60487229"
---
# <a name="speech-to-text-rest-api"></a>API REST de reconnaissance vocale

Comme alternative à la [Speech SDK](speech-sdk.md), autoriser les Services de reconnaissance vocale vous permet de convertir la parole-texte à l’aide d’une API REST. Chaque point de terminaison accessible est associé à une région. Votre application nécessite une clé d’abonnement pour le point de terminaison que vous prévoyez d’utiliser.

Avant d’utiliser l’API REST de reconnaissance vocale, comprendre :
* Les demandes qui utilisent l’API REST peuvent contenir uniquement les 10 secondes de l’enregistrement audio.
* L’API REST de reconnaissance vocale retourne uniquement les résultats finaux. Les résultats partiels ne sont pas fournis.

Si vous devez envoyer un contenu audio plus long pour votre application, vous pouvez utiliser le [Kit de développement logiciel (SDK) Speech](speech-sdk.md) ou une [transcription par lot](batch-transcription.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="regions-and-endpoints"></a>Régions et points de terminaison

Ces régions sont prises en charge pour la transcription de reconnaissance vocale à l’aide de l’API REST. Veillez à sélectionner le point de terminaison correspondant à votre région d’abonnement.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

## <a name="query-parameters"></a>Paramètres de requête

Ces paramètres peuvent être inclus dans la chaîne de la requête REST.

| Paramètre | Description | Obligatoire/facultatif |
|-----------|-------------|---------------------|
| `language` | Identifie la langue parlée qui est reconnue. Voir [Langues prises en charge](language-support.md#speech-to-text). | Obligatoire |
| `format` | Spécifie le format du résultat. Les valeurs acceptées sont `simple` et `detailed`. Les résultats simples incluent `RecognitionStatus`, `DisplayText`, `Offset` et `Duration`. Les réponses détaillées incluent plusieurs résultats avec des valeurs de niveau de confiance et quatre différentes représentations. Le paramètre par défaut est `simple`. | Facultatif |
| `profanity` | Spécifie comment traiter la vulgarité dans les résultats de la reconnaissance. Les valeurs acceptées sont `masked`, qui remplace les obscénités par des astérisques, `removed`, qui supprime les obscénités du résultat, ou `raw`, qui inclut les obscénités dans le résultat. Le paramètre par défaut est `masked`. | Facultatif |

## <a name="request-headers"></a>En-têtes de requête

Ce tableau répertorie les en-têtes obligatoires et facultatifs pour les demandes de reconnaissance vocale.

|En-tête| Description | Obligatoire/facultatif |
|------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | Votre clé d’abonnement Services de reconnaissance vocale. | Cet en-tête ou `Authorization` est requis. |
| `Authorization` | Un jeton d’autorisation précédé du mot `Bearer`. Pour en savoir plus, consultez [Authentification](#authentication). | Cet en-tête ou `Ocp-Apim-Subscription-Key` est requis. |
| `Content-type` | Décrit le format et le codec des données audio fournies. Les valeurs acceptées sont `audio/wav; codecs=audio/pcm; samplerate=16000` et `audio/ogg; codecs=opus`. | Obligatoire |
| `Transfer-Encoding` | Spécifie que les données audio sont envoyées en bloc plutôt que dans un seul fichier. Utilisez uniquement cet en-tête si vous envoyez les données audio en bloc. | Facultatif |
| `Expect` | Si vous utilisez le transfert en bloc, envoyez `Expect: 100-continue`. Les Services de reconnaissance vocale accuser réception de la demande initiale et attend des données supplémentaires.| Requis si vous envoyez les données audio en bloc. |
| `Accept` | Si cette valeur est fournie, elle doit être `application/json`. Les Services de reconnaissance vocale offrent des résultats au format JSON. Certains frameworks de requêtes web fournissent une valeur par défaut incompatible si vous n’en spécifiez pas une. Il est donc conseillé de toujours inclure `Accept`. | Cette étape est facultative mais recommandée. |

## <a name="audio-formats"></a>Formats audio

L’audio est envoyé dans le corps de la requête HTTP `POST`. Il doit être dans l’un des formats de ce tableau :

| Format | Codec | Bitrate | Échantillonnage |
|--------|-------|---------|-------------|
| WAV | PCM | 16 bits | 16 kHz, mono |
| OGG | OPUS | 16 bits | 16 kHz, mono |

>[!NOTE]
>Les formats ci-dessus sont pris en charge via l’API REST et WebSocket dans les Services de reconnaissance vocale. Pour l’instant, le [kit de développement logiciel (SDK) Speech](speech-sdk.md) ne prend en charge que le format WAV avec le codec PCM.

## <a name="sample-request"></a>Exemple de requête

Il s’agit d’une requête HTTP standard. L’exemple ci-dessous inclut le nom d’hôte et les en-têtes requis. Il est important de noter que le service attend également des données audio, ce qui n’est pas inclus dans cet exemple. Comme mentionné précédemment, l’envoi en bloc est recommandé, mais pas nécessaire.

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
| 100 | Continue | La requête initiale a été acceptée. Passez à l’envoi du reste des données. (Utilisé avec le transfert en bloc.) |
| 200 | OK | La requête a réussi ; le corps de réponse est un objet JSON. |
| 400 | Demande incorrecte | Le code de langue n’est pas fourni ou n’est pas une langue prise en charge ; fichier audio non valide. |
| 401 | Non autorisé | La clé d’abonnement ou le jeton d’autorisation n’est pas valide dans la région spécifiée, ou le point de terminaison n’est pas valide. |
| 403 | Interdit | Clé d’abonnement ou jeton d’autorisation manquant. |

## <a name="chunked-transfer"></a>Transfert en bloc

Transfert mémorisé en bloc (`Transfer-Encoding: chunked`) peut aider à réduire la latence de reconnaissance, car il permet aux Services de reconnaissance vocale commencer le traitement du fichier audio pendant leur transmission. L’API REST ne fournit pas de résultats partiels ou intermédiaires. Cette option est destinée uniquement à améliorer la réactivité.

Cet exemple de code montre comment envoyer l’audio en bloc. Seul le premier segment doit contenir l’en-tête du fichier audio. `request` est un objet HTTPWebRequest connecté au point de terminaison REST approprié. `audioFile` est le chemin vers un fichier audio sur disque.

```csharp

    HttpWebRequest request = null;
    request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
    request.SendChunked = true;
    request.Accept = @"application/json;text/xml";
    request.Method = "POST";
    request.ProtocolVersion = HttpVersion.Version11;
    request.Host = host;
    request.ContentType = @"audio/wav; codecs=audio/pcm; samplerate=16000";
    request.Headers["Ocp-Apim-Subscription-Key"] = args[1];
    request.AllowWriteStreamBuffering = false;

using (fs = new FileStream(audioFile, FileMode.Open, FileAccess.Read))
{
    /*
    * Open a request stream and write 1024 byte chunks in the stream one at a time.
    */
    byte[] buffer = null;
    int bytesRead = 0;
    using (Stream requestStream = request.GetRequestStream())
    {
        /*
        * Read 1024 raw bytes from the input audio file.
        */
        buffer = new Byte[checked((uint)Math.Min(1024, (int)fs.Length))];
        while ((bytesRead = fs.Read(buffer, 0, buffer.Length)) != 0)
        {
            requestStream.Write(buffer, 0, bytesRead);
        }

        // Flush
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

Le `detailed` format inclut les mêmes données que le `simple` mettre en forme, ainsi qu’avec `NBest`, une liste des interprétations autre du même résultat de reconnaissance. Ces résultats sont classés de la plus faible à la plus forte probabilité, la première entrée étant identique au principal résultat de la reconnaissance.  Lorsque vous utilisez le format `detailed`, `DisplayText` est fourni en tant que `Display` pour chaque résultat dans la liste `NBest`.

Chaque objet de la liste `NBest` inclut :

| Paramètre | Description |
|-----------|-------------|
| `Confidence` | Le score de confiance de l’entrée, compris entre 0,0 (aucun niveau de confiance) et 1,0 (confiance totale) |
| `Lexical` | La forme lexicale du texte reconnu : les mots reconnus. |
| `ITN` | La forme « normalisation du texte inversée » (canonique) du texte reconnu, avec numéros de téléphone, chiffres, abréviations (« docteur smith » en « dr smith ») et autres transformations appliquées. |
| `MaskedITN` | La forme « normalisation du texte inversée » avec masquage des grossièretés appliqué, si nécessaire. |
| `Display` | La forme d’affichage du texte reconnu, avec signes de ponctuation et mise en majuscules ajoutés. Ce paramètre est identique à la valeur `DisplayText` fournie lorsque le format est défini sur `simple`. |

## <a name="sample-responses"></a>Exemples de réponses

Il s’agit d’une réponse classique pour la reconnaissance `simple`.

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

Il s’agit d’une réponse classique pour la reconnaissance `detailed`.

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
