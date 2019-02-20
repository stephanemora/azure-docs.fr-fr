---
title: API REST des services Speech - Services Speech
titleSuffix: Azure Cognitive Services
description: Découvrez comment utiliser les API REST de reconnaissance vocale et de synthèse vocale. Cet article vous présente les options d’autorisation, les options de requête, et vous explique comment structurer une demande et recevoir une réponse.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 0ce33f20d44ac284655569ff66825533650b9d9c
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2019
ms.locfileid: "55998935"
---
# <a name="speech-service-rest-apis"></a>API REST du service Speech

Comme alternative au [Kit de développement logiciel (SDK) Speech](speech-sdk.md), le service Speech vous permet d’utiliser la reconnaissance vocale et la synthèse vocale grâce à un ensemble d’API REST. Chaque point de terminaison accessible est associé à une région. Votre application nécessite une clé d’abonnement pour le point de terminaison que vous prévoyez d’utiliser.

Avant d’utiliser les API REST, tenez compte des éléments suivants :
* Les demandes de reconnaissance vocale effectuées à l’aide de l’API REST peuvent uniquement contenir 10 secondes d’enregistrement audio.
* L’API REST de reconnaissance vocale retourne uniquement les résultats finaux. Les résultats partiels ne sont pas fournis.
* L’API REST de synthèse vocale nécessite un en-tête d’autorisation. Cela signifie que vous devez échanger un jeton pour accéder au service. Pour en savoir plus, consultez [Authentification](#authentication).

## <a name="authentication"></a>Authentication

Chaque demande à l’API REST de reconnaissance vocale ou de synthèse vocale nécessite un en-tête d’autorisation. Ce tableau présente les en-têtes pris en charge pour chaque service :

| En-têtes d'autorisation pris en charge | Reconnaissance vocale | Synthèse vocale |
|------------------------|----------------|----------------|
| Ocp-Apim-Subscription-Key | OUI | Non  |
| Autorisation : Support | OUI | OUI |

Lorsque vous utilisez l’en-tête `Ocp-Apim-Subscription-Key`, vous devez uniquement fournir votre clé d’abonnement. Par exemple : 

```
'Ocp-Apim-Subscription-Key': 'YOUR_SUBSCRIPTION_KEY'
```

Lorsque vous utilisez l’en-tête `Authorization: Bearer`, vous devez envoyer une demande au point de terminaison `issueToken`. Dans cette requête, vous échangez votre clé d’abonnement contre un jeton d’accès valide pendant 10 minutes. Dans les sections suivantes, vous apprendrez à obtenir, utiliser et actualiser un jeton.

### <a name="how-to-get-an-access-token"></a>Obtenir un jeton d’accès

Pour obtenir un jeton d’accès, vous devrez envoyer une demande au point de terminaison `issueToken` à l’aide de `Ocp-Apim-Subscription-Key` et de votre clé d’abonnement.

Ces régions et points de terminaison sont pris en charge :

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-token-service.md)]

Utilisez ces exemples pour créer votre demande de jeton d’accès.

#### <a name="http-sample"></a>Exemple HTTP

Cet exemple est une simple requête HTTP pour obtenir un jeton. Remplacez `YOUR_SUBSCRIPTION_KEY` par votre clé d’abonnement de service de reconnaissance vocale. Si votre abonnement n’est pas dans la région USA Ouest, remplacez l’en-tête `Host` par le nom d’hôte de votre région.

```http
POST /sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
```

Le corps de la réponse contient le jeton d’accès au format JSON Web Token (JWT).

#### <a name="powershell-sample"></a>Exemple de code PowerShell

Cet exemple est un simple script PowerShell pour obtenir un jeton d’accès. Remplacez `YOUR_SUBSCRIPTION_KEY` par votre clé d’abonnement de service de reconnaissance vocale. Veillez à utiliser le point de terminaison correct pour la région correspondant à votre abonnement. Cet exemple est actuellement configuré pour l’Ouest des États-Unis.

