---
title: Limites et frontières - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker présente des limites de métadonnées pour certaines parties de la base de connaissances et du service. Il est important de maintenir votre base de connaissances à l’intérieur de ces limites pour les tests et la publication.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 12/10/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: ddf2cb5730f123038c5dbde7ab07b4022f021ced
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75381118"
---
# <a name="qna-maker-knowledge-base-limits-and-boundaries"></a>Limites de la base de connaissances QnA Maker

Les limites QnA Maker fournies ci-dessous combinent les [limites du niveau tarifaire de Recherche cognitive Azure](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity) et les [limites du niveau tarifaire de QnA Maker](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/). Vous devez connaître les deux ensembles de limites pour connaître le nombre de bases de connaissances que vous pouvez créer par ressource et la taille de chaque base de connaissances.

## <a name="knowledge-bases"></a>Bases de connaissances

Le nombre maximal de bases de connaissances est basé sur les [limites de niveau de Recherche cognitive Azure](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity).

|**Niveau de Recherche cognitive Azure** | **Gratuit** | **De base** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Nombre maximal de bases de connaissances publiées autorisées|2|14|49|199|199|2 999|

 Par exemple, si votre niveau comporte 15 index autorisés, vous pouvez publier 14 bases de connaissances (1 index par base de connaissances publiée). Le quinzième index, `testkb`, est utilisé pour toutes les bases de connaissances à des fins de création et de test.

## <a name="extraction-limits"></a>Limites d’extraction

### <a name="maximum-file-size"></a>Taille maximale du fichier

|Format|Taille maximale des fichiers (Mo)|
|--|--|
|`.docx`|10|
|`.pdf`|25|
|`.tsv`|10|
|`.txt`|10|
|`.xlsx`|3|

### <a name="maximum-number-of-files"></a>Nombre maximal de fichiers

Le nombre maximal de fichiers qui peuvent être extraits et la taille de fichier maximale sont basés sur vos **[limites du niveau de tarification QnA Maker](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)** .

### <a name="maximum-number-of-deep-links-from-url"></a>Nombre maximal de liens ciblés à partir de l’URL

Le nombre maximal de liens ciblés pouvant être analysés pour l’extraction de questions/réponses à partir d’une page URL est de **20**.

## <a name="metadata-limits"></a>Limites de métadonnées

### <a name="by-azure-cognitive-search-pricing-tier"></a>Par niveau tarifaire de Recherche cognitive Azure

Le nombre maximal de champs de métadonnées par base de connaissances est basé sur les **[limites de niveau de Recherche cognitive Azure](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)** .

|**Niveau de Recherche cognitive Azure** | **Gratuit** | **De base** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Nombre maximal de champs de métadonnées par service QnA Maker (pour tous les Kbits/s)|1 000|100*|1 000|1 000|1 000|1 000|

### <a name="by-name-and-value"></a>Par nom et valeur

La longueur et les caractères acceptables pour le nom et la valeur des métadonnées sont listés dans le tableau suivant.

|Élément|Caractères autorisés|Modèle d’expression régulière|Caractères max.|
|--|--|--|--|
|Name|Sont autorisés<br>les caractères alphanumériques (lettres et chiffres) et<br>`_` (trait de soulignement)|`^[a-zA-Z0-9_]+$`|100|
|Valeur|Tous sauf<br>`:` (deux points)<br>`|` (barre verticale)|`^[^:|]+$`|500|
|||||

## <a name="knowledge-base-content-limits"></a>Limites de contenu de la base de connaissances
Limites globales sur le contenu de la base de connaissances :
* Longueur du texte de réponse : 25 000
* Longueur du texte de question : 1 000
* Longueur du texte de valeur/clé des métadonnées : 100
* Caractères pris en charge pour le nom des métadonnées : lettres, chiffres et `_`
* Caractères pris en charge pour la valeur des métadonnées : Tous sauf `:` et `|`
* Longueur de nom de fichier : 200
* Formats de fichier pris en charge : « .tsv », « .pdf », « .txt », « .docx », « .xlsx ».
* Nombre maximal de questions alternatives : 300
* Nombre maximal de paires question-réponse : Varie en fonction du **[niveau de Recherche cognitive Azure](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#document-limits)** choisi. Une paire question-réponse est mappée à un document dans l'index Recherche cognitive Azure.
* Page HTML/URL : 1 million de caractères

## <a name="create-knowledge-base-call-limits"></a>Créer des limites d’appel de base de connaissances :
Elles représentent les limites pour chaque action de création d’une base de connaissances, autrement dit, pour chaque clic sur *Créer une base de connaissances* ou pour chaque appel de l’API CreateKnowledgeBase.
* Nombre maximal de questions alternatives par réponse : 300
* Nombre maximal d’URL : 10
* Nombre maximal de fichiers : 10

## <a name="update-knowledge-base-call-limits"></a>Limites d’appel de mise à jour de la base de connaissances
Elles représentent les limites pour chaque action de mise à jour, autrement dit, pour chaque clic sur *Save and train* (Enregistrer et former) ou pour chaque appel de l’API UpdateKnowledgeBase.
* Longueur de chaque nom de source : 300
* Nombre maximal de questions alternatives ajoutées ou supprimées : 300
* Nombre maximal de champs de métadonnées ajoutés ou supprimés : 10
* Nombre maximal d’URL pouvant être actualisées : 5

## <a name="next-steps"></a>Étapes suivantes

Découvrez quand et comment changer de [niveau tarifaire](How-To/set-up-qnamaker-service-azure.md#upgrade-qna-maker).
