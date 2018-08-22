---
title: Examiner la sortie d’Azure Video Indexer | Microsoft Docs
description: Cette rubrique examine la sortie de Video Indexer.
services: cognitive services
documentationcenter: ''
author: juliako
manager: cflower
ms.service: cognitive-services
ms.topic: article
ms.date: 05/30/2018
ms.author: juliako
ms.openlocfilehash: 7f61833d70cea3a6d176f5a08eb0db1c30e70e86
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2018
ms.locfileid: "35370848"
---
# <a name="examine-the-video-indexer-output-produced-by-v1-api"></a>Examiner la sortie de Video Indexer générée par l’API v1

> [!Note]
> Les API Video Indexer v1 sont désormais déconseillées et seront supprimées à partir du 1er août 2018. Vous devez commencer à utiliser les API Video Indexer v2 pour éviter les interruptions.
>
> Pour développer des contenus avec les API Video Indexer v2, consultez les instructions fournies [ici](https://api-portal.videoindexer.ai/). 

Si vous appelez l’API **Get Breakdowns** et si l’état de la réponse est OK, vous obtenez une sortie JSON détaillée en tant que contenu de la réponse. Le contenu JSON détaille les insights des vidéos spécifiées, y compris la transcription, les OCR, les personnes. Les détails incluent des mots clés (rubriques), les visages, les blocs. Chaque bloc inclut des intervalles de temps, les lignes de transcription, les lignes de l’OCR, les sentiments, les visages et les miniatures des blocs.

Vous pouvez utiliser l’API **Get Breakdowns** pour obtenir la répartition complète d’une vidéo en tant que contenu JSON.  
 
    GET https://videobreakdown.azure-api.net/Breakdowns/Api/Partner/Breakdowns/63c6d532ff HTTP/1.1
    Host: videobreakdown.azure-api.net
    Ocp-Apim-Subscription-Key: ••••••••••••••••••••••••••••••••
    
Vous pouvez également examiner visuellement les insights résumés de la vidéo en appuyant sur le bouton **Lire** dans le portail Video Indexer. Pour plus d’informations, voir [View and edit video insights](video-indexer-view-edit.md) (Afficher et modifier les insights des vidéos).

![Insights](./media/video-indexer-output-json/video-indexer-summarized-insights.png)

Cet article examine le contenu JSON retourné par l’API **Get Breakdowns**. Il peut être utile d’examiner l’article [Concepts](video-indexer-concepts.md).

> [!NOTE]
> Tous les jetons d’accès dans Video Indexer arrivent à expiration au bout d’une heure.

## <a name="root-elements"></a>Éléments racines

Attribut | Description
---|---
id|ID de la vidéo. Exemple : 63c6d532ff.
partition|Une partition logique que l’utilisateur peut spécifier lors du chargement afin de le rechercher ultérieurement.
Nom|Nom de la vidéo. Exemple : Azure Monitor.
description|Description de la vidéo. Par exemple, « John Kemnetz rejoint Scott Hanselman pour montrer comment exploiter au mieux les données de surveillance Azure avec Azure Monitor ».
userName|Le créateur de la vidéo. Par exemple, des vidéos de Channel 9.
createTime |Heure de création. Par exemple, 2017-03-31T16:36:41.4504249 + 00:00.
privacyMode|Votre vidéo peut avoir l’un des modes suivants : **Privé**, **Public**. **Public** : tous les membres de votre compte et toutes les personnes disposant d’un lien vers la vidéo peuvent la visionner. **Privé** : tous les membres de votre compte peuvent la visionner.
isOwned|True si l’utilisateur actuel est le propriétaire de la vidéo. Sinon, False.  
isBase|True si la répartition est basée sur une vidéo source. False si la répartition provient d’une playlist dérivée d’une autre répartition.
durationInSeconds|Durée de la vidéo.
summarizedInsights|Contient un attribut [summarizedInsights](#summarizedInsights).
breakdowns|Peut contenir une ou plusieurs [répartitions](#breakdowns)
social|Contient un élément **social** qui décrit le nombre de mentions j’aime et de vues de la vidéo.

## <a name="summarizedinsights"></a>summarizedInsights

Cette section présente le résumé des insights.

Attribut | Description
---|---
Nom|Nom de la vidéo. Exemple : Azure Monitor.
shortId|ID de la vidéo. Exemple : 63c6d532ff.
privacyMode|Votre vidéo peut avoir l’un des modes suivants : **Privé**, **Public**. **Public** : tous les membres de votre compte et toutes les personnes disposant d’un lien vers la vidéo peuvent la visionner. **Privé** : tous les membres de votre compte peuvent la visionner.
duration|Contient la durée d’un insight. La durée est exprimée en secondes.
thumbnailUrl|URL complète de la miniature de la vidéo. Par exemple, «https://www.videoindexer.ai/api/Thumbnail/3a9e38d72e/d1f5fac5-e8ae-40d9-a04a-6b2928fb5d10?accessToken=eyJ0eXAiOiJKV1QiLCJhbGciO....» Notez que l’URL contient un jeton d’accès d’une heure si la vidéo est privée. Après une heure, l’URL ne sera plus valide et vous devrez de nouveau obtenir la répartition avec une nouvelle URL dedans, ou appeler GetAccessToken pour obtenir un nouveau jeton d’accès et construire l’URL complète manuellement ('https://www.videoindexer.ai/api/Thumbnail/[shortId]/[ThumbnailId]?accessToken=[accessToken]').
visages|Peut contenir un ou plusieurs [visages](#faces)
topics|Peut contenir une ou plusieurs [rubriques](#topics)
sentiments|Peut contenir un ou plusieurs [sentiments](#sentiments)
audioEffects| Peut contenir un ou plusieurs [effets audio](#audioEffects)
brands| Peut contenir une ou plusieurs [marques](#brands)
Statistiques|Pour plus d’informations, consultez la section [Statistiques](#Statistics)
.
### <a name="statistics"></a>Statistiques

|NOM|Description|
|---|---|
|CorrespondenceCount|Nombre de correspondances contenues dans la vidéo.|
|WordCount|Nombre de mots par intervenant.|
|SpeakerNumberOfFragments|Quantité de fragments de l’intervenant dans une vidéo.|
|SpeakerLongestMonolog|Monologue le plus long de l’intervenant. Si le monologue de l’intervenant comporte des silences, ils sont inclus. Les silences du début et de la fin du monologue sont supprimés.| 
|SpeakerTalkToListenRatio|Le calcul est basé sur le temps passé sur le monologue de l’intervenant (sans les silences intermédiaires) divisé par la durée totale de la vidéo. L’heure est arrondie à la troisième décimale.|

## <a name="breakdowns"></a>breakdowns

Cette section présente les détails des insights.

Attribut | Description
---|---
id|ID de répartition. Exemple : 63c6d532ff.
state|L’état de traitement de l’ID de répartition donné. Peut être l’un des états suivants : chargé, en cours de traitement, traité, échoué.
processingProgress|État d’avancement. Par exemple, 10 %.
externalId| Vous pouvez définir externalId lors du chargement. Par exemple, « 4f9c3500-eca7-4ab3-987e-a745017af698. » Vous pouvez rechercher vos vidéos plus tard à partir de cet ID externe.
externalUrl|Vous pouvez définir externalUrl lors du chargement. 
metadata|Vous pouvez définir metadata lors du chargement. 
insights|Peut contenir un ou plusieurs [insights](#insights)
thumbnailUrl|URL complète de la miniature de la vidéo. Par exemple : «https://www.videoindexer.ai/api/Thumbnail/3a9e38d72e/d1f5fac5-e8ae-40d9-a04a-6b2928fb5d10?accessToken=eyJ0eXAiOiJKV1QiLCJhbGciO..». Notez que l’URL contient un jeton d’accès d’une heure si la vidéo est privée. Après une heure, l’URL ne sera plus valide et vous devrez de nouveau obtenir la répartition avec une nouvelle URL dedans, ou appeler GetAccessToken pour obtenir un nouveau jeton d’accès et construire l’URL complète manuellement ('https://www.videoindexer.ai/api/Thumbnail/[shortId]/[ThumbnailId]?accessToken=[accessToken]').
publishedUrl|L'URL publiée. Par exemple, «https://BreakdownMedia.azureedge.net:443/d5e5232d-48e2-4fbc-9893-0ea6335da563/Azure%20Monitor%20%20Azure%20Friday.ism/manifest.»
viewToken|Le jeton du porteur
sourceLanguage|La langue source. Les langues suivantes sont prises en charge : chinois, anglais, français, allemand, italien, japonais, portugais, russe, espagnol.
Langage|La langue de la transcription.

## <a name="insights"></a>insights

Attribut | Description 
---|---
transcriptBlocks|Peut contenir un ou plusieurs [transcriptBlocks](#transcriptBlocks)
topics|Peut contenir une ou plusieurs [rubriques](#topics)
visages|Peut contenir un ou plusieurs [visages](#faces)
participants|Peut contenir un ou plusieurs [participants](#participants)
contentModeration|Peut contenir un [contentModeration](#contentModeration)
audioEffectsCategories|Peut contenir une ou plusieurs [catégories d’effets audio](#audioEffectsCategories)

## <a name="faces"></a>visages

### <a name="summarizedinsights"></a>summarizedInsights

Les **visages** qui apparaissent sous **summarizedInsights**, affichent un résumé de chaque visage trouvé dans la vidéo.

Attribut | Description 
---|---
id|L’ID d’une personne. Par exemple, 11775.
shortId|ID court. Une playlist peut être dérivée de plusieurs répartitions, cet ID est nécessaire pour savoir lequel de ces répartitions est l’origine de chaque face.  
Nom|Si le visage est reconnu, le nom de la personne est ajouté. Par exemple, « Scott Hanselman. » Si le visage est inconnu, « UNKNOWN # » est ajouté. 
description|Si le visage est reconnu, la description est remplie à partir d’une recherche de l’API Bing. Sinon, la description est **null**.
title|Si le visage est reconnu, la description est remplie à partir d’une recherche de l’API Bing. Sinon, le titre est **null**.
thumbnailFullUrl|URL complète de la miniature du visage. Par exemple, «https://www.videoindexer.ai/api/Thumbnail/3a9e38d72e/d1f5fac5-e8ae-40d9-a04a-6b2928fb5d10?accessToken=eyJ0eXAiOiJKV1QiLCJhbGciO....» Notez que l’URL contient un jeton d’accès d’une heure si la vidéo est privée. Après une heure, l’URL ne sera plus valide et vous devrez de nouveau obtenir la répartition avec une nouvelle URL dedans, ou appeler GetAccessToken pour obtenir un nouveau jeton d’accès et construire l’URL complète manuellement ('https://www.videoindexer.ai/api/Thumbnail/[shortId]/[ThumbnailId]?accessToken=[accessToken]').
appearances|Peut contenir une ou plusieurs [apparences](#appearances)
seenDuration|La durée pendant laquelle le visage est apparu (en secondes).
seenDurationRatio|Présence par rapport à la durée de la vidéo (0-1).

### <a name="breakdown-insights"></a>breakdown insights

Les **visages** qui apparaissent sous **répartitions**, décrivent les détails de chaque visage trouvé dans la vidéo.

Attribut | Description 
---|---
id|L’ID d’une personne. Par exemple, 11775.
bingId|
Nom|Si le visage est reconnu, le nom de la personne est ajouté. Par exemple, « Scott Hanselman ». Si le visage est inconnu, « UNKNOWN # » est ajouté. 
thumbnailId|Par exemple, 616468f0-1636-4efa-94e7-262f2e575059.
description|Si le visage est reconnu, la description est remplie à partir d’une recherche de l’API Bing. Sinon, la description est **null**.
title|Si le visage est reconnu, la description est remplie à partir d’une recherche de l’API Bing. Sinon, le titre est **null**.
imageUrl|Cette URL pointe vers une image extraite de la source vidéo.  
confidence|Le score de confiance (une valeur élevée est préférable).
knownPersonId|L’ID d’une personne connue (par exemple, une célébrité). Si une personne n’est pas connue, l’ID contient des zéros. Par exemple, e3eaff5f-ee1b-4eac-80ce-ebac47aadf64.

## <a name="topics"></a>topics

### <a name="summarizedinsights"></a>summarizedInsights

Les **rubriques** qui apparaissent sous **summarizedInsights**, affichent un résumé de chaque rubrique trouvée dans la vidéo.

Attribut | Description 
---|---
Nom|Le nom de la rubrique (par exemple, « Azure »). 
appearances|Peut contenir une ou plusieurs [apparences](#appearances).
isTranscript|True si trouvé dans une transcription. False, si trouvé dans une reconnaissance optique de caractères (OCR).

### <a name="breakdown-insights"></a>breakdown insights

Les **rubriques** qui apparaissent sous **répartitions**, décrivent les détails de chaque rubrique trouvée dans la vidéo.

|Attribut | Description |
|---|---|
|id|ID unique de la rubrique.|
|Nom|Nom de la rubrique.|
|stem|Cet attribut n’est pas utilisé actuellement.|
|words|Cet attribut n’est pas utilisé actuellement.|
|rank|Note de pertinence (une valeur élevée est préférable).|

## <a name="sentiments"></a>sentiments

Attribut | Description
---|---
sentimentKey| Actuellement, les sentiments suivants sont pris en charge : positif, négatif, neutre. 
appearances|Peut contenir une ou plusieurs [apparences](#appearances)|.
seenDurationRatio|Présence par rapport à la durée de la vidéo (0-1).

## <a name="audioeffects"></a>audioEffects

Attribut | Description 
---|---
audioEffectKey| Les valeurs autorisées sont : voix, silence, applaudissements.
appearances|Peut contenir une ou plusieurs [apparences](#appearances)
seenDurationRatio|Présence par rapport à la durée de la vidéo (0-1).
seenDuration|Durée pendant laquelle l’effet audio était présent (en secondes).

## <a name="appearances"></a>appearances

Attribut | Description 
---|---
startTime| Valeur de temps.
endTime|Valeur de temps.
startSeconds| Valeur de temps.
endSeconds| Valeur de temps.

## <a name="participants"></a>participants

Attribut | Description 
---|---
id|ID du participant.
Nom|Nom du participant. Par exemple, Intervenant n°1.
pictureUrl|L’attribut **pictureUrl** est réservé pour une utilisation ultérieure.

## <a name="contentmoderation"></a>contentModeration

Attribut | Description 
---|---
adultClassifierValue|Le niveau de confiance que la vidéo comporte un contenu pour adultes.
racyClassifierValue|Le niveau de confiance que la vidéo comporte un contenu osé.
bannedWordsCount|Nombre de propos injurieux. 
bannedWordsRatio|Ratio de propos injurieux par rapport au nombre total de mots.
reviewRecommended|Valeur booléenne indiquant si la vidéo doit être revue manuellement.
isAdult|Valeurs booléennes indiquant si la vidéo est considérée comme une vidéo pour adultes après une revue manuelle.

## <a name="audioeffectscategories"></a>audioEffectsCategories

Attribut | Description 
---|---
Type|ID de la catégorie.
key|Une des valeurs suivantes : voix, silence, applaudissements. 

## <a name="transcriptblocks"></a>transcriptBlocks

Attribut | Description
---|---
id|ID du bloc.
lignes|Peut contenir une ou plusieurs [lignes](#lines)
sentimentIds|L’attribut **sentimentIds** est réservé pour une utilisation ultérieure.
thumbnailIds|L’attribut **thumbnailIds** est réservé pour une utilisation ultérieure.
tendance|Le sentiment dans le bloc (0-1, négatif vers positif).
visages|Peut contenir un ou plusieurs [visages](#faces).
ocrs|Peut contenir une ou plusieurs [OCR](#ocrs).
audioEffectInstances|Peut contenir une ou plusieurs [instances d’effets audio](#audioEffectInstances).
scenes|Peut contenir une ou plusieurs [scènes](#scenes).
annotations|Peut contenir zéro ou plusieurs [annotations](#annotations).

## <a name="ocrs"></a>ocrs

Décrit à quel moment le contenu de texte a été trouvé dans la vidéo. 

Attribut | Description 
---|---
timeRange|L’intervalle de temps dans la vidéo d’origine.
adjustedTimeRange|AdjustedTimeRange est l’intervalle de temps par rapport à la playlist actuelle. Dans la mesure où vous pouvez créer une playlist à partir de différentes lignes de plusieurs vidéos, vous pouvez prendre une vidéo d’une heure et en tirer une seule ligne, par exemple, 10:10-15:15. Dans ce cas, vous aurez une playlist avec une ligne, où l’intervalle de temps est 10:00-10:15, mais le paramètre adjustedTimeRange sera 00:00-00:15.
lignes|Peut contenir une ou plusieurs [lignes](#lines).

## <a name="lines"></a>lignes

### <a name="transcriptblocks"></a>transcriptBlocks

Les **lignes** qui apparaissent sous **transcriptBlocks**, décrivent les lignes de transcription trouvées dans la vidéo.

Attribut | Description 
---|---
id| ID de la ligne.
timeRange|L’intervalle de temps dans la vidéo d’origine.
adjustedTimeRange|AdjustedTimeRange est l’intervalle de temps par rapport à la playlist actuelle. Dans la mesure où vous pouvez créer une playlist à partir de différentes lignes de plusieurs vidéos, vous pouvez prendre une vidéo d’une heure et en tirer une seule ligne, par exemple, 10:10-15:15. Dans ce cas, vous aurez une playlist avec une ligne, où l’intervalle de temps est 10:00-10:15, mais le paramètre adjustedTimeRange sera 00:00-00:15.
participantID| L’ID de l’intervenant de cette ligne.
texte| La transcription.
isIncluded| Toujours True dans les répartitions de base. Dans les playlists dérivées, les lignes incluses dans la source vidéo, sont définies sur isIncluded=true. Toutes les autres lignes ont la valeur False.

### <a name="ocrs"></a>ocrs

Les **lignes** qui apparaissent sous **OCR**, décrivent des lignes d’OCR trouvées dans la vidéo.

Attribut | Description 
---|---
id|ID de l’OCR.
width|Cet attribut n’est pas utilisé actuellement.
height|Cet attribut n’est pas utilisé actuellement.
Langage|Langue de l’OCR.
textData|Texte de l’OCR.
confidence|Le score de confiance (une valeur élevée est préférable).

## <a name="scenes"></a>scenes

Attribut | Description 
---|---
id|L’ID de la scène.
timeRange|Contient une **plage horaire**.
keyFrame|L’heure de l’image clé.
captures|Peut contenir une ou plusieurs [captures](#shots).

## <a name="shots"></a>captures

Attribut | Description 
---|---
id||ID de la capture.
timeRange|Contient une **plage horaire**.
keyFrame|L’heure de l’image clé.

## <a name="audioeffectinstances"></a>audioEffectInstances

Attribut | Description 
---|---
Type|L’index de l’événement audio : rires = 1, applaudissements = 2, musique = 3, voix = 4, silence = 5
ranges|Peut contenir un ou plusieurs [plages](#ranges).

## <a name="ranges"></a>ranges

Les **plages** qui apparaissent sous **audioEffectInstances**, décrivent des effets audio dans ces plages.

Attribut | Description 
---|---
timeRange|L’intervalle de temps dans la vidéo d’origine.
adjustedTimeRange|AdjustedTimeRange est l’intervalle de temps par rapport à la playlist actuelle. Dans la mesure où vous pouvez créer une playlist à partir de différentes lignes de plusieurs vidéos, vous pouvez prendre une vidéo d’une heure et en tirer une seule ligne, par exemple, 10:00-10:15. Dans ce cas, vous aurez une playlist avec une ligne, où l’intervalle de temps est 10:00-10:15, mais le paramètre adjustedTimeRange sera 00:00-00:15.

## <a name="annotations"></a>annotations

Retourne les balises basées sur les objets reconnaissables, les êtres vivants, les scènes, les actions et les modèles visuels.

|Attribut|Description|
|---|---|
|id|ID de l’annotation.|
|NOM|Le nom de l’annotation (par exemple, une personne, un jeu sportif, des cadres noirs).|
|Appearances|Peut contenir une ou plusieurs apparences.|

## <a name="brands"></a>brands

Noms des entreprises et des marques de produits détectés dans la reconnaissance vocale et/ou la reconnaissance optique de caractères des vidéos. Cela n’inclut pas la reconnaissance visuelle des marques ni la détection des logos.

Attribut | Description
---|---
id | L’ID d’une marque.
Nom | Le nom de la marque tiré de Bing ou une marque personnalisée.  
wikiId | Suffixe de l’URL Wikipédia de marque. Exemple : «Target_Corporation » est le suffixe de [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation).
wikiUrl | URL Wikipédia de la marque, le cas échéant. Par exemple : [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation).
confidence | Valeur de confiance du détecteur de marque de Video Indexer (0-1).
description | Description de la marque extraite de Wikipédia. 
appearances | Peut contenir une ou plusieurs apparences.
seenDuration | Présence par rapport à la durée de la vidéo (0-1).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la façon de créer votre propre répartition, consultez [Afficher et modifier les insights Video Indexer](video-indexer-view-edit.md).

Pour plus d’informations sur l’incorporation de widgets dans votre application, voir [Embed Video Indexer widgets into your applications](video-indexer-embed-widgets.md) (Incorporer des widgets Video Indexer à vos applications). 

## <a name="see-also"></a>Voir aussi

[API Video Indexer](https://videobreakdown.portal.azure-api.net/docs/services/582074fb0dc56116504aed75/operations/5857caeb0dc5610f9ce979e4)

[Présentation de Video Indexer](video-indexer-overview.md)

