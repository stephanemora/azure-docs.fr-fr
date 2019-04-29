---
title: Limites et frontières - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker présente des limites de métadonnées pour certaines parties de la base de connaissances et du service. Il est important de maintenir votre base de connaissances à l’intérieur de ces limites pour les tests et la publication.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 02/26/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: fe15cafceea8128735f7241fa5e4187d4d9c47a9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60518986"
---
# <a name="qna-maker-knowledge-base-limits-and-boundaries"></a>Limites de la base de connaissances QnA Maker
Liste complète des limites dans QnA Maker.

## <a name="knowledge-bases"></a>Bases de connaissances

* Nombre maximal de bases de connaissances selon les [limites de niveau de la Recherche Azure](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)

|**Niveau de la Recherche Azure** | **Gratuit** | **De base** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Nombre maximal de bases de connaissances publiées autorisées|2|14|49|199|199|2 999|

 Par exemple, si votre niveau comporte 15 index autorisés, vous pouvez publier 14 bases de connaissances (1 index par base de connaissances publiée). Le quinzième index, `testkb`, est utilisé pour toutes les bases de connaissances à des fins de création et de test. 

## <a name="extraction-limits"></a>Limites d’extraction
* Nombre maximal de fichiers pouvant être extraits et taille de fichier maximale : consultez [Tarifs de QnAMaker](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)
* Nombre maximal de liens ciblés pouvant être analysés pour l’extraction de questions/réponses de pages HTML de FAQ : 20

## <a name="metadata-limits"></a>Limites de métadonnées
* Nombre maximal de champs de métadonnées par base de connaissances selon les [limites de niveau de la Recherche Azure](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)

|**Niveau de la Recherche Azure** | **Gratuit** | **De base** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Nombre maximal de champs de métadonnées par service QnA Maker (pour tous les Kbits/s)|1 000|100*|1 000|1 000|1 000|1 000|

## <a name="knowledge-base-content-limits"></a>Limites de contenu de la base de connaissances
Limites globales sur le contenu de la base de connaissances :
* Longueur du texte de réponse : 25 000
* Longueur du texte de question : 1 000
* Longueur du texte de valeur/clé des métadonnées : 100
* Caractères pris en charge pour le nom des métadonnées : lettres, chiffres et _  
* Caractères pris en charge pour la valeur des métadonnées : tout sauf : et | 
* Longueur de nom de fichier : 200
* Formats de fichier pris en charge : « .tsv », « .pdf », « .txt », « .docx », « .xlsx ».
* Nombre maximal de questions alternatives : 100
* Nombre maximal de paires question-réponse : Varie selon le [niveau recherche Azure](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#document-limits) choisi. Une paire de questions et réponses est mappé à un document sur les index de recherche Azure. 

## <a name="create-knowledge-base-call-limits"></a>Créer des limites d’appel de base de connaissances :
Elles représentent les limites pour chaque action de création d’une base de connaissances, autrement dit, pour chaque clic sur *Créer une base de connaissances* ou pour chaque appel de l’API CreateKnowledgeBase.
* Nombre maximal de questions alternatives par réponse : 100
* Nombre maximal d’URL : 10
* Nombre maximal de fichiers : 10

## <a name="update-knowledge-base-call-limits"></a>Limites d’appel de mise à jour de la base de connaissances
Elles représentent les limites pour chaque action de mise à jour, autrement dit, pour chaque clic sur *Save and train* (Enregistrer et former) ou pour chaque appel de l’API UpdateKnowledgeBase.
* Longueur de chaque nom de source : 300
* Nombre maximal de questions alternatives ajoutées ou supprimées : 100
* Nombre maximal de champs de métadonnées ajoutés ou supprimés : 10
* Nombre maximal d’URL pouvant être actualisées : 5.

## <a name="next-steps"></a>Étapes suivantes

Apprenez quand et comment changer les niveaux de service :

* [QnA Maker](how-to/upgrade-qnamaker-service.md#upgrade-qna-maker-management-sku): Lorsque vous devez disposer de plusieurs fichiers sources ou plus de documents dans votre base de connaissances, au-delà de votre niveau actuel, mettez à niveau votre service QnA Maker niveau tarifaire.
* [App Service](how-to/upgrade-qnamaker-service.md#upgrade-app-service): Lorsque votre base de connaissances doit répondre à un plus grand nombre de demandes de votre application client, mettez à niveau le niveau tarifaire de vos services applicatifs.
* [Recherche Azure](how-to/upgrade-qnamaker-service.md#upgrade-azure-search-service): Lorsque vous prévoyez de disposer de plusieurs bases de connaissances, mettez à niveau votre niveau tarifaire du service Recherche Azure.