```Powershell
$FetchTokenHeader = @{
  'Content-type'='application/x-www-form-urlencoded';
  'Content-Length'= '0';
  'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
}

$OAuthToken = Invoke-RestMethod -Method POST -Uri https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken
 -Headers $FetchTokenHeader

# show the token received
$OAuthToken

```

#### <a name="curl-sample"></a>Exemple cURL

cURL est un outil en ligne de commande disponible dans Linux (ainsi que dans le sous-système Windows pour Linux). Cette commande cURL montre comment obtenir un jeton d’accès. Remplacez `YOUR_SUBSCRIPTION_KEY` par votre clé d’abonnement de service de reconnaissance vocale. Veillez à utiliser le point de terminaison correct pour la région correspondant à votre abonnement. Cet exemple est actuellement configuré pour l’Ouest des États-Unis.

```cli
curl -v -X POST
 "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken" \
 -H "Content-type: application/x-www-form-urlencoded" \
 -H "Content-Length: 0" \
 -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

#### <a name="c-sample"></a>Exemple de code C#

La classe C# montre comment obtenir un jeton d’accès. Transmettez votre clé d’abonnement du service Speech quand vous instanciez la classe. Si votre abonnement ne figure pas dans la région Ouest des États-Unis, modifiez la valeur `FetchTokenUri` afin qu’elle corresponde à la région de votre abonnement.

```cs
/*
    * This class demonstrates how to get a valid access token.
    */
public class Authentication
{
    public static readonly string FetchTokenUri =
        "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken";
    private string subscriptionKey;
    private string token;

    public Authentication(string subscriptionKey)
    {
        this.subscriptionKey = subscriptionKey;
        this.token = FetchTokenAsync(FetchTokenUri, subscriptionKey).Result;
    }

    public string GetAccessToken()
    {
        return this.token;
    }

    private async Task<string> FetchTokenAsync(string fetchUri, string subscriptionKey)
    {
        using (var client = new HttpClient())
        {
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            UriBuilder uriBuilder = new UriBuilder(fetchUri);

            var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
            Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
            return await result.Content.ReadAsStringAsync();
        }
    }
}
```

### <a name="how-to-use-an-access-token"></a>Utiliser un jeton d’accès

Le jeton d’accès doit être envoyé au service en tant qu’en-tête `Authorization: Bearer <TOKEN>`. Chaque jeton d’accès est valide pour une durée de 10 minutes. Vous pouvez à tout moment obtenir un nouveau jeton, mais pour réduire la latence et le trafic réseau, nous recommandons d’utiliser le même jeton durant neuf minutes.

Voici un exemple de demande HTTP envoyée à l’API REST de synthèse vocale :

```http
POST /cognitiveservices/v1 HTTP/1.1
Authorization: Bearer YOUR_ACCESS_TOKEN
Host: westus.tts.speech.microsoft.com
Content-type: application/ssml+xml
Content-Length: 199
Connection: Keep-Alive

<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice name='Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)'>
    Hello, world!
</voice></speak>
```

### <a name="how-to-renew-an-access-token-using-c"></a>Renouveler un jeton accès à l’aide de C#

Ce code C# est un remplacement instantané pour la classe présentée précédemment. La classe `Authentication` obtient automatiquement un nouveau jeton d’accès toutes les neuf minutes à l’aide d’un minuteur. Cette approche garantit qu’un jeton valide est toujours disponible pendant l’exécution de votre programme.

> [!NOTE]
> Au lieu d’utiliser un minuteur, vous pouvez stocker un horodatage de l’obtention du dernier jeton. Vous pouvez ensuite en demander un nouveau uniquement s’il est sur le point d’expirer. Cette approche évite de demander de nouveaux jetons inutilement, et peut être plus adaptée pour les programmes qui effectuent des requêtes de reconnaissance vocale peu fréquentes.

Comme précédemment, assurez-vous que la valeur `FetchTokenUri` correspond à la région de votre abonnement. Transmettez votre clé d’abonnement quand vous instanciez la classe.

```cs
public class Authentication
{
    public static readonly string FetchTokenUri =
        "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken";
    private string subscriptionKey;
    private string token;
    private Timer accessTokenRenewer;

