---
title: Publier une base de connaissances
titleSuffix: QnA Maker - Azure Cognitive Services
description: La publication de votre base de connaissances est l’étape finale de la mise à disposition de votre base de connaissances comme point de terminaison de question et de réponse. Lorsque vous publiez une base de connaissances, ses contenus QnA se déplacent à partir de l’index de test vers un index de production dans la recherche Azure.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 12/11/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 585e5f8f955a405b08bef42588ab5caff71c7bba
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55865911"
---
# <a name="publish-a-knowledge-base-using-the-qna-maker-portal"></a>Publier une base de connaissances à partir du portail QnA Maker

La publication de votre base de connaissances est l’étape finale avant la mise à disposition de votre base de connaissances comme point de terminaison de questions/réponses à une application cliente. 

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
