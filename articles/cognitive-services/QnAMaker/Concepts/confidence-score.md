---
title: Score de confiance - QnA Maker
titleSuffix: Azure Cognitive Services
description: Un score de confiance indique le degré de correspondance entre la question de l’utilisateur et la réponse renvoyée.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: 33da5cf5724b8314ce813f12eb077d9a15ec1b2a
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47041521"
---
# <a name="confidence-score"></a>Score de confiance

Un score de confiance indique le degré de correspondance entre la question de l’utilisateur et la réponse renvoyée.

Lorsqu’une question de l’utilisateur est comparée au contenu de la base de connaissances, plusieurs réponses peuvent être retournées. Les réponses sont retournées dans un ordre de classement décroissant du score de confiance.

Un score de confiance est compris entre 0 et 100.

|Valeur du score|Confiance|
|--|--|
|100|Une correspondance exacte entre la question de l’utilisateur et une question de la base de connaissances|
|90|Confiance élevée, la plupart des mots correspondent|
|40-60|Confiance moyenne, les mots importants correspondent|
|10|Confiance faible, les mots importants ne correspondent pas|
|0|Aucune correspondance des mots|


## <a name="similar-confidence-scores"></a>Scores de confiance similaires
Lorsque plusieurs réponses ont un score de confiance similaire, il est probable que la question était trop générique et donc mise en correspondance à probabilité égale avec plusieurs réponses. Essayez de mieux structurer vos Questions et réponses afin que chaque entité QnA ait une intention distincte.


## <a name="improving-confidence-scores"></a>Amélioration des scores de confiance
Pour améliorer le score de confiance d’une réponse spécifique à une question de l’utilisateur, vous pouvez ajouter la question de l’utilisateur à la base de connaissances en tant que question alternative liée à cette réponse.
   
## <a name="confidence-score-differences"></a>Différences entre les scores de confiance
Le score de confiance d’une réponse peut changer sensiblement entre le test et la version publiée de la base de connaissances, même si le contenu est le même. En effet, le contenu de la base de connaissances de test et de la base de connaissances publiée se trouvent dans des index Recherche Azure différents.

Consultez ici le fonctionnement de l’opération de [publication](../How-To/publish-knowledge-base.md).


## <a name="no-match-found"></a>Aucune correspondance trouvée
Si aucune bonne correspondance n’est trouvée par la fonction de classement, le score de confiance de 0,0 ou « None » est retourné, et la réponse par défaut est « Aucune bonne correspondance trouvée dans la base de connaissances ». Vous pouvez remplacer cette réponse par défaut dans le code du bot ou de l’application appelant le point de terminaison. Vous pouvez également définir la réponse de remplacement dans Azure, et cela modifie la valeur par défaut pour toutes les bases de connaissances déployées dans un service QnA Maker particulier.

1. Accédez au [portail Azure](http://portal.azure.com) et au groupe de ressources qui représente le service QnA Maker que vous avez créé.

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
