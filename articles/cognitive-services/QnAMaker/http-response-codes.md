---
title: Codes de réponse HTTP d’API – QnA Maker
titleSuffix: Azure Cognitive Services
description: Élucidez les codes de réponse HTTP retournés par les API QnA Maker. Cela vous aidera à résoudre les erreurs.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/24/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: d5b97f9c3b29732ba901f783ac04a3ada338ab69
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55866438"
---
# <a name="qna-maker-api-http-response-codes"></a>Codes de réponse HTTP d’API QnA Maker
Les API de [gestion](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff) et prédiction retournent des codes de réponse HTTP. Bien que les messages de réponse contiennent des informations propres à une requête, le code d’état de réponse HTTP est général. 

### <a name="management"></a>gestion

|Code|Explication|
|:--|--|
|2xx|Succès|
|400|Les paramètres de la requête sont incorrects, ce qui signifie que les paramètres obligatoires sont manquants, incorrects ou trop grands|
|400|Le corps de la requête est incorrect, ce qui signifie que l’objet JSON est manquant, mal formé ou trop grand|
|401|Clé non valide|
|403|Interdit : vous n’avez pas les autorisations appropriées|
|404|La base de connaissances n’existe pas|
