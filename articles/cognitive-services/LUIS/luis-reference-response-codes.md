---
title: Codes de réponse HTTP des API LUIS (Language Understanding) - Azure | Microsoft Docs
titleSuffix: Azure
description: Comprendre les codes de réponse HTTP retournés par les API de point de terminaison et de création LUIS
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: luis
ms.topic: article
ms.date: 04/16/2018
ms.author: diberry
ms.openlocfilehash: 07982716ff655f980844ca3dbcf7e8d7df1827c4
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47031218"
---
# <a name="luis-api-http-response-codes"></a>Codes de réponse HTTP des API LUIS
Les API de [création](https://aka.ms/luis-authoring-apis) et de [point de terminaison](https://aka.ms/luis-endpoint-apis) retournent des codes de réponse HTTP. Bien que les messages de réponse contiennent des informations propres à une requête, le code d’état de réponse HTTP est général. 

## <a name="common-status-codes"></a>Codes d’état courants
Le tableau suivant liste certains des codes d’état de réponse HTTP les plus courants pour les API de [création](https://aka.ms/luis-authoring-apis) et de [point de terminaison](https://aka.ms/luis-endpoint-apis) :

|Code|API|Explication|
|:--|--|--|
|400|Création, Point de terminaison|Les paramètres de la requête sont incorrects, ce qui signifie que les paramètres obligatoires sont manquants, incorrects ou trop grands|
|400|Création, Point de terminaison|Le corps de la requête est incorrect, ce qui signifie que l’objet JSON est manquant, mal formé ou trop grand|
|401|Création|Vous avez utilisé une clé d’abonnement de point de terminaison au lieu d’une clé de création|
|401|Création, Point de terminaison|La clé est vide, mal formée ou non valide|
|401|Création, Point de terminaison| La clé ne correspond pas à la région|
|401|Création|Vous n’êtes pas propriétaire ou collaborateur|
|401|Création|L’ordre des appels d’API n’est pas valide|
|403|Création, Point de terminaison|La limite de quota mensuel de clés est dépassée|
|409|Point de terminaison|L’application est encore en cours de chargement|
|410|Point de terminaison|L’application doit être ré-entraînée et republiée|
|414|Point de terminaison|La requête dépasse la limite maximale du nombre de caractères|
|429|Création, Point de terminaison|Le débit maximal est dépassé (requêtes/seconde)|