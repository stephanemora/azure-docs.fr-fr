---
title: Filtres et manifestes dynamiques Azure Media Services | Microsoft Docs
description: Cette rubrique décrit comment créer des filtres pour que votre client puisse les utiliser pour diffuser des sections spécifiques d'un flux. Media Services crée des manifestes dynamiques pour l’archivage de cette diffusion sélective.
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
ms.date: 01/24/2019
ms.author: juliako
ms.openlocfilehash: 9c463740acf6ef464880a43e0e68de683b97f64f
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55813417"
---
# <a name="filters-and-dynamic-manifests"></a>Filtres et manifestes dynamiques

Quand vous transmettez votre contenu à un client (événements de streaming en direct ou vidéo à la demande), le fichier manifeste de l’élément multimédia par défaut ne permet pas toujours au client d’interagir avec le contenu comme il le voudrait. Azure Media Services vous permet d’appliquer des filtres de comptes et des filtres d’éléments multimédias à votre contenu. 

Les filtres sont des règles côté serveur qui permettent à vos clients d’effectuer des opérations comme : 

- Lire une seule section d'une vidéo (au lieu de la vidéo entière). Par exemple : 

    - Réduisez le manifeste pour afficher un sous-clip d'un événement en direct (« filtrage de sous-clip »), ou
    - Découpez le début d'une vidéo (« découpage d'une vidéo »).

- Distribuer uniquement les rendus spécifiés et/ou les pistes de langue spécifiées qui sont prises en charge par l’appareil utilisé pour lire le contenu (« filtrage de rendu »). 
- Ajustez la fenêtre de présentation (DVR) afin de fournir une longueur limitée de la fenêtre du DVR dans le lecteur (« ajustement de la fenêtre de présentation »).

Cette rubrique décrit les [concepts](#concepts) et présente les définitions des filtres. Elle fournit ensuite des précisions sur les scénarios les plus courants. À la fin de l’article, vous trouverez des liens montrant comment créer des filtres par programmation.  

## <a name="concepts"></a>Concepts

### <a name="dynamic-manifests"></a>Manifestes dynamiques

Media Services propose des **manifestes dynamiques** basés sur des filtres prédéfinis. Une fois que vous avez défini les filtres, vos clients peuvent les utiliser pour diffuser un rendu spécifique ou des sous-clips de votre vidéo. Ils peuvent spécifier des filtres dans l'URL de diffusion en continu. Il serait possible d’appliquer des filtres à des protocoles de streaming à débit adaptatif : HLS (HTTP Live Streaming) Apple, MPEG-DASH et Smooth Streaming. 

Le tableau suivant présente des exemples d’URL utilisant des filtres :

|Protocole|Exemples|
|---|---|
|HLS V4|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|HLS V3|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3,filter=myAccountFilter)`|
|MPEG DASH|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=myAssetFilter)`|

> [!NOTE]
> Les manifestes dynamiques ne changent pas l'élément multimédia ni son manifeste par défaut. Votre client peut choisir de demander un flux avec ou sans filtres. 
> 
> 

### <a name="manifest-files"></a>Fichiers manifeste

Quand vous encodez un élément multimédia pour une diffusion à débit binaire adaptatif, un fichier **manifeste** (ou sélection) est créé (il s'agit d'un fichier texte ou XML). Le fichier **manifeste** inclut des métadonnées de diffusion en continu telles que les suivantes : type de piste (audio, vidéo ou texte), nom de piste, heure de début et de fin, débit binaire (qualités), langues de piste, fenêtre de présentation (fenêtre glissante de durée fixe), codec vidéo (FourCC). Il indique également au lecteur de récupérer le fragment suivant en fournissant des informations sur les fragments vidéo pouvant être lus suivants disponibles et leur emplacement. Les fragments (ou segments) correspondent aux « blocs » réels d'un contenu vidéo.

Voici un exemple de fichier manifeste HLS : 

```
#EXT-X-MEDIA:TYPE=AUDIO,GROUP-ID="audio",NAME="aac_eng_2_128041_2_1",LANGUAGE="eng",DEFAULT=YES,AUTOSELECT=YES,URI="QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)"
#EXT-X-STREAM-INF:BANDWIDTH=536209,RESOLUTION=320x180,CODECS="avc1.64000d,mp4a.40.2",AUDIO="audio"
QualityLevels(380658)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=536209,RESOLUTION=320x180,CODECS="avc1.64000d",URI="QualityLevels(380658)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=884474,RESOLUTION=480x270,CODECS="avc1.640015,mp4a.40.2",AUDIO="audio"
QualityLevels(721426)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=884474,RESOLUTION=480x270,CODECS="avc1.640015",URI="QualityLevels(721426)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=1327838,RESOLUTION=640x360,CODECS="avc1.64001e,mp4a.40.2",AUDIO="audio"
QualityLevels(1155246)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=1327838,RESOLUTION=640x360,CODECS="avc1.64001e",URI="QualityLevels(1155246)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=2414544,RESOLUTION=960x540,CODECS="avc1.64001f,mp4a.40.2",AUDIO="audio"
QualityLevels(2218559)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=2414544,RESOLUTION=960x540,CODECS="avc1.64001f",URI="QualityLevels(2218559)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=3805301,RESOLUTION=1280x720,CODECS="avc1.640020,mp4a.40.2",AUDIO="audio"
QualityLevels(3579378)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=3805301,RESOLUTION=1280x720,CODECS="avc1.640020",URI="QualityLevels(3579378)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=139017,CODECS="mp4a.40.2",AUDIO="audio"
QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)
```

