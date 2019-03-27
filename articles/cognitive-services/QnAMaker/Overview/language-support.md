---
title: Prise en charge linguistique - QnA Maker
titleSuffix: Azure Cognitive Services
description: La langue de la base de connaissances influe sur la capacité de QnA Maker à extraire automatiquement les questions et les réponses des sources, ainsi que sur la pertinence des résultats fournis par QnA Maker en réponse aux requêtes de l’utilisateur. Liste de cultures et de langues naturelles prises en charge par QnA Maker pour votre base de connaissances. Ne mélangez pas différentes langues dans une même base de connaissances.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/21/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: a6304a93b1409cff871ed1c4c1d7e66d6c8c6f53
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58497684"
---
# <a name="language-support-for-qna-maker"></a>Prise en charge linguistique pour QnA Maker

La langue de la base de connaissances influe sur la capacité de QnA Maker à extraire automatiquement les questions et les réponses des [sources](../Concepts/data-sources-supported.md), ainsi que sur la pertinence des résultats fournis par QnA Maker en réponse aux requêtes de l’utilisateur.

## <a name="auto-extraction"></a>Extraction automatique
QnA Maker prend en charge l’extraction de questions/réponses dans n’importe quelle page de langues, mais l’extraction est beaucoup plus efficace pour les langues suivantes, car QnA Maker utilise des mots clés pour identifier les questions.

|Langues prises en charge| Paramètres régionaux|
|-----|----|
|Anglais|en-*|
|Anglais|fr-*|
|Italien|it-*|
|Allemand|de-*|
|Espagnol|es-*|

## <a name="primary-language-detection"></a>Détection de la langue principale

La langue principale utilisée pour la détection est définie pour la ressource de QnA Maker et toutes les bases de connaissances créés sur cette ressource, lorsque le premier document ou l’URL est ajouté à la première base de connaissances. La langue ne peut pas être modifiée. 

Si l’utilisateur prévoit de prendre en charge plusieurs langues, dont ils ont besoin d’avoir une nouvelle ressource de QnA Maker pour chaque langue. Découvrez comment [créer une base de connaissances en langage QnA Maker](../how-to/language-knowledge-base.md).  

Vérifiez que la langue principale en procédant comme suit :

1. Connectez-vous au [Portail Azure](http://portal.azure.com).  
1. Recherchez et sélectionnez la ressource de recherche Azure créée dans le cadre de votre ressource QnA Maker. Le nom de ressource de recherche Azure commence avec le même nom que la ressource de QnA Maker et auront le type **service de recherche**. 
1. À partir de la **vue d’ensemble** page de la ressource de recherche, sélectionnez **index**. 
1. Sélectionnez l’index **testkb**.
1. Sélectionnez le **champs** onglet. 
1. Afficher le **analyseur** colonne pour le **questions** et **réponse** champs. 


## <a name="query-matching-and-relevance"></a>Correspondance et pertinence des requêtes
QnA Maker dépend des [analyseurs de langue](https://docs.microsoft.com/rest/api/searchservice/language-support) de la recherche Azure pour fournir des résultats. Des fonctionnalités spéciales générant un nouveau classement sont disponibles pour les langues en-* afin d’améliorer la pertinence.

Même si les fonctionnalités de Recherche Azure sont à égalité pour les langues prises en charge, QnA Maker possède un outil de classement supplémentaire qui se trouve au-dessus des résultats de recherche Azure. Dans ce modèle thématiques, nous utilisons certaines fonctionnalités de word et sémantiques spéciales dans fr-*, qui ne sont pas encore disponibles pour d’autres langages. Nous ne pas rendre ces fonctionnalités disponibles, qu’elles font partie du fonctionnement interne de thématiques de QnA Maker. 

QnA Maker [détecte automatiquement la langue de la base de connaissances](#primary-language-detection) lors de la création et définit l’analyseur en conséquence. Vous pouvez créer des bases de connaissances dans les langues suivantes. 

|Langues prises en charge|
|-----|
|Arabe|
|Arménien|
Bengali|
|Basque|
|Bulgare|
|Catalan|
|Chinois simplifié|
|Chinois traditionnel|
|Croate|
|Tchèque|
|Danois|
|Néerlandais|
|Anglais|
|Estonien|
|Finnois|
|Anglais|
|Galicien|
|Allemand|
|Grec|
|Goudjrati|
|Hébreu|
|Hindi|
|Hongrois|
|Islandais|
|Indonésien|
|Irlandais|
|Italien|
|Japonais|
|Kannada|
|Coréen|
|Letton|
|Lituanien|
|Malayalam|
|Malais|
|Norvégien|
|Polonais|
|Portugais|
|Pendjabi|
|Roumain|
|Russe|
|Serbe (cyrillique)|
|Serbe (latin)|
|Slovaque|
|Slovène|
|Espagnol|
|Suédois|
|Tamoul|
|Télougou|
|Thaï|
|Turc|
|Ukrainien|
|Ourdou|
|Vietnamien|
