---
title: Glossaire - QnA Maker
titleSuffix: Azure Cognitive Services
description: Le service QnA Maker présente de nombreux termes nouveaux issus du traitement de Machine Learning et du langage naturel ainsi que des termes propres au service. Cette liste vous aidera à comprendre ces termes.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 02/21/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: bb3b262f3bde0599cb6dea009d0fbbeafb1c529a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61430679"
---
# <a name="glossary-for-qna-maker-knowledge-base-and-service"></a>Glossaire de la base de connaissances et du service QnA Maker

## <a name="qna-maker-service"></a>Service d’établissement QnA
L’une des conditions préalables requises pour commencer à utiliser QnA Maker est de posséder un service QnA Maker. En achetant un niveau QnA Maker, vous définissez des ressources dans votre abonnement Azure pour créer et gérer votre base de connaissances. Chaque compte d’utilisateur QnA Maker permet de créer plusieurs services QnA Maker dans un abonnement Azure.

## <a name="knowledge-base"></a>Base de connaissances
Une base de connaissances est le référentiel de questions et réponses créées, conservées et utilisées par le biais de QnA Maker. Chaque couche QnA Maker peut servir à plusieurs bases de connaissances.

## <a name="endpoint"></a>Point de terminaison
Point de terminaison HTTP basé sur REST assurant la maintenance du contenu de votre base de connaissances et pouvant être intégré dans votre application, généralement un chatbot. 

## <a name="test-knowledge-base"></a>Base de connaissances de test
Une base de connaissances peut être de test ou publiée. La base de connaissances de test est la version en cours de modification, d’enregistrement et de test. Elle garantit la précision et l’exhaustivité des réponses. Les modifications apportées à la base de connaissances de test n’affectent pas l’utilisateur final de votre application ni du chatbot.

## <a name="published-knowledge-base"></a>Base de connaissances publiée
Une base de connaissances peut être de test ou publiée.  La base de connaissances publiée est la version utilisée dans votre application ou chatbot. En publiant une base de connaissances, vous placez le contenu de la base de connaissances de test dans la version publiée de celle-ci. Étant donné que la base de connaissances publiée est la version utilisée par l’application via le point de terminaison, soyez vigilant. Vous devez vous assurer que le contenu est correct et bien testé.

## <a name="query"></a>Interroger
Une requête de l’utilisateur est la question que l’utilisateur final ou le testeur pose dans la base de connaissances. Cette requête est souvent dans un format de langage naturel ou bien composée de quelques mots-clés qui représentent la question.

## <a name="response"></a>response
La réponse est la réponse telle que récupérée dans la base de connaissances. Il s’agit de la meilleure correspondance pour une requête utilisateur donnée.

## <a name="confidence-score"></a>Score de confiance
Le score de confiance de la réponse est une valeur numérique comprise entre 0 et 100. Une valeur de 100 équivaut à une correspondance exacte entre la requête de l’utilisateur et une question dans la base de connaissances. De plus, la réponse fournie à la requête de l’utilisateur est la réponse correcte et appropriée. Des réponses sont généralement classés par le score de confiance et celui dont le score de confiance plus élevé est pris en charge en tant que le [réponse par défaut](concepts/confidence-score.md#change-default-answer).
