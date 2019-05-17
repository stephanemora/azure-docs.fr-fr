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
ms.date: 02/20/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: cdfa5212f321cc6ec976ea9df301243acc54a23f
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65794856"
---
# <a name="qna-maker-api-http-response-codes"></a>Codes de réponse HTTP d’API QnA Maker
Les API de [gestion](https://go.microsoft.com/fwlink/?linkid=2092179) et prédiction retournent des codes de réponse HTTP. Bien que les messages de réponse contiennent des informations propres à une requête, le code d’état de réponse HTTP est général. 

### <a name="management"></a>gestion

|Code|Explication|
|:--|--|
|2xx|Succès|
|400|Les paramètres de la requête sont incorrects, ce qui signifie que les paramètres obligatoires sont manquants, incorrects ou trop grands|
|400|Le corps de la requête est incorrect, ce qui signifie que l’objet JSON est manquant, mal formé ou trop grand|
|401|Clé non valide|
|403|Interdit : vous n’avez pas les autorisations appropriées|
|404|La base de connaissances n’existe pas|
|410|Cette API est déconseillée et n’est plus disponible.|
