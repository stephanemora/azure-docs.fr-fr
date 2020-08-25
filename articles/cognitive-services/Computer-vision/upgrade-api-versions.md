---
title: Effectuer une mise à niveau de l’API Vision par ordinateur vers v3.0
titleSuffix: Azure Cognitive Services
description: Découvrez comment effectuer une mise à niveau vers l’API de lecture Vision par ordinateur v3.0 à partir de la version v2.0/v2.1.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 08/11/2020
ms.author: pafarley
ROBOTS: NOINDEX
ms.openlocfilehash: 6e695fcfacac19ca82273d84d049bdb2afe14b54
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88214182"
---
# <a name="upgrade-to-computer-vision-v30-read-api-from-v20v21"></a>Mettre à niveau vers l’API de lecture Vision par ordinateur v3.0 à partir de v2.0/v2.1

Ce guide montre comment mettre à niveau votre code d’API REST Vision par ordinateur v2.0 ou v2.1 vers les opérations de lecture v3.0. 

## <a name="upgrade-batch-read-file-to-read"></a>Effectuer une mise à niveau de `Batch Read File` vers `Read`


1. Modifiez le chemin d’accès de l’API pour `Batch Read File` 2.x comme suit :


    |Read 2.x |Read 3.0  |
    |----------|-----------|
    |https://{endpoint}/vision/**v2.0/read/core/asyncBatchAnalyze**     |https://{endpoint}/vision/**v3.0/read/analyze**[?language]|
    
    Un nouveau paramètre facultatif de _langue_ est disponible. Si vous ne connaissez pas la langue de votre document ou s’il est multilingue, ne l’incluez pas. 

2. Modifiez le chemin d’accès de l’API pour `Get Read Results` dans 2.x comme suit :

    |Read 2.x |Read 3.0  |
    |----------|-----------|
    |https://{endpoint}/vision/**v2.0**/read/**operations**/{operationId}     |https://{endpoint}/vision/**v3.0**/read/**analyzeResults**/{operationId}|

3. Modifiez le code pour vérifier les résultats json depuis `Get Read Operation Result`. Lorsque l’appel de `Get Read Operation Result` aboutit, il renvoie un champ de chaîne d’état dans le corps du json. Les valeurs suivantes issues de la version v2.0 ont été modifiées pour mieux s’aligner aux autres API REST du service cognitif. 
 
    |Read 2.x |Read 3.0  |
    |----------|-----------|
    |`"NotStarted"` |   `"notStarted"`|
    |`"Running"` | `"running"`|
    |`"Failed"` | `"failed"`|
    |`"Succeeded"` | `"succeeded"`|
    
