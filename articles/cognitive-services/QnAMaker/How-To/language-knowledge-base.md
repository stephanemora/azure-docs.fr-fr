---
title: Concepts de langage - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker prend en charge le contenu de la base de connaissances dans de nombreuses langues. Toutefois, chaque service QnA Maker doit être réservé à une seule langue. La première base de connaissances créée ciblant un service QnA Maker particulier définit la langue de ce service.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: diberry
ms.openlocfilehash: 38701e8bbef1c5d78eca2242105e81fe7261c0f6
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2020
ms.locfileid: "84693088"
---
# <a name="language-support-of-knowledge-base-content-for-qna-maker"></a>Prise en charge linguistique du contenu de la base de connaissances pour QnA Maker

La langue du service est sélectionnée lorsque vous créez la première base de connaissances dans la ressource. Toutes les bases de connaissances supplémentaires de la ressource doivent être dans la même langue.

La langue détermine la pertinence des résultats que QnA Maker fournit en réponse aux requêtes de l’utilisateur.

## <a name="one-language-for-all-knowledge-bases-in-resource"></a>Une langue pour toutes les bases de connaissances de la ressource

QnA Maker vous permet de sélectionner la langue de votre service QnA, lors de la création de la première base de connaissances. Toutes les bases de connaissances d’une ressource QnA Maker doivent être dans la même langue. Cette langue ne peut pas être modifiée.

La création de bases de connaissances dans différentes langues dans une ressource affecte négativement la pertinence des résultats que QnA Maker fournit en réponse aux requêtes de l’utilisateur.

Passez en revue la liste des [langues prises en charge](../overview/language-support.md#languages-supported) et la façon dont les langues affectent [la correspondance et la pertinence](#query-matching-and-relevance).

## <a name="select-language-when-creating-first-knowledge-base"></a>Sélectionner la langue lors de la création de la première base de connaissances

La sélection de la langue fait partie des étapes de création de la première base de connaissances dans une ressource.

![Capture d’écran de la sélection de la langue pour la première base de connaissances dans le portail QnA Maker](../media/language-support/select-language-when-creating-knowledge-base.png)

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

## <a name="verify-language"></a>Vérifier la langue

Vous pouvez vérifier la langue de votre ressource QnA Maker à partir de la page Paramètres du service dans QnA Maker.

![Capture d’écran de la page Paramètres du service du portail QnA Maker](../media/language-support/language-knowledge-base.png)


## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Migrer une base de connaissances](../Tutorials/migrate-knowledge-base.md)
