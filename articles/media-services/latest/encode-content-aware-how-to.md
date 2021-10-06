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
ms.date: 09/17/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 09d3f199cc17cc1457413f673afe3c6be0646f58
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128668618"
---
# <a name="how-to-use-the-content-aware-encoding-preset"></a>Comment utiliser la présélection d’encodage sensible au contenu

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="introduction"></a>Introduction
Les [présélections de l’encodage sensible au contenu](encode-content-aware-concept.md) améliorent la préparation du contenu par [streaming à débit adaptatif](encode-autogen-bitrate-ladder.md), où les vidéos doivent être encodées à plusieurs vitesses de transmission. Elles comprennent une logique personnalisée qui permet à l’encodeur de fournir la valeur de vitesse optimale pour une résolution donnée, sans nécessiter une analyse de calcul étendue. La présélection détermine le nombre optimal de couches, le débit approprié et les paramètres de résolution pour la remise par diffusion en continu adaptative.

Par rapport à la présélection de streaming adaptatif, l’encodage sensible au contenu est plus efficace pour les vidéos de complexité faible ou moyenne, où les fichiers de sortie auront une vitesse de transmission inférieure, mais toujours à une qualité qui offre un bon confort visuel.

### <a name="using-the-content-aware-encoding-presets"></a>Utilisation de l’encodage sensible au contenu

Pour encoder avec la présélection sensible au contenu, utilisez l’objet [BuiltInStandardEncoderPreset](/dotnet/api/microsoft.azure.management.media.models.builtinstandardencoderpreset) et définissez la propriété [PresetName](/dotnet/api/microsoft.azure.management.media.models.builtinstandardencoderpreset.presetname) sur l’un des noms prédéfinis intégrés suivants.

- **ContentAwareEncoding** : Cette présélection prend en charge H.264.
- **H265ContentAwareEncoding** : Cette présélection prend en charge HEVC (H.265)

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

## <a name="configure-output-settings"></a>Configurer les paramètres de sortie

En outre, les développeurs peuvent également contrôler la plage de sorties que la présélection d’encodage sensible au contenu utilise afin de déterminer quels sont les paramètres optimaux pour encoder l’échelle de streaming à débit adaptatif.

Avec la classe **PresetConfigurations**, les développeurs peuvent passer un ensemble de contraintes et d’options à la présélection d’encodage sensible au contenu pour contrôler les fichiers résultants générés par l’encodeur. Les propriétés sont particulièrement utiles si vous souhaitez limiter l’ensemble de l’encodage à une résolution maximale spécifique afin de contrôler l’expérience ou les coûts de vos tâches d’encodage.  Il est également utile de pouvoir contrôler les vitesses de transmission maximales et minimales que votre audience peut prendre en charge sur un réseau mobile ou dans une région du monde ayant des contraintes de bande passante.

La classe PresetConfigurations vous permet d’ajuster les paramètres suivants sur les présélections d’encodage sensible au contenu. 


