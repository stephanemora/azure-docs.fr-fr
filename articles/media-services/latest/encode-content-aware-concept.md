---
title: Présélection d’encodage sensible au contenu
description: Cet article décrit l’encodage sensible au contenu dans Microsoft Azure Media Services v3.
services: media-services
documentationcenter: ''
author: jiayali-ms
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 09/16/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: a34e2d16edb4a8ec9ba40a4426fcbbd9b2127b16
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128590401"
---
# <a name="content-aware-encoding"></a>Encodage sensible au contenu

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="overview-of-the-content-aware-encoding-preset"></a>Vue d’ensemble de la présélection d’encodage sensible au contenu

Pour préparer le contenu à être diffusé à l’aide du [streaming à débit adaptatif](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming), la vidéo doit être encodée à plusieurs débits (du plus élevé au plus faible) et plusieurs résolutions. Cette technique permet aux lecteurs vidéo modernes sur Apple iOS, Android, Windows et Mac d’utiliser des protocoles de diffusion en continu qui diffusent le contenu de manière fluide sans mise en mémoire tampon. Ces différents rendus de taille (résolution) et de qualité (débit) d’affichage permettent au lecteur de sélectionner la meilleure version de la vidéo que les conditions actuelles du réseau peuvent supporter. Le réseau peut varier considérablement : LTE, 4G, 5G, Wi-Fi public ou réseau domestique.

