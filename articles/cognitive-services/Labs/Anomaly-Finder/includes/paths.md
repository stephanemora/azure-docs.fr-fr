---
title: Fichier Include
description: Fichier Include
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.component: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: fb02496d9c107a2c21acca6c65ef69fdfceb4597
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2018
ms.locfileid: "48904637"
---
<a name="paths"></a>
## <a name="paths"></a>Chemins

<a name="anomalydetection-post"></a>
### <a name="detect-anomaly-points-for-the-time-series-data-points-requested"></a>Détecter les points d’anomalies pour les points de données de série chronologique demandés
```
POST /anomalydetection
```


#### <a name="parameters"></a>parameters

|type|NOM|Description|Schéma|
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



