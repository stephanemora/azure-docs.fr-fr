---
title: Utiliser la synthèse vocale à l’aide des services Speech | Microsoft Docs
description: Découvrez comment utiliser la fonctionnalité Synthèse vocale du service Speech.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 6c358b5a40b1d8e91c2e1af5eb493b13604cf82e
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37045056"
---
# <a name="use-text-to-speech-in-speech-service"></a>Utiliser la fonctionnalité Synthèse vocale du service Speech

Le service Speech vous offre une fonctionnalité de synthèse vocale par le biais d’une simple requête HTTP. Vous PUBLIEZ le texte à énoncer sur le point de terminaison approprié, puis le service vous renvoie un fichier audio (`.wav`) contenant la voix de synthèse. Votre application peut ensuite utiliser ce fichier audio selon vos besoins.

Le corps de la requête de publication POST pour la synthèse vocale peut prendre la forme d’un texte brut (ASCII ou UTF8) ou d’un document [SSML](speech-synthesis-markup.md). Les requêtes portant sur un texte brut sont énoncées avec une voix par défaut. Dans la plupart des cas, vous utiliserez un corps SSML. La requête HTTP doit inclure un jeton d’autorisation. 

Les différents points de terminaison de synthèse vocale régionaux sont indiqués ci-après. Utilisez le point de terminaison adapté à votre abonnement.

[!include[](includes/endpoints-text-to-speech.md)]

## <a name="specify-a-voice"></a>Spécifier une voix

Pour spécifier une voix, utilisez la balise [SSML](speech-synthesis-markup.md) `<voice>`. Par exemple : 

```xml
<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
  <voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>
    Hello, world!
  </voice>
</speak>
```

Pour obtenir la liste des voix disponibles avec leur nom, consultez la section [Text to Speech voices](supported-languages.md#text-to-speech) (Synthèse vocale).

## <a name="make-a-request"></a>Exécuter une requête

Une requête HTTP Synthèse vocale est effectuée en mode POST, le corps de la requête contenant le texte à énoncer. La longueur maximale du corps de la requête HTTP est de 1 024 caractères. La requête doit comporter les en-têtes suivants : 

En-tête|Valeurs|Commentaires
-|-|-
|`Content-Type` | `application/ssml+xml` | Format du texte d’entrée.
|`X-Microsoft-OutputFormat`|     `raw-16khz-16bit-mono-pcm`<br>`audio-16khz-16kbps-mono-siren`<br>`riff-16khz-16kbps-mono-siren`<br>`riff-16khz-16bit-mono-pcm`<br>`audio-16khz-128kbitrate-mono-mp3`<br>`audio-16khz-64kbitrate-mono-mp3`<br>`audio-16khz-32kbitrate-mono-mp3`<br>`raw-24khz-16bit-mono-pcm`<br>`riff-24khz-16bit-mono-pcm`<br>`audio-24khz-160kbitrate-mono-mp3`<br>`audio-24khz-96kbitrate-mono-mp3`<br>`audio-24khz-48kbitrate-mono-mp3` | Format audio de sortie.
|`User-Agent`   |Nom de l’application | Le nom d’application est requis et doit comporter moins de 255 caractères.
| `Authorization`   | Jeton d’autorisation obtenu par présentation de votre clé d’abonnement au service de jetons. Chaque jeton a une durée de validité de dix minutes. Consultez la section [REST APIs: Authentication](rest-apis.md#authentication) (API REST : Authentification).

> [!NOTE]
> Si la voix que vous avez sélectionnée ne présente pas la même vitesse de transmission que le format de sortie spécifié, les données audio sont rééchantillonnées si nécessaire. Les voix à 24 kHz ne prennent pas en charge les formats de sortie `audio-16khz-16kbps-mono-siren` et `riff-16khz-16kbps-mono-siren`. 

Voici un exemple de requête.

```xml
POST /cognitiveservices/v1
HTTP/1.1
Host: westus.tts.speech.microsoft.com
X-Microsoft-OutputFormat: riff-24khz-16bit-mono-pcm
Content-Type: application/ssml+xml
User-Agent: Test TTS application
Authorization: (authorization token)

<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>
    Hello, world!
</voice> </speak>
```

Le corps de la réponse présentant l’état 200 contient les données audio au format de sortie spécifié.

```
HTTP/1.1 200 OK
Content-Length: XXX
Content-Type: audio/x-wav

Response audio payload
```

Si une erreur se produit, les codes d’état ci-après sont utilisés. Le corps de la réponse concernant l’erreur contient également une description du problème.

|Code|Description|Problème|
|-|-|-|
400 |Demande incorrecte |Un paramètre obligatoire est manquant, vide ou présente une valeur Null. Il est également possible que la valeur transmise à un paramètre obligatoire ou facultatif ne soit pas valide. Ce problème est généralement dû à un en-tête trop long.
401|Non autorisé |La requête n’est pas autorisée. Vérifiez que votre clé d’abonnement ou votre jeton sont valides.
413|Entité de requête trop volumineuse|L’entrée SSML comporte plus de 1 024 caractères.
|502|Passerelle incorrecte    | Problème de réseau ou côté serveur. Cette erreur peut également signaler des en-têtes non valides.

Pour plus d’informations sur l’API REST Synthèse vocale, consultez l’article décrivant les [API REST](rest-apis.md#text-to-speech) associées.

## <a name="next-steps"></a>Étapes suivantes

- [Obtenir votre abonnement d’essai gratuit du service Speech](https://azure.microsoft.com/try/cognitive-services/)
- [Reconnaissance vocale dans C#](quickstart-csharp-windows.md)