### <a name="get-and-examine-manifest-files"></a>Obtenir et examiner des fichiers manifeste

Vous spécifiez une liste de conditions de propriétés de suivi de filtre selon laquelle les pistes de votre flux de données (en live streaming ou en vidéo à la demande) doivent être incluses dans le manifeste créé dynamiquement. Pour obtenir et examiner les propriétés des pistes, vous devez d’abord charger le manifeste Smooth Streaming.

Le tutoriel [Charger, encoder et diffuser en continu des fichiers avec .NET](stream-files-tutorial-with-api.md) vous montre comment créer des URL de diffusion en continu avec .NET (voir la section [Création d’URL](stream-files-tutorial-with-api.md#get-streaming-urls)). Si vous exécutez l’application, une des URL pointe vers le manifeste Smooth Streaming : `https://amsaccount-usw22.streaming.media.azure.net/00000000-0000-0000-0000-0000000000000/ignite.ism/manifest`.<br/>Copiez et collez l’URL dans la barre d’adresse d’un navigateur. Le fichier va être téléchargé. Vous pouvez l’ouvrir dans un éditeur de texte de votre choix.

Pour l’exemple REST, consultez [Charger, encoder et diffuser en continu](stream-files-tutorial-with-rest.md#list-paths-and-build-streaming-urls).

### <a name="monitor-the-bitrate-of-a-video-stream"></a>Surveiller la vitesse de transmission d’un flux vidéo

Vous pouvez utiliser la [page de démo Azure Media Player](http://aka.ms/amp) pour surveiller la vitesse de transmission d’un flux vidéo. La page de démo affiche des informations de diagnostic dans l’onglet **Diagnostics** :

![Diagnostics Azure Media Player][amp_diagnostics]

## <a name="defining-filters"></a>Définition des filtres

Il existe deux types de filtres d'éléments multimédias : 

* [Filtres de compte](https://docs.microsoft.com/rest/api/media/accountfilters) (globaux) : applicables à n'importe quel élément multimédia dans le compte Azure Media Services et dont la durée de vie correspond à celle du compte.
* [Filtres d’élément multimédia](https://docs.microsoft.com/rest/api/media/assetfilters) (locaux) : applicables uniquement à un élément multimédia avec lequel le filtre a été associé au moment de la création et dont la durée de vie correspond à celle de l'élément multimédia. 

Les types [Filtres de compte](https://docs.microsoft.com/rest/api/media/accountfilters) et [Filtre d’élément multimédia](https://docs.microsoft.com/rest/api/media/assetfilters) utilisent exactement les mêmes propriétés pour définir/décrire le filtre. Sauf lorsque vous créez le **filtre d’élément multimédia**, vous devez spécifier le nom de la ressource à laquelle vous souhaitez associer le filtre.

Selon votre scénario, vous choisissez le type de filtre le plus adapté (filtre d’élément multimédia ou de compte). Les filtres de compte conviennent aux profils d'appareil (filtrage de rendu) alors que les filtres d’élément multimédia peuvent être utilisés pour découper un élément multimédia spécifique.

Utilisez les propriétés suivantes pour décrire les filtres. 

|Nom|Description|
|---|---|
|firstQuality|Première qualité de vitesse de transmission du filtre.|
|presentationTimeRange|Plage horaire de présentation. Cette propriété sert à filtrer les points de début/fin du manifeste, la durée de la fenêtre de présentation, et la position de départ du flux en direct. <br/>Pour plus d’informations, consultez [PresentationTimeRange](#PresentationTimeRange).|
|tracks|Conditions de sélection des pistes. Pour plus d’informations, consultez [tracks](#tracks)|

### <a name="presentationtimerange"></a>PresentationTimeRange

Utilisez cette propriété avec les **filtres d’élément multimédia**. Il n’est pas recommandé de définir la propriété avec des **filtres de compte**.

|Nom|Description|
|---|---|
|**endTimestamp**|Limite absolue de l’heure de fin. Applicable à la vidéo à la demande (VoD). Pour la présentation en direct, cette propriété est ignorée (mode silencieux) puis appliquée lorsque la présentation se termine et que le flux est transmis en VoD.<br/><br/>La valeur représente un point de fin absolu du flux. Elle est arrondie au début GOP suivant le plus proche.<br/><br/>Utilisez StartTimestamp et EndTimestamp pour ajuster la sélection (manifeste). Par exemple, StartTimestamp=40000000 et EndTimestamp = 100000000 généreront une sélection contenant les éléments multimédia situés entre StartTimestamp et EndTimestamp. Si un fragment approche la limite, le fragment entier sera inclus dans le manifeste.<br/><br/>Consultez également la définition **forceEndTimestamp** qui suit.|
|**forceEndTimestamp**|S’applique aux filtres en direct.<br/><br/>**forceEndTimestamp** est une valeur booléenne qui indique si **endTimestamp** a été défini sur une valeur valide. <br/><br/>Si la valeur est **true**, la valeur **endTimestamp** doit être spécifiée. Si elle n’est pas spécifiée, une demande incorrecte est retournée.<br/><br/>Si, par exemple, vous souhaitez définir un filtre commençant à 5 minutes dans la vidéo en entrée, et qui reste valable jusqu'à la fin du flux de données, définissez **forceEndTimestamp** sur la valeur false et omettez le paramètre **endTimestamp**.|
|**liveBackoffDuration**|S’applique à la lecture en direct uniquement. La propriété est utilisée pour définir la position de la lecture en direct. Cette règle vous permet de différer la position de lecture en direct et de créer une mémoire tampon côté serveur pour les lecteurs. La valeur LiveBackoffDuration dépend de la position en direct. La durée d’interruption en direct maximale est de 300 secondes.|
|**presentationWindowDuration**|S’applique à la lecture en direct. Utilisez **presentationWindowDuration** pour appliquer une fenêtre glissante à la sélection. Par exemple, définissez presentationWindowDuration=1200000000 pour appliquer une fenêtre glissante de deux minutes. Les éléments multimédia situés à 2 minutes de la session en direct figureront dans la sélection. Si un fragment approche la limite, le fragment entier sera inclus dans la sélection. La durée minimale de la fenêtre de présentation est de 60 secondes.|
|**startTimestamp**|S’applique aux flux de vidéo à la demande ou en direct. La valeur représente un point de début absolu du flux. Elle est arrondie au début GOP suivant le plus proche.<br/><br/>Utilisez **startTimestamp** et **endTimestamp** pour ajuster la sélection (manifeste). Par exemple, startTimestamp=40000000 et endTimestamp = 100000000 généreront une sélection contenant les éléments multimédia situés entre StartTimestamp et EndTimestamp. Si un fragment approche la limite, le fragment entier sera inclus dans le manifeste.|
|**timescale**|S’applique aux flux de vidéo à la demande ou en direct. L’échelle de temps utilisée par les horodatages et les durées spécifiés ci-dessus. L’échelle de temps par défaut est 10 000 000. Une autre échelle de temps peut être utilisée. La valeur par défaut est 10000000 HNS (cent nanosecondes).|

### <a name="tracks"></a>Pistes

Vous spécifiez une liste de conditions de propriétés de suivi de filtre (FilterTrackPropertyConditions) selon laquelle les pistes de votre flux de données (en live streaming ou en vidéo à la demande) doivent être incluses dans le manifeste créé dynamiquement. Les filtres sont combinés à l’aide d’une opération logique **AND** et **OR**.

Les conditions de propriétés de suivi de filtre décrivent les types de suivi, les valeurs (spécifiées dans le tableau suivant) et les opérations (Equal, NotEqual). 

|Nom|Description|
|---|---|
|**Bitrate**|Utilisez la vitesse de transmission de la piste pour le filtrage.<br/><br/>La valeur recommandée est une plage de vitesses de transmission, en bits par seconde. Par exemple, « 0-2427000 ».<br/><br/>Remarque : vous pouvez utiliser une valeur de vitesse de transmission spécifique, comme 250000 (bits par seconde), mais cette approche n'est pas recommandée car les vitesses de transmission exacts peuvent varier d’un élément multimédia à un autre.|
|**FourCC**|Utilisez la valeur FourCC de la piste pour le filtrage.<br/><br/>La valeur est le premier élément du format de codecs, tel que spécifié dans [RFC 6381](https://tools.ietf.org/html/rfc6381). À l’heure actuelle, les codecs suivants sont pris en charge : <br/>Pour la vidéo : « avc1 », « hev1 », « hvc1 »<br/>Pour l’audio : « mp4a », « ec-3 »<br/><br/>Pour déterminer les valeurs FourCC des pistes d’un élément multimédia, [obtenez et examinez le fichier manifest](#get-and-examine-manifest-files).|
|**Langage**|Utilisez la langue de la piste pour le filtrage.<br/><br/>La valeur est la balise d’une langue que vous souhaitez inclure, comme indiqué dans RFC 5646. Exemple : « en ».|
|**Nom**|Utilisez le nom de la piste pour le filtrage.|
|**Type**|Utilisez le type de la piste pour le filtrage.<br/><br/>Les valeurs suivantes sont autorisées : « video », « audio » ou « text ».|

### <a name="example"></a>Exemples

```json
{
  "properties": {
    "presentationTimeRange": {
      "startTimestamp": 0,
      "endTimestamp": 170000000,
      "presentationWindowDuration": 9223372036854776000,
      "liveBackoffDuration": 0,
      "timescale": 10000000,
      "forceEndTimestamp": false
    },
    "firstQuality": {
      "bitrate": 128000
    },
    "tracks": [
      {
        "trackSelections": [
          {
            "property": "Type",
            "operation": "Equal",
            "value": "Audio"
          },
          {
            "property": "Language",
            "operation": "NotEqual",
            "value": "en"
          },
          {
            "property": "FourCC",
            "operation": "NotEqual",
            "value": "EC-3"
          }
        ]
      },
      {
        "trackSelections": [
          {
            "property": "Type",
            "operation": "Equal",
            "value": "Video"
          },
          {
            "property": "Bitrate",
            "operation": "Equal",
            "value": "3000000-5000000"
          }
        ]
      }
    ]
  }
}
```

## <a name="rendition-filtering"></a>Filtrage de rendu

Vous pouvez choisir d'encoder votre élément multimédia pour plusieurs profils d'encodage (H.264 Baseline, H.264 High, AACL, AACH, Dolby Digital Plus) et plusieurs qualités de débit binaire. Toutefois, tous les appareils client ne prennent pas en charge tous les profils et débits binaires de tous vos éléments multimédias. Par exemple, les anciens appareils Android prennent uniquement en charge H.264 Baseline+AACL. L'envoi à des vitesses de transmission plus élevées à un appareil qui ne peut pas en tirer partie ne fait que gaspiller la bande passante et entraîner des calculs inutiles pour l'appareil. Ce dernier doit en effet décoder toutes les informations données, pour finalement les réduire pour pouvoir les afficher.

Avec un manifeste dynamique, vous pouvez créer des profils d'appareil mobile, de console, HD/SD, etc. et inclure les pistes et les qualités que vous voulez intégrer dans chaque profil.

![Exemple de filtrage de rendu][renditions2]

Dans l’exemple suivant, un encodeur a été utilisé pour encoder un élément multimédia mezzanine en sept rendus vidéo ISO MP4s (de 180 p à 1 080 p). L’élément multimédia encodé peut être empaqueté de manière dynamique dans l’un des protocoles de diffusion en continu suivants : HLS, MPEG DASH et Smooth.  En haut du diagramme, le manifeste HLS de l'élément multimédia sans aucun filtre apparaît (il contient les sept rendus).  Dans la partie inférieure gauche apparaît le manifeste HLS auquel un filtre nommé « ott » a été appliqué. Le filtre « ott » indique de supprimer toutes les vitesses de transmission inférieures à 1 Mbit/s, ce qui entraîne la suppression des deux niveaux de qualité les plus bas dans la réponse. Dans la partie inférieure droite apparaît le manifeste HLS auquel un filtre nommé « mobile » a été appliqué. Le filtre « mobile » indique de supprimer les rendus pour lesquels la résolution est supérieure à 720 p, ce qui entraîne la suppression des deux rendus à 1 080 p.

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

## <a name="considerations-and-limitations"></a>Considérations et limitations

- Les valeurs **forceEndTimestamp**, **presentationWindowDuration** et **liveBackoffDuration** ne doivent pas être définies pour un filtre de vidéo à la demande (VoD). Elles sont utilisées uniquement pour les scénarios de filtre en direct. 
- Le manifeste dynamique fonctionne dans les limites d'un groupe d'images (GOP) (images clés), par conséquent, le découpage est précis au niveau du GOP. 
- Vous pouvez utiliser le même nom de filtre pour les filtres de compte et d’élément multimédia. Les filtres d’élément multimédia ont une priorité plus élevée et remplacent les filtres de compte.
- Si vous mettez à jour un filtre, il peut falloir jusqu'à 2 minutes pour que le point de terminaison de diffusion en continu actualise les règles. Si le contenu a été servi à l'aide de filtres (puis mis en cache dans des proxys et des caches CDN), la mise à jour de ces filtres peut entraîner des défaillances du lecteur. Il est recommandé d’effacer le cache après la mise à jour du filtre. Si cette option n'est pas possible, envisagez d'utiliser un filtre différent.
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
