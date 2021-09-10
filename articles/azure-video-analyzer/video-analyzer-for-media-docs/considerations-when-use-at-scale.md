---
title: Éléments à prendre en compte lors de l’utilisation d’Azure Video Analyzer for Media (anciennement Video Indexer) à grande échelle - Azure
titleSuffix: Azure Video Analyzer for Media
description: Cette rubrique explique quels sont les éléments à prendre en compte lors de l’utilisation d’Azure Video Analyzer for Media (anciennement Video Indexer) à grande échelle.
services: azure-video-analyzer
author: Juliako
manager: femila
ms.topic: how-to
ms.subservice: azure-video-analyzer-media
ms.date: 11/13/2020
ms.author: juliako
ms.openlocfilehash: 8784b82c59575a569730949d71473027cd30479a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122562555"
---
# <a name="things-to-consider-when-using-video-analyzer-for-media-at-scale"></a>Points à prendre en compte lors de l’utilisation de Video Analyzer for Media à grande échelle

Lorsque vous utilisez Azure Video Analyzer for Media (anciennement Video Indexer) pour indexer des vidéos et que votre archive de vidéos augmente, envisagez une mise à l’échelle. 

Cet article apporte des réponses à des questions telles que :

* Dois-je prendre en considération des contraintes technologiques ?
* Existe-t-il un moyen intelligent et efficace pour y parvenir ?
* Puis-je empêcher les dépenses excédentaires au cours du processus ?

L’article propose six pratiques idéales concernant l’utilisation de Video Analyzer for Media à grande échelle.

## <a name="when-uploading-videos-consider-using-a-url-over-byte-array"></a>Lors du chargement de vidéos, envisagez d’utiliser une URL sur un tableau d’octets

