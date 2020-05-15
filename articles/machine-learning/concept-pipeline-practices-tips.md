---
title: Itération et évolution des pipelines de machine learning
titleSuffix: Azure Machine Learning
description: Modèles, pratiques et conseils pour un développement rapide
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 05/01/2020
ms.openlocfilehash: 2ea353469ed111eebb591aa6ba86c652683cc2f0
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858181"
---
# <a name="iterating-and-evolving-machine-learning-pipelines"></a>Itération et évolution des pipelines de machine learning

Les pipelines Azure Machine Learning offrent un moyen efficace pour modulariser votre code, réutiliser les résultats et optimiser vos ressources de calcul. Vous trouverez ici quelques conseils et pratiques à suivre quand vous utilisez les pipelines.

## <a name="how-do-you-get-started-with-pipelines"></a>Comment faire pour commencer à utiliser les pipelines ?

Il y a plusieurs options possibles pour commencer à utiliser les pipelines :

* Si vous apprenez mieux en lisant la documentation et en recréant le processus de construction, nous vous conseillons l’article [Créer et exécuter des pipelines de Machine Learning avec le kit SDK Azure Machine Learning](how-to-create-your-first-pipeline.md) 
* Si vous aimez apprendre de manière interactive dans un notebook Jupyter, essayez le pipeline développé dans le notebook [Pipelines Azure Machine Learning : Bien démarrer](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-getting-started.ipynb)
* Si vous préférez une expérience orientée code, le clonage du dépôt de [démonstration des pipelines Azure Machine Learning](https://github.com/microsoft/aml-pipelines-demo) fournit un bon point de départ

## <a name="how-do-you-modularize-pipeline-code"></a>Comment faire pour modulariser le code de pipeline ? 

Les modules et la classe `ModuleStep` constituent un excellent moyen pour modulariser votre code ML. Toutefois, gardez à l’esprit que la transition entre les étapes de pipeline est beaucoup plus coûteuse qu’un appel de fonction. La question à vous poser n’est pas vraiment « Ces fonctions et ces données sont-elles conceptuellement différentes de celles de cette autre section ? » mais plutôt « Est-ce que je veux que ces fonctions et ces données évoluent séparément ? » ou encore « Ce calcul est-il coûteux et puis-je réutiliser sa sortie ? » Pour plus d’informations, consultez ce notebook qui explique [comment créer Module et ModuleVersion, et comment les utiliser dans un pipeline avec ModuleStep](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-how-to-use-modulestep.ipynb).

Comme nous l’avons vu précédemment, la séparation de la préparation des données de l’entraînement est souvent l’une des possibilités. Parfois, la préparation des données est une opération tellement complexe et longue que vous devez décomposer le processus en étapes de pipelines séparés. D’autres possibilités incluent l’analyse et le test post-entraînement. 

## <a name="how-do-you-speed-pipeline-iteration"></a>Comment faire pour accélérer l’itération des pipelines ? 

Voici des techniques courantes pour une itération rapide des pipelines : 

- Cloner le pipeline (ce qui crée une copie) et réexécuter le pipeline rapidement
- Conserver l’instance de calcul en cours d’exécution, afin d’éviter le temps de démarrage
- Configurer la réutilisation des données et des étapes afin d’éviter au pipeline d’avoir à recalculer les données qui ne changent pas

Quand vous souhaitez effectuer une itération rapide, vous pouvez cloner votre pipeline existant pour créer un autre pipeline, puis réexécuter le pipeline. Une autre technique utile est de conserver votre instance de calcul à chaud, car vous évitez ainsi le coût de lancement du nouveau calcul. Si vous configurez l’étape pour autoriser la réutilisation du résultat d’une exécution, l’exécution répétée réutilisera les résultats dans la mesure du possible (en l’absence de modification des étapes).

## <a name="how-do-you-collaborate-using-ml-pipelines"></a>Comment collaborer à l’aide des pipelines ML ? 

Les pipelines séparés sont des lignes naturelles le long desquelles fractionner l’effort. Plusieurs développeurs, voire plusieurs équipes, peuvent travailler sur différentes étapes, tant que les données et les arguments passés entre les étapes sont validés. 

Pendant le développement actif, vous pouvez récupérer les résultats de l’exécution de `PipelineRun` et `StepRun` à partir de l’espace de travail, utiliser ces objets pour télécharger les sorties intermédiaire et finale, et utiliser ces artefacts dans votre propre travail modularisé.

## <a name="use-pipelines-to-test-techniques-in-isolation"></a>Utiliser des pipelines pour tester les techniques de manière isolée

Dans les scénarios réels, les solutions ML nécessitent généralement une large personnalisation de chaque étape. Les données brutes doivent souvent être préparées d’une manière ou d’une autre : filtrées, transformées et enrichies. Les processus d’entraînement peuvent avoir plusieurs architectures potentielles et, pour le deep learning, de nombreuses variantes possibles dans les tailles de couches et les fonctions d’activation. Même avec une architecture cohérente, la recherche d’hyperparamètres peut apporter des gains significatifs.

En plus des outils comme [AutoML](concept-automated-ml.md) et la [recherche d’hyperparamètres automatisée](how-to-tune-hyperparameters.md), les pipelines peuvent être un outil efficace pour les solutions de test A/B. Si vous avez plusieurs variantes de vos étapes de pipeline, vous pouvez facilement générer des exécutions distinctes pour tester les variantes : 

```python
data_preparation_variants = [data1, data2, data3]
data_augmentation_variants = [aug1, aug2]
architecture_variants = [train1, train2, train3]

# Cartesian product
all_variants = np.array(np.meshgrid(data_preparation_variants,data_augmentation_variants,architecture_variants)).T.reshape(-1,3)

pipelines = [Pipeline(workspace=ws, steps=variant.tolist(), description=str(variant)) for variant in all_variants]

```

