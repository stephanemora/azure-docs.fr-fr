---
title: Créer une entrée de travail Azure Media Services à partir d’une URL HTTPS | Microsoft Docs
description: Cette rubrique montre comment créer une entrée de travail à partir d’une URL HTTP(s).
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 09/24/2018
ms.author: juliako
ms.openlocfilehash: 8b8bfb578b9a7190e93da721531041bb93a9a03c
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47224730"
---
# <a name="create-a-job-input-from-an-https-url"></a>Créer une entrée de travail à partir d’une URL HTTPS

Dans Media Services v3, lorsque vous soumettez des travaux pour traiter vos vidéos, vous devez indiquer à Media Services où trouver la vidéo d’entrée. Une des options consiste à spécifier une URL HTTP(s) en tant qu’entrée de travail (comme illustré dans cet exemple). Notez qu’AMS v3 ne prend pas en charge l’encodage de transfert mémorisé en bloc sur les URL HTTPS. Pour obtenir un exemple complet, consultez cet [exemple github](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

## <a name="net-sample"></a>Exemple de code .NET

Le code suivant montre comment créer un travail avec une entrée d’URL HTTP(s).

[!code-csharp[Main](../../../media-services-v3-dotnet-quickstarts/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="next-steps"></a>Étapes suivantes

[Créer une entrée de travail à partir d’un fichier local](job-input-from-local-file-how-to.md).
