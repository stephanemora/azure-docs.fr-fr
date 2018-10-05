---
title: Publier une base de connaissances - QnA Maker
titleSuffix: Azure Cognitive Services
description: La publication de votre base de connaissances est l’étape finale de la mise à disposition de votre base de connaissances comme point de terminaison de question et de réponse. Lorsque vous publiez une base de connaissances, ses contenus QnA se déplacent à partir de l’index de test vers un index de production dans la recherche Azure.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: eceb0554d35935f1aee77c4c054cb7e65b327845
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47033411"
---
# <a name="publish-a-knowledge-base"></a>Publier une base de connaissances

La publication de votre base de connaissances est l’étape finale de la mise à disposition de votre base de connaissances comme point de terminaison de question et de réponse. 

Lorsque vous publiez une base de connaissances, ses contenus QnA se déplacent à partir de l’index de test vers un index de production dans la recherche Azure.

![Publier un index de test de production](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

## <a name="publish-a-knowledge-base"></a>Publier une base de connaissances

1. Une fois que les modifications ont été effectuées dans votre base de connaissances, sélectionnez **Publier** dans la barre de navigation supérieure. Vous pouvez publier le nombre imparti de bases de connaissances pour la Recherche d’Azure. 

    ![Publier une base de connaissances](../media/qnamaker-how-to-publish-kb/publish.png)

2. Sélectionnez à nouveau **Publier** pour afficher les détails du point de terminaison qui peuvent être utilisés dans votre code d’application ou bot.

    ![Publier une base de connaissances](../media/qnamaker-how-to-publish-kb/publish-success.png)
    
## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Obtenir une analyse de votre base de connaissances](./get-analytics-knowledge-base.md)
