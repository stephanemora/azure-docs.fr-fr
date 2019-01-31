---
title: Codes de réponse HTTP d’API – QnA Maker
titleSuffix: Azure Cognitive Services
description: Élucidez les codes de réponse HTTP retournés par les API QnA Maker. Cela vous aidera à résoudre les erreurs.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/24/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 9eb5f1aefc8a36727e837af79f553108f624fc70
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55207962"
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
