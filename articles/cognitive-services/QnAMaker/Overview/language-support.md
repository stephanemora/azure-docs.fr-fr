---
title: Prise en charge linguistique - QnA Maker
titleSuffix: Azure Cognitive Services
description: Liste de cultures et de langues naturelles prises en charge par QnA Maker pour votre base de connaissances. Ne mélangez pas différentes langues dans une même base de connaissances.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 02/04/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 5a4938ec37cf006e5cd6342687e2c50cadd5272e
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2019
ms.locfileid: "55731068"
---
# <a name="language-and-region-support-for-qna-maker"></a>Prise en charge de la région et de la langue pour QnA Maker

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

## <a name="query-matching-and-relevance"></a>Correspondance et pertinence des requêtes
QnA Maker dépend des [analyseurs de langue](https://docs.microsoft.com/rest/api/searchservice/language-support) de la recherche Azure pour fournir des résultats. Des fonctionnalités spéciales générant un nouveau classement sont disponibles pour les langues en-* afin d’améliorer la pertinence.

Même si les fonctionnalités de Recherche Azure sont à égalité pour les langues prises en charge, QnA Maker possède un outil de classement supplémentaire qui se trouve au-dessus des résultats de recherche Azure. Dans ce modèle d’outil de classement, nous utilisons une sémantique spéciale et des fonctionnalités basées sur des mots en en-*, qui ne sont pas encore disponibles pour d’autres langues. Nous ne les mettons pas à disposition, car elles font partie du fonctionnement interne de l’outil de classement. 

QnA Maker détecte automatiquement la langue de la base de connaissances lors de la création et définit l’analyseur en conséquence. Vous pouvez créer des bases de connaissances dans les langues suivantes. Lisez [cet article](../How-To/language-knowledge-base.md) pour plus d’informations sur la façon dont QnA Maker gère les langues.


> [!Tip]
> Une fois configurés, les analyseurs de langue ne peuvent pas être modifiés. En outre, l’analyseur de langue s’applique à toutes les bases de connaissances dans un [service de QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Si vous prévoyez d’avoir des bases de connaissances dans une autre langue, vous devez les créer sous des services QnA Maker distincts.

|Langues prises en charge|
|-----|
|Arabe|
|Arménien|.
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
