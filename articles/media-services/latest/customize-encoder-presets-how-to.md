---
title: Encoder une transformation personnalisée avec Azure Media Services v3 | Microsoft Docs
description: Cette rubrique explique comment utiliser Azure Media Services v3 pour encoder une transformation personnalisée.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 10/15/2018
ms.author: juliako
ms.openlocfilehash: c3318020b54beb121ac48d993a8ce794ac8e377f
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49376633"
---
# <a name="how-to-encode-with-a-custom-transform"></a>Comment encoder avec une transformation personnalisée

Lors de l’encodage avec Azure Media Services, vous pouvez commencer rapidement avec l’un des préréglages intégrés recommandés et basés sur les bonnes pratiques, comme illustré dans le tutoriel [Streaming de fichiers](stream-files-tutorial-with-api.md), ou vous pouvez choisir de créer un préréglage personnalisé pour les besoins de votre scénario ou votre appareil. 

> [!Note]
> Dans Azure Media Services v3, toutes les vitesses d’encodage sont données en bits par seconde. Ceci diffère des préréglages de Media Encoder Standard de REST v2. Par exemple, un débit en bits dans v2 serait spécifié sous la forme « 128 », mais dans v3, il serait de « 128000 ».

## <a name="download-the-sample"></a>Télécharger l’exemple

Clonez un dépôt GitHub qui contient l’exemple .NET Core complet sur votre machine avec la commande suivante :  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```
 
L’exemple de préréglage personnalisé se trouve dans le dossier [EncodeCustomTransform](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/EncodeCustomTransform/).

## <a name="create-a-transform-with-a-custom-preset"></a>Créer une transformation avec un préréglage personnalisé 

Quand vous créez une [transformation](https://docs.microsoft.com/rest/api/media/transforms), vous devez spécifier ce qu’elle doit produire comme sortie. Le paramètre requis est un objet **TransformOutput**, comme indiqué dans le code ci-dessous. Chaque objet **TransformOutput** contient un **préréglage**. Le **préréglage** décrit les instructions détaillées concernant les opérations de traitement vidéo et/ou audio qui doivent être utilisées pour générer l’objet **TransformOutput** souhaité. Le **TransformOutput** suivant crée des valeurs de sortie de codecs et de couche personnalisés.

Lorsque vous créez une [transformation](https://docs.microsoft.com/rest/api/media/transforms), vous devez tout d’abord vérifier s’il en existe déjà une à l’aide de la méthode **Get**, comme indiqué dans le code qui suit.  Dans Media Services v3, les méthodes **Get** appliquées sur des entités retournent **null** si l’entité n’existe pas (une vérification du nom ne respectant pas la casse).

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/EncodeCustomTransform/MediaV3ConsoleApp/Program.cs#EnsureTransformExists)]

## <a name="next-steps"></a>Étapes suivantes

[Streaming de fichiers](stream-files-tutorial-with-api.md) 
