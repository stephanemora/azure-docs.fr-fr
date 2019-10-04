---
title: S’authentifier auprès de la Reconnaissance vocale Bing | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Exiger une authentification pour utiliser l’API Reconnaissance vocale Bing
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: d1e708ff29293b87935d0d191ba44ad4a11917a0
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965654"
---
# <a name="authenticate-to-the-speech-api"></a>S’authentifier auprès de l’API Microsoft Speech

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

La Reconnaissance vocale Bing prend en charge l’authentification avec :

- Une clé d’abonnement
- Un jeton d’autorisation

## <a name="use-a-subscription-key"></a>Utiliser une clé d’abonnement

Pour utiliser le service Speech, vous devez d’abord vous abonner à l’API Microsoft Speech qui fait partie de Cognitive Services (précédemment Project Oxford). Vous pouvez obtenir des clés d’abonnement d’essai à partir de la page [d’abonnement à Cognitive Services](https://azure.microsoft.com/try/cognitive-services/). Après avoir sélectionné l’API Microsoft Speech, sélectionnez **Obtenir la clé API** pour obtenir la clé. Cette opération renvoie une clé primaire et une clé secondaire. Les deux clés étant liées au même quota, vous pouvez utiliser l’une ou l’autre.

Pour une utilisation à long terme ou une augmentation du quota, inscrivez-vous pour obtenir un [compte Azure](https://azure.microsoft.com/free/).

Pour utiliser l’API REST Speech, vous devez passer la clé d’abonnement dans le champ `Ocp-Apim-Subscription-Key` de l’en-tête de la demande.

Nom| Format| Description
----|-------|------------
Ocp-Apim-Subscription-Key | ASCII | YOUR_SUBSCRIPTION_KEY

Voici un exemple d’en-tête de demande :

```HTTP
POST https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codec=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: speech.platform.bing.com
Transfer-Encoding: chunked
Expect: 100-continue
```

> [!IMPORTANT]
> Si vous utilisez des [bibliothèques clientes](../GetStarted/GetStartedClientLibraries.md) dans votre application, vérifiez que vous pouvez obtenir le jeton d’autorisation avec votre clé d’abonnement, comme décrit dans la section suivante. Les bibliothèques clientes utilisent la clé d’abonnement pour obtenir un jeton d’autorisation, puis utilisent le jeton pour l’authentification.

## <a name="use-an-authorization-token"></a>Utiliser un jeton d’autorisation

Vous pouvez également utiliser un jeton d’autorisation pour l’authentification comme preuve d’authentification/autorisation. Pour obtenir ce jeton, vous devez d’abord obtenir une clé d’abonnement auprès de l’API Microsoft Speech, comme décrit dans la [section précédente](#use-a-subscription-key).

### <a name="get-an-authorization-token"></a>Obtenir un jeton d’autorisation

Une fois que vous avez une clé d’abonnement valide, envoyez une demande POST au service de jetons de Cognitive Services. Dans la réponse, vous recevez le jeton d’autorisation au format JWT (JSON Web Token).

> [!NOTE]
> Le délai d’expiration du jeton est de 10 minutes. Pour renouveler le jeton, consultez la section suivante.

L’URI du service de jetons se trouve ici :

```
https://api.cognitive.microsoft.com/sts/v1.0/issueToken
```

L’exemple de code suivant montre comment obtenir un jeton d’accès. Remplacez `YOUR_SUBSCRIPTION_KEY` par votre propre clé d’abonnement :

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```Powershell
$FetchTokenHeader = @{
  'Content-type'='application/x-www-form-urlencoded';
  'Content-Length'= '0';
  'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
}

$OAuthToken = Invoke-RestMethod -Method POST -Uri https://api.cognitive.microsoft.com/sts/v1.0/issueToken -Headers $FetchTokenHeader

# show the token received
$OAuthToken

```

# <a name="curltabcurl"></a>[curl](#tab/curl)

L’exemple utilise curl sur Linux avec bash. S’il n’est pas disponible sur votre plateforme, vous devrez peut-être installer curl. L’exemple fonctionne également sur Cygwin sur Windows, Git Bash, zsh et d’autres interpréteurs de commandes.

```
curl -v -X POST "https://api.cognitive.microsoft.com/sts/v1.0/issueToken" -H "Content-type: application/x-www-form-urlencoded" -H "Content-Length: 0" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
    /*
     * This class demonstrates how to get a valid access token.
     */
    public class Authentication
    {
        public static readonly string FetchTokenUri = "https://api.cognitive.microsoft.com/sts/v1.0";
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
                uriBuilder.Path += "/issueToken";

                var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
                Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
                return await result.Content.ReadAsStringAsync();
            }
        }
    }
```

---

Voici un exemple de demande POST :

```HTTP
POST https://api.cognitive.microsoft.com/sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
Connection: Keep-Alive
```

Si vous ne pouvez pas obtenir un jeton d’autorisation auprès du service de jetons, vérifiez si votre clé d’abonnement est toujours valide. Si vous utilisez une clé d’essai, accédez à la page [d’abonnement à Cognitive Services](https://azure.microsoft.com/try/cognitive-services/), cliquez sur « Se connecter » pour vous connecter à l’aide du compte que vous avez utilisé pour appliquer la clé d’essai et vérifiez si la clé d’abonnement est arrivée à expiration ou dépasse le quota.

### <a name="use-an-authorization-token-in-a-request"></a>Utiliser un jeton d’autorisation dans une demande

Chaque fois que vous appelez l’API Microsoft Speech, vous devez passer le jeton d’autorisation dans l’en-tête `Authorization`. L’en-tête `Authorization` doit contenir un jeton d’accès JWT.

L’exemple suivant montre comment utiliser un jeton d’autorisation quand vous appelez l’API REST Speech.

> [!NOTE]
> Remplacez `YOUR_AUDIO_FILE` par le chemin de votre fichier audio préenregistré. Remplacez `YOUR_ACCESS_TOKEN` par le jeton d’autorisation que vous avez obtenu à l’étape précédente [Obtenir un jeton d’autorisation](#get-an-authorization-token).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```Powershell

$SpeechServiceURI =
'https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed'

# $OAuthToken is the authrization token returned by the token service.
$RecoRequestHeader = @{
  'Authorization' = 'Bearer '+ $OAuthToken;
  'Transfer-Encoding' = 'chunked'
  'Content-type' = 'audio/wav; codec=audio/pcm; samplerate=16000'
}

# Read audio into byte array
$audioBytes = [System.IO.File]::ReadAllBytes("YOUR_AUDIO_FILE")

$RecoResponse = Invoke-RestMethod -Method POST -Uri $SpeechServiceURI -Headers $RecoRequestHeader -Body $audioBytes

# Show the result
$RecoResponse

```

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -v -X POST "https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed" -H "Transfer-Encoding: chunked" -H "Authorization: Bearer YOUR_ACCESS_TOKEN" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
```

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
HttpWebRequest request = null;
request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
request.SendChunked = true;
request.Accept = @"application/json;text/xml";
request.Method = "POST";
request.ProtocolVersion = HttpVersion.Version11;
request.Host = @"speech.platform.bing.com";
request.ContentType = @"audio/wav; codec=audio/pcm; samplerate=16000";
request.Headers["Authorization"] = "Bearer " + token;

// Send an audio file by 1024 byte chunks
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

---

### <a name="renew-an-authorization-token"></a>Renouveler un jeton d’autorisation

Le jeton d’autorisation expire après une période donnée (10 minutes). Vous devez renouveler le jeton d’autorisation avant son expiration.

Le code suivant est un exemple d’implémentation en C# montrant comment renouveler le jeton d’autorisation :

```cs
    /*
     * This class demonstrates how to get a valid O-auth token.
     */
    public class Authentication
    {
        public static readonly string FetchTokenUri = "https://api.cognitive.microsoft.com/sts/v1.0";
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
                uriBuilder.Path += "/issueToken";

                var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
                Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
                return await result.Content.ReadAsStringAsync();
            }
        }
    }
```
