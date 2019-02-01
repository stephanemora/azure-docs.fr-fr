---
title: Base de connaissances - QnA Maker
titleSuffix: Azure Cognitive Services
description: Une base de connaissances QnA Maker est composée d’un ensemble de paires de questions/réponses (QnA) et de métadonnées facultatives associées à chaque paire QnA.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 12/18/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: d036b93bc043f74dedae24455ca0a43cd808a936
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55227112"
---
# <a name="what-is-a-qna-maker-knowledge-base"></a>Qu’est-ce qu’une base de connaissances QnA Maker ?

Une base de connaissances QnA Maker est composée d’un ensemble de paires de questions/réponses (QnA) et de métadonnées facultatives associées à chaque paire QnA.

## <a name="key-knowledge-base-concepts"></a>Principaux concepts liés aux bases de connaissances

* **Questions** : une question contient du texte qui représente une requête utilisateur. 
* **Réponses** : une réponse est la réponse retournée quand une requête de l’utilisateur est mise en correspondance avec la question associée.  
* **Métadonnées** : les métadonnées sont des étiquettes associées à une paire QnA. Elles sont représentées sous forme de paires clé-valeur. Les balises de métadonnées servent à filtrer les paires QnA et à limiter le jeu sur lequel la mise en correspondance de requête est effectuée.

Une seule entité QnA, représentée par un ID numérique de QnA, a plusieurs variantes d’une question (autres questions), toutes mappées à une réponse unique. De plus, chaque paire de ce type peut avoir plusieurs champs de métadonnées associés.

![ID de base de connaissances QnA Maker](../media/qnamaker-concepts-knowledgebase/knowledgebase.png) 

## <a name="knowledge-base-content-format"></a>Format du contenu de la base de connaissances

Quand vous ingérez du contenu riche dans une base de connaissances, QnA Maker tente de le convertir en Markdown. Pour comprendre les formats Markdown compréhensibles par la plupart des clients de conversation, consultez [ce](https://aka.ms/qnamaker-docs-markdown-support) blog.

Les champs de métadonnées sont composés de paires clé-valeur séparées par un signe deux-points **(Product:Shredder)**. La clé et la valeur doivent être uniquement du texte. La clé de métadonnées ne doit pas contenir d’espace.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Cycle de vie du développement d’une base de connaissances](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>Voir aussi

[Vue d’ensemble de QnA Maker](../Overview/overview.md)
