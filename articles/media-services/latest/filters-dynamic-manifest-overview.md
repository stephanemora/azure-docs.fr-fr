---
title: Filtres et manifestes dynamiques Azure Media Services | Microsoft Docs
description: Cette rubrique décrit comment créer des filtres pour que votre client puisse les utiliser pour diffuser des sections spécifiques d'un flux. Media Services crée des manifestes dynamiques pour obtenir cette diffusion sélective.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 2da4ee5d60290485d87af86885dda0d72a625fef
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60323084"
---
# <a name="dynamic-manifests"></a>Manifestes dynamiques

Media Services propose des **manifestes dynamiques** basés sur des filtres prédéfinis. Lorsque vous définissez des filtres (voir [Définir des filtres](filters-concept.md)), vos clients peuvent les utiliser pour diffuser un rendu ou des sous-clips spécifiques de votre vidéo. Ils peuvent spécifier des filtres dans l'URL de diffusion en continu. Il serait possible d’appliquer des filtres à des protocoles de streaming à débit adaptatif : HLS (HTTP Live Streaming) Apple, MPEG-DASH et Smooth Streaming. 

Le tableau suivant présente des exemples d’URL utilisant des filtres :

|Protocole|Exemples|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

> [!NOTE]
> Les manifestes dynamiques ne changent pas l'élément multimédia ni son manifeste par défaut. Votre client peut choisir de demander un flux avec ou sans filtres. 
> 

Cette rubrique décrit les concepts liés aux **Manifestes dynamiques** et fournit des exemples de scénarios dans lesquels vous pouvez utiliser cette fonctionnalité.

## <a name="manifests-overview"></a>Vue d’ensemble des manifestes

