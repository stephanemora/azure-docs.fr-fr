---
title: Encoder de transformation personnalisée à l’aide de Media Services v3 .NET - Azure | Microsoft Docs
description: Cette rubrique montre comment utiliser Azure Media Services v3 pour coder une transformation personnalisée à l’aide de .NET.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: seodec18
ms.date: 03/11/2019
ms.author: juliako
ms.openlocfilehash: ed2ae50aa9d7a26ed6e0569264ee981f7be35525
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2019
ms.locfileid: "58754561"
---
# <a name="how-to-encode-with-a-custom-transform---net"></a>Comment encoder avec une transformation personnalisée - .NET

Lors de l’encodage avec Azure Media Services, vous pouvez démarrer rapidement avec l’une des présélections recommandées intégrées basées sur les meilleures pratiques du secteur, comme illustré dans le [diffusion en continu de fichiers](stream-files-tutorial-with-api.md) didacticiel. Vous pouvez également créer un paramètre prédéfini pour cibler vos exigences de scénario ou un périphérique spécifiques.

## <a name="considerations"></a>Considérations

Lorsque vous créez des paramètres prédéfinis personnalisés, les considérations suivantes s’appliquent :

* Toutes les valeurs de hauteur et la largeur du contenu de AVC doivent être un multiple de 4.
* Dans Azure Media Services v3, tous les débits binaires de codage sont en bits par seconde. Cela diffère des présélections avec nos API v2, utiliser des kilobits par seconde en tant que l’unité. Par exemple, si la vitesse de transmission dans v2 a été spécifié en tant que 128 (kilobits par seconde), dans v3 il serait défini à 128000 (bits/seconde).

## <a name="prerequisites"></a>Conditions préalables 

[Créer un compte Media Services](create-account-cli-how-to.md). <br/>Veillez à mémoriser le nom du groupe de ressources et le nom du compte Media Services. 

## <a name="download-the-sample"></a>Télécharger l’exemple

Clonez un dépôt GitHub qui contient l’exemple .NET Core complet sur votre machine avec la commande suivante :  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```
 
L’exemple de préréglage personnalisé se trouve dans le dossier [EncodeCustomTransform](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/EncodeCustomTransform/).

## <a name="create-a-transform-with-a-custom-preset"></a>Créer une transformation avec un préréglage personnalisé 

Quand vous créez une [transformation](https://docs.microsoft.com/rest/api/media/transforms), vous devez spécifier ce qu’elle doit produire comme sortie. Le paramètre requis est un objet [TransformOutput](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#transformoutput), comme indiqué dans le code ci-dessous. Chaque objet **TransformOutput** contient un **préréglage**. Le **préréglage** décrit les instructions détaillées concernant les opérations de traitement vidéo et/ou audio qui doivent être utilisées pour générer l’objet **TransformOutput** souhaité. Le **TransformOutput** suivant crée des valeurs de sortie de codecs et de couche personnalisés.

Lorsque vous créez une [transformation](https://docs.microsoft.com/rest/api/media/transforms), vous devez tout d’abord vérifier s’il en existe déjà une à l’aide de la méthode **Get**, comme indiqué dans le code qui suit. Dans Media Services v3, les méthodes **Get** appliquées sur des entités retournent **null** si l’entité n’existe pas (une vérification du nom ne respectant pas la casse).

### <a name="example"></a>Exemples

L’exemple suivant définit un ensemble de sorties que nous voulons être générée lorsque cette transformation est utilisée. Tout d’abord, nous ajoutons une couche AacAudio pour l’encodage audio et deux couches H264Video pour l’encodage vidéo. Dans les couches vidéo, nous attribuons les étiquettes afin qu’ils peuvent être utilisés dans les noms de fichiers de sortie. Ensuite, nous voulons la sortie pour inclure également les miniatures. Dans l’exemple ci-dessous, nous spécifions les images au format PNG, généré à 50 % de la résolution de la vidéo d’entrée et aux trois horodatages - {25 %, 50 %, 75} de la longueur de la vidéo d’entrée. Enfin, nous spécifions le format pour les fichiers de sortie - un pour la vidéo + audio et l’autre pour les miniatures. Étant donné que nous avons plusieurs H264Layers, nous devons utiliser des macros qui produisent des noms uniques par couche. Nous pouvons utiliser un `{Label}` ou `{Bitrate}` macro, l’exemple montre l’ancienne base de données.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/EncodeCustomTransform/MediaV3ConsoleApp/Program.cs#EnsureTransformExists)]

## <a name="next-steps"></a>Étapes suivantes

[Streaming de fichiers](stream-files-tutorial-with-api.md) 