| Propriété                   | Description                                                                                                                                                                         |
| ------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| complexité              | Vitesse, Équilibrée ou Qualité. Permet de configurer les paramètres de l’encodeur afin de contrôler l’équilibre entre la vitesse et la qualité. Exemple : définir Complexity sur Speed pour un encodage plus rapide mais avec une efficacité de compression inférieure |
| interleaveOutput          |    Contrôle la mise en forme du fichier MP4 de sortie.  Le fichier peut contenir à la fois de l’audio et de la vidéo afin de faciliter son partage. Toutefois, l’audio et la vidéo peuvent faire l’objet de deux fichiers distincts afin de faciliter la liaison tardive de pistes audio supplémentaires ou la modification ultérieure de l’audio.                                                                                                                                                                                 |
| keyFrameIntervalInSeconds |        Définit la distance entre les images clés qui est utilisée pour chaque groupe d’images lors de l’encodage des fichiers.  Les protocoles standard modernes de streaming adaptatif utilisent un intervalle de 2 secondes.  Selon les conditions réseau que vous utilisez pour la transmission, il peut vous être utile d’utiliser des intervalles plus longs. Pour savoir si c’est votre cas, consultez votre fournisseur CDN ou faites des expérimentations sur vos réseaux.                                                                                                                                                                             |
| maxBitrateBps             |               Limite la vitesse de transmission maximale de l’échelle de streaming adaptatif.  Cette contrainte est utile pour la maîtrise des coûts de stockage et des coûts de distribution réseau.  En outre, cela vous permet de maintenir vos vitesses de transmission dans une plage prise en charge par vos clients.                                                                                                                                                                       |
| maxHeight                 |       Résolution la plus élevée qui est autorisée à être encodée dans l’échelle adaptative.  Cela permet également de maîtriser les coûts et de contrôler l’expérience de votre audience. Si vous le souhaitez, vous pouvez limiter tous les encodages en utilisant une définition standard ou une définition maximale de 720P.                                                                                                                                                                              |
| maxLayers                 |        Cette propriété contrôle le nombre de couches que comprend l’échelle de streaming adaptatif. Cela permet également de contrôler le nombre de fichiers de sortie MP4 que comprend le conteneur de stockage.  Le fait d’utiliser une valeur inférieure entraîne la réduction des coûts. En effet, puisque vous générez moins de rendus de sortie, le nombre total de minutes d’encodage est réduit. Pour maîtriser les coûts d’encodage et rendre vos factures plus prévisibles, vous pouvez combiner l’utilisation d’une valeur inférieure à celle de maxHeight et de maxBitrateBps.                                                                                                                                                                             |
| minBitrateBps             |                           Cela contrôle la vitesse de transmission la plus basse que l’encodeur peut choisir.  À utiliser pour optimiser la qualité de vos rendus à faible vitesse de transmission et de faible résolution.                                                                                                                                                         |
| minHeight                 |     Ce paramètre contrôle la résolution la plus basse que l’échelle de débit adaptatif peut produire. Cela permet d’éviter que les clients sélectionnent un rendu de très basse résolution qui peut être trop flou pour le type de contenu que vous encodez. Ceci est très utile pour contrôler la qualité de l’expérience que vous offrez.                                                                                                                                                                                |
|

### <a name="example-code-for-configuring-content-aware-preset"></a>Exemple de code pour la configuration de la présélection sensible au contenu

L’extrait de code suivant est tiré de l’exemple [Encode with content-aware, H.246, and constraints](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_H264_ContentAware_Constrained).

[!code-csharp[Main](../../../media-services-v3-dotnet/VideoEncoding/Encoding_H264_ContentAware_Constrained/Program.cs#PresetConfigurations)]

## <a name="samples"></a>exemples

Les exemples suivants montrent les différents codecs et contraintes qui peuvent être utilisés. Ils montrent également comment instancier la présélection, et comment envoyer et superviser une tâche d’encodage.

### <a name="net"></a>.NET

| Exemple | Description|
|---------|------------------|
| [Encode with content-aware and H.246](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_H264_ContentAware) | Montre l’utilisation la plus simple de l’encodage sensible au contenu H.264 sans aucune contrainte |
| [Encode with content-aware, H.246, and constraints](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_H264_ContentAware_Constrained) | Montre comment utiliser la classe PresetConfigurations pour contraindre le comportement de sortie de la présélection|
| [Encode with content-aware and HEVC (H.265)](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_HEVC_ContentAware) | Montre une utilisation de base du codec HEVC avec l’encodage sensible au contenu et sans aucune contrainte.  La classe PresetConfigurations est également prise en charge pour HEVC et peut être ajoutée à cet exemple|

> [!NOTE]
> Les travaux d’encodage à l’aide de la présélection `ContentAwareEncoding` sont facturés en fonction des minutes de sortie uniquement. AMS utilise l’encodage en deux passes et aucun frais supplémentaire n’est associé à l’utilisation de l’une des présélections au-delà des indications de la [page de tarification](https://azure.microsoft.com/pricing/details/media-services/#overview).
  
## <a name="next-steps"></a>Étapes suivantes

* [Tutoriel : Télécharger, encoder et diffuser des vidéos avec Media Services v3](stream-files-tutorial-with-api.md)
* [Tutoriel : Encoder un fichier distant basé sur une URL et diffuser la vidéo en continu - REST](stream-files-tutorial-with-rest.md)
* [Tutoriel : Encoder un fichier distant basé sur une URL et diffuser la vidéo en continu – CLI](stream-files-cli-quickstart.md)
* [Tutoriel : Encoder un fichier distant basé sur une URL et diffuser la vidéo en continu – .NET](stream-files-dotnet-quickstart.md)
* [Tutoriel : Encoder un fichier distant basé sur une URL et diffuser la vidéo en continu – Node.js](stream-files-nodejs-quickstart.md)
