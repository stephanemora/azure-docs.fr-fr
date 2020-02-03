---
title: Conception pour la langue – QnA Maker
description: La ressource QnA Maker et toutes les bases de connaissances à l’intérieur de celle-ci prennent en charge une seule langue. L’unilinguisme est nécessaire pour produire des résultats de réponse optimaux à une requête.
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: 5cb1dcd35649debbafd2e234606ad4c9d6906ea6
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76843419"
---
# <a name="design-knowledge-base-for-content-language"></a>Concevoir une base de connaissances pour une langue de contenu

La ressource QnA Maker et toutes les bases de connaissances à l’intérieur de celle-ci prennent en charge une seule langue. L’unilinguisme est nécessaire pour produire des résultats de réponse optimaux à une requête.

## <a name="single-language-per-resource"></a>Une seule langue par ressource

Considérations relatives à QnA Maker pour la prise en charge linguistique :

* Un service QnA Maker et toutes ses bases de connaissances ne prennent en charge qu’une seule langue.
* La langue est définie explicitement lors de la création de la première base de connaissances du service.
* La langue est déterminée à partir des fichiers et URL ajoutés lors de la création de la base de connaissances.
* Il n’est pas possible de modifier la langue pour d’autres bases de connaissances du service.
* Les services Recherche cognitive (ranker #1) et QnA Maker (ranker #2) utilisent la langue pour générer la meilleure réponse à une requête.

## <a name="supporting-multiple-languages"></a>Prise en charge multilingue

Si vous avez besoin de prendre en charge un système de base de connaissances comprenant plusieurs langues, vous pouvez choisir l’une des méthodes suivantes :

* Utilisez le [service Texte de la traduction](../../translator/translator-info-overview.md) pour traduire une question dans une seule langue avant de l’envoyer à votre base de connaissances. Cela vous permet de vous concentrer sur la qualité d’une seule langue et la qualité des autres questions et réponses.
* Créez une ressource QnA Maker, ainsi qu’une base de connaissances à l’intérieur de cette ressource, pour chaque langue. Cela vous permet de gérer d’autres questions et du texte de réponse plus nuancé pour chaque langue. Vous bénéficiez ainsi d’une flexibilité sensiblement accrue, mais devez exposer des frais de maintenance bien plus élevés lorsque les questions ou réponses changent dans toutes les langues.

Voir [langues prises en charge](../overview/language-support.md) pour QnA Maker.

### <a name="support-each-language-with-a-qna-maker-resource"></a>Prendre en charge chaque langue avec une ressource QnA Maker

* Créez une ressource QnA Maker pour chaque langue.
* Ajoutez uniquement des fichiers et URL pour cette langue.
* Utilisez une convention d’affectation de noms pour la ressource afin d’identifier la langue. Un exemple de cela est `qna-maker-fr` pour toutes les bases de connaissances pour les documents français.

## <a name="next-steps"></a>Étapes suivantes

Découvrez les [concepts](query-knowledge-base.md) sur la façon d’interroger la base de connaissances pour obtenir une réponse.