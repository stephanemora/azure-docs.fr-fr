---
title: Résolution des problèmes rencontrés avec la Reconnaissance vocale Bing | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Comment résoudre les problèmes rencontrés lors de l’utilisation de la Reconnaissance vocale Bing
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ms.openlocfilehash: bcb8b421eeb0db215aa21b93a7792e0e978a4f48
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55215467"
---
# <a name="troubleshooting-bing-speech"></a>Résolution des problèmes rencontrés avec la Reconnaissance vocale Bing

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

## <a name="error-http-403-forbidden"></a>Erreur `HTTP 403 Forbidden`

Lorsque vous utilisez l’API de reconnaissance vocale, elle retourne une erreur `HTTP 403 Forbidden`.

### <a name="cause"></a>Cause :

Cette erreur est souvent due à des problèmes d’authentification. Les demandes de connexion sans en-tête `Ocp-Apim-Subscription-Key` ou `Authorization` valide sont rejetées par le service avec une réponse `HTTP 403 Forbidden`.

Si vous utilisez une clé d’abonnement pour l’authentification, la raison peut être que

- la clé d’abonnement est manquante ou non valide
- le quota d’utilisation de la clé d’abonnement est dépassé
- le champ `Ocp-Apim-Subscription-Key` n’est pas défini dans l’en-tête de demande, lorsque l’API REST est appelée.

Si vous utilisez un jeton d’autorisation pour l’authentification, les raisons suivantes peuvent provoquer l’erreur.

- L’en-tête `Authorization` est manquant dans la demande lors de l’utilisation de REST.
- Le jeton d’autorisation spécifié dans l’en-tête d’autorisation est non valide.
- Le jeton d'autorisation a expiré. Le jeton d’accès expire dans un délai de 10 minutes.

Pour plus d’informations sur l’authentification, consultez la page [Authentification](How-to/how-to-authentication.md).

### <a name="troubleshooting-steps"></a>Étapes de dépannage

#### <a name="verify-that-your-subscription-key-is-valid"></a>Vérifiez que votre clé d’abonnement est valide.

Vous pouvez exécuter la commande suivante à titre de vérification. Pensez à remplacer *YOUR_SUBSCRIPTION_KEY* par votre propre clé d’abonnement. Si votre clé d’abonnement est valide, vous recevez dans la réponse le jeton d’autorisation au format JWT (JSON Web Token). Sinon, vous obtenez une erreur dans la réponse.

> [!NOTE]
> Remplacez `YOUR_SUBSCRIPTION_KEY` par votre propre clé d’abonnement.

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

L’exemple utilise curl sur Linux avec bash. S’il n’est pas disponible sur votre plateforme, vous devrez peut-être installer curl. Cet exemple devrait fonctionner également sur Cygwin sur Windows, Git Bash, zsh et d’autres interpréteurs de commandes.

```
curl -v -X POST "https://api.cognitive.microsoft.com/sts/v1.0/issueToken" -H "Content-type: application/x-www-form-urlencoded" -H "Content-Length: 0" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```
---

Veillez à utiliser la même clé d’abonnement dans votre application ou dans la demande REST que celle utilisée ci-dessus.

#### <a name="verify-the-authorization-token"></a>Vérifiez le jeton d'autorisation.

Cette étape est nécessaire uniquement si vous utilisez un jeton d’autorisation pour l’authentification. Exécutez la commande suivante pour vérifier que le jeton d’autorisation est toujours valide. La commande effectue une demande POST au service et attend un message de réponse du service. Si vous recevez encore l’erreur HTTP `403 Forbidden`, vérifiez bien que le jeton d’accès est défini correctement et n’a pas expiré.

> [!IMPORTANT]
> Le jeton expire dans un délai de 10 minutes.
> [!NOTE]
> Remplacez `YOUR_AUDIO_FILE` par le chemin d’accès à votre fichier audio préenregistré et `YOUR_ACCESS_TOKEN` par le jeton d’autorisation retourné à l’étape précédente.

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

---

## <a name="error-http-400-bad-request"></a>Erreur `HTTP 400 Bad Request`

La raison de cette erreur est généralement que le corps de la demande contient des données audio non valides. Seul le fichier WAV est pris en charge pour le moment.

## <a name="error-http-408-request-timeout"></a>Erreur `HTTP 408 Request Timeout`

Cette erreur est probablement due à ce qu’aucune donnée audio n’est envoyée au service et que le service retourne cette erreur après l’expiration du délai d’attente. Pour l’API REST, les données audio doivent être placées dans le corps de la demande.

## <a name="the-recognitionstatus-in-the-response-is-initialsilencetimeout"></a>L’élément `RecognitionStatus` dans la réponse est `InitialSilenceTimeout`

Des données audio sont généralement à l’origine de ce problème. Par exemple,

- le contenu audio commence par un long silence. Le service arrête la reconnaissance après un certain nombre de secondes et retourne `InitialSilenceTimeout`.
- le contenu audio utilise un format de codec non pris en charge, ce qui fait que les données audio sont traitées comme un silence.
