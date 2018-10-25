---
title: Score de confiance - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Explication du score de confiance
services: cognitive-services
author: tulasim88
manager: pchoudh
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 09/27/2018
ms.author: tulasim
ms.openlocfilehash: 9bcd2ccfc5fb8ddc71d2df178dd214be2a67160d
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/08/2018
ms.locfileid: "48856851"
---
# <a name="confidence-score"></a>Score de confiance
Quand une requête d’utilisateur trouve une correspondance dans une base de connaissances, QnA Maker renvoie des réponses pertinentes, ainsi qu’un score de confiance. Ce score indique la probabilité que la réponse corresponde à la requête de l’utilisateur. 

Un score de confiance est une valeur comprise de 0 à 100. Un score de 100 est probablement une correspondance exacte, tandis qu’un score de 0 signifie qu’aucune réponse correspondante n’a été trouvée. Plus le score et élevé,  plus la réponse est fiable. Plusieurs réponses peuvent correspondre à une requête donnée. Dans ce cas, les réponses sont retournées dans l’ordre décroissant du score de confiance.

L’exemple ci-dessous présente une entité QnA avec 2 questions. 


![Exemple de paire QnA](../media/qnamaker-concepts-confidencescore/ranker-example-qna.png)

Pour l’exemple ci-dessus, vous pouvez vous attendre à des scores tels que ceux de l’exemple de plage de scores ci-dessous, pour différents types de requêtes utilisateur :


![Plage de scores classées](../media/qnamaker-concepts-confidencescore/ranker-score-range.png)


Le tableau suivant indique la confiance généralement associée à un score donné.

|Valeur du score|Signification du score|Exemple de requête|
|--|--|--|
|90 - 100|Correspondance presque exacte entre la requête de l’utilisateur et une question de la base de connaissances|« Mes modifications ne sont pas mises à jour dans la base de connaissances après publication »|
|> 70|Niveau de confiance élevé, généralement une bonne réponse correspondant complètement à la requête de l’utilisateur|« J’ai publié ma base de connaissances, mais elle n’est pas à jour »|
|50 - 70|Confiance moyenne, généralement une assez bonne réponse correspondant à l’intention principale de la requête de l’utilisateur|« Dois-je enregistrer mes mises à jour avant de publier ma base de connaissances ? »|
|30 - 50|Confiance faible, généralement une réponse connexe correspondance partiellement l’intention de la requête de l’utilisateur|« Que font l’enregistrement et l’apprentissage ? »|
|< 30|Confiance très faible, généralement une réponse ne correspondant pas à la requête de l’utilisateur, à l’exception de certains mots ou expressions |« Où puis-je ajouter des synonymes à ma base de connaissances »|
|0|Aucune correspondance, donc aucune réponse retournée.|« Combien coûte le service ? »|

## <a name="choose-a-score-threshold"></a>Choisir un seuil de score
Le tableau ci-dessus présente les scores attendus sur la plupart des bases de connaissances. Cependant, chaque base de connaissances étant différente et comprenant différents types de mots, d’intentions et d’objectifs, nous vous recommandons de tester et choisir le seuil optimal pour vous. Le seuil par défaut et recommandé qui devrait fonctionner pour la plupart des bases de connaissances est **50**.

Lorsque vous choisissez votre seuil, n’oubliez pas l’équilibre entre exactitude et couverture, et ajustez votre seuil à vos besoins.

- Si l’**exactitude** (ou précision) est primordiale pour votre scénario, augmentez votre seuil. Ainsi, chaque fois que vous retournerez une réponse, celle-ci sera beaucoup plus FIABLE et susceptible d’être celle qu’attendent les utilisateurs. Dans ce cas, vous risquez de laisser davantage de questions sans réponse. *Exemple :* si vous fixez le seuil à **70**, vous risquez de passer à côté d’exemples ambigus tels que « qu’est-ce qu’enregistrer et former ? ».

- Si la **couverture** (ou rappel) est primordiale et que vous souhaitez répondre au plus grand nombre possible de questions, même si la réponse n’a qu’une relation très vague ou lointaine avec la question, ABAISSEZ le seuil. Cela signifie qu’il pourrait y avoir davantage de cas où la réponse ne correspond pas à la requête réelle de l’utilisateur, mais fournit des informations vaguement associées à la question. *Par exemple :* si vous fixez le seuil à **30**, vous risquez de fournir des réponses n’ayant qu’un lointain rapport à des requêtes telles que « Où puis-je modifier ma base de connaissances ? »


## <a name="improve-confidence-scores"></a>Améliorer les scores de confiance
Pour améliorer le score de confiance d’une réponse spécifique à une question de l’utilisateur, vous pouvez ajouter la question de l’utilisateur à la base de connaissances en tant que question alternative liée à cette réponse.


## <a name="similar-confidence-scores"></a>Scores de confiance similaires
Lorsque plusieurs réponses ont un score de confiance similaire, il est probable que la question était trop générique et donc mise en correspondance à probabilité égale avec plusieurs réponses. Essayez de mieux structurer vos Questions et réponses afin que chaque entité QnA ait une intention distincte.


## <a name="confidence-score-differences"></a>Différences entre les scores de confiance
Le score de confiance d’une réponse peut changer sensiblement entre le test et la version publiée de la base de connaissances, même si le contenu est le même. En effet, le contenu de la base de connaissances de test et de la base de connaissances publiée se trouvent dans des index Recherche Azure différents.
Consultez ici le fonctionnement de l’opération de [publication](../How-To/publish-knowledge-base.md).


## <a name="no-match-found"></a>Aucune correspondance trouvée
Si aucune bonne correspondance n’est trouvée par la fonction de classement, le score de confiance de 0,0 ou « None » est retourné, et la réponse par défaut est « Aucune bonne correspondance trouvée dans la base de connaissances ». Vous pouvez remplacer cette réponse par défaut dans le code du bot ou de l’application appelant le point de terminaison. Vous pouvez également définir la réponse de remplacement dans Azure, et cela modifie la valeur par défaut pour toutes les bases de connaissances déployées dans un service QnA Maker particulier.

1. Accédez au [portail Azure](https://portal.azure.com) et au groupe de ressources qui représente le service QnA Maker que vous avez créé.

2. Cliquez pour ouvrir **App Service**.

    ![Accéder à App Service](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. Cliquez sur **Paramètres de l’application** et modifiez le champ **DefaultAnswer** pour entrer la réponse par défaut souhaitée. Cliquez sur **Enregistrer**.

    ![Modifier la réponse par défaut](../media/qnamaker-concepts-confidencescore/change-response.png)

4. Redémarrer votre App Service

    ![Le service d’application QnA Maker redémarre](../media/qnamaker-faq/qnamaker-appservice-restart.png)


## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Sources de données prises en charge](./data-sources-supported.md)
## <a name="see-also"></a>Voir aussi 
[Vue d’ensemble de QnA Maker](../Overview/overview.md)