4. Modifiez votre code pour interpréter le JSON de résultat de la reconnaissance finale depuis `Get Read Operation Result`. 

    Notez les modifications suivantes apportées au json :
    
    - Dans la version v2.x, `"Get Read Operation Result"` renvoie le json de reconnaissance OCR lorsque l’état est `"Succeeded"`. Dans la version v3.0, ce champ est `"succeeded"`.
    - Pour obtenir la racine du tableau de pages, remplacez la hiérarchie json `"recognitionResults"` par `"analyzeResult"`/`"readResults"`. La hiérarchie json des mots et lignes par page reste inchangée et dès lors, aucune modification de code n’est requise.
    -   L’angle de page `"clockwiseOrientation"` a été renommé par `"angle"` et la plage a été modifiée de 0-360 degrés à -180 à 180 degrés. Selon votre code, vous pouvez ou non être tenu d’apporter des modifications car la plupart des fonctions mathématiques peuvent gérer l’une ou l’autre plage.
    -   L’API v3.0 présente également les améliorations suivantes : ajout de `"createdDateTime"` et `"lastUpdatedDateTime"` pour vous permettre de suivre la durée du traitement. Pour plus d’informations, consultez la documentation. 
        - `"version"` indique la version de l’API utilisée pour générer les résultats
        - Ajout de `"confidence"` par mot . Cette valeur est étalonnée afin qu’une valeur de 0,95 signifie qu’il existe 95 % de chances que la reconnaissance soit correcte. Le score de confiance peut être utilisé pour sélectionner le texte à envoyer à des fins de révision humaine. 
    
    
        Dans la version 2.X, le format de sortie est le suivant : 
    
    ```json
    {
        {
                "status": "Succeeded",
                "recognitionResults": [
                    {
                    "page": 1,
                    "language": "en",
                    "clockwiseOrientation": 349.59,
                    "width": 2661,
                    "height": 1901,
                    "unit": "pixel",
                    "lines": [
                        {
                        "boundingBox": [
                            67,
                            646,
                            2582,
                            713,
                            2580,
                            876,
                            67,
                            821
                        ],
                        "text": "The quick brown fox jumps",
                        "words": [
                            {
                            "boundingBox": [
                                143,
                                650,
                                435,
                                661,
                                436,
                                823,
                                144,
                                824
                            ],
                            "text": "The",
                            },
            // The rest of result is omitted for brevity 
            
    }
    ```
    
    Dans la version v3.0, il a été ajusté :
    
    ```json
    {
        {
            "status": "succeeded",
            "createdDateTime": "2020-05-28T05:13:21Z",
            "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
            "analyzeResult": {
            "version": "3.0.0",
            "readResults": [
                {
                "page": 1,
                "language": "en",
                "angle": 0.8551,
                "width": 2661,
                "height": 1901,
                "unit": "pixel",
                "lines": [
                    {
                    "boundingBox": [
                        67,
                        646,
                        2582,
                        713,
                        2580,
                        876,
                        67,
                        821
                    ],
                    "text": "The quick brown fox jumps",
                    "words": [
                        {
                        "boundingBox": [
                            143,
                            650,
                            435,
                            661,
                            436,
                            823,
                            144,
                            824
                        ],
                        "text": "The",
                        "confidence": 0.958
                        },
        // The rest of result is omitted for brevity 
        
    }
    ```

## <a name="upgrade-from-recognize-text-to-read"></a>Effectuer une mise à niveau de `Recognize Text` vers `Read`
`Recognize Text` est une opération de la *préversion* qui est *dépréciée dans toutes les versions de l’API Vision par ordinateur*. Vous devez migrer de `Recognize Text` vers `Read` (v3.0) ou `Batch Read File` (v2.0, v2.1). La version v3.0 de `Read`, qui comprend des modèles plus récents et plus performants pour la reconnaissance de texte et des fonctionnalités supplémentaires, est recommandée. Pour effectuer une mise à niveau de `Recognize Text` vers `Read` :

1. Modifiez le chemin d’accès de l’API pour `Recognize Text` v2.x comme suit :


    |Reconnaître le texte 2.x |Read 3.0  |
    |----------|-----------|
    |https://{endpoint}/vision/**v2.0/recognizeText[?mode]**|https://{endpoint}/vision/**v3.0/read/analyze**[?language]|
    
    Le paramètre _Mode_ n'est pas pris en charge dans `Read`. Les textes manuscrits et imprimés sont automatiquement pris en charge.
    
    Un nouveau paramètre facultatif _Langue_ est disponible dans la version v3.0. Si vous ne connaissez pas la langue de votre document ou s’il est multilingue, ne l’incluez pas. 

2. Modifiez le chemin d’accès de l’API pour `Get Recognize Text Operation Result` v2.x comme suit :

    |Reconnaître le texte 2.x |Read 3.0  |
    |----------|-----------|
    |https://{endpoint}/vision/**v2.0/textOperations/** {operationId}|https://{endpoint}/vision/**v3.0/read/analyzeResults**/{operationId}|

