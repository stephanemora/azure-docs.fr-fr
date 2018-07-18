---
title: Glossaire - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Glossaire
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: e28cddec005cb6ba99b9f60d8b03a11f1bc97062
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35369660"
---
# <a name="glossary"></a>Glossaire

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

## <a name="query"></a>Requête
Une requête de l’utilisateur est la question que l’utilisateur final ou le testeur pose dans la base de connaissances. Cette requête est souvent dans un format de langage naturel ou bien composée de quelques mots-clés qui représentent la question.

## <a name="response"></a>response
La réponse est la réponse telle que récupérée dans la base de connaissances. Il s’agit de la meilleure correspondance pour une requête utilisateur donnée.

## <a name="confidence-score"></a>Score de confiance
Le score de confiance de la réponse est une valeur numérique comprise entre 0 et 100. Une valeur de 100 équivaut à une correspondance exacte entre la requête de l’utilisateur et une question dans la base de connaissances. De plus, la réponse fournie à la requête de l’utilisateur est la réponse correcte et appropriée. Les réponses sont généralement classées selon le score de confiance et celle dont le score de confiance est supérieur est considérée comme la réponse par défaut.
