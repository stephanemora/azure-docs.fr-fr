---
title: Dépannage du kit de développement logiciel Speech de Microsoft Cognitive Services | Microsoft Docs
description: Dépannage du kit de développement logiciel (SDK) Speech de Microsoft Cognitive Services
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: wolfma
ms.openlocfilehash: 16eaebcf9494ab57521068a9418ccf2ac7f5a8fe
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35369953"
---
# <a name="troubleshooting-speech-services-sdk"></a>Dépannage du SDK des services Speech

Cet article fournit des informations pour vous aider à résoudre les problèmes éventuels d’utilisation du kit de développement Speech.

## <a name="error-websocket-upgrade-failed-with-an-authentication-error-403"></a>Erreur `WebSocket Upgrade failed with an authentication error (403).`

Le point de terminaison pour votre service ou votre région est peut-être incorrect. Vérifiez une nouvelle fois l’URI pour vous assurer qu’il est correct. Consultez également la section suivante, car il pourrait s’agir d’un problème concernant la clé d’abonnement ou le jeton d’autorisation.

## <a name="error-http-403-forbidden-or-error-http-401-unauthorized"></a>Erreur `HTTP 403 Forbidden` ou erreur `HTTP 401 Unauthorized`

Cette erreur est souvent due à des problèmes d’authentification. Les demandes de connexion sans en-tête `Ocp-Apim-Subscription-Key` ou `Authorization` valide sont rejetées. Elles renvoient un code d’état 401 ou 403.

* Si vous utilisez une clé d’abonnement pour l’authentification, en voici les raisons possibles :

    - La clé d’abonnement est manquante ou non valide.
    - Vous avez dépassé le quota auquel vous aviez droit dans votre abonnement.

* Si vous utilisez un jeton d’autorisation pour l’authentification, en voici les raisons possibles :

    - Le jeton d’autorisation n’est pas valide.
    - Le jeton d’autorisation a expiré.

### <a name="validate-your-subscription-key"></a>Valider votre clé d’abonnement

Assurez-vous de détenir une clé d’abonnement valide. Pour cela, exécutez l’une des commandes ci-dessous.

> [!NOTE]
> Remplacez respectivement `YOUR_SUBSCRIPTION_KEY` et `YOUR_REGION` par votre propre clé d’abonnement et la région associée.

* PowerShell

    ```Powershell
    $FetchTokenHeader = @{
      'Content-type'='application/x-www-form-urlencoded'
      'Content-Length'= '0'
      'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
    }
    $OAuthToken = Invoke-RestMethod -Method POST -Uri https://YOUR_REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken -Headers $FetchTokenHeader
    $OAuthToken
    ```

* cURL

    ```
    curl -v -X POST "https://YOUR_REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY" -H "Content-type: application/x-www-form-urlencoded" -H "Content-Length: 0"
    ```

### <a name="validate-an-authorization-token"></a>Valider un jeton d’autorisation

Si vous utilisez un jeton d’autorisation pour l’authentification, exécutez l’une des commandes suivantes pour vérifier que le jeton d’autorisation est toujours valide. Les jetons sont valides pendant 10 minutes.

> [!NOTE]
> Remplacez `YOUR_AUDIO_FILE` par le chemin d’accès au fichier audio préenregistré, `YOUR_ACCESS_TOKEN` par le jeton d’autorisation renvoyé à l’étape précédente et `YOUR_REGION` par la région qui convient.

* PowerShell

    ```Powershell
    $SpeechServiceURI =
    'https://YOUR_REGION.stt.speech.microsoft.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US'
    
    # $OAuthToken is the authorization token returned by the token service.
    $RecoRequestHeader = @{
      'Authorization' = 'Bearer '+ $OAuthToken
      'Transfer-Encoding' = 'chunked'
      'Content-type' = 'audio/wav; codec=audio/pcm; samplerate=16000'
    }
    
    # Read audio into byte array
    $audioBytes = [System.IO.File]::ReadAllBytes("YOUR_AUDIO_FILE")
    
    $RecoResponse = Invoke-RestMethod -Method POST -Uri $SpeechServiceURI -Headers $RecoRequestHeader -Body $audioBytes
    
    # Show the result
    $RecoResponse
    ```

* cURL

    ```
    curl -v -X POST "https://YOUR_REGION.stt.speech.microsoft.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US" -H "Authorization: Bearer YOUR_ACCESS_TOKEN" -H "Transfer-Encoding: chunked" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
    ```

---

## <a name="error-http-400-bad-request"></a>Erreur `HTTP 400 Bad Request`

Cette erreur est généralement due au fait que le corps de la requête contient des données audio non valides. Seul le format `WAV` est pris en charge. De même, vérifiez les en-têtes de la requête pour vous assurer que vous spécifiez des valeurs `Content-Type` et `Content-Length` appropriées.

## <a name="error-http-408-request-timeout"></a>Erreur `HTTP 408 Request Timeout`

L’erreur est probablement due au fait qu’aucune donnée audio n’est envoyée au service. Cette erreur peut également avoir lieu en cas de problèmes sur le réseau.

## <a name="the-recognitionstatus-in-the-response-is-initialsilencetimeout"></a>L’élément `RecognitionStatus` dans la réponse est `InitialSilenceTimeout`

Des données audio sont généralement à l’origine de ce problème. Par exemple : 

* Il existe un long stretch de silence au début de l’enregistrement audio. Le service arrête la reconnaissance après un certain nombre de secondes et renvoie `InitialSilenceTimeout`.
* Le contenu audio utilise un format de codec non pris en charge, ce qui fait que les données audio sont traitées comme s’il s’agissait d’un silence.

## <a name="next-steps"></a>Étapes suivantes

* [Notes de publication](releasenotes.md)

