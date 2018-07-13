---
title: Documentation de référence pour les entités prédéfinies de LUIS - Azure | Microsoft Docs
titleSuffix: Azure
description: Cet article contient des listes des entités prédéfinies qui sont incluses dans Language Understanding (LUIS).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: v-geberr
ms.openlocfilehash: 7ce50e4c0be605e1700a2c18533cb087384f524c
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/22/2018
ms.locfileid: "36316882"
---
# <a name="entities-per-culture"></a>Entités par culture

Language Understanding (LUIS) fournit des entités prédéfinies. Quand une entité prédéfinie est incluse dans votre application, LUIS inclut la prédiction d’entité correspondante dans la réponse du point de terminaison. Tous les exemples d’énoncés sont également étiquetés avec l’entité. Le comportement des entités prédéfinies **ne peut pas** être modifié. Sauf indication contraire, les entités prédéfinies sont disponibles dans tous les paramètres régionaux de l’application LUIS (cultures). Le tableau suivant montre les entités prédéfinies qui sont pris en charge pour chaque culture.

Entité prédéfinie   |   Anglais (États-Unis)<br>```En-us```   |   Français (France)<br>```fr-FR```   |   Italien (Italie)<br>```it-IT```   |   Espagnol (Espagne)<br>```es-ES```   |   Chinois<br>```zh-CN```   |   Allemand<br>```de-DE```   |   Portugais (Brésil)<br>```pt-BR```   |   Japonais (Japon)<br>```ja-JP```   |   Coréen (Corée)<br>```ko-kr```   | Français (Canada)<br>```fr-CA```   |   Espagnol (Mexique)<br>```es-MX```   |   Néerlandais (Pays-Bas)<br>```nl-NL```   |
------|:------:|------|------|------|------|------|------|------|------|------|------|------|
[Age](luis-reference-prebuilt-age.md) :<br>year<br>month<br>week<br>day   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   -   |
[Devise](luis-reference-prebuilt-currency.md) :<br>dollar<br>Unité fractionnelle (par exemple, centime)  |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   -   |
[Datetimev2](luis-reference-prebuilt-datetimev2.md) :<br>date<br>daterange<br>time<br>plage horaire   |    ✔   |   ✔   |   -   |   ✔   |    ✔   |   -   |   ✔   |   -   |   -   |   -   |   -   |   -   |
[Dimension](luis-reference-prebuilt-dimension.md) :<br>volume<br>superficie<br>poids<br>informations (p. ex. : bit/octet)<br>longueur (p. ex. : mètre)<br>vitesse (p. ex : km par heure)  |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   -   |
[Email](luis-reference-prebuilt-email.md)   |    ✔   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |
[Number](luis-reference-prebuilt-number.md)   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   -   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |
[Temperature](luis-reference-prebuilt-temperature.md) :<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   -   |
[URL](luis-reference-prebuilt-url.md)   |    ✔   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |

Voir les notes sur [entités prédéfinies désapprouvée](luis-reference-prebuilt-deprecated.md)

## <a name="examples-of-prebuilt-entities-in-en-us-culture"></a>Exemples d’entités prédéfinies dans la culture en-us
Le tableau suivant répertorie les entités prédéfinies avec des exemples de données et les valeurs renvoyées.

Entité prédéfinie   |   Exemple d’énoncé   |   JSON
------|------|------|
 ```builtin.age```   |   ```100 year old```   |```{ "type": "builtin.age", "entity": "100 year old" }```|  
 ```builtin.age```   |   ```19 years old```   |```{ "type": "builtin.age", "entity": "19 years old" }```|
 ```builtin.currency```     |   ```1000.00 US dollars```   |```{ "type": "builtin.currency", "entity": "1000.00 us dollars" }```
 ```builtin.currency```     |   ```$ 67.5 B```   |```{ "type": "builtin.currency", "entity": "$ 67.5" }```|
 ```builtin.datetimeV2``` | Voir [builtin.datetimev2](luis-reference-prebuilt-datetimev2.md) | Voir [builtin.datetimev2](luis-reference-prebuilt-datetimev2.md) |
 ```builtin.dimension```     |   ```2 miles```   |```{ "type": "builtin.dimension", "entity": "2 miles" }```|
 ```builtin.dimension```     |  ```650 square kilometers```   |```{ "type": "builtin.dimension", "entity": "650 square kilometers" }```|
 ```builtin.email```     |  ```patti.owens@microsoft.com```   |```{ "type": "builtin.email", "entity": "patti.owens@microsoft.com" }```|
 ```builtin.number```     |  ```ten```   |``` { "type": "builtin.number", "entity": "ten" } ```|
 ```builtin.number```     |   ```3.1415```   |```  { "type": "builtin.number", "entity": "3 . 1415" }``` |
 ```builtin.ordinal```     |   ```first```   |```{ "type": "builtin.ordinal", "entity": "first" }``` |
 ```builtin.ordinal```     |   ```10th```   | ```{ "type": "builtin.ordinal", "entity": "10th" }``` |  
 ```builtin.percentage```   |   ```The stock price increase by 7 $ this year```   |```{ "type": "builtin.percentage", "entity": "7 %" }```|
 ```builtin.phonenumber```   |   ```my mobile is 00 44 161 1234567```   |```{ "type": "builtin.phonenumber", "entity": "00 44 161 1234567" }```|
 ```builtin.temperature```     |   ```10 degrees celsius```   | ```{ "type": "builtin.temperature", "entity": "10 degrees celcius" }```|   
 ```builtin.temperature```     |   ```78 F```   |```{ "type": "builtin.temperature", "entity": "78 f" }```|
 ```builtin.url```     |   ```http://www.luis.ai is a great cognitive service```   |```{ "type": "builtin.url", "entity": "http://www.luis.ai" }```|


## <a name="contribute-to-prebuilt-entity-cultures"></a>Contribuer aux cultures d’entités prédéfinies
Les entités prédéfinies sont développées dans le projet open source Recognizers-Text. Veuillez [contribuer](https://github.com/Microsoft/Recognizers-Text) au projet. Ce projet inclut des exemples de devise par culture. 

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les entités [number](luis-reference-prebuilt-number.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md) et [currency](luis-reference-prebuilt-currency.md). 