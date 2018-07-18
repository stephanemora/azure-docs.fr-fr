---
title: Guide pratique pour transférer un flux audio en bloc | Microsoft Docs
description: Comment utiliser un transfert en bloc pour envoyer un flux audio au service de reconnaissance vocale
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/15/2017
ms.author: zhouwang
ms.openlocfilehash: 7d02340932dfc547893c4c40cbe08978b7b93756
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35368368"
---
# <a name="chunked-transfer-encoding"></a>Encodage de transfert en bloc

Pour transcrire la reconnaissance vocale, l’API de reconnaissance vocale de Microsoft vous permet d’envoyer le contenu audio en un seul bloc ou de le fractionner en petits blocs. Pour envoyer le contenu audio au service de manière efficace et réduire la latence de la transcription, il est recommandé d’utiliser [l’encodage de transfert en bloc](https://en.wikipedia.org/wiki/Chunked_transfer_encoding). D’autres implémentations peuvent entraîner une latence perçue par l’utilisateur plus élevée. Pour plus d’informations, consultez la page [Flux audio](../concepts.md#audio-streams).

> [!NOTE]
> Vous ne pouvez pas charger plus de 10 secondes de contenu audio dans une demande, et la durée totale de la demande ne peut pas dépasser 14 secondes.
> [!NOTE]
> Vous ne devez spécifier l’encodage de transfert en bloc que si vous utilisez les [API REST](../GetStarted/GetStartedREST.md) pour appeler le service de reconnaissance vocale. Les applications qui utilisent des [bibliothèques clientes](../GetStarted/GetStartedClientLibraries.md) n’ont pas besoin de configurer l’encodage de transfert en bloc.

Le code suivant montre comment définir l’encodage de transfert en bloc et envoyer un fichier audio fractionné en blocs de 1 024 octets.

```cs

HttpWebRequest request = null;
request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
request.SendChunked = true;
request.Accept = @"application/json;text/xml";
request.Method = "POST";
request.ProtocolVersion = HttpVersion.Version11;
request.Host = @"speech.platform.bing.com";
request.ContentType = @"audio/wav; codec=audio/pcm; samplerate=16000";
request.Headers["Ocp-Apim-Subscription-Key"] = "YOUR_SUBSCRIPTION_KEY";

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
