---
title: Prise en charge linguistique - QnA Maker
titleSuffix: Azure Cognitive Services
description: Liste de cultures et de langues naturelles prises en charge par QnA Maker pour votre base de connaissances. Ne mélangez pas différentes langues dans une même base de connaissances.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 09/24/2019
ms.openlocfilehash: c990b6980dea871679b0b301e293e4fb94748db7
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2020
ms.locfileid: "89650902"
---
# <a name="language-support-for-a-qna-maker-resource-and-knowledge-bases"></a>Prise en charge linguistique pour une ressource QnA Maker et bases de connaissances

La langue du service est sélectionnée lorsque vous créez la première base de connaissances dans la ressource. Toutes les bases de connaissances supplémentaires de la ressource doivent être dans la même langue. 

La langue détermine la pertinence des résultats que QnA Maker fournit en réponse aux requêtes de l’utilisateur. La ressource QnA Maker et toutes les bases de connaissances à l’intérieur de celle-ci prennent en charge une seule langue. L’unilinguisme est nécessaire pour produire des résultats de réponse optimaux à une requête.

## <a name="single-language-per-resource"></a>Une seule langue par ressource

Tenez compte des éléments suivants :

* Un service QnA Maker et toutes ses bases de connaissances ne prennent en charge qu’une seule langue.
* La langue est définie explicitement lors de la création de la première base de connaissances du service.
* La langue est déterminée à partir des fichiers et URL ajoutés lors de la création de la base de connaissances.
* Il n’est pas possible de modifier la langue pour d’autres bases de connaissances du service.
* Les services Recherche cognitive (ranker #1) et QnA Maker (ranker #2) utilisent la langue pour générer la meilleure réponse à une requête.

## <a name="supporting-multiple-languages"></a>Prise en charge multilingue

Pour prendre en charge un système de base de connaissances comprenant plusieurs langues, vous pouvez :

* Utiliser le [service Translator](../../translator/translator-info-overview.md) pour traduire une question dans une langue individuelle avant de l’envoyer à votre base de connaissances. Cela vous permet de vous concentrer sur la qualité d’une seule langue et la qualité des autres questions et réponses.
* Créez une ressource QnA Maker, ainsi qu’une base de connaissances à l’intérieur de cette ressource, pour chaque langue. Cela vous permet de gérer d’autres questions et du texte de réponse plus nuancé pour chaque langue. Vous bénéficiez ainsi d’une flexibilité sensiblement accrue, mais devez exposer des frais de maintenance bien plus élevés lorsque les questions ou réponses changent dans toutes les langues.

Voir [langues prises en charge](../overview/language-support.md) pour QnA Maker.

### <a name="support-each-language-with-a-qna-maker-resource"></a>Prendre en charge chaque langue avec une ressource QnA Maker

* Créez une ressource QnA Maker pour chaque langue.
* Ajoutez uniquement des fichiers et URL pour cette langue.
* Utilisez une convention d’affectation de noms pour la ressource afin d’identifier la langue. Un exemple de cela est `qna-maker-fr` pour toutes les bases de connaissances pour les documents français.


## <a name="languages-supported"></a>Langues prises en charge

La liste suivante contient les langues prises en charge pour une ressource QnA Maker. 

|Langage|
|--|
|Arabe|
|Arménien|
|Bangla|
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

## <a name="query-matching-and-relevance"></a>Correspondance et pertinence des requêtes
QnA Maker dépend des [analyseurs de langue Recherche cognitive Azure](https://docs.microsoft.com/rest/api/searchservice/language-support) pour fournir des résultats.

Même si les fonctionnalités de Recherche cognitive Azure sont à égalité pour les langues prises en charge, QnA Maker possède un outil de classement supplémentaire qui se trouve au-dessus des résultats de recherche Azure. Dans ce modèle d’outil de classement, nous utilisons une sémantique spéciale et des fonctionnalités basées sur des mots dans les langues suivantes.

|Langues avec outil de classement supplémentaire|
|--|
|Chinois|
|Tchèque|
|Néerlandais|
|Anglais|
|Français|
|Allemand|
|Hongrois|
|Italien|
|Japonais|
|Coréen|
|Polonais|
|Portugais|
|Espagnol|
|Suédois|

Ce classement supplémentaire est un travail interne de l’outil de classement de QnA Maker.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Sélection de la langue](../how-to/language-knowledge-base.md)
