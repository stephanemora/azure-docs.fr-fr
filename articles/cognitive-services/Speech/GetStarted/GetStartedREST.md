---
title: Bien démarrer avec l’API Reconnaissance vocale Bing à l’aide de REST | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Utilisez REST pour accéder à l’API Reconnaissance vocale dans Microsoft Cognitive Services afin de convertir du contenu vocal en texte.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ead4026ecec4878c69bc21a9ebc989eaf3d69a13
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60515132"
---
# <a name="quickstart-use-the-bing-speech-recognition-rest-api"></a>Démarrage rapide : Utiliser l’API REST de reconnaissance vocale Bing

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Le service cloud Reconnaissance vocale Bing vous permet de développer des applications à l’aide de l’API REST pour convertir du contenu vocal en texte.

## <a name="prerequisites"></a>Prérequis

### <a name="subscribe-to-the-speech-api-and-get-a-free-trial-subscription-key"></a>S’abonner à l’API Microsoft Speech et obtenir une clé d’abonnement d’essai

L’API Microsoft Speech fait partie de Cognitive Services (anciennement Project Oxford). Vous pouvez obtenir des clés d’abonnement d’essai à partir de la page [d’abonnement à Cognitive Services](https://azure.microsoft.com/try/cognitive-services/). Après avoir sélectionné l’API Microsoft Speech, sélectionnez **Obtenir la clé API** pour obtenir la clé. Cette opération renvoie une clé primaire et une clé secondaire. Les deux clés étant liées au même quota, vous pouvez utiliser l’une ou l’autre.

> [!IMPORTANT]
>* Obtenez une clé d’abonnement. Pour accéder à l’API REST, vous devez avoir une [clé d’abonnement](https://azure.microsoft.com/try/cognitive-services/).
>
>* Utilisez votre clé d’abonnement. Dans les exemples de REST suivants, remplacez YOUR_SUBSCRIPTION_KEY par votre propre clé d’abonnement.
>
>* Reportez-vous à la page [d’authentification](../how-to/how-to-authentication.md) pour savoir comment obtenir une clé d’abonnement.

### <a name="prerecorded-audio-file"></a>Fichier audio préenregistré

Dans cet exemple, nous utilisons un fichier audio enregistré pour illustrer l’utilisation de l’API REST. Enregistrez vous-même un fichier audio en prononçant une expression courte. Par exemple, « Quel temps fait-il aujourd’hui ? » ou « Rechercher des films amusants ». L’API de reconnaissance vocale prend également en charge l’entrée de microphone externe.

> [!NOTE]
> L’exemple requiert que le contenu audio soit enregistré dans un fichier WAV avec encodage **PCM canal unique (mono), 16 KHz**.

## <a name="build-a-recognition-request-and-send-it-to-the-speech-recognition-service"></a>Générer une demande de reconnaissance et l’envoyer au service de reconnaissance vocale

L’étape suivante de la reconnaissance vocale consiste à envoyer une demande POST aux points de terminaison HTTP Speech avec l’en-tête et le corps de demande appropriés.

### <a name="service-uri"></a>URI de service

L’URI du service de reconnaissance vocale est défini en fonction des [modes de reconnaissance](../concepts.md#recognition-modes) et des [langues de reconnaissance](../concepts.md#recognition-languages) :

```HTTP
https://speech.platform.bing.com/speech/recognition/<RECOGNITION_MODE>/cognitiveservices/v1?language=<LANGUAGE_TAG>&format=<OUTPUT_FORMAT>
```

`<RECOGNITION_MODE>` spécifie le mode de reconnaissance et doit être défini sur une des valeurs suivantes : `interactive`, `conversation` ou `dictation`. Ce paramètre représente un chemin de ressource requis dans l’URI. Pour plus d’informations, consultez [Modes de reconnaissance](../concepts.md#recognition-modes).

`<LANGUAGE_TAG>` est un paramètre obligatoire dans la chaîne de requête. Il définit la langue cible pour la conversion audio : par exemple, `en-US` pour l’anglais (États-Unis). Pour plus d’informations, consultez [Langues de reconnaissance](../concepts.md#recognition-languages).

`<OUTPUT_FORMAT>` est un paramètre facultatif dans la chaîne de requête. Ses valeurs autorisées sont `simple` et `detailed`. Par défaut, le service retourne les résultats dans le format `simple`. Pour plus d’informations, consultez [Format de sortie](../concepts.md#output-format).

Quelques exemples d’URI de service sont répertoriés dans le tableau suivant.

| Mode de reconnaissance  | Langage | Format de sortie | URI de service |
|---|---|---|---|
| `interactive` | pt-br | Default | https:\//speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=pt-BR |
| `conversation` | en-US | Détaillé | https :\//speech.platform.bing.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US & format = détaillées |
| `dictation` | fr-FR | Simple | https:\//speech.platform.bing.com/speech/recognition/dictation/cognitiveservices/v1?language=fr-FR&format=simple |

> [!NOTE]
> L’URI de service n’est nécessaire que quand votre application utilise des API REST pour appeler le service de reconnaissance vocale. Si vous utilisez une des [bibliothèques clientes](GetStartedClientLibraries.md), vous n’avez généralement pas besoin de savoir quel URI est utilisé. Les bibliothèques clientes peuvent utiliser différents URI de service, applicables uniquement à une bibliothèque cliente spécifique. Pour plus d’informations, consultez la bibliothèque de client de votre choix.

### <a name="request-headers"></a>En-têtes de requête

Vous devez définir les champs suivants dans l’en-tête de la demande :

- `Ocp-Apim-Subscription-Key`: Chaque fois que vous appelez le service, vous devez passer votre clé d’abonnement dans l’en-tête `Ocp-Apim-Subscription-Key`. Le service Speech prend également en charge la transmission de jetons d’autorisation à la place de clés d’abonnement. Pour en savoir plus, consultez [Authentification](../How-to/how-to-authentication.md).
- `Content-type`: le champ `Content-type` décrit le format et le codec du flux audio. Seul le format de fichier WAV et l’encodage PCM Mono 16000 sont pris en charge. La valeur de Content-type pour ce format est `audio/wav; codec=audio/pcm; samplerate=16000`.

Le champ `Transfer-Encoding` est facultatif. Si vous définissez ce champ sur `chunked`, vous pouvez fractionner le contenu audio en petits blocs. Pour plus d’informations, consultez [Transfert en bloc](../How-to/how-to-chunked-transfer.md).

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

### <a name="send-a-request-to-the-service"></a>Envoyer une demande au service

L’exemple suivant montre comment envoyer une demande de reconnaissance vocale aux points de terminaison REST Speech. Il utilise le mode de reconnaissance `interactive`.

> [!NOTE]
> Remplacez `YOUR_AUDIO_FILE` par le chemin de votre fichier audio préenregistré. Remplacez `YOUR_SUBSCRIPTION_KEY` par votre propre clé d’abonnement.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```Powershell

$SpeechServiceURI =
'https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed'

# $OAuthToken is the authorization token returned by the token service.
$RecoRequestHeader = @{
  'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY';
  'Transfer-Encoding' = 'chunked';
  'Content-type' = 'audio/wav; codec=audio/pcm; samplerate=16000'
}

# Read audio into byte array
$audioBytes = [System.IO.File]::ReadAllBytes("YOUR_AUDIO_FILE")

$RecoResponse = Invoke-RestMethod -Method POST -Uri $SpeechServiceURI -Headers $RecoRequestHeader -Body $audioBytes

# Show the result
$RecoResponse

```

# <a name="curltabcurl"></a>[curl](#tab/curl)

L’exemple utilise curl sur Linux avec bash. S’il n’est pas disponible sur votre plateforme, vous devrez peut-être installer curl. L’exemple fonctionne également sur Cygwin sur Windows, Git Bash, zsh et d’autres interpréteurs de commandes.

> [!NOTE]
> Conservez le caractère `@` avant le nom du fichier audio quand vous remplacez `YOUR_AUDIO_FILE` par le chemin de votre fichier audio préenregistré, car il indique que la valeur de `--data-binary` est un nom de fichier au lieu de données.

```
curl -v -X POST "https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed" -H "Transfer-Encoding: chunked" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
```

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
HttpWebRequest request = null;
request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
request.SendChunked = true;
request.Accept = @"application/json;text/xml";
request.Method = "POST";
request.ProtocolVersion = HttpVersion.Version11;
request.ContentType = @"audio/wav; codec=audio/pcm; samplerate=16000";
request.Headers["Ocp-Apim-Subscription-Key"] = "YOUR_SUBSCRIPTION_KEY";

// Send an audio file by 1024 byte chunks
using (FileStream fs = new FileStream(YOUR_AUDIO_FILE, FileMode.Open, FileAccess.Read))
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

## <a name="process-the-speech-recognition-response"></a>Traiter la réponse de la reconnaissance vocale

Après avoir traité la demande, le service Speech retourne les résultats dans une réponse au format JSON.

> [!NOTE]
> Si le code précédent retourne une erreur, consultez [Dépannage](../troubleshooting.md) pour localiser la cause possible.

L’extrait de code suivant montre comment vous pouvez lire la réponse à partir du flux.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```Powershell
# show the response in JSON format
ConvertTo-Json $RecoResponse
```

# <a name="curltabcurl"></a>[curl](#tab/curl)

Dans cet exemple, curl retourne directement le message de réponse dans une chaîne. Si vous souhaitez l’afficher dans le format JSON, vous pouvez utiliser des outils supplémentaires, par exemple, jq.

```
curl -X POST "https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed" -H "Transfer-Encoding: chunked" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE | jq
```

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
/*
* Get the response from the service.
*/
Console.WriteLine("Response:");
using (WebResponse response = request.GetResponse())
{
    Console.WriteLine(((HttpWebResponse)response).StatusCode);

    using (StreamReader sr = new StreamReader(response.GetResponseStream()))
    {
        responseString = sr.ReadToEnd();
    }

    Console.WriteLine(responseString);
    Console.ReadLine();
}
```

---

L’exemple suivant est une réponse JSON :

```json
OK
{
  "RecognitionStatus": "Success",
  "Offset": 22500000,
  "Duration": 21000000,
  "NBest": [{
    "Confidence": 0.941552162,
    "Lexical": "find a funny movie to watch",
    "ITN": "find a funny movie to watch",
    "MaskedITN": "find a funny movie to watch",
    "Display": "Find a funny movie to watch."
  }]
}
```

## <a name="limitations"></a>Limites

L’API REST présente certaines limitations :

- Elle prend en charge les flux audio uniquement jusqu’à 15 secondes.
- Elle ne prend pas en charge les résultats intermédiaires pendant la reconnaissance. Les utilisateurs reçoivent uniquement le résultat de reconnaissance final.

Pour supprimer ces limitations, utilisez les [bibliothèques clientes](GetStartedClientLibraries.md) Speech, ou bien utilisez directement le [protocole WebSocket Speech](../API-Reference-REST/websocketprotocol.md).

## <a name="whats-next"></a>Étapes suivantes

- Pour voir comment utiliser l’API REST en C#, Java, etc., consultez ces [exemples d’applications](../samples.md).
- Pour rechercher et corriger les erreurs, consultez [Dépannage](../troubleshooting.md).
- Pour utiliser des fonctionnalités plus avancées, découvrez comment démarrer avec les [bibliothèques clientes](GetStartedClientLibraries.md) Speech.

### <a name="license"></a>Licence

Tous les kits SDK Cognitive Services et les exemples sont fournis sous licence MIT. Pour plus d’informations, consultez [Licence](https://github.com/Microsoft/Cognitive-Speech-STT-JavaScript/blob/master/LICENSE.md).
