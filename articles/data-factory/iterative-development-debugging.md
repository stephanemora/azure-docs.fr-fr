---
title: Développement et débogage itératifs dans Azure Data Factory | Microsoft Docs
description: Apprenez à développer et déboguer les pipelines de Data Factory dans le portail Azure.
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.date: 05/14/2018
ms.topic: article
ms.service: data-factory
services: data-factory
documentationcenter: ''
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.openlocfilehash: e403afa5c870b2d007a8c5e1d46162cd899ead29
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/14/2018
ms.locfileid: "34164820"
---
# <a name="iterative-development-and-debugging-with-azure-data-factory"></a>Développement et débogage itératifs dans Azure Data Factory

Azure Data Factory vous permet de développer et de déboguer de manière itérative les pipelines de Data Factory.

Pour une présentation de huit minutes et la démonstration de cette fonctionnalité, regardez la vidéo suivante :

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Iterative-development-and-debugging-with-Azure-Data-Factory/player]

## <a name="iterative-debugging-features"></a>Fonctionnalités de débogage itératif
Créez des pipelines et effectuez des séries de tests à l’aide de la fonctionnalité **Déboguer** dans le canevas du pipeline sans écrire une seule ligne de code.

![Fonctionnalité Déboguer sur le canevas du pipeline](media/iterative-development-debugging/iterative-development-image1.png)

Affichez les résultats de vos séries de tests dans la fenêtre **Sortie** du canevas du pipeline.

![Fenêtre Sortie du canevas du pipeline](media/iterative-development-debugging/iterative-development-image2.png)

Après la réussite d’une série de tests, ajoutez d’autres activités à votre pipeline et continuez le débogage de façon itérative. Vous pouvez également **Annuler** une série de tests alors qu’elle est en cours d’exécution.

![Annuler une série de tests](media/iterative-development-debugging/iterative-development-image3.png)

Lorsque vous effectuez des séries de tests, vous n’êtes pas obligé de publier vos modifications sur la fabrique de données avant de sélectionner **Déboguer**. Cela est utile dans les scénarios où vous souhaitez vous assurer que les modifications fonctionnent comme prévu avant de mettre à jour le flux de travail de la fabrique de données.

## <a name="more-info-about-debugging"></a>Plus d’informations sur le débogage

1. Les séries de tests lancées avec la fonctionnalité **Déboguer** ne sont pas disponibles dans la liste sur l’onglet **Surveiller**. Seules s’affichent les séries de tests déclenchées au moyen des déclencheurs **Déclencher maintenant**, **Planification** ou **Fenêtre bascule** sur l’onglet **Surveiller**. Vous pouvez voir la dernière série de tests lancée grâce à la fonctionnalité **Déboguer** dans la fenêtre **Sortie** du canevas du pipeline.

2. En sélectionnant **Déboguer**, le pipeline est exécuté. Par exemple, si le pipeline contient une activité de copie, la série de tests copie des données de la source vers la destination. Par conséquent, nous vous recommandons d’utiliser des dossiers test pour vos activités de copie et autres lors du débogage. Une fois que vous avez débogué le pipeline, basculez vers les dossiers que vous souhaitez utiliser lors des opérations normales.

## <a name="setting-breakpoints-for-debugging"></a>Paramétrer des points d’arrêt pour le débogage

Data Factory vous permet également de déboguer jusqu’à ce que vous atteigniez une activité précise dans le canevas du pipeline. Placez simplement un point d’arrêt sur l’activité jusqu’à laquelle vous souhaitez tester, puis sélectionnez **Déboguer**. Data Factory garantit que le test s’exécute uniquement jusqu’à l’activité de point d’arrêt sur le canevas du pipeline. Cette fonctionnalité *Déboguer jusqu’à* est utile lorsque vous ne souhaitez pas tester le pipeline en entier, mais uniquement un sous-ensemble des activités à l’intérieur du pipeline.

![Points d’arrêt sur le canevas du pipeline](media/iterative-development-debugging/iterative-development-image4.png)

Pour définir un point d’arrêt, sélectionnez un élément du canevas du pipeline. Une option *Déboguer jusqu’à* apparaît sous la forme d’un cercle rouge vide dans le coin supérieur droit de l’élément.

![Avant de définir un point d’arrêt sur l’élément sélectionné](media/iterative-development-debugging/iterative-development-image5.png)

Après avoir sélectionné l’option *Déboguer jusqu’à*, elle se transforme en un cercle rouge plein pour indiquer que le point d’arrêt est activé.

![Après avoir défini un point d’arrêt sur l’élément sélectionné](media/iterative-development-debugging/iterative-development-image6.png)

## <a name="next-steps"></a>Étapes suivantes
[Intégration et déploiement continus dans Azure Data Factory](continuous-integration-deployment.md)
