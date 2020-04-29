---
title: Développement et débogage itératifs dans Azure Data Factory
description: Apprenez à développer et déboguer les pipelines de Data Factory dans le portail Azure.
ms.date: 09/26/2018
ms.topic: conceptual
ms.service: data-factory
services: data-factory
documentationcenter: ''
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: 49e406f0730189439bb1c2529b23d8ad6d760a2f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81411527"
---
# <a name="iterative-development-and-debugging-with-azure-data-factory"></a>Développement et débogage itératifs dans Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

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

Lorsque vous effectuez des séries de tests, vous n’êtes pas obligé de publier vos modifications sur la fabrique de données avant de sélectionner **Déboguer**. Cette fonctionnalité est utile dans les scénarios où vous souhaitez vous assurer que les modifications fonctionnent comme prévu avant de mettre à jour le flux de travail de la fabrique de données.

> [!IMPORTANT]
> En sélectionnant **Déboguer**, le pipeline est exécuté. Par exemple, si le pipeline contient une activité de copie, la série de tests copie des données de la source vers la destination. Par conséquent, nous vous recommandons d’utiliser des dossiers test pour vos activités de copie et autres lors du débogage. Une fois que vous avez débogué le pipeline, basculez vers les dossiers que vous souhaitez utiliser lors des opérations normales.

## <a name="visualizing-debug-runs"></a>Visualisation des exécutions de débogage

Vous pouvez visualiser toutes les exécutions de débogage qui sont en cours pour votre fabrique de données au même endroit. Sélectionnez **View debug runs** (Afficher les exécutions de débogage) dans le coin supérieur droit de la page. Cette fonctionnalité est utile dans les scénarios où des pipelines maîtres lancent des exécutions de débogage pour les pipelines enfants, et où vous voulez un seul affichage pour voir toutes les exécutions de débogage actives.

![Sélectionner l’icône View active debug runs (Afficher les exécutions de débogage actives)](media/iterative-development-debugging/view-debug-runs-image1.png)

![Exemple de liste des exécutions de débogage actives](media/iterative-development-debugging/view-debug-runs-image2.png)

Si vous avez des sessions de débogage Data Flow actives, celles-ci apparaissent dans la partie inférieure de la fenêtre de débogage active. Vous pouvez sélectionner une session Data Flow active et arrêter le cluster correspondant.

![Exemple de liste d’exécutions de débogage Data Flow actives](media/data-flow/dfsessions.png)

## <a name="monitoring-debug-runs"></a>Supervision des exécutions de débogage

Les séries de tests lancées avec la fonctionnalité **Déboguer** ne sont pas disponibles dans la liste sur l’onglet **Surveiller**. Seules s’affichent les séries de tests déclenchées au moyen des déclencheurs **Déclencher maintenant**, **Planification** ou **Fenêtre bascule** sur l’onglet **Surveiller**. Vous pouvez voir la dernière série de tests lancée grâce à la fonctionnalité **Déboguer** dans la fenêtre **Sortie** du canevas du pipeline.

## <a name="setting-breakpoints-for-debugging"></a>Paramétrer des points d’arrêt pour le débogage

Data Factory vous permet également de déboguer jusqu’à ce que vous atteigniez une activité précise dans le canevas du pipeline. Placez simplement un point d’arrêt sur l’activité jusqu’à laquelle vous souhaitez tester, puis sélectionnez **Déboguer**. Data Factory garantit que le test s’exécute uniquement jusqu’à l’activité de point d’arrêt sur le canevas du pipeline. Cette fonctionnalité *Déboguer jusqu’à* est utile lorsque vous ne souhaitez pas tester le pipeline en entier, mais uniquement un sous-ensemble des activités à l’intérieur du pipeline.

![Points d’arrêt sur le canevas du pipeline](media/iterative-development-debugging/iterative-development-image4.png)

Pour définir un point d’arrêt, sélectionnez un élément du canevas du pipeline. Une option *Déboguer jusqu’à* apparaît sous la forme d’un cercle rouge vide dans le coin supérieur droit de l’élément.

![Avant de définir un point d’arrêt sur l’élément sélectionné](media/iterative-development-debugging/iterative-development-image5.png)

Après que vous avez sélectionné l’option *Déboguer jusqu’à*, elle se transforme en un cercle rouge plein pour indiquer que le point d’arrêt est activé.

![Après avoir défini un point d’arrêt sur l’élément sélectionné](media/iterative-development-debugging/iterative-development-image6.png)

## <a name="next-steps"></a>Étapes suivantes
[Intégration et déploiement continus dans Azure Data Factory](continuous-integration-deployment.md)
