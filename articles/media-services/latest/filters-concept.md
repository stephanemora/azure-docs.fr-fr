---
title: Définition de filtres dans Azure Media Services
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
ms.date: 02/12/2019
ms.author: juliako
ms.openlocfilehash: 09de372ffdb48c00fde9a43c07f8f8b574462d1f
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/18/2019
ms.locfileid: "56372933"
---
# <a name="define-account-filters-and-asset-filters"></a>Définir des filtres de comptes et des filtres d'éléments multimédias  

Quand vous transmettez votre contenu à un client (événements de streaming en direct ou vidéo à la demande), le fichier manifeste de l’élément multimédia par défaut ne permet pas toujours au client d’interagir avec le contenu comme il le voudrait. Azure Media Services vous permet d’appliquer des filtres de comptes et des filtres d’éléments multimédias à votre contenu. 

Les filtres sont des règles côté serveur qui permettent à vos clients d’effectuer des opérations comme : 

- Lire une seule section d'une vidéo (au lieu de la vidéo entière). Par exemple : 
  - Réduisez le manifeste pour afficher un sous-clip d'un événement en direct (« filtrage de sous-clip »), ou
  - Découpez le début d'une vidéo (« découpage d'une vidéo »).
- Distribuer uniquement les rendus spécifiés et/ou les pistes de langue spécifiées qui sont prises en charge par l’appareil utilisé pour lire le contenu (« filtrage de rendu »). 
- Ajustez la fenêtre de présentation (DVR) afin de fournir une longueur limitée de la fenêtre du DVR dans le lecteur (« ajustement de la fenêtre de présentation »).

Media Services propose des [manifestes dynamiques](filters-dynamic-manifest-overview.md) basés sur des filtres prédéfinis. Lorsque vous définissez des filtres, vos clients peuvent les utiliser dans l'URL de diffusion en continu. Il serait possible d’appliquer des filtres à des protocoles de streaming à débit adaptatif : HLS (HTTP Live Streaming) Apple, MPEG-DASH et Smooth Streaming.

Le tableau suivant présente des exemples d’URL utilisant des filtres :

|Protocole|Exemples|
|---|---|
|HLS V4|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|HLS V3|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3,filter=myAccountFilter)`|
|MPEG DASH|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=myAssetFilter)`|

## <a name="define-filters"></a>Définir des filtres

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

## <a name="example"></a>Exemples

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

## <a name="next-steps"></a>Étapes suivantes

Les articles suivants vous montrent comment créer des filtres par programmation.  

- [Créer des filtres avec les API REST](filters-dynamic-manifest-rest-howto.md)
- [Créer des filtres avec .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Créer des filtres avec l’interface CLI](filters-dynamic-manifest-cli-howto.md)