    //Access token expires every 10 minutes. Renew it every 9 minutes.
    private const int RefreshTokenDuration = 9;

    public Authentication(string subscriptionKey)
    {
        this.subscriptionKey = subscriptionKey;
        this.token = FetchToken(FetchTokenUri, subscriptionKey).Result;

        // renew the token on set duration.
        accessTokenRenewer = new Timer(new TimerCallback(OnTokenExpiredCallback),
                                        this,
                                        TimeSpan.FromMinutes(RefreshTokenDuration),
                                        TimeSpan.FromMilliseconds(-1));
    }

    public string GetAccessToken()
    {
        return this.token;
    }

    private void RenewAccessToken()
    {
        this.token = FetchToken(FetchTokenUri, this.subscriptionKey).Result;
        Console.WriteLine("Renewed token.");
    }

    private void OnTokenExpiredCallback(object stateInfo)
    {
        try
        {
            RenewAccessToken();
        }
        catch (Exception ex)
        {
            Console.WriteLine(string.Format("Failed renewing access token. Details: {0}", ex.Message));
        }
        finally
        {
            try
            {
                accessTokenRenewer.Change(TimeSpan.FromMinutes(RefreshTokenDuration), TimeSpan.FromMilliseconds(-1));
            }
            catch (Exception ex)
            {
                Console.WriteLine(string.Format("Failed to reschedule the timer to renew access token. Details: {0}", ex.Message));
            }
        }
    }

