---
title: Guide pratique pour transférer un flux audio en bloc | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Comment utiliser un transfert en bloc pour envoyer un flux audio au service Reconnaissance vocale Bing
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX
ms.openlocfilehash: cf21b11f76592f5df2d81e6bdc10413c79fe895e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46979224"
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
