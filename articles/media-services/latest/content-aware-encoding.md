---
title: Présélection expérimentale pour l’encodage sensible au contenu - Azure | Microsoft Docs
description: Cet article décrit l’encodage sensible au contenu dans Microsoft Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/05/2019
ms.author: juliako
ms.custom: ''
ms.openlocfilehash: c2846759a8daa04fc5c1d3b7f69e2c061bacb272
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/14/2020
ms.locfileid: "75933481"
---
# <a name="experimental-preset-for-content-aware-encoding"></a>Présélection expérimentale pour l’encodage sensible au contenu

Afin de préparer le contenu pour une diffusion en [streaming à débit adaptatif](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming), la vidéo doit être encodée à plusieurs débits (du plus élevé au plus faible). Pour garantir une dégradation appropriée de la qualité, le débit et la résolution de la vidéo sont réduits en parallèle. Il en résulte ce qu’on appelle une échelle d’encodage, c’est-à-dire un tableau de résolutions et de débits. Consultez les [présélections d’encodage intégrées](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset) Media Services.

## <a name="overview"></a>Vue d’ensemble

L’intérêt d’aller au-delà d’une approche dans laquelle une présélection s’adapte à tous les types de vidéos a augmenté après la publication du [blog](https://medium.com/netflix-techblog/per-title-encode-optimization-7e99442b62a2) Netflix en décembre 2015. Depuis cette date, plusieurs solutions d’encodage sensible au contenu ont été publiées ; consultez [cet article](https://www.streamingmedia.com/Articles/Editorial/Featured-Articles/Buyers-Guide-to-Per-Title-Encoding-130676.aspx) pour obtenir une vue d’ensemble. Le concept est de tenir compte du contenu, c’est-à-dire personnaliser ou ajuster l’échelle d’encodage à la complexité de chaque vidéo. À chaque résolution, il existe un débit au-delà duquel toute augmentation de la qualité n’est pas perceptive : l’encodeur fonctionne à la valeur de débit optimale. Le niveau suivant d’optimisation consiste à sélectionner les résolutions en fonction du contenu, par exemple, une vidéo de présentation PowerPoint ne bénéficie d’aucune amélioration en passant sous 720p. Pour aller plus loin, l’encodeur peut être chargé afin d’optimiser les paramètres pour chaque scène de la vidéo. Netflix a décrit [cette approche](https://medium.com/netflix-techblog/optimized-shot-based-encodes-now-streaming-4b9464204830) en 2018.

Début 2017, Microsoft a publié la présélection [Adaptive Streaming](autogen-bitrate-ladder.md) (diffusion en continu adaptative) pour résoudre le problème de variabilité de la qualité et de résolution des vidéos source. Nos clients bénéficiaient d’une grande variété de contenus, certains en 1080p, d’autres en 720p, et quelques-uns en SD voire à des résolutions inférieures. En outre, tout le contenu source n’affichait pas les mezzanines haute qualité proposées par les studios de cinéma ou de télévision. La présélection Adaptive Streaming résout ces problèmes en s'assurant que l'échelle de débit ne dépasse jamais la résolution ou le débit moyen de la mezzanine en entrée.

La nouvelle présélection d’encodage sensible au contenu étend ce mécanisme, en incorporant une logique personnalisée permettant à l’encodeur de rechercher la valeur optimale de vitesse de transmission pour une résolution donnée, mais sans nécessiter une analyse informatique complète. Cette présélection produit un ensemble de fichiers MP4 alignés sur le groupe d’images. Étant donné un contenu d’entrée, le service effectue une analyse initiale légère du contenu d’entrée et utilise les résultats pour déterminer le nombre optimal de couches, le débit approprié et les paramètres de résolution pour la livraison par diffusion en continu adaptative. Cette présélection est particulièrement efficace pour les vidéos de complexité faible et moyenne, où les fichiers de sortie sont à des vitesses de transmission inférieures à la présélection Diffusion en continu adaptative, mais à une qualité qui offre toujours une bonne expérience aux viewers. La sortie contiendra des fichiers MP4 avec vidéo et audio entrelacées.

Consultez les exemples de graphiques suivants qui comparent le résultat obtenu avec des mesures de qualité comme [PSNR](https://en.wikipedia.org/wiki/Peak_signal-to-noise_ratio) et [VMAF](https://en.wikipedia.org/wiki/Video_Multimethod_Assessment_Fusion). La source avait été créée en concaténant de petits clips très complexes extraits de films et de séries TV, destinés à soumettre l’encodeur à un test de contrainte. Par définition, cette présélection produit des résultats qui varient selon le contenu : cela signifie également que pour certains contenus, la réduction du débit ou l’amélioration de la qualité reste minime.

![Courbe de distorsion du débit (RD) avec PSNR](media/cae-experimental/msrv1.png)

**Figure 1 : Courbe de distorsion du débit (RD) avec la métrique PSNR pour une source très complexe**

![Courbe de distorsion du débit (RD) avec VMAF](media/cae-experimental/msrv2.png)

**Figure 2 : Courbe de distorsion du débit (RD) avec la métrique VMAF pour une source très complexe**

Voici les résultats pour une autre catégorie de contenu source, où l’encodeur a pu déterminer que l’entrée était de mauvaise qualité (de nombreux artefacts de compression en raison d’une faible vitesse de transmission). Notez qu’avec la présélection sensible au contenu, l’encodeur a décidé de ne produire qu’une seule couche de sortie, à une vitesse de transmission suffisamment faible pour que la plupart des clients puissent lire le flux sans saccade.

![Courbe de distorsion du débit (RD) avec PSNR](media/cae-experimental/msrv3.png)

**Figure 3 : Courbe de distorsion du débit (RD) avec PSNR pour une entrée de faible qualité (à 1080p)**

![Courbe de distorsion du débit (RD) avec VMAF](media/cae-experimental/msrv4.png)

**Figure 4 : Courbe de distorsion du débit (RD) avec VMAF pour une entrée de faible qualité (à 1080p)**

## <a name="use-the-experimental-preset"></a>Utiliser la présélection expérimentale

Vous pouvez créer des transformations qui utilisent cette présélection comme suit. Si vous utilisez un tutoriel [de ce type](stream-files-tutorial-with-api.md), vous pouvez mettre à jour le code comme suit :

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

> [!NOTE]
> Les algorithmes sous-jacents sont sujets à des améliorations supplémentaires. Au fil du temps, des modifications pourront être (et seront) appliquées à la logique utilisée pour générer des échelles de vitesse de transmission, dans le but de fournir un algorithme robuste et adapté à une grande variété de conditions d’entrée. Les travaux d'encodage qui utilisent cette présélection seront toujours facturés en fonction du nombre de minutes de sortie, et la ressource de sortie peut être diffusée à partir de nos points de terminaison de streaming dans des protocoles comme DASH et HLS.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous connaissez cette nouvelle option d'optimisation de vos vidéos, nous vous invitons à l'essayer. Vous pouvez nous envoyer vos commentaires en utilisant les liens à la fin de cet article.
