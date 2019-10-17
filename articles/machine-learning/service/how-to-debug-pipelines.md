---
title: Déboguer et résoudre les problèmes de pipelines de machine learning
titleSuffix: Azure Machine Learning
description: Déboguez et résolvez les problèmes de pipelines de machine learning dans le SDK Azure Machine Learning pour Python. Découvrez les écueils habituels du développement de pipelines ainsi que des conseils pour vous aider à déboguer les scripts avant et pendant l’exécution à distance.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: trbye
ms.author: trbye
author: trevorbye
ms.date: 10/03/2019
ms.openlocfilehash: 3df95f88c057fa564078dbf05d5dfa4b26150f6a
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/04/2019
ms.locfileid: "71959654"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>Déboguer et résoudre les problèmes de pipelines de machine learning

Dans cet article, vous allez découvrir comment déboguer et résoudre les problèmes de [pipelines de machine learning](concept-ml-pipelines.md) dans le [SDK Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

Les sections suivantes offrent une vue d’ensemble des écueils habituels de la création de pipelines et exposent différentes stratégies pour déboguer votre code qui s’exécute dans un pipeline. Servez-vous des conseils suivants quand vous avez des difficultés à exécuter un pipeline comme prévu. 

## <a name="testing-scripts-locally"></a>Tester les scripts localement

Parmi les échecs les plus communément observés dans un pipeline figurent l’exécution incongrue d’un script attaché (script de suppression de données, script de scoring, etc.) ou la présence d’erreurs d’exécution dans le contexte de calcul distant qu’il est difficile de déboguer dans votre espace de travail du portail Azure. 

Les pipelines eux-mêmes ne peuvent pas être exécutés localement, mais l’exécution de scripts en isolation sur votre ordinateur local vous permet de déboguer plus rapidement dans la mesure où vous n’avez pas besoin d’attendre le processus de génération de calcul et d’environnement. Cela demande un peu de travail de développement :

* Si vos données se trouvent dans un magasin de données cloud, vous devez les télécharger et les rendre accessibles à votre script. Utiliser un petit échantillon de données est un bon moyen de réduire la durée d’exécution et d’être rapidement renseigné sur le comportement du script.
* Si vous tentez de simuler une étape de pipeline intermédiaire, vous devrez peut-être créer manuellement les types d’objet que le script attend de l’étape précédente.
* Vous devrez aussi définir votre propre environnement et répliquer les dépendances définies dans votre environnement de calcul distant.

Une fois que vous avez configuré un script pour qu’il s’exécute dans un environnement local, il est beaucoup plus facile d’effectuer certaines tâches de débogage, notamment :

* Attacher une configuration de débogage personnalisée
* Suspendre l’exécution et inspecter l’état des objets
* Intercepter les erreurs de type ou les erreurs logiques qui n’apparaîtront pas avant l’exécution

> [!TIP] 
> Une fois que vous pouvez vérifier que votre script s’exécute comme prévu, nous vous recommandons d’exécuter le script dans un pipeline à une seule étape avant de tenter de l’exécuter dans un pipeline à plusieurs étapes.

## <a name="debugging-scripts-from-remote-context"></a>Déboguer les scripts à partir du contexte distant

Tester les scripts localement est un excellent moyen de déboguer des fragments de code importants et une logique complexe avant de commencer à créer un pipeline. Mais à un moment donné, vous devrez probablement déboguer des scripts pendant l’exécution effective du pipeline, surtout quand il s’agira de diagnostiquer le comportement que les étapes du pipeline interagiront. Nous vous recommandons d’utiliser à loisir les instructions `print()` dans les scripts d’étapes pour voir l’état des objets et les valeurs attendues pendant l’exécution distante, comme vous le feriez pour déboguer du code JavaScript.

Le fichier journal `70_driver_log.txt` contient : 

* Toutes les instructions imprimées pendant l’exécution de votre script
* La rapport des appels de procédure du script 

Pour trouver ce fichier journal (et d’autres) sur le portail, commencez par cliquer sur le pipeline dans votre espace de travail.

![Page Pipelines sur le portail](./media/how-to-debug-pipelines/pipeline-1.png)

Accédez à l’exécution parente du pipeline.

![Exécution parente du pipeline](./media/how-to-debug-pipelines/pipeline-2.png)

Cliquez sur l’ID d’exécution de l’étape qui vous intéresse.

![Page Pipelines sur le portail](./media/how-to-debug-pipelines/pipeline-3.png)

Accédez à l’onglet **Journaux**. D’autres journaux renseignent sur le processus de génération d’images de votre environnement et les scripts de préparation d’étape.

![Page Pipelines sur le portail](./media/how-to-debug-pipelines/pipeline-4.png)

> [!TIP]
> Les exécutions relatives aux *pipelines publiés* se trouvent sur le portail sous l’onglet **Pipelines** de votre espace de travail. Les exécutions relatives aux *pipelines non publiés* se trouvent sous **Expériences**.

## <a name="troubleshooting-tips"></a>Conseils de dépannage

Le tableau suivant présente les problèmes courants qui se produisent pendant le développement de pipelines ainsi que les solutions possibles.

| Problème | Solution possible |
|--|--|
| Impossible de transmettre les données au répertoire `PipelineData` | Vérifiez que vous avez créé un répertoire dans le script qui correspond à l’emplacement où votre pipeline attend les données de sortie de l’étape. Dans la plupart des cas, un argument d’entrée définit le répertoire de sortie, puis crée le répertoire explicitement. Utilisez `os.makedirs(args.output_dir, exist_ok=True)` pour créer le répertoire de sortie. Pour obtenir un exemple de script de scoring qui illustre ce modèle de conception, consultez ce [tutoriel](tutorial-pipeline-batch-scoring-classification.md#write-a-scoring-script). |
| Bogues de dépendance | Si vous avez développé et testé des scripts localement, mais que vous détectez des problèmes de dépendance pendant leur exécution sur une cible de calcul distante dans le pipeline, vérifiez que les dépendances et les versions de votre environnement de calcul correspondent à celles de votre environnement de test. |
| Erreurs ambiguës liées aux cibles de calcul | La suppression et la recréation des cibles de calcul peuvent résoudre certains problèmes liés aux cibles de calcul. |
| Le pipeline ne réutilise pas les étapes | La réutilisation d’étape est activée par défaut, mais vérifiez que vous ne l’avez pas désactivée dans une étape du pipeline. Si la réutilisation est désactivée, le paramètre `allow_reuse` de l’étape est défini sur `False`. |
| Le pipeline se réexécute inutilement | Pour faire en sorte que les étapes ne se réexécutent que lorsque leurs données sous-jacents ou leurs scripts changent, découplez vos répertoires pour chaque étape. Si vous utilisez le même répertoire source pour plusieurs étapes, des réexécutions inutiles peuvent se produire. Utilisez le paramètre `source_directory` sur un objet d’étape de pipeline pour pointer vers votre répertoire isolé pour cette étape, et vérifiez que vous n’utilisez pas le même chemin `source_directory` pour plusieurs étapes. |

## <a name="next-steps"></a>Étapes suivantes

* Consultez les informations de référence sur le SDK pour obtenir de l’aide sur les packages [azureml-pipelines-core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) et [azureml-pipelines-steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py).

* Suivez le [tutoriel avancé](tutorial-pipeline-batch-scoring-classification.md) sur l’utilisation de pipelines pour le scoring par lots.
