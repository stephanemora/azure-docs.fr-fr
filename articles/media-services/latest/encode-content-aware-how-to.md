---
title: Comment utiliser la présélection d’encodage sensible au contenu
description: Cet article décrit la façon d’utiliser l’encodage sensible au contenu prédéfini dans Microsoft Azure Media Services v3.
services: media-services
documentationcenter: ''
author: jiayali-ms
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.date: 08/17/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: b6537547db2bb3371b6d7b4cd36ebf9ffc48530c
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122527867"
---
# <a name="how-to-use-the-content-aware-encoding-preset"></a>Comment utiliser la présélection d’encodage sensible au contenu

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="introduction"></a>Introduction
La [présélection de l’encodage prenant en charge le contenu](encode-content-aware-how-to.md) améliore la préparation du contenu par [streaming à débit adaptatif](encode-autogen-bitrate-ladder.md), où les vidéos doivent être encodées à plusieurs vitesses de transmission. Elle comprend une logique personnalisée qui permet à l’encodeur de rechercher la valeur de débit optimal pour une résolution donnée sans nécessiter une analyse de calcul étendue. La présélection détermine le nombre optimal de couches, le débit approprié et les paramètres de résolution pour la remise par diffusion en continu adaptative. 

Par rapport à la présélection de diffusion en continu adaptative, l’encodage de contenu est plus efficace pour les vidéos de complexité faible et moyenne, où les fichiers de sortie seront à une vitesse de transmission inférieure, mais toujours à une qualité qui offre un bonne confort visuel.

## <a name="use-the-content-aware-encoding-preset"></a>Utiliser la présélection d’encodage sensible au contenu
Vous pouvez créer des transformations qui utilisent cette présélection comme suit.

> [!NOTE]
> Veillez à utiliser la présélection **ContentAwareEncoding**, et non ContentAwareEncodingExperimental. Si vous souhaitez encoder avec HEVC, vous pouvez utiliser **H265ContentAwareEncoding**.

```csharp
TransformOutput[] output = new TransformOutput[]
{
   new TransformOutput
   {
      // The preset for the Transform is set to one of Media Services built-in sample presets.
      // You can customize the encoding settings by changing this to use "StandardEncoderPreset" class.
      Preset = new BuiltInStandardEncoderPreset()
      {
         // This sample uses the new preset for content-aware encoding
         PresetName = EncoderNamedPreset.ContentAwareEncoding
      }
   }
};
```

Consultez la section [Étapes suivantes](#next-steps) pour accéder à des tutoriels qui utilisent la transformation des sorties. La ressource de sortie peut être fournie à partir de points de terminaison de streaming Media Services dans des protocoles tels que MPEG-DASH et HLS (comme indiqué dans les didacticiels).

> [!NOTE]
> Les travaux d’encodage à l’aide de la présélection `ContentAwareEncoding` sont facturés en fonction des minutes de sortie uniquement. AMS utilise l’encodage en deux passes et aucun frais supplémentaire n’est associé à l’utilisation de l’une des présélections au-delà des indications de la [page de tarification](https://azure.microsoft.com/pricing/details/media-services/#overview).
  
## <a name="next-steps"></a>Étapes suivantes

* [Tutoriel : Télécharger, encoder et diffuser des vidéos avec Media Services v3](stream-files-tutorial-with-api.md)
* [Tutoriel : Encoder un fichier distant basé sur une URL et diffuser la vidéo en continu - REST](stream-files-tutorial-with-rest.md)
* [Tutoriel : Encoder un fichier distant basé sur une URL et diffuser la vidéo en continu – CLI](stream-files-cli-quickstart.md)
* [Tutoriel : Encoder un fichier distant basé sur une URL et diffuser la vidéo en continu – .NET](stream-files-dotnet-quickstart.md)
* [Tutoriel : Encoder un fichier distant basé sur une URL et diffuser la vidéo en continu – Node.js](stream-files-nodejs-quickstart.md)