Le processus d’encodage du contenu en plusieurs rendus nécessite la génération d’une « échelle d’encodage », une table de résolutions et de débits qui indique à l’encodeur ce qu’il doit générer. Pour obtenir un exemple d’échelle de ce type, consultez [présélections d’encodage intégrées](/rest/api/media/transforms/createorupdate#encodernamedpreset) de Media Services.

Idéalement, vous devez connaître le type de contenu que vous encodez. À l’aide de ces informations, vous pouvez adapter l’échelle d’encodage à la complexité et au mouvement de votre vidéo source. Cela signifie qu’à chaque taille d’affichage (résolution) de l’échelle, il doit y avoir un débit au-delà duquel toute augmentation de la qualité n’est pas perceptible : l’encodeur fonctionne à cette valeur de débit optimale.

Le niveau d’optimisation suivant qui peut être effectué consiste à sélectionner les résolutions en fonction du contenu, par exemple, la vidéo d’une présentation PowerPoint avec peu de texte peut paraître floue si elle est encodée en dessous de 720 lignes de pixels de hauteur. En outre, il se peut également qu’une vidéo change de mouvement et de complexité tout au long de son visionnage, en fonction de la façon dont elle a été tournée et montée.  Cela permet d’affiner et d’ajuster les paramètres d’encodage à chaque limite de scène ou de capture. Un encodeur intelligent peut être chargé d’optimiser les paramètres d’encodage pour chaque scène de la vidéo.

Azure Media Services propose une présélection [Adaptive Streaming](encode-autogen-bitrate-ladder.md) (diffusion en continu adaptative) qui résout partiellement le problème de variabilité du débit et de la résolution des vidéos sources. Toutefois, cette présélection n’analyse pas le contenu source pour déterminer son niveau de complexité ou la quantité de mouvement qu’il contient. 

La présélection d’encodage sensible au contenu améliore la présélection d’encodage « streaming à débit adaptatif » plus statique, en ajoutant une logique permettant à l’encodeur de rechercher la valeur optimale de débit pour une résolution donnée, mais sans nécessiter une analyse informatique approfondie. Cette présélection génère une « échelle » unique de fichiers MP4 alignés sur le groupe d’images en fonction du fichier source. À partir d’une vidéo source, la présélection effectue une analyse rapide initiale du contenu d’entrée et utilise les résultats pour déterminer le nombre optimal de couches, le débit et les résolutions nécessaires pour fournir l’expérience de streaming à débit adaptatif de la plus haute qualité. Cette présélection est efficace pour les vidéos de complexité faible à moyenne, où les fichiers de sortie auront des débits inférieurs à ceux de la présélection Diffusion en continu adaptative plus statique, mais avec une qualité qui offre toujours une bonne expérience aux spectateurs. Le dossier de sortie contiendra plusieurs fichiers MP4 avec vidéo et audio prêts pour la diffusion en continu.

## <a name="configure-output-settings"></a>Configurer les paramètres de sortie

En outre, les développeurs peuvent également contrôler la plage de sorties que la présélection d’encodage sensible au contenu utilise afin de déterminer quels sont les paramètres optimaux pour encoder l’échelle de streaming à débit adaptatif.

Avec la classe **PresetConfigurations**, les développeurs peuvent transmettre un ensemble de contraintes et d’options à la présélection d’encodage sensible au contenu pour contrôler les fichiers résultants générés par l’encodeur. Les propriétés sont particulièrement utiles si vous souhaitez limiter l’ensemble de l’encodage à une résolution maximale spécifique afin de contrôler l’expérience ou les coûts de vos tâches d’encodage.  Il est également utile de pouvoir contrôler les débits maximum et minimum que votre audience peut prendre en charge sur un réseau mobile ou dans une région du monde ayant des contraintes de bande passante.

## <a name="supported-codecs"></a>Codecs pris en charge

La présélection d’encodage sensible au contenu peut être utilisée avec les codecs suivants :
-  H.264
-  HEVC (H.265)

## <a name="how-to-use"></a>Utilisation de procédures

Pour plus d’informations sur l’utilisation de la présélection dans votre code et des liens vers des exemples complets, consultez le [guide pratique de l’encodage sensible au contenu](./encode-content-aware-How-to.md).

## <a name="technical-details-on-content-aware-preset"></a>Détails techniques sur la présélection sensible au contenu

Abordons à présent plus en détail le fonctionnement de la présélection d’encodage sensible au contenu.  Les exemples de graphiques suivants illustrent la comparaison à l’aide de métriques de qualité comme [PSNR](https://en.wikipedia.org/wiki/Peak_signal-to-noise_ratio) et [VMAF](https://en.wikipedia.org/wiki/Video_Multimethod_Assessment_Fusion). La source avait été créée en concaténant de petits clips très complexes extraits de films et de séries TV, destinés à soumettre l’encodeur à un test de contrainte. Par définition, cette présélection produit des résultats qui varient selon le contenu : cela signifie également que pour certains contenus, la réduction du débit ou l’amélioration de la qualité reste minime.

![Courbe de distorsion du débit (RD) avec PSNR](media/encode-content-aware-concept/msrv1.png)

**Figure 1 : Courbe de distorsion du débit (RD) avec la métrique PSNR pour une source très complexe**

![Courbe de distorsion du débit (RD) avec VMAF](media/encode-content-aware-concept/msrv2.png)

**Figure 2 : Courbe de distorsion du débit (RD) avec la métrique VMAF pour une source très complexe**

Voici les résultats pour une autre catégorie de contenu source, où l’encodeur a pu déterminer que l’entrée était de mauvaise qualité (de nombreux artefacts de compression en raison d’une faible vitesse de transmission). Avec la présélection sensible au contenu, l’encodeur a décidé de ne produire qu’une seule couche de sortie, à un débit suffisamment faible pour que la plupart des clients puissent lire le flux sans saccade.

![Courbe de distorsion du débit (RD) avec PSNR](media/encode-content-aware-concept/msrv3.png)

**Figure 3 : Courbe de distorsion du débit (RD) avec PSNR pour une entrée de faible qualité (à 1080p)**

![Courbe de distorsion du débit (RD) avec VMAF](media/encode-content-aware-concept/msrv4.png)

**Figure 4 : Courbe de distorsion du débit (RD) avec VMAF pour une entrée de faible qualité (à 1080p)**

  
## <a name="next-steps"></a>Étapes suivantes
* [Comment utiliser les présélections d’encodage sensibles au contenu](encode-content-aware-how-to.md)
* [Tutoriel : Télécharger, encoder et diffuser des vidéos avec Media Services v3](stream-files-tutorial-with-api.md)