Video Analyzer for Media vous donne la possibilité de charger des vidéos à partir d’une URL ou directement en envoyant le fichier sous la forme d’un tableau d’octets, cette dernière solution s’accompagne de certaines contraintes. Pour plus d’informations, consultez [considérations et limitations relatives au chargement](upload-index-videos.md#uploading-considerations-and-limitations)

Tout d’abord, le fichier présente des limitations en termes de taille. La taille du fichier de tableau d’octets est limitée à 2 Go par rapport à la limite de taille de chargement de 30 Go lors de l’utilisation de l’URL.

Prenez ensuite en compte certains des problèmes qui peuvent affecter vos performances et, par conséquent, la possibilité de mettre à l’échelle :

* L’envoi de fichiers en plusieurs parties signifie une dépendance élevée liée à votre réseau, 
* à la fiabilité des appareils, 
* à la connectivité, 
* à la vitesse de chargement, 
* ainsi que des paquets perdus sur le web.

:::image type="content" source="./media/considerations-when-use-at-scale/first-consideration.png" alt-text="Premier point à prendre en compte lors de l’utilisation de Video Analyzer for Media à grande échelle":::

Lorsque vous chargez des vidéos à partir d’une URL, il vous suffit de fournir un chemin d’accès à l’emplacement d’un fichier multimédia, et Video Indexer se charge du reste (consultez le champ `videoUrl` dans l’API [charger une vidéo](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video)).

> [!TIP]
> Utilisez le paramètre `videoUrl` facultatif de l’API de chargement de vidéos.

Pour voir un exemple de chargement de vidéos à partir d’une URL, consultez [cet exemple](upload-index-videos.md#code-sample). Vous pouvez également utiliser [AzCopy](../../storage/common/storage-use-azcopy-v10.md) pour obtenir de façon rapide et fiable votre contenu sur un compte de stockage à partir duquel vous pouvez l’envoyer à Video Analyzer for Media à l’aide de l’[URL SAS](../../storage/common/storage-sas-overview.md). Azure Video Analyzer for Media recommande d’utiliser des URL SAP *en lecture seule*.

## <a name="automatic-scaling-of-media-reserved-units"></a>Mise à l’échelle automatique d’unités réservées Multimédia 

Depuis le 1er août 2021, Azure Video Analyzer for Media (anciennement Video Indexer) permet la mise à l’échelle automatique d’[unités réservées](../../media-services/latest/concept-media-reserved-units.md) par [Azure Media Services](../../media-services/latest/media-services-overview.md), de sorte que vous n’avez pas besoin de les gérer via Azure Video Analyzer for Media. Cela permet d’optimiser le prix, par exemple, de le réduire dans de nombreux cas, en fonction des besoins grâce à la mise à l’échelle automatique. 

## <a name="respect-throttling"></a>Respect de la limitation

Video Analyzer for Media est conçu pour gérer l’indexation à grande échelle, et lorsque vous souhaitez en tirer pleinement parti, vous devez également connaître les capacités du système et concevoir votre intégration en conséquence. Vous ne souhaitez pas envoyer de demande de téléchargement pour un lot de vidéos uniquement pour découvrir que certains films n’ont pas été téléchargés et recevoir un code de réponse HTTP 429 (trop de requêtes). Cela peut se produire car vous avez envoyé plus de demandes que la [limite de films par minute que nous prenons en charge](upload-index-videos.md#uploading-considerations-and-limitations). Video Analyzer for Media ajoute un en-tête de `retry-after` dans la réponse HTTP, l’en-tête spécifie à quel moment vous devez réaliser votre prochaine tentative. Veillez à le respecter avant d’essayer votre prochaine requête.

:::image type="content" source="./media/considerations-when-use-at-scale/respect-throttling.jpg" alt-text="Concevez correctement votre intégration, respect de la limitation":::

## <a name="use-callback-url"></a>Utilisez l’URL de rappel

Nous vous recommandons, au lieu d’interroger constamment l’état de votre requête dès que vous avez envoyé la demande de chargement, d’ajouter une [URL de rappel](upload-index-videos.md#callbackurl) et d’attendre la mise à jour de Video Analyzer for Media. Dès qu’il y a un changement d’état dans votre demande de chargement, vous recevez une notification POST vers l’URL que vous avez spécifiée.

Vous pouvez ajouter une URL de rappel comme l’un des paramètres de l’[API Charger la vidéo](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video). Consultez les exemples de code dans le [référentiel GitHub](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/). 

Pour l’URL de rappel, vous pouvez également utiliser Azure Functions, une plateforme basée sur les événements serverless qui peut être déclenchée par HTTP et implémenter un flux suivant.

### <a name="callback-url-definition"></a>définition de l’URL de rappel

[!INCLUDE [callback url](./includes/callback-url.md)]

## <a name="use-the-right-indexing-parameters-for-you"></a>Utilisez les paramètres d’indexation appropriés pour vous

Lorsque vous prenez des décisions en rapport avec l’utilisation de Video Analyzer for Media à grande échelle, découvrez comment en tirer le meilleur parti avec les paramètres appropriés à vos besoins. Pensez à votre cas d’utilisation. En définissant des paramètres différents vous permettant de réaliser des économies et d’accélérer le processus d’indexation de vos vidéos.

Avant de charger et d’indexer votre vidéo, lisez cette brève [documentation](upload-index-videos.md), consultez [indexingPreset](upload-index-videos.md#indexingpreset) et [streamingPreset](upload-index-videos.md#streamingpreset) pour mieux comprendre quelles sont vos options.

Par exemple, ne définissez pas la présélection sur diffusion en continu si vous n’envisagez pas de regarder la vidéo ; n’indexez pas la vidéo si vous avez uniquement besoin d’aperçus audio.

## <a name="index-in-optimal-resolution-not-highest-resolution"></a>Index en résolution optimale, pas dans la résolution la plus élevée

Vous vous demandez peut-être la qualité vidéo dont vous avez besoin pour l’indexation de vos vidéos ? 

Dans de nombreux cas, il n’y a pratiquement aucune différence de performances d’indexation entre les vidéos HD (720P) et les vidéos 4K. Au bout du compte, vous obtiendrez quasiment les mêmes aperçus avec la même confiance. Plus la qualité du film que vous chargez est élevée, plus la taille du fichier est élevée, et cela entraîne une puissance de calcul supérieure et plus de temps nécessaire pour le chargement de la vidéo.

Par exemple, pour la fonctionnalité de détection de visages, une résolution plus élevée peut être utile en présence de nombreux visages plus petits mais importants dans le contexte. Toutefois, cela s’accompagne d’une augmentation quadratique du runtime et d’un risque accru de faux positifs.

Par conséquent, nous vous recommandons de vérifier que vous disposez des bons résultats pour votre cas d’usage et de le tester localement en premier lieu. Téléchargez la même vidéo en 720P et en 4K, puis comparez les insights que vous recevez.

## <a name="next-steps"></a>Étapes suivantes

[Examiner la sortie d’Azure Video Analyzer for Media générée par l’API](video-indexer-output-json-v2.md)