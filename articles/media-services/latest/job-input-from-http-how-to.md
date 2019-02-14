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
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 6919bf49e46a65a02d2444277c8005fbb6f4ca1f
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2019
ms.locfileid: "55991648"
---
# <a name="create-a-job-input-from-an-https-url"></a>Créer une entrée de travail à partir d’une URL HTTPS

Dans Media Services v3, lorsque vous soumettez des travaux pour traiter vos vidéos, vous devez indiquer à Media Services où trouver la vidéo d’entrée. Une des options consiste à spécifier une URL HTTP(s) en tant qu’entrée de travail (comme illustré dans cet exemple). Notez qu’AMS v3 ne prend pas en charge l’encodage de transfert mémorisé en bloc sur les URL HTTPS. Pour obtenir un exemple complet, consultez cet [exemple GitHub](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

## <a name="net-sample"></a>Exemple de code .NET

Le code suivant montre comment créer un travail avec une entrée d’URL HTTP(s).

[!code-csharp[Main](../../../media-services-v3-dotnet-quickstarts/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="next-steps"></a>Étapes suivantes

[Créer une entrée de travail à partir d’un fichier local](job-input-from-local-file-how-to.md).