Media Services prend en charge HLS, MPEG DASH, protocoles de diffusion en continu lisse. Dans le cadre de [empaquetage dynamique](dynamic-packaging-overview.md), les manifestes de client de diffusion en continu (HLS Master Playlist DASH MPD Media Presentation Description () et Smooth Streaming) sont générées dynamiquement selon le sélecteur de format dans l’URL. Consultez les protocoles de remise dans [cette section](dynamic-packaging-overview.md#delivery-protocols). 

### <a name="get-and-examine-manifest-files"></a>Obtenir et examiner des fichiers manifeste

Vous spécifiez une liste de conditions de propriétés de suivi de filtre selon laquelle les pistes de votre flux de données (en live streaming ou en vidéo à la demande) doivent être incluses dans le manifeste créé dynamiquement. Pour obtenir et examiner les propriétés des pistes, vous devez d’abord charger le manifeste Smooth Streaming.

Le tutoriel [Charger, encoder et diffuser en continu des fichiers avec .NET](stream-files-tutorial-with-api.md) vous montre comment créer des URL de diffusion en continu avec .NET (voir la section [Création d’URL](stream-files-tutorial-with-api.md#get-streaming-urls)). Si vous exécutez l’application, une des URL pointe vers le manifeste Smooth Streaming : `https://amsaccount-usw22.streaming.media.azure.net/00000000-0000-0000-0000-0000000000000/ignite.ism/manifest`.<br/>Copiez et collez l’URL dans la barre d’adresse d’un navigateur. Le fichier va être téléchargé. Vous pouvez l’ouvrir dans un éditeur de texte de votre choix.

Pour l’exemple REST, consultez [Charger, encoder et diffuser en continu](stream-files-tutorial-with-rest.md#list-paths-and-build-streaming-urls).

### <a name="monitor-the-bitrate-of-a-video-stream"></a>Surveiller la vitesse de transmission d’un flux vidéo

Vous pouvez utiliser la [page de démo Azure Media Player](https://aka.ms/amp) pour surveiller la vitesse de transmission d’un flux vidéo. La page de démo affiche des informations de diagnostic dans l’onglet **Diagnostics** :

![Diagnostics Azure Media Player][amp_diagnostics]

## <a name="rendition-filtering"></a>Filtrage de rendu

Vous pouvez choisir d'encoder votre élément multimédia pour plusieurs profils d'encodage (H.264 Baseline, H.264 High, AACL, AACH, Dolby Digital Plus) et plusieurs qualités de débit binaire. Toutefois, tous les appareils client ne prennent pas en charge tous les profils et débits binaires de tous vos éléments multimédias. Par exemple, les anciens appareils Android prennent uniquement en charge H.264 Baseline+AACL. L'envoi à des vitesses de transmission plus élevées à un appareil qui ne peut pas en tirer partie ne fait que gaspiller la bande passante et entraîner des calculs inutiles pour l'appareil. Ce dernier doit en effet décoder toutes les informations données, pour finalement les réduire pour pouvoir les afficher.

Avec un manifeste dynamique, vous pouvez créer des profils d'appareil mobile, de console, HD/SD, etc. et inclure les pistes et les qualités que vous voulez intégrer dans chaque profil.

![Exemple de filtrage de rendu][renditions2]

Dans l’exemple suivant, un encodeur a été utilisé pour encoder un élément multimédia mezzanine en sept rendus vidéo ISO MP4s (de 180 p à 1 080 p). L’élément multimédia encodé peut être [empaqueté dynamiquement](dynamic-packaging-overview.md) dans un des protocoles de diffusion en continu suivants : HLS, MPEG DASH et Smooth.  En haut du diagramme, le manifeste HLS de l'élément multimédia sans aucun filtre apparaît (il contient les sept rendus).  Dans la partie inférieure gauche apparaît le manifeste HLS auquel un filtre nommé « ott » a été appliqué. Le filtre « ott » indique de supprimer toutes les vitesses de transmission inférieures à 1 Mbit/s, ce qui entraîne la suppression des deux niveaux de qualité les plus bas dans la réponse. Dans la partie inférieure droite apparaît le manifeste HLS auquel un filtre nommé « mobile » a été appliqué. Le filtre « mobile » indique de supprimer les rendus pour lesquels la résolution est supérieure à 720 p, ce qui entraîne la suppression des deux rendus à 1 080 p.

![Filtrage de rendu][renditions1]

## <a name="removing-language-tracks"></a>Suppression des pistes de langue
Vos éléments multimédias peuvent inclure plusieurs langues audio telles que l'anglais, l'espagnol, le français, etc. Généralement, le Kit de développement logiciel (SDK) du lecteur gère la sélection de pistes audio par défaut et les pistes audio disponibles par sélection de l'utilisateur. Il est difficile de développer ces Kits de développement logiciel (SDK) de lecteur, car différentes implémentations sont requises entre les infrastructures de lecteur propres aux appareils. De plus, sur certaines plateformes, les API de lecteur sont limitées et n'incluent pas de fonctionnalité de sélection audio, ce qui ne permet pas aux utilisateurs de sélectionner ou de modifier la piste audio par défaut. Avec les filtres d'éléments multimédias, vous pouvez contrôler le comportement en créant des filtres qui incluent uniquement les langues audio souhaitées.

![Filtrage des pistes de langue][language_filter]

## <a name="trimming-start-of-an-asset"></a>Découpage du début d'un élément multimédia
Pour la plupart des événements de diffusion en continu en direct, les opérateurs exécutent des tests avant l'événement réel. Par exemple, ils peuvent inclure une ardoise comme celle-ci avant le début de l’événement : « Le programme va commencer dans un instant ». Si le programme est en cours d’archivage, le test et les données d’ardoise sont également archivés et donc inclus dans la présentation. Toutefois, ces informations ne doivent pas être montrées aux clients. Avec un manifeste dynamique, vous pouvez créer un filtre sur l'heure de début et supprimer les données indésirables du manifeste.

![Découpage du début][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>Création de sous-clips (vues) à partir d’une archive en direct
De nombreux événements en direct ont une durée d'exécution longue et une archive en direct peut inclure plusieurs événements. Une fois l’événement en direct terminé, les diffuseurs peuvent décomposer cette archive en direct selon des séquences de démarrage et d’arrêt du programme logiques. Ensuite, il suffit de publier ces programmes virtuels séparément, sans post-traitement de l’archive en direct, et sans créer d’éléments multimédias distincts (qui ne bénéficient pas des fragments mis en cache existants dans les CDN). Par exemple, ces programmes virtuels peuvent correspondre aux mi-temps d’un match de football ou de basket, aux tours de batte au base-ball ou à des événements sportifs ponctuels.

Avec un manifeste dynamique, vous pouvez créer des filtres en utilisant des heures de début/fin et créer des vues virtuelles par-dessus votre archive en direct. 

![Filtre de sous-clip][subclip_filter]

Élément multimédia filtré :

![Ski][skiing]

## <a name="adjusting-presentation-window-dvr"></a>Ajustement de la fenêtre de présentation (DVR)
Actuellement, Azure Media Services propose une archive circulaire où la durée peut être configurée entre 5 minutes et 25 heures. Le filtrage de manifeste peut servir à créer une fenêtre DVR de substitution par-dessus l'archive, sans supprimer le média. Il existe de nombreux scénarios où les diffuseurs veulent fournir une fenêtre DVR limitée à déplacer avec la session en direct et qui garde en même temps une plus grande fenêtre d’archivage. Un diffuseur peut utiliser les données en dehors de la fenêtre DVR pour mettre en avant des clips ou il peut proposer des fenêtres DVR différentes pour différents appareils. Par exemple, la plupart des appareils mobiles ne gèrent pas les grandes fenêtres DVR (une fenêtre DVR peut durer 2 minutes pour les appareils mobiles alors qu’elle dure une heure pour les ordinateurs de bureau).

![Fenêtre DVR][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>Ajustement de LiveBackoff (position en direct)
Le filtrage de manifeste peut servir à supprimer plusieurs secondes à partir du bord en direct d'un programme en direct. Il permet aux diffuseurs de regarder la présentation sur le point de publication d’aperçu et de créer des points d’insertion d’annonce avant que les utilisateurs ne reçoivent le flux (reculé de 30 secondes). Les diffuseurs peuvent ensuite transmettre ces annonces à leurs infrastructures client à temps pour qu'ils reçoivent et traitent les informations avant l'opportunité d'annonce.

Outre la prise en charge d’annonces, le paramètre LiveBackoff peut servir à ajuster la position des utilisateurs, afin que lorsque les clients dérivent et atteignent la session en direct, ils puissent toujours obtenir des fragments du serveur au lieu d’obtenir une erreur HTTP 404 ou 412.

![livebackoff_filter][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>Combinaison de plusieurs règles dans un seul filtre
Vous pouvez combiner plusieurs règles de filtrage dans un filtre unique. Par exemple, vous pouvez définir une « règle de plage » pour supprimer les ardoises d’une archive en direct tout en éliminant les débits binaires disponibles. Lors de l’application de plusieurs règles de filtrage, le résultat final est l’intersection de toutes les règles.

![plusieurs règles][multiple-rules]

## <a name="combining-multiple-filters-filter-composition"></a>Combinaison de plusieurs filtres (composition de filtre)

Vous pouvez également combiner plusieurs filtres dans une URL unique. 

Le scénario suivant explique les avantages de la combinaison de filtres :

1. Vous devez filtrer vos qualités vidéos pour des appareils mobiles tels qu’Android ou iPAD (afin de limiter les qualités vidéos). Pour supprimer les qualités indésirables, vous pouvez créer un filtre de compte adapté aux profils de vos appareils. Les filtres de compte peuvent être utilisés pour tous vos éléments multimédia sous le même compte Media Services sans aucune autre association. 
2. Vous pouvez aussi avoir besoin d’ajuster l’heure de début et de fin d’un élément multimédia. Pour ce faire, il vous suffit de créer un filtre d’élément multimédia et de définir l’heure de début et de fin. 
3. Vous pouvez combiner ces deux filtres (sans combinaison, vous devez ajouter le filtrage de la qualité au filtre de réglage, ce qui complique davantage l’utilisation de filtres).

Pour combiner des filtres, vous devez définir les noms de filtre dans l’URL du manifeste ou de la liste de lecture en les délimitant avec des points-virgules. Supposons que vous ayez un filtre nommé *MyMobileDevice* qui filtre les qualités et un autre nommé *MyStartTime* qui définit une heure de début spécifique. Vous pouvez les combiner comme suit :

Vous pouvez combiner jusqu’à trois filtres. 

Pour plus d’informations, consultez [ce](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) blog.

## <a name="associate-filters-with-streaming-locator"></a>Associer des filtres de localisateur de diffusion en continu

Vous pouvez spécifier une liste de filtres de compte d’actif ou, s’applique également à votre localisateur de diffusion en continu. Le [l’empaquetage dynamique](dynamic-packaging-overview.md) s’applique à cette liste de filtres avec ceux de votre client spécifie l’URL. Cette combinaison génère une [dyanamic manifeste](filters-dynamic-manifest-overview.md), qui est basé sur les filtres dans l’URL + filtres que vous spécifiez dans le localisateur de diffusion en continu. Nous vous recommandons d’utiliser cette fonctionnalité si vous souhaitez appliquer des filtres, mais ne souhaitez pas exposer les noms de filtre dans l’URL.

## <a name="considerations-and-limitations"></a>Considérations et limitations

- Les valeurs **forceEndTimestamp**, **presentationWindowDuration** et **liveBackoffDuration** ne doivent pas être définies pour un filtre de vidéo à la demande (VoD). Elles sont utilisées uniquement pour les scénarios de filtre en direct. 
- Le manifeste dynamique fonctionne dans les limites d'un groupe d'images (GOP) (images clés), par conséquent, le découpage est précis au niveau du GOP. 
- Vous pouvez utiliser le même nom de filtre pour les filtres de compte et d’élément multimédia. Les filtres d’élément multimédia ont une priorité plus élevée et remplacent les filtres de compte.
- Si vous mettez à jour un filtre, il peut prendre jusqu'à 2 minutes pour le point de terminaison de diffusion en continu actualise les règles. Si le contenu a été servi à l'aide de filtres (puis mis en cache dans des proxys et des caches CDN), la mise à jour de ces filtres peut entraîner des défaillances du lecteur. Il est recommandé d’effacer le cache après la mise à jour du filtre. Si cette option n'est pas possible, envisagez d'utiliser un filtre différent.
- Les clients doivent manuellement télécharger le manifeste et analyser la valeur startTimestamp exacte et l’échelle de temps.
    
    - Pour déterminer les propriétés des pistes d’un élément multimédia, [obtenez et examinez le fichier manifeste](#get-and-examine-manifest-files).
    - Voici la formule pour définir les propriétés d’horodatage du filtre d’élément multimédia : <br/>startTimestamp = &lt;heure de début dans le manifeste&gt; +  &lt;heure de début prévue du filtre en secondes&gt;* échelle de temps


## <a name="next-steps"></a>Étapes suivantes

Les articles suivants vous montrent comment créer des filtres par programmation.  

- [Créer des filtres avec les API REST](filters-dynamic-manifest-rest-howto.md)
- [Créer des filtres avec .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Créer des filtres avec l’interface CLI](filters-dynamic-manifest-cli-howto.md)

[renditions1]: ./media/filters-dynamic-manifest-overview/media-services-rendition-filter.png
[renditions2]: ./media/filters-dynamic-manifest-overview/media-services-rendition-filter2.png

[rendered_subclip]: ./media/filters-dynamic-manifests/media-services-rendered-subclip.png
[timeline_trim_event]: ./media/filters-dynamic-manifests/media-services-timeline-trim-event.png
[timeline_trim_subclip]: ./media/filters-dynamic-manifests/media-services-timeline-trim-subclip.png

[multiple-rules]:./media/filters-dynamic-manifest-overview/media-services-multiple-rules-filters.png

[subclip_filter]: ./media/filters-dynamic-manifest-overview/media-services-subclips-filter.png
[trim_event]: ./media/filters-dynamic-manifests/media-services-timeline-trim-event.png
[trim_subclip]: ./media/filters-dynamic-manifests/media-services-timeline-trim-subclip.png
[trim_filter]: ./media/filters-dynamic-manifest-overview/media-services-trim-filter.png
[redered_subclip]: ./media/filters-dynamic-manifests/media-services-rendered-subclip.png
[livebackoff_filter]: ./media/filters-dynamic-manifest-overview/media-services-livebackoff-filter.png
[language_filter]: ./media/filters-dynamic-manifest-overview/media-services-language-filter.png
[dvr_filter]: ./media/filters-dynamic-manifest-overview/media-services-dvr-filter.png
[skiing]: ./media/filters-dynamic-manifest-overview/media-services-skiing.png
[amp_diagnostics]: ./media/filters-dynamic-manifest-overview/amp_diagnostics.png