    private async Task<string> FetchToken(string fetchUri, string subscriptionKey)
    {
        using (var client = new HttpClient())
        {
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            UriBuilder uriBuilder = new UriBuilder(fetchUri);

            var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
            Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
            return await result.Content.ReadAsStringAsync();
        }
    }
}
```

## <a name="speech-to-text-api"></a>API de reconnaissance vocale

L’API REST de reconnaissance vocale prend uniquement en charge des énoncés courts. Les requêtes peuvent contenir jusqu’à 10 secondes de contenu audio et durer 14 secondes maximum. L’API REST ne renvoie que les résultats finaux, pas les résultats partiels ou intermédiaires.

Si vous devez envoyer un contenu audio plus long pour votre application, vous pouvez utiliser le [Kit de développement logiciel (SDK) Speech](speech-sdk.md) ou une [transcription par lot](batch-transcription.md).

### <a name="regions-and-endpoints"></a>Régions et points de terminaison

Ces régions sont prises en charge pour la transcription de reconnaissance vocale à l’aide de l’API REST. Veillez à sélectionner le point de terminaison correspondant à votre région d’abonnement.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

### <a name="query-parameters"></a>Paramètres de requête

Ces paramètres peuvent être inclus dans la chaîne de la requête REST.

| Paramètre | Description | Obligatoire/facultatif |
|-----------|-------------|---------------------|
| `language` | Identifie la langue parlée qui est reconnue. Voir [Langues prises en charge](language-support.md#speech-to-text). | Obligatoire |
| `format` | Spécifie le format du résultat. Les valeurs acceptées sont `simple` et `detailed`. Les résultats simples incluent `RecognitionStatus`, `DisplayText`, `Offset` et `Duration`. Les réponses détaillées incluent plusieurs résultats avec des valeurs de niveau de confiance et quatre différentes représentations. Le paramètre par défaut est `simple`. | Facultatif |
| `profanity` | Spécifie comment traiter la vulgarité dans les résultats de la reconnaissance. Les valeurs acceptées sont `masked`, qui remplace les obscénités par des astérisques, `removed`, qui supprime les obscénités du résultat, ou `raw`, qui inclut les obscénités dans le résultat. Le paramètre par défaut est `masked`. | Facultatif |

### <a name="request-headers"></a>En-têtes de requête

Ce tableau répertorie les en-têtes obligatoires et facultatifs pour les demandes de reconnaissance vocale.

|En-tête| Description | Obligatoire/facultatif |
|------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | Votre clé d’abonnement du service Speech. | Cet en-tête ou `Authorization` est requis. |
| `Authorization` | Un jeton d’autorisation précédé du mot `Bearer`. Pour en savoir plus, consultez [Authentification](#authentication). | Cet en-tête ou `Ocp-Apim-Subscription-Key` est requis. |
| `Content-type` | Décrit le format et le codec des données audio fournies. Les valeurs acceptées sont `audio/wav; codecs=audio/pcm; samplerate=16000` et `audio/ogg; codecs=opus`. | Obligatoire |
| `Transfer-Encoding` | Spécifie que les données audio sont envoyées en bloc plutôt que dans un seul fichier. Utilisez uniquement cet en-tête si vous envoyez les données audio en bloc. | Facultatif |
| `Expect` | Si vous utilisez le transfert en bloc, envoyez `Expect: 100-continue`. Le service Speech accuse réception de la requête initiale et attend des données supplémentaires.| Requis si vous envoyez les données audio en bloc. |
| `Accept` | Si cette valeur est fournie, elle doit être `application/json`. Le service de reconnaissance vocale Speech fournit les résultats au format JSON. Certains frameworks de requêtes web fournissent une valeur par défaut incompatible si vous n’en spécifiez pas une. Il est donc conseillé de toujours inclure `Accept`. | Cette étape est facultative mais recommandée. |

### <a name="audio-formats"></a>Formats audio

L’audio est envoyé dans le corps de la requête HTTP `POST`. Il doit être dans l’un des formats de ce tableau :

| Format | Codec | Bitrate | Échantillonnage |
|--------|-------|---------|-------------|
| WAV | PCM | 16 bits | 16 kHz, mono |
| OGG | OPUS | 16 bits | 16 kHz, mono |

>[!NOTE]
>Les formats ci-dessus sont pris en charge via l’API REST et WebSocket dans le service Speech. Pour l’instant, le [kit de développement logiciel (SDK) Speech](speech-sdk.md) ne prend en charge que le format WAV avec le codec PCM.

### <a name="sample-request"></a>Exemple de requête

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

### <a name="http-status-codes"></a>Codes d’état HTTP

Le code d’état HTTP de chaque réponse indique la réussite ou des erreurs courantes.

| Code d'état HTTP | Description | Raison possible |
|------------------|-------------|-----------------|
| 100 | Continue | La requête initiale a été acceptée. Passez à l’envoi du reste des données. (Utilisé avec le transfert en bloc.) |
| 200 | OK | La requête a réussi ; le corps de réponse est un objet JSON. |
| 400 | Demande incorrecte | Le code de langue n’est pas fourni ou n’est pas une langue prise en charge ; fichier audio non valide. |
| 401 | Non autorisé | La clé d’abonnement ou le jeton d’autorisation n’est pas valide dans la région spécifiée, ou le point de terminaison n’est pas valide. |
| 403 | Interdit | Clé d’abonnement ou jeton d’autorisation manquant. |

### <a name="chunked-transfer"></a>Transfert en bloc

Le transfert en bloc (`Transfer-Encoding: chunked`) peut aider à réduire la latence de reconnaissance, car il permet au service Speech de commencer le traitement du fichier audio pendant sa transmission. L’API REST ne fournit pas de résultats partiels ou intermédiaires. Cette option est destinée uniquement à améliorer la réactivité.

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

### <a name="response-parameters"></a>Paramètres de réponse

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

Le format `detailed` inclut les mêmes données que le format `simple`, ainsi que `NBest`, une liste d’autres interprétations du même résultat de la reconnaissance vocale. Ces résultats sont classés de la plus faible à la plus forte probabilité, la première entrée étant identique au principal résultat de la reconnaissance.  Lorsque vous utilisez le format `detailed`, `DisplayText` est fourni en tant que `Display` pour chaque résultat dans la liste `NBest`.

Chaque objet de la liste `NBest` inclut :

| Paramètre | Description |
|-----------|-------------|
| `Confidence` | Le score de confiance de l’entrée, compris entre 0,0 (aucun niveau de confiance) et 1,0 (confiance totale) |
| `Lexical` | La forme lexicale du texte reconnu : les mots reconnus. |
| `ITN` | La forme « normalisation du texte inversée » (canonique) du texte reconnu, avec numéros de téléphone, chiffres, abréviations (« docteur smith » en « dr smith ») et autres transformations appliquées. |
| `MaskedITN` | La forme « normalisation du texte inversée » avec masquage des grossièretés appliqué, si nécessaire. |
| `Display` | La forme d’affichage du texte reconnu, avec signes de ponctuation et mise en majuscules ajoutés. Ce paramètre est identique à la valeur `DisplayText` fournie lorsque le format est défini sur `simple`. |

### <a name="sample-responses"></a>Exemples de réponses

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

## <a name="text-to-speech-api"></a>API Synthèse vocale

L’API REST de synthèse vocale prend en charge les voix de synthèse vocale neuronales et standard qui, à leur tour, prennent chacune en charge une langue et un dialecte spécifiques, identifiés par les paramètres régionaux.

* Pour obtenir la liste complète des voix, consultez [prise en charge linguistique](language-support.md#text-to-speech).
* Pour obtenir des informations sur la disponibilité régionale, consultez [régions](regions.md#text-to-speech).

> [!IMPORTANT]
> Les prix varient pour les voix standard, personnalisées et neurales. Pour plus d’informations, voir la [tarification](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="request-headers"></a>En-têtes de requête

Ce tableau répertorie les en-têtes obligatoires et facultatifs pour les demandes de reconnaissance vocale.

| En-tête | Description | Obligatoire/facultatif |
|--------|-------------|---------------------|
| `Authorization` | Un jeton d’autorisation précédé du mot `Bearer`. Pour plus d’informations, consultez [Authentification](#authentication). | Obligatoire |
| `Content-Type` | Spécifie le type de contenu pour le texte fourni. Valeur acceptée : `application/ssml+xml`. | Obligatoire |
| `X-Microsoft-OutputFormat` | Spécifie le format de la sortie audio. Pour obtenir une liste complète des valeurs acceptées, consultez [Sorties audio](#audio-outputs). | Obligatoire |
| `User-Agent` | Nom de l'application. Doit être inférieur à 255 caractères. | Obligatoire |

### <a name="audio-outputs"></a>Sorties audio

Liste de formats audio pris en charge envoyés dans chaque demande en tant qu’en-tête `X-Microsoft-OutputFormat`. Chaque format indique la vitesse de transmission et le type d’encodage. Speech Service prend en charge les sorties audio de 24 kHz, 16 kHz et 8 kHz.

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

### <a name="request-body"></a>Corps de la demande

Le corps de chaque requête `POST` est envoyée en tant que [Speech Synthesis Markup Language (SSML)](speech-synthesis-markup.md). SSML vous permet de choisir la voix et la langue de la synthèse vocale renvoyée par le service de synthèse vocale. Pour obtenir la liste complète des voix prises en charge, consultez [prise en charge linguistique](language-support.md#text-to-speech).

> [!NOTE]
> Si vous utilisez une voix personnalisée, le corps d’une requête peut être envoyé sous forme de texte brut (ASCII ou UTF-8).

### <a name="sample-request"></a>Exemple de requête

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

### <a name="http-status-codes"></a>Codes d’état HTTP

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
