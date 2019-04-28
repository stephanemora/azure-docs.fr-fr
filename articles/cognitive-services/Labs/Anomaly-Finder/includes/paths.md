---
title: Fichier Include
description: Fichier Include
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: 80503ad154a9fc4d01614ffd2816f9d5fd497fdb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60309503"
---
<a name="paths"></a>
## <a name="paths"></a>Chemins

<a name="anomalydetection-post"></a>
### <a name="detect-anomaly-points-for-the-time-series-data-points-requested"></a>Détecter les points d’anomalies pour les points de données de série chronologique demandés
```
POST /anomalydetection
```


#### <a name="parameters"></a>parameters

|Type|Nom|Description|Schéma|
|---|---|---|---|
|**Corps**|**body**  <br>*obligatoire*|Les points de données de série chronologique et la période si nécessaire.|[requête](#request)|


#### <a name="responses"></a>Réponses

|Code HTTP|Description|Schéma|
|---|---|---|
|**200**|Opération réussie.|Tableau < [response](#response) >|
|**400**|Impossible d’analyser une requête JSON.|Pas de contenu|
|**403**|Le certificat que vous avez fourni n’est pas accepté par le serveur.|Pas de contenu|
|**405**|Méthode non autorisée.|Pas de contenu|
|**500**|Erreur interne du serveur.|Pas de contenu|


#### <a name="consumes"></a>Consomme

* `application/json`


#### <a name="produces"></a>Produit

* `application/json`


#### <a name="tags"></a>Balises

* anomalydetection



