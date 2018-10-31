---
title: API REST du service Speech
description: Informations de référence sur les API REST du service Speech.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: erhopf
ms.openlocfilehash: 7f3daf71f4d94371af5f7d98c4e03761d7217a2a
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50025835"
---
# <a name="speech-service-rest-apis"></a>API REST du service Speech

Les API REST du service Azure Cognitive Services Speech sont similaires aux API fournies par l’[API Reconnaissance vocale Bing](https://docs.microsoft.com/azure/cognitive-services/Speech). Les points de terminaison sont différents de ceux utilisés par le service Reconnaissance vocale Bing. Des points de terminaison régionaux sont disponibles, et vous devez utiliser une clé d’abonnement qui correspond au point de terminaison que vous utilisez.

## <a name="speech-to-text"></a>Reconnaissance vocale

Les points de terminaison pour l’API REST de reconnaissance vocale sont affichés dans le tableau suivant. Utilisez celui qui correspond à votre région d’abonnement.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

> [!NOTE]
> Si vous avez personnalisé le modèle acoustique, le modèle de langage ou la prononciation, utilisez le point de terminaison personnalisé à la place.

Cette API prend en charge uniquement les énoncés courts. Les requêtes peuvent contenir jusqu’à 10 secondes de contenu audio et durer 14 secondes maximum. L’API REST retourne uniquement les résultats finaux, et non les résultats partiels ou intermédiaires. Le service Speech a également une API de [transcription par lot](batch-transcription.md) capable de transcrire des séquences audio plus longues.


### <a name="query-parameters"></a>Paramètres de requête

Les paramètres suivants peuvent être inclus dans la chaîne de la requête REST.

|Nom du paramètre|Obligatoire ou facultatif|Signification|
|-|-|-|
|`language`|Obligatoire|Identificateur de la langue reconnaitre. Voir [Langues prises en charge](language-support.md#speech-to-text).|
|`format`|Facultatif<br>Par défaut : `simple`|Format de résultat, `simple` ou `detailed`. Les résultats simples incluent `RecognitionStatus`, `DisplayText`, `Offset` et la durée. Les résultats détaillés incluent plusieurs candidats avec des valeurs de niveau de confiance et quatre différentes représentations.|
|`profanity`|Facultatif<br>Par défaut : `masked`|Comment traiter la vulgarité dans les résultats de la reconnaissance. Peut être `masked` (remplace les obscénités par des astérisques), `removed` (supprime toutes les obscénités) ou `raw` (inclut les obscénités).

### <a name="request-headers"></a>En-têtes de requête

Les champs suivants sont envoyés dans l’en-tête de requête HTTP.

|En-tête|Signification|
|------|-------|
|`Ocp-Apim-Subscription-Key`|Votre clé d’abonnement du service Speech. Vous devez fournir cet en-tête ou `Authorization`.|
|`Authorization`|Un jeton d’autorisation précédé du mot `Bearer`. Vous devez fournir cet en-tête ou `Ocp-Apim-Subscription-Key`. Voir [Authentification](#authentication).|
|`Content-type`|Décrit le format et le codec des données audio. Actuellement, cette valeur doit être `audio/wav; codec=audio/pcm; samplerate=16000`.|
|`Transfer-Encoding`|facultatif. Si spécifié, doit être `chunked` pour permettre l’envoi des données audio en plusieurs petits blocs au lieu d’un seul fichier.|
|`Expect`|Si vous utilisez le transfert en bloc, envoyez `Expect: 100-continue`. Le service Speech accuse réception de la requête initiale et attend des données supplémentaires.|
|`Accept`|facultatif. S’il est fourni, doit inclure `application/json`, car le service Speech fournit les résultats au format JSON. (Certains frameworks de requêtes web fournissent une valeur par défaut incompatible si vous n’en spécifiez pas une. Il est donc conseillé de toujours inclure `Accept`.)|

### <a name="audio-format"></a>Format audio

L’audio envoyé dans le corps de la requête HTTP `POST`. Il doit être au format WAV 16 bits avec canal unique PCM (mono) à 16 KHz, dans les formats/encodages suivants.

* Format WAV avec le codec PCM
* Format Ogg avec le codec OPUS

>[!NOTE]
>Les formats ci-dessus sont pris en charge via l’API REST et WebSocket dans le service Speech. Pour l’instant, le [kit de développement logiciel (SDK) Speech](/index.yml) ne prend en charge que le format WAV avec le codec PCM.

### <a name="chunked-transfer"></a>Transfert en bloc

Le transfert en bloc (`Transfer-Encoding: chunked`) peut aider à réduire la latence de reconnaissance, car il permet au service Speech de commencer le traitement du fichier audio pendant sa transmission. L’API REST ne fournit pas de résultats partiels ou intermédiaires. Cette option est destinée uniquement à améliorer la réactivité.

Le code suivant montre comment envoyer des données audio en blocs. Seul le premier segment doit contenir l’en-tête du fichier audio. `request` est un objet HTTPWebRequest connecté au point de terminaison REST approprié. `audioFile` est le chemin vers un fichier audio sur disque.

```csharp
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

### <a name="example-request"></a>Exemple de requête

Voici une requête standard.

```HTTP
POST speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codec=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.stt.speech.microsoft.com
Transfer-Encoding: chunked
Expect: 100-continue
```

### <a name="http-status"></a>État HTTP.

L’état HTTP de la réponse indique la réussite ou des conditions d’erreur courantes.

Code HTTP|Signification|Raison possible
-|-|-|
100|Continue|La requête initiale a été acceptée. Passez à l’envoi du reste des données. (Utilisé avec le transfert en bloc.)
200|OK|La requête a réussi ; le corps de réponse est un objet JSON.
400|Demande incorrecte|Le code de langue n’est pas fourni ou n’est pas une langue prise en charge ; fichier audio non valide.
401|Non autorisé|La clé d’abonnement ou le jeton d’autorisation n’est pas valide dans la région spécifiée, ou le point de terminaison n’est pas valide.
403|Interdit|Clé d’abonnement ou jeton d’autorisation manquant.

### <a name="json-response"></a>Réponse JSON

Les résultats sont retournés au format JSON. Le format `simple` inclut uniquement les champs de niveau supérieur suivants.

|Nom du champ|Contenu|
|-|-|
|`RecognitionStatus`|État, tel que `Success` pour une reconnaissance ayant réussi. Voir le tableau suivant.|
|`DisplayText`|Le texte reconnu après mise en majuscules, ponctuation, normalisation du texte inversée (conversion de texte lu en formes plus courtes, par exemple 200 pour « deux cents » ou « Dr. Smith » pour « docteur smith ») et masquage des grossièretés. Présent uniquement en cas de réussite.|
|`Offset`|Moment (en unités de 100 nanosecondes) à partir duquel la voix identifiée commence dans le flux audio.|
|`Duration`|Durée (en unités de 100 nanosecondes) de la voix reconnue dans le flux audio.|

Le champ `RecognitionStatus` peut contenir les valeurs suivantes.

|Valeur d’état|Description
|-|-|
| `Success` | La reconnaissance a réussi et le champ DisplayText est présent. |
| `NoMatch` | Des paroles ont été détectées dans le flux audio, mais aucun mot de la langue cible n’a été mis en correspondance. Signifie généralement que la langue de reconnaissance est différente de celle parlée par l’orateur. |
| `InitialSilenceTimeout` | Le début du flux audio contenait uniquement un silence, et le service a dépassé son délai d’attente de paroles. |
| `BabbleTimeout` | Le début du flux audio contenait uniquement du bruit, et le service a dépassé son délai d’attente de paroles. |
| `Error` | Le service de reconnaissance a rencontré une erreur interne et n’a pas pu continuer. Réessayez si possible. |

> [!NOTE]
> Si l’audio est composé uniquement de grossièretés et que le paramètre de requête `profanity` a la valeur `remove`, le service ne retourne pas de résultat de reconnaissance vocale.


Le format `detailed` inclut les mêmes champs que le format `simple`, ainsi qu’un champ `NBest`. Le champ `NBest` est une liste d’interprétations alternatives du même discours, classées de la plus probable à la moins probable. La première entrée est identique au principal résultat de reconnaissance. Chaque entrée contient les champs suivants :

|Nom du champ|Contenu|
|-|-|
|`Confidence`|Le score de confiance de l’entrée, compris entre 0,0 (aucun niveau de confiance) et 1,0 (confiance totale)
|`Lexical`|La forme lexicale du texte reconnu : les mots reconnus.
|`ITN`|La forme « normalisation du texte inversée » (canonique) du texte reconnu, avec numéros de téléphone, chiffres, abréviations (« docteur smith » en « dr smith ») et autres transformations appliquées.
|`MaskedITN`| La forme « normalisation du texte inversée » avec masquage des grossièretés appliqué, si nécessaire.
|`Display`| La forme d’affichage du texte reconnu, avec signes de ponctuation et mise en majuscules ajoutés. Identique à `DisplayText` dans le résultat de niveau supérieur.

### <a name="sample-responses"></a>Exemples de réponses

Voici une réponse classique pour la reconnaissance `simple`.

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

Voici une réponse classique pour la reconnaissance `detailed`.

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

## <a name="text-to-speech"></a>Synthèse vocale

Voici les points de terminaison REST pour l’API Synthèse vocale du service Speech. Utilisez le point de terminaison correspondant à votre région d’abonnement.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

Le service Speech prend en charge la sortie audio 24 KHz en plus de la sortie 16 Khz prise en charge par Reconnaissance vocale Bing. Quatre formats de sortie 24 KHz peuvent être utilisés dans l’en-tête HTTP `X-Microsoft-OutputFormat`, ainsi que deux voix 24 KHz, `Jessa24kRUS` et `Guy24kRUS`.

Paramètres régionaux | Langage   | Sexe | Mappage du nom du service
-------|------------|--------|------------
fr-FR  | Français | Féminin | « Voix de synthèse vocale pour la reconnaissance vocale Microsoft Server (en-US, Jessa24kRUS) »
fr-FR  | Français | Masculin   | « Voix de synthèse vocale pour la reconnaissance vocale Microsoft Server (en-US, Guy24kRUS) »

Vous trouverez une liste complète des voix disponibles dans [Langues prises en charge](language-support.md#text-to-speech).

### <a name="request-headers"></a>En-têtes de requête

Les champs suivants sont envoyés dans l’en-tête de requête HTTP.

|En-tête|Signification|
|------|-------|
|`Authorization`|Un jeton d’autorisation précédé du mot `Bearer`. Requis. Voir [Authentification](#authentication).|
|`Content-Type`|Type de contenu d’entrée : `application/ssml+xml`.|
|`X-Microsoft-OutputFormat`|Format audio de sortie. Voir le tableau suivant.|
|`User-Agent`|Nom de l’application. Obligatoire ; doit contenir moins de 255 caractères.|

Les formats de sortie audio disponibles (`X-Microsoft-OutputFormat`) incorporent à la fois une vitesse de transmission et un encodage.

|||
|-|-|
`raw-16khz-16bit-mono-pcm`         | `raw-8khz-8bit-mono-mulaw`
`riff-8khz-8bit-mono-mulaw`     | `riff-16khz-16bit-mono-pcm`
`audio-16khz-128kbitrate-mono-mp3` | `audio-16khz-64kbitrate-mono-mp3`
`audio-16khz-32kbitrate-mono-mp3`  | `raw-24khz-16bit-mono-pcm`
`riff-24khz-16bit-mono-pcm`        | `audio-24khz-160kbitrate-mono-mp3`
`audio-24khz-96kbitrate-mono-mp3`  | `audio-24khz-48kbitrate-mono-mp3`

> [!NOTE]
> Si la voix que vous avez sélectionnée ne présente pas la même vitesse de transmission que le format de sortie spécifié, les données audio sont rééchantillonnées si nécessaire. Toutefois, es voix à 24 kHz ne prennent pas en charge les formats de sortie `audio-16khz-16kbps-mono-siren` et `riff-16khz-16kbps-mono-siren`.

### <a name="request-body"></a>Corps de la demande

Le texte à convertir en parole est envoyé comme corps d’une requête HTTP `POST` en texte brut (ASCII ou UTF-8) ou au format SSML ([Speech Synthesis Markup Language](speech-synthesis-markup.md)) (UTF-8). Les requêtes en texte brut utilisent la langue et la voix par défaut du service. Envoyez les données au format SSML pour utiliser une voix différente.

### <a name="sample-request"></a>Exemple de requête

La requête HTTP suivante utilise un corps SSML pour choisir la voix. Le corps ne doit pas compter plus de 1000 caractères.

```xml
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

### <a name="http-response"></a>Réponse HTTP

L’état HTTP de la réponse indique la réussite ou des conditions d’erreur courantes.

Code HTTP|Signification|Raison possible
-|-|-|
200|OK|La requête a réussi ; le corps de réponse est un fichier audio.
400 |Demande incorrecte |Un paramètre obligatoire est manquant, vide ou présente une valeur Null. Il est également possible que la valeur transmise à un paramètre obligatoire ou facultatif ne soit pas valide. Ce problème est généralement dû à un en-tête trop long.
401|Non autorisé |La demande n’est pas autorisée. Vérifiez que votre clé d’abonnement ou votre jeton est valide et dans la région appropriée.
413|Entité de requête trop volumineuse|L’entrée SSML comporte plus de 1 024 caractères.
429|Trop de demandes|Vous avez dépassé le quota ou le taux de requêtes autorisé pour votre abonnement.
502|Passerelle incorrecte | Problème de réseau ou côté serveur. Cette erreur peut également signaler des en-têtes non valides.

Si l’état HTTP est `200 OK`, le corps de la réponse contient un fichier audio au format demandé. Ce fichier peut être lu pendant son transfert ou son enregistrement dans un fichier ou une mémoire tampon en vue d’une lecture ultérieure ou autre utilisation.

## <a name="authentication"></a>Authentification

L’envoi d’une requête à l’API REST du service Speech nécessite une clé d’abonnement ou un jeton d’accès. En général, il est plus facile d’envoyer directement la clé d’abonnement. Le service Speech obtient ensuite le jeton d’accès pour vous. Pour limiter le temps de réponse, vous souhaiterez peut-être utiliser un jeton d’accès à la place.

Pour obtenir un jeton, présentez votre clé d’abonnement à un point de terminaison `issueToken` de service Speech régional, comme indiqué dans le tableau suivant. Utilisez le point de terminaison correspondant à votre région d’abonnement.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-token-service.md)]

Chaque jeton d’accès est valide pour une durée de 10 minutes. Vous pouvez obtenir un nouveau jeton à tout moment. Si vous le souhaitez, vous pouvez obtenir un jeton juste avant chaque requête d’API REST de reconnaissance vocale. Pour réduire la latence et le trafic réseau, nous recommandons d’utiliser le même jeton pendant neuf minutes.

Les sections suivantes montrent comment obtenir un jeton et comment l’utiliser dans une requête.

### <a name="get-a-token-http"></a>Obtenir un jeton : HTTP

L’exemple suivant est un exemple de requête HTTP pour l’obtention d’un jeton. Remplacez `YOUR_SUBSCRIPTION_KEY` par votre clé d’abonnement de service de reconnaissance vocale. Si votre abonnement n’est pas dans la région USA Ouest, remplacez l’en-tête `Host` par le nom d’hôte de votre région.

```
POST /sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
```

Le corps de la réponse à cette requête est le jeton d’accès au format Java Web Token (JWT).

### <a name="get-a-token-powershell"></a>Obtenir un jeton : PowerShell

Le script Windows PowerShell ci-dessous illustre comment obtenir un jeton d’accès. Remplacez `YOUR_SUBSCRIPTION_KEY` par votre clé d’abonnement de service de reconnaissance vocale. Si votre abonnement n’est pas dans la région USA Ouest, modifiez le nom d’hôte de l’URI fournie en conséquence.

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

### <a name="get-a-token-curl"></a>Obtenir un jeton : cURL

cURL est un outil en ligne de commande disponible dans Linux (ainsi que dans le sous-système Windows pour Linux). La commande cURL ci-dessous illustre comment obtenir un jeton d’accès. Remplacez `YOUR_SUBSCRIPTION_KEY` par votre clé d’abonnement de service de reconnaissance vocale. Si votre abonnement n’est pas dans la région USA Ouest, modifiez le nom d’hôte de l’URI fournie en conséquence.

> [!NOTE]
> La commande est affichée sur plusieurs lignes pour plus de lisibilité, mais vous devez la taper sur une seule ligne à l’invite de l’interpréteur de commandes.

```
curl -v -X POST
 "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken"
 -H "Content-type: application/x-www-form-urlencoded"
 -H "Content-Length: 0"
 -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

### <a name="get-a-token-c"></a>Obtenir un jeton : C#

La classe C# ci-dessous illustre comment obtenir un jeton d’accès. Transmettez votre clé d’abonnement du service Speech quand vous instanciez la classe. Si votre abonnement n’est pas dans la région USA Ouest, modifiez le nom d’hôte de `FetchTokenUri` en conséquence.

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

### <a name="use-a-token"></a>Utilisation d’un jeton

Afin d’utiliser un jeton dans une requête API REST, fournissez le dans l’en-tête `Authorization`, à la suite du mot `Bearer`. Voici un exemple de requête REST de synthèse vocale qui contient un jeton. Remplacez `YOUR_ACCESS_TOKEN` par votre propre jeton. Utilisez le nom d’hôte correct dans l’en-tête `Host`.

```xml
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

### <a name="renew-authorization"></a>Renouveler une autorisation

Le jeton d’autorisation expire après 10 minutes. Renouvelez votre autorisation en obtenant un nouveau jeton avant son expiration. Vous pouvez par exemple obtenir un nouveau jeton après neuf minutes.

Le code C# suivant est un remplacement instantané pour la classe présentée précédemment. La classe `Authentication` obtient automatiquement un nouveau jeton d’accès toutes les neuf minutes à l’aide d’un minuteur. Cette approche garantit qu’un jeton valide est toujours disponible pendant l’exécution de votre programme.

> [!NOTE]
> Au lieu d’utiliser un minuteur, vous pouvez stocker un horodatage de l’obtention du dernier jeton. Vous pouvez ensuite en demander un nouveau uniquement s’il est sur le point d’expirer. Cette approche évite de demander de nouveaux jetons inutilement, et peut être plus adaptée pour les programmes qui effectuent des requêtes de reconnaissance vocale peu fréquentes.

Comme précédemment, assurez-vous que la valeur `FetchTokenUri` correspond à la région de votre abonnement. Transmettez votre clé d’abonnement quand vous instanciez la classe.

```cs
/*
    * This class demonstrates how to maintain a valid access token.
    */
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

## <a name="next-steps"></a>Étapes suivantes

- [Obtenir votre abonnement d’essai gratuit au service Speech](https://azure.microsoft.com/try/cognitive-services/)
- [Personnaliser les modèles acoustiques](how-to-customize-acoustic-models.md)
- [Personnaliser les modèles de langage](how-to-customize-language-model.md)
