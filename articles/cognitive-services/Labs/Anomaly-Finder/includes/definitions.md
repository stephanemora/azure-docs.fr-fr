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
ms.openlocfilehash: a49df0c18ef9db9d0d41ca2e714474e6386ae7d1
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2018
ms.locfileid: "48904654"
---
<a name="definitions"></a>
## <a name="definitions"></a>Définitions

<a name="point"></a>
### <a name="point"></a>Point

|NOM|Description|Schéma|
|---|---|---|
|**Timestamp**  <br>*facultatif*|Horodatage du point de données. Assurez-vous qu’il est aligné sur minuit, et utilisez une chaîne de date et d’heure UTC, comme 2017-08-01T00:00:00Z.|chaîne (date-heure)|
|**Valeur**  <br>*facultatif*|Valeur de mesure de données.|nombre (double)|


<a name="request"></a>
### <a name="request"></a>Requête

|NOM|Description|Schéma|
|---|---|---|
|**Période**  <br>*facultatif*|Période des points de données. Si la valeur est nulle ou absente, l’API détermine automatiquement la période.|nombre (double)|
|**Points**  <br>*facultatif*|Points de données de séries chronologiques. Les données doivent être triées par date dans l’ordre croissant afin de mettre en correspondance le résultat de l’anomalie. Si les données ne sont pas triées correctement ou s’il existe un horodatage en double, l’API détecte correctement les points d’anomalie, mais ne pourrez pas faire correspondre correctement les points retournés et le résultat. Dans ce cas, un message d’avertissement est ajouté dans la réponse.|< [point](#point) > tableau|


<a name="response"></a>
### <a name="response"></a>response

|NOM|Description|Schéma|
|---|---|---|
|**ExpectedValues**  <br>*facultatif*|Valeur prédite par le modèle basé sur l’apprentissage. Si les points de données de saisie sont triés par date dans l’ordre croissant, l’index du tableau peut être utilisé pour mapper la valeur attendue et la valeur d’origine.|< nombre (double) > tableau|
|**IsAnomaly**  <br>*facultatif*|Résultat indiquant si les points de données sont des anomalies ou non dans les deux directions (pics ou creux). true signifie que le point est une anomalie, false signifie que le point n’est pas une anomalie. Si les points de données de saisie sont triés par date dans l’ordre croissant, l’index du tableau peut être utilisé pour mapper la valeur attendue et la valeur d’origine.|< booléen > tableau|
|**IsAnomaly_Neg**  <br>*facultatif*|Résultat indiquant si les points de données sont des anomalies dans une direction négative (creux). true signifie que la direction de l’anomalie est négative. Si les points de données de saisie sont triés par date dans l’ordre croissant, l’index du tableau peut être utilisé pour mapper la valeur attendue et la valeur d’origine.|< booléen > tableau|
|**IsAnomaly_Pos**  <br>*facultatif*|Résultat indiquant si les points de données sont des anomalies dans une direction positive (pics). true signifie que la direction de l’anomalie est positive. Si les points de données de saisie sont triés par date dans l’ordre croissant, l’index du tableau peut être utilisé pour mapper la valeur attendue et la valeur d’origine.|< booléen > tableau|
|**LowerMargin**  <br>*facultatif*|(ExpectedValue - LowerMargin) détermine la limite inférieure où le point de données est encore considéré comme normal. Si les points de données de saisie sont triés par date dans l’ordre croissant, l’index du tableau peut être utilisé pour mapper la valeur attendue et la valeur d’origine.|< nombre (double) > tableau|
|**Période**  <br>*facultatif*|Période utilisée par l’API pour détecter les points présentant des anomalies.|nombre (flottant)|
|**UpperMargin**  <br>*facultatif*|Somme d’ExpectedValue et d’UpperMargin déterminant la limite supérieure où le point de données est encore considéré comme normal. Si les points de données de saisie sont triés par date dans l’ordre croissant, l’index du tableau peut être utilisé pour mapper la valeur attendue et la valeur d’origine.|< nombre (double) > tableau|
|**WarningText**  <br>*facultatif*|Si les points de données de saisie fournis ne suivent pas la règle requise par l’API et que les données peuvent toujours être détectées par l’API, l’API analyse les données et ajoute les informations d’avertissement dans ce champ.|chaîne|



