---
title: API REST du service de reconnaissance vocale | Microsoft Docs
description: Informations de référence pour les API REST du service de reconnaissance vocale.
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 05/09/2018
ms.author: v-jerkin
ms.openlocfilehash: 311d0cb7f208c0f720b8611510fb65efc65c12bc
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39112871"
---
# <a name="speech-service-rest-apis"></a>API REST du service de reconnaissance vocale

Les API REST du service de reconnaissance vocale unifié sont similaires aux API fournies par l’[API Speech](https://docs.microsoft.com/azure/cognitive-services/Speech) (anciennement appelée Service de reconnaissance vocale Bing). Les points de terminaison sont différents des points de terminaison utilisés par le service de reconnaissance vocale précédent.

## <a name="speech-to-text"></a>Reconnaissance vocale

Dans l’API de reconnaissance vocale, seuls les points de terminaison utilisés diffèrent du service de reconnaissance vocale précédent API de reconnaissance vocale. Les nouveaux points de terminaison sont affichés dans le tableau ci-dessous. Utilisez celui qui correspond à votre région d’abonnement.

[!include[](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

L’API de reconnaissance vocale est par ailleurs similaire à l’[API REST](https://docs.microsoft.com/azure/cognitive-services/speech/getstarted/getstartedrest) pour l’API de reconnaissance vocale précédente.

L’API REST de reconnaissance vocale ne prend en charge que des énoncés courts. Les requêtes peuvent contenir jusqu’à 10 secondes de contenu audio et durer 14 secondes maximum. L’API REST ne renvoie que les résultats finaux, pas les résultats partiels ou intermédiaires.

> [!NOTE]
> Si vous avez personnalisé le modèle acoustique, le modèle de langage ou la prononciation, utilisez le point de terminaison personnalisé à la place.

## <a name="text-to-speech"></a>Synthèse vocale

La nouvelle API Synthèse vocale prend en charge 24 KHz de sortie audio. L’en-tête `X-Microsoft-OutputFormat` peut désormais contenir les valeurs suivantes.

|||
|-|-|
`raw-16khz-16bit-mono-pcm`         | `audio-16khz-16kbps-mono-siren`
`riff-16khz-16kbps-mono-siren`     | `riff-16khz-16bit-mono-pcm`
`audio-16khz-128kbitrate-mono-mp3` | `audio-16khz-64kbitrate-mono-mp3`
`audio-16khz-32kbitrate-mono-mp3`  | `raw-24khz-16bit-mono-pcm`
`riff-24khz-16bit-mono-pcm`        | `audio-24khz-160kbitrate-mono-mp3`
`audio-24khz-96kbitrate-mono-mp3`  | `audio-24khz-48kbitrate-mono-mp3`

Le service de reconnaissance vocale fournit désormais deux voix 24 KHz :

Paramètres régionaux | Langage   | Sexe | Mappage du nom du service
-------|------------|--------|------------
fr-FR  | Français | Féminin | « Voix de synthèse vocale pour la reconnaissance vocale Microsoft Server (en-US, Jessa24kRUS) » 
fr-FR  | Français | Masculin   | « Voix de synthèse vocale pour la reconnaissance vocale Microsoft Server (en-US, Guy24kRUS) »

Voici les points de terminaison REST pour le service vocal unifié API Synthèse vocale. Utilisez le point de terminaison correspondant à votre région d’abonnement.

[!include[](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

Gardez ces différences à l’esprit lorsque vous vous référez à la [documentation de l’API REST](https://docs.microsoft.com/azure/cognitive-services/speech/api-reference-rest/bingvoiceoutput) pour l’API Microsoft Speech précédente.

## <a name="authentication"></a>Authentification

L’envoi d’une requête à l’API REST du service Microsoft Speech nécessite un jeton d’accès. Vous obtenez un jeton en fournissant votre clé d’abonnement à un point de terminaison `issueToken` de service Microsoft Speech régional, affiché dans le tableau ci-dessous. Utilisez le point de terminaison correspondant à votre région d’abonnement.

[!include[](../../../includes/cognitive-services-speech-service-endpoints-token-service.md)]

Chaque jeton d’accès est valide pour une durée de 10 minutes. Vous pouvez obtenir un nouveau jeton à tout moment ; notamment, si vous le souhaitez, juste avant chaque requête API REST Microsoft Speech. Pour réduire la latence et le trafic réseau, toutefois, nous recommandons d’utiliser le même jeton durant neuf minutes.

Les sections suivantes montrent comment obtenir un jeton et comment l’utiliser dans une requête.

### <a name="getting-a-token-http"></a>Obtenir un jeton : HTTP

Ci-dessous vous trouverez un exemple de requête HTTP pour l’obtention d’un jeton. Remplacez `YOUR_SUBSCRIPTION_KEY` par votre clé d’abonnement de service de reconnaissance vocale. Si votre abonnement n’est pas dans la région USA Ouest, remplacez l’en-tête `Host` par le nom d’hôte de votre région.

```
POST /sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
```

Le corps de la réponse à cette requête est le jeton d’accès au format Java Web Token (JWT).

### <a name="getting-a-token-powershell"></a>Obtenir un jeton : PowerShell

Le script Windows PowerShell ci-dessous illustre comment obtenir un jeton d’accès. Remplacez `YOUR_SUBSCRIPTION_KEY` par votre clé d’abonnement de service de reconnaissance vocale. Si votre abonnement n’est pas dans la région USA Ouest, modifiez le nom d’hôte de l’URI fournie en conséquences.

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

### <a name="getting-a-token-curl"></a>Obtenir un jeton : cURL

cURL est un outil en ligne de commande disponible dans Linux (ainsi que dans le sous-système Windows pour Linux). La commande cURL ci-dessous illustre l’obtention d’un jeton d’accès. Remplacez `YOUR_SUBSCRIPTION_KEY` par votre clé d’abonnement de service de reconnaissance vocale. Si votre abonnement n’est pas dans la région USA Ouest, modifiez le nom d’hôte de l’URI fournie en conséquences.

> [!NOTE]
> La commande est affichée sur plusieurs lignes pour plus de lisibilité, mais doit être saisie sur une seule ligne à l’invite de l’interpréteur de commandes.

```
curl -v -X POST 
 "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken" 
 -H "Content-type: application/x-www-form-urlencoded" 
 -H "Content-Length: 0" 
 -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

### <a name="getting-a-token-c"></a>Obtenir un jeton : C#

La classe C# ci-dessous illustre l’obtention d’un jeton d’accès. Transmettez votre clé d’abonnement du service de reconnaissance vocale lors de l’instanciation de la classe. Si votre abonnement n’est pas dans la région USA Ouest, modifiez le nom d’hôte de `FetchTokenUri` en conséquences.

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

### <a name="using-a-token"></a>Utiliser un jeton

Afin d’utiliser un jeton dans une requête API REST, fournissez le dans l’en-tête `Authorization`, à la suite du mot `Bearer`. Voici, par exemple, un exemple de requête REST de synthèse vocale contenant un jeton. Remplacez votre jeton actuel par `YOUR_ACCESS_TOKEN` et utilisez le nom d’hôte approprié dans l’en-tête `Host`.

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

### <a name="renewing-authorization"></a>Renouvellement de l’autorisation

Le jeton d’autorisation expire après 10 minutes. Renouvelez votre autorisation en obtenant un nouveau jeton avant son expiration, par exemple, après neuf minutes. 

Le code C# suivant est un remplacement instantané pour la classe présentée précédemment. La classe `Authentication` obtient automatiquement un nouveau jeton d’accès toutes les neuf minutes à l’aide d’un minuteur. Cette approche garantit qu’un jeton valide est toujours disponible pendant l’exécution de votre programme.

> [!NOTE]
> Au lieu d’utiliser un minuteur, vous pourriez stocker un timestamp du moment où le jeton actuel a été obtenu, puis en demander un nouveau seulement si le jeton actuel s’apprête à expirer. Cette approche évite de demander de nouveaux jetons inutilement et peut être plus adaptée pour les programmes qui effectuent des demandes de reconnaissance vocale peu fréquentes.

Comme précédemment, assurez-vous que la valeur `FetchTokenUri` correspond à la région de votre abonnement. Transmettez votre clé d’abonnement lors de l’instanciation de la classe.

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

