---
title: Comment publier une base de connaissances - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Comment publier une base de connaissances
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: e9dbeacfb0df98c6b8f084c263690c05fe966cdc
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35369557"
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
