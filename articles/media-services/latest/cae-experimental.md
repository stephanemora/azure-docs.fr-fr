---
title: Présélection un expérimental pour l’encodage de contenu prenant en charge - Azure | Microsoft Docs
description: Cet article décrit l’encodage de contenu prenant en charge dans Azure Media Services
services: media-services
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/05/2019
ms.author: sethm
ms.custom: ''
ms.openlocfilehash: 3c50502a8b873503ee937914fac5f2d92cb23a2b
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59288380"
---
# <a name="experimental-preset-for-content-aware-encoding"></a>Expérimental présélection d’encodage de contenu prenant en charge

Pour préparer le contenu pour une remise par [streaming à débit adaptatif](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming), vidéo doit être encodé à plusieurs débits (élevée à la plus petite). Afin de garantir une dégradation progressive de la qualité, comme la vitesse de transmission est réduite est donc la résolution de la vidéo. Il en résulte une échelle encodage ce que l'on appelle – un tableau des résolutions et vitesses de transmission, comme vous pouvez le voir certains des fixes Présélections d’encodage, tel que [H264MultipleBitrate1080p](../previous/media-services-mes-preset-h264-multiple-bitrate-1080p.md).

## <a name="overview"></a>Présentation

Leur intérêt à passer au-delà d’une approche d’une présélection-s’adapte-all-vidéos de procédure augmenté après la publication de Netflix leurs [blog](https://medium.com/netflix-techblog/per-title-encode-optimization-7e99442b62a2) en décembre 2015. Depuis lors, plusieurs solutions pour l’encodage de contenu prenant en charge ont été publiées dans la place de marché ; consultez [cet article](https://www.streamingmedia.com/Articles/Editorial/Featured-Articles/Buyers-Guide-to-Per-Title-Encoding-130676.aspx) pour une vue d’ensemble. L’idée est de connaître le contenu, personnaliser ou ajuster l’échelle des vitesses de codage à la complexité de la vidéo individuelle. À chaque résolution, il existe une vitesse de transmission au-delà de laquelle toute augmentation de la qualité n’est pas perceptive : l’encodeur fonctionne au niveau de cette valeur de la vitesse de transmission optimale. Le niveau suivant de l’optimisation consiste à sélectionner les résolutions en fonction du contenu, par exemple, une vidéo de présentation PowerPoint ne bénéficie pas de passer ci-dessous 720p. Pour aller plus loin, l’encodeur peut être chargé pour optimiser les paramètres pour chaque photo dans la vidéo. Netflix décrit [une telle approche](https://medium.com/netflix-techblog/optimized-shot-based-encodes-now-streaming-4b9464204830) en 2018.

Début 2017, Microsoft a publié le [diffusion adaptative en continu](autogen-bitrate-ladder.md) prédéfini pour résoudre le problème de la variabilité de la qualité et résolution vidéo source. Nos clients bénéficient d’une combinaison de variable de contenu, certains à 1080p, d’autres personnes à 720p et quelques à SD et résolutions inférieures. En outre, pas tout le contenu source a été mezzanines de haute qualité à partir d’un film ou les studios de télévision. Adaptive Streaming adresses prédéfinies ces problèmes en vous assurant que l’échelle des vitesses de transmission ne dépasse jamais la résolution ou la vitesse de transmission moyenne de la mezzanine d’entrée.

La présélection d’encodage contenu prenant en charge expérimentale étend ce mécanisme, en incorporant une logique personnalisée qui permet à l’encodeur de rechercher la valeur de la vitesse de transmission optimale pour une résolution donnée, mais sans nécessiter d’analyse complète de calcul. Le résultat net est que cette nouvelle présélection produit une sortie qui a le débit binaire inférieur que la présélection diffusion adaptative en continu, mais avec une qualité élevée. Consultez les graphiques d’exemple suivants qui indiquent la comparaison à l’aide de mesures de qualité comme [PSNR](https://en.wikipedia.org/wiki/Peak_signal-to-noise_ratio) et [VMAF](https://en.wikipedia.org/wiki/Video_Multimethod_Assessment_Fusion). La source a été créée en concaténant les petits clips de captures de complexité élevée à partir de films et émissions TV, destinée à souligner l’encodeur. Par définition, cette présélection produit des résultats qui varient de contenu : cela signifie également que pour du contenu, il ne peut pas être une réduction significative de la vitesse de transmission ou amélioration de qualité.

![Courbe de distorsion de vitesse (RD) à l’aide de PSNR](media/cae-experimental/msrv1.png)

**Figure 1 : Courbe de distorsion de vitesse (RD) à l’aide de la métrique PSNR pour la source de complexité élevée**

![Courbe de distorsion de vitesse (RD) à l’aide de VMAF](media/cae-experimental/msrv2.png)

**Figure 2 : Courbe de distorsion de vitesse (RD) à l’aide de la métrique VMAF pour la source de complexité élevée**

La présélection actuellement met l’accent sur haute complexité, vidéos de source de haute qualité (films, émissions TV). Travail est en cours pour s’adapter au contenu de complexité faible (par exemple, des présentations PowerPoint), ainsi que des vidéos de qualité médiocre. Cette présélection utilise également le même ensemble de résolutions que la diffusion en continu adaptative de la présélection. Microsoft travaille sur des méthodes pour sélectionner l’ensemble minimal de résolutions en fonction du contenu. Comme suit sont les résultats pour une autre catégorie de contenu de la source, où l’encodeur a été en mesure de déterminer que l’entrée a été de mauvaise qualité (nombreux artefacts de compression en raison de la faible vitesse de transmission). Notez que, avec l’instance expérimentale, présélection, l’encodeur décidé produire qu’une seule couche de sortie – à une vitesse de transmission suffisamment faible pour que la plupart des clients seraient en mesure de lire les données sans faire du surplace.

![Courbe de bureau à distance à l’aide de PSNR](media/cae-experimental/msrv3.png)

**Figure 3 : Courbe de bureau à distance à l’aide de PSNR pour l’entrée de basse qualité (au 1080p)**

![Courbe de bureau à distance à l’aide de VMAF](media/cae-experimental/msrv4.png)

**Figure 4 : Courbe de bureau à distance à l’aide de VMAF pour l’entrée de basse qualité (au 1080p)**

## <a name="use-the-experimental-preset"></a>Utiliser la présélection expérimentale

Vous pouvez créer des transformations qui utilisent cette présélection comme suit. Si vous utilisez un didacticiel [telle que celle-ci](stream-files-tutorial-with-api.md), vous pouvez mettre à jour le code comme suit :

```csharp
TransformOutput[] output = new TransformOutput[]
{
   new TransformOutput
   {
      // The preset for the Transform is set to one of Media Services built-in sample presets.
      // You can customize the encoding settings by changing this to use "StandardEncoderPreset" class.
      Preset = new BuiltInStandardEncoderPreset()
      {
         // This sample uses the new experimental preset for content-aware encoding
         PresetName = EncoderNamedPreset.ContentAwareEncodingExperimental
      }
   }
};
```

> [!NOTE]
> Le préfixe « expérimental » est utilisé ici pour signaler que les algorithmes sous-jacents sont évoluent en permanence. Il peut et aura des changements au fil du temps à la logique employée pour la génération des échelles de vitesse de transmission, dans le but de converger sur un algorithme qui est solide et s’adapte à un large éventail de conditions d’entrée. Encodage des travaux à l’aide de cette présélection continuera d’être minutes de sortie en fonction de facturation et la ressource de sortie peut être fournie par nos points de terminaison de diffusion en continu dans les protocoles tels que DASH et HLS.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris sur cette nouvelle option de l’optimisation de vos vidéos, nous vous invitons à essayer. Vous pouvez nous envoyer vos commentaires en utilisant les liens à la fin de cet article, ou nous avertir plus directement au <amsved@microsoft.com>.
