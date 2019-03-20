---
title: Publier une base de connaissances
titleSuffix: QnA Maker API - Azure Cognitive Services
description: Publication de votre base de connaissances avec le service de l’API QnA Maker est la dernière étape pour rendre votre base de connaissances disponible comme un point de terminaison répondant à la question. Lorsque vous publiez une base de connaissances, ses contenus QnA se déplacent à partir de l’index de test vers un index de production dans la recherche Azure.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/11/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: c65654e7d6b2e66a07116ea9555ed316ace88912
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58091951"
---
# <a name="publish-a-knowledge-base-using-the-qna-maker-api-service-portal"></a>Publier une base de connaissances à l’aide du portail de service API QnA Maker

Publication de votre base de connaissances avec le service de l’API QnA Maker est la dernière étape pour rendre votre base de connaissances disponible comme un point de terminaison répondant à la question. 

Quand vous publiez une base de connaissances, ses contenus de questions/réponses se déplacent de l’index de test vers un index de production dans Recherche Azure.

![Publier un index de test de production](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer. 

## <a name="publish-a-knowledge-base"></a>Publier une base de connaissances

1. Une fois que les modifications ont été effectuées dans votre base de connaissances, sélectionnez **Publier** dans la barre de navigation supérieure. Vous pouvez publier le nombre imparti de bases de connaissances pour la Recherche d’Azure. 

    ![Publier une base de connaissances](../media/qnamaker-how-to-publish-kb/publish.png)

2. Sélectionnez à nouveau **Publier** pour afficher les détails du point de terminaison qui peuvent être utilisés dans votre code d’application ou bot.

    ![Base de connaissances publiée avec succès](../media/qnamaker-how-to-publish-kb/publish-success.png)
    
## <a name="clean-up-resources"></a>Supprimer des ressources

Quand vous en avez fini avec la base de connaissances, supprimez-la du portail QnA Maker.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Obtenir une analyse de votre base de connaissances](./get-analytics-knowledge-base.md)
