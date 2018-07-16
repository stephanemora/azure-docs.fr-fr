---
title: Langues prises en charges - QnA Maker - Azure Cognitive Services | Microsoft Docs
description: Découvrez les langues prises en charge pour QnA Maker.
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: b210f59129a962046787b27d003c2872a54f6c8e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35370213"
---
# <a name="supported-languages"></a>Langues prises en charge

La langue de la base de connaissances influe sur la capacité de QnA Maker à extraire automatiquement les questions et les réponses des [sources](../Concepts/data-sources-supported.md), ainsi que sur la pertinence des résultats fournis par QnA Maker en réponse aux requêtes de l’utilisateur.

## <a name="auto-extraction"></a>Extraction automatique
QnA Maker prend en charge l’extraction de questions/réponses dans n’importe quelle page de langues, mais l’extraction est beaucoup plus efficace pour les langues suivantes, car QnA Maker utilise des mots clés pour identifier les questions.

|Langues prises en charge| Paramètres régionaux|
|-----|----|
|Français|en-*|
|Français|fr-*|
|Italien|it-*|
|Allemand|de-*|
|Espagnol|es-*|

## <a name="query-matching-and-relevance"></a>Correspondance et pertinence des requêtes
QnA Maker dépend des [analyseurs de langue](https://docs.microsoft.com/en-us/rest/api/searchservice/language-support) de la recherche Azure pour fournir des résultats. Des fonctionnalités spéciales générant un nouveau classement sont disponibles pour les langues en-* afin d’améliorer la pertinence.

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
|Français|
|Estonien|
|Finnois|
|Français|
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
