---
title: Créer une entrée de travail Azure Media Services à partir d’une URL HTTP(s) | Microsoft Docs
description: Cette rubrique montre comment créer une entrée de travail à partir d’une URL HTTP(s).
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: d429665de64dacc5818d1d26c2a9029531cd39b3
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/14/2018
ms.locfileid: "34159925"
---
# <a name="create-a-job-input-from-an-https-url"></a>Créer une entrée de travail à partir d’une URL HTTP(s)

Dans Media Services v3, lorsque vous soumettez des travaux pour traiter vos vidéos, vous devez indiquer à Media Services où trouver la vidéo d’entrée. Une des options consiste à spécifier une URL HTTP(s) en tant qu’entrée de travail (comme illustré dans cet exemple). Pour obtenir un exemple complet, consultez cet [exemple github](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

## <a name="net-sample"></a>Exemple .Net

Le code suivant montre comment créer un travail avec une entrée d’URL HTTP(s).

[!code-csharp[Main](../../../media-services-v3-dotnet-quickstarts/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="next-steps"></a>Étapes suivantes

[Créer une entrée de travail à partir d’un fichier local](job-input-from-local-file-how-to.md).