3. Modifiez le code pour vérifier les résultats json depuis `Get Recognize Text Operation Result`. Lorsque l’appel de `Get Read Operation Result` aboutit, il renvoie un champ de chaîne d’état dans le corps du json. 
 
    |Reconnaître le texte 2.x |Read 3.0  |
    |----------|-----------|
    |`"NotStarted"` |   `"notStarted"`|
    |`"Running"` | `"running"`|
    |`"Failed"` | `"failed"`|
    |`"Succeeded"` | `"succeeded"`|


4. Modifiez votre code pour interpréter le JSON de résultat de la reconnaissance finale depuis `Get Recognize Text Operation Result` pour prendre en charge `Get Read Operation Result`.

    Notez les modifications suivantes apportées au json :    

    - Dans la version v2.x, `"Get Read Operation Result"` renvoie le json de reconnaissance OCR lorsque l’état est `"Succeeded"`. Dans la version v3.0, ce champ est `"succeeded"`.
    - Pour obtenir la racine du tableau de pages, remplacez la hiérarchie json `"recognitionResult"` par `"analyzeResult"`/`"readResults"`. La hiérarchie json des mots et lignes par page reste inchangée et dès lors, aucune modification de code n’est requise.
    -   L’API v3.0 présente également les améliorations suivantes. Pour plus d’informations, consultez les informations de référence sur l’API : ajout de `"createdDateTime"` et `"lastUpdatedDateTime"` pour vous permettre de suivre la durée du traitement. Pour plus d’informations, consultez la documentation. 
        - `"version"` indique la version de l’API utilisée pour générer les résultats
        - Ajout de `"confidence"` par mot . Cette valeur est étalonnée afin qu’une valeur de 0,95 signifie qu’il existe 95 % de chances que la reconnaissance soit correcte. Le score de confiance peut être utilisé pour sélectionner le texte à envoyer à des fins de révision humaine. 
        - `"angle"` orientation générale du texte dans le sens des aiguilles d’une montre, mesurée en degrés entre (-180, 180].
        -  `"width"` et `"height"` vous donnent les dimensions de votre document, et `"unit"` fournit l’unité de ces dimensions (pixels ou pouces, en fonction du type de document).
        - `"page"` documents multipages pris en charge
        - `"language"` langue d’entrée du document (à partir du paramètre facultatif _Langue_).


    Dans la version 2.X, le format de sortie est le suivant : 
    
    ```json
    {
        {
                "status": "Succeeded",
                "recognitionResult": [
                    {
                    "lines": [
                        {
                        "boundingBox": [
                            67,
                            646,
                            2582,
                            713,
                            2580,
                            876,
                            67,
                            821
                        ],
                        "text": "The quick brown fox jumps",
                        "words": [
                            {
                            "boundingBox": [
                                143,
                                650,
                                435,
                                661,
                                436,
                                823,
                                144,
                                824
                            ],
                            "text": "The",
                            },
            // The rest of result is omitted for brevity 
            
    }
    ```
    
    Dans la version v3.0, il a été ajusté :
    
    ```json
    {
        {
            "status": "succeeded",
            "createdDateTime": "2020-05-28T05:13:21Z",
            "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
            "analyzeResult": {
            "version": "3.0.0",
            "readResults": [
                {
                "page": 1,
                "angle": 0.8551,
                "width": 2661,
                "height": 1901,
                "unit": "pixel",
                "lines": [
                    {
                    "boundingBox": [
                        67,
                        646,
                        2582,
                        713,
                        2580,
                        876,
                        67,
                        821
                    ],
                    "text": "The quick brown fox jumps",
                    "words": [
                        {
                        "boundingBox": [
                            143,
                            650,
                            435,
                            661,
                            436,
                            823,
                            144,
                            824
                        ],
                        "text": "The",
                        "confidence": 0.958
                        },
        // The rest of result is omitted for brevity 
        
    }
    ```
    
## <a name="all-other-operations"></a>Autres opérations :

Il n’existe aucun autre changement cassant entre les versions v2.X et v3.0 de API Vision par ordinateur. Vous pouvez simplement modifier le chemin d’accès de l’API pour remplacer `v2.0` par `v3.0`.
