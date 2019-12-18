---
title: Présentation des pipelines ML
titleSuffix: Azure Machine Learning
description: Cet article présente les avantages des pipelines Machine Learning (ML) que vous pouvez créer avec le SDK Azure Machine Learning pour Python. Les pipelines Machine Learning sont utilisés par les scientifiques des données pour créer, optimiser et gérer leurs workflows de Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 11/06/2019
ms.openlocfilehash: 84a01597570a488652e3db2345bdf68b52d4bf5b
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74973575"
---
# <a name="what-are-azure-machine-learning-pipelines"></a>Présentation des pipelines Azure Machine Learning

Les pipelines Azure Machine Learning vous permettent de créer des workflows dans vos projets Machine Learning. Ces workflows présentent un certain nombre d’avantages : 

+ Simplicité
+ Vitesse
+ Répétabilité
+ Flexibilité
+ Contrôle de version et suivi
+ Modularité 
+ Assurance qualité
+ Contrôle des coûts

Ces avantages deviennent significatifs dès que votre projet de Machine Learning passe au-delà de l’exploration pure pour aller vers l’itération. Même les pipelines en une étape simples peuvent être utiles. Les projets Machine Learning sont souvent dans un état complexe, et cela peut soulager de savoir que l’exécution précise d’un workflow unique peut être un processus trivial.

Découvrez comment [créer votre premier pipeline](how-to-create-your-first-pipeline.md).

![Pipelines de Machine Learning dans Azure Machine Learning](./media/concept-ml-pipelines/pipeline-flow.png)

<a name="compare"></a>
### <a name="which-azure-pipeline-technology-should-i-use"></a>Quelle technologie de pipeline Azure dois-je utiliser ?

Le cloud Azure offre plusieurs autres pipelines, chacun ayant un objectif différent. Le tableau suivant liste les différents pipelines et une explication de leur utilisation :

| Pipeline | Résultat | Canal canonique |
| ---- | ---- | ---- |
| Pipelines Azure Machine Learning | Définit des workflows Machine Learning réutilisables qui peuvent être utilisés comme modèle pour vos scénarios de Machine Learning. | Données -> modèle |
| [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) | Regroupe les activités de déplacement, de transformation et de contrôle des données nécessaires pour effectuer une tâche.  | Données -> données |
| [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) | Intégration et livraison continues de votre application à n’importe quelle plateforme/n’importe quel cloud  | Code -> application/service |

## <a name="what-can-azure-ml-pipelines-do"></a>Que peuvent faire les pipelines Azure ML ?

Un pipeline Azure Machine Learning est un workflow exécutable indépendamment d’une tâche Machine Learning complète. Les sous-tâches sont encapsulées sous la forme d’une série d’étapes au sein du pipeline. Un pipeline Azure Machine Learning peut être aussi simple qu’un pipeline qui appelle un script Python, de sorte qu’il _permet_ de faire à peu près n’importe quoi. Les pipelines _doivent_ se concentrer sur les tâches Machine Learning telles que :

+ Préparation des données, notamment l’importation, la validation et le nettoyage, la description et la transformation, la normalisation et la mise en lots
+ Configuration de la formation, notamment le paramétrage des arguments, les filePaths et les configurations de journalisation/création de rapports
+ Formation et validation efficaces et répétées, qui peuvent inclure la spécification de sous-ensembles de données spécifiques, différentes ressources de calcul matériel, le traitement distribué et la surveillance de la progression
+ Déploiement, y compris le contrôle de version, la mise à l’échelle, l’approvisionnement et le contrôle d’accès 

Ces étapes indépendantes permettent à plusieurs scientifiques de données de travailler en même temps sur le même pipeline sans surcharger les ressources de calcul. Des étapes distinctes facilitent également l'utilisation de différents types/tailles de calcul pour chaque étape.

Une fois conçu, le pipeline fait souvent l’objet de réglages précis au cours du cycle d’entraînement du pipeline. Quand vous réexécutez un pipeline, l’exécution passe directement aux étapes qui doivent être réexécutées, par exemple un script d’entraînement mis à jour. Les étapes qui n’ont pas besoin d’être réexécutées sont ignorées. La même analyse s’applique aux scripts inchangés utilisés pour l’exécution de l’étape. Cette fonctionnalité de réutilisation permet d'échapper aux étapes coûteuses et fastidieuses telles que l'ingestion et la transformation de données si les données sous-jacentes n'ont pas changé.

Avec Azure Machine Learning, vous pouvez utiliser différents kits de ressources et frameworks, comme PyTorch ou TensorFlow, à chaque étape de votre pipeline. Azure effectue la coordination des différentes [cibles de calcul](concept-azure-machine-learning-architecture.md) utilisées, afin que les données intermédiaires puissent être partagées avec les cibles de calcul en aval.

Vous pouvez [suivre les métrique de vos expériences de pipeline](https://docs.microsoft.com/azure/machine-learning/service/how-to-track-experiments) directement dans le Portail Azure ou dans la [page d’arrivée de votre espace de travail (préversion)](https://ml.azure.com). Après la publication d'un pipeline, vous pouvez configurer un point de terminaison REST qui vous permet de réexécuter le pipeline à partir de n'importe quelle plateforme ou pile.

En résumé, toutes les tâches complexes du cycle de vie Machine Learning peuvent être facilitées avec les pipelines. D’autres technologies de pipeline Azure ont leurs propres atouts, par exemple les [pipelines Azure Data Factory](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) pour utiliser des données et les [pipelines Azure ](https://azure.microsoft.com/services/devops/pipelines/) pour l’intégration et le déploiement continus. Toutefois, si votre objectif est le Machine Learning, les pipelines Azure Machine Learning sont susceptibles d’être le meilleur choix pour vos besoins en matière de workflow. 

## <a name="what-are-azure-ml-pipelines"></a>Présentation des pipelines Azure ML

Un pipeline Azure ML effectue un workflow logique complet avec une séquence ordonnée d’étapes. Chaque étape est une action de traitement séparée. Les pipelines s’exécutent dans le contexte d’une [expérience](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py) Azure Machine Learning.

Lors des premières étapes d’un projet ML, il est bien d’avoir un seul notebook Jupyter ou script Python qui effectue toutes les tâches de configuration des ressources et de l’espace de travail Azure, la préparation des données, la configuration d’exécution, la formation et la validation. Toutefois, tout comme les fonctions et les classes deviennent rapidement préférables à un seul bloc de code impératif, les workflows ML deviennent rapidement préférables à un notebook ou un script monolithique. 

Grâce à la modularisation des tâches ML, les pipelines prennent en charge l’impératif informatique selon lequel un composant doit « faire une (seule) chose bien ». La modularité est clairement essentielle à la réussite du projet lors de la programmation en équipe, mais même en cas de travail seul, même un petit projet ML implique des tâches distinctes, chacune présentant un bon niveau de complexité. Tâches incluses : configuration de l’espace de travail et accès aux données, préparation des données, définition et configuration du modèle, et déploiement. Tandis que les sorties d’une ou plusieurs tâches forment les entrées vers une autre, les détails d’implémentation exacts d’une tâche sont, au mieux, des distractions inutiles dans la suivante. Au pire, l’état de calcul d’une tâche peut provoquer un bogue dans une autre. 

### <a name="analyzing-dependencies"></a>Analyse des dépendances

De nombreux écosystèmes de programmation possèdent des outils qui orchestrent les dépendances de ressources, de bibliothèques ou de compilation. En règle générale, ces outils utilisent des timestamps de fichier pour calculer les dépendances. Lorsqu’un fichier est modifié, seul celui-ci et ses dépendants sont mis à jour (téléchargés, recompilés ou empaquetés). Les pipelines Azure ML étendent ce concept de façon spectaculaire. Comme les outils de compilation traditionnels, les pipelines calculent les dépendances entre les étapes et effectuent uniquement les recalculs nécessaires. 

Toutefois, l’analyse des dépendances dans les pipelines Azure ML est plus sophistiquée que les simples timestamps. Chaque étape peut s’exécuter dans un environnement matériel et logiciel différent. La préparation des données peut être un processus long, mais il n’est pas nécessaire de l’exécuter sur du matériel avec des GPU puissantes, certaines étapes peuvent nécessiter des logiciels spécifiques au système d’exploitation, vous pouvez utiliser la formation distribuée, etc. Bien que les économies liées à l’optimisation des ressources puissent être significatives, il peut être difficile de jongler manuellement entre les différentes variations des ressources matérielles et logicielles. Il est encore plus difficile de faire tout cela sans jamais faire d’erreur dans les données que vous transférez entre les étapes. 

Les pipelines permettant d’apporter une solution à ce problème. Azure Machine Learning orchestre automatiquement toutes les dépendances entre les étapes de pipeline. Cette orchestration peut inclure l’ajout et la suppression d’images Docker, l’attachement et le détachement de ressources de calcul et le déplacement de données entre les étapes de manière cohérente et automatique.

### <a name="reusing-results"></a>Réutilisation des résultats

En outre, la sortie d’une étape peut, si vous le souhaitez, être réutilisée. Si vous spécifiez la réutilisation comme possibilité et qu’il n’existe aucune dépendance en amont déclenchant un recalcul, le service de pipeline utilisera une version mise en cache des résultats de l’étape. Une telle réutilisation peut réduire considérablement le temps de développement. Si vous avez une tâche de préparation des données complexe, vous devrez probablement la réexécuter plus souvent que ce qui est strictement nécessaire. Les pipelines vous soulagent de ce souci : si nécessaire, l’étape s’exécutera. Sinon, elle ne s’exécutera pas.

Toutes ces analyses de dépendance, orchestrations et activations sont gérées par Azure Machine Learning quand vous instanciez un objet [Pipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline(class)?view=azure-ml-py), le transmettez à une `Experiment`, puis appelez `submit()`. 

### <a name="coordinating-the-steps-involved"></a>Coordination des étapes impliquées

Lorsque vous créez et exécutez un objet `Pipeline`, les étapes de haut niveau suivantes se produisent :

+ Pour chaque étape, le service calcule les conditions requises pour :
    + Ressources de calcul matérielles
    + Ressources du système d’exploitation (image(s) Docker)
    + Ressources logicielles (dépendances Conda / virtualenv)
    + Entrées de données 
+ Le service détermine les dépendances entre les étapes, ce qui aboutit à un graphique d’exécution dynamique
+ Quand chaque nœud dans le graphique d’exécution s’exécute :
    + Le service configure l’environnement matériel et logiciel requis (en réutilisant peut-être les ressources existantes)
    + L’étape s’exécute, en fournissant des informations de journalisation et de surveillance à l’objet `Experiment` qui les contient
    + Lorsque l’étape est terminée, ses sorties sont préparées comme entrées à l’étape suivante et/ou écrites dans le stockage
    + Les ressources qui ne sont plus nécessaires sont finalisées et détachées

![Étapes de pipeline](media/how-to-create-your-first-pipeline/run_an_experiment_as_a_pipeline.png)

## <a name="building-pipelines-with-the-python-sdk"></a>Création de pipelines avec le kit de développement logiciel (SDK) Python

Dans le [Kit de développement logiciel (SDK) Azure Machine Learning Python ](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), un pipeline est un objet Python défini dans le module `azureml.pipeline.core`. Un objet [Pipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py) contient une séquence ordonnée d’un ou plusieurs objets [PipelineStep](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep?view=azure-ml-py). La classe `PipelineStep` est abstraite et les étapes réelles sont des sous-classes telles que [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep?view=azure-ml-py), [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.pythonscriptstep?view=azure-ml-py) ou [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py). La classe [ModuleStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep?view=azure-ml-py) contient une séquence réutilisable d’étapes qui peuvent être partagées entre les pipelines. Un `Pipeline` s’exécute dans le cadre d’une `Experiment`.

Un pipeline Azure ML est associé à un espace de travail Azure Machine Learning et une étape de pipeline est associée à une cible de calcul disponible dans cet espace de travail. Pour plus d’informations, consultez [Créer et gérer des espaces de travail Azure Machine Learning dans le Portail Azure](https://docs.microsoft.com/azure/machine-learning/service/how-to-manage-workspace) ou [Qu’est-ce qu’une cible de calcul dans Azure Machine Learning ?](https://docs.microsoft.com/azure/machine-learning/service/concept-compute-target).

Dans Azure Machine Learning, une cible de calcul est l’environnement dans lequel une phase ML se produit. L’environnement logiciel peut être une machine virtuelle distante, Capacité de calcul Azure Machine Learning, Azure Databricks, Azure Batch, etc. L’environnement matériel peut également varier considérablement, en fonction de la prise en charge de la GPU, de la mémoire, du stockage, etc. Vous pouvez spécifier la cible de calcul pour chaque étape, ce qui vous donne un contrôle affiné sur les coûts. Vous pouvez utiliser des ressources plus ou moins puissantes pour l’action spécifique, le volume de données et les besoins de performances de votre projet. 

## <a name="building-pipelines-with-the-designer"></a>Création de pipelines avec le concepteur

Les développeurs qui préfèrent une aire de conception visuelle peuvent utiliser le concepteur Azure Machine Learning pour créer des pipelines. Vous pouvez accéder à cet outil à partir de la sélection **Concepteur** sur la page d’accueil de votre espace de travail.  Le concepteur vous permet de glisser-déplacer des étapes sur l’aire de conception. Pour un développement rapide, vous pouvez utiliser des modules existants dans le spectre des tâches ML. Les modules existants couvrent tout, de la transformation des données au déploiement, en passant par la sélection de l’algorithme et par la formation. Vous pouvez également créer un pipeline entièrement personnalisé en combinant vos propres étapes définies dans les scripts Python.

Lorsque vous concevez visuellement des pipelines, les entrées et sorties d’une étape sont visibles. Vous pouvez faire glisser et déposer des connexions de données, ce qui vous permet de comprendre et de modifier rapidement le flux de données de votre pipeline.
 
![Exemple de concepteur Azure Machine Learning](./media/concept-ml-pipelines/designer-drag-and-drop.gif)

### <a name="understanding-the-execution-graph"></a>Fonctionnement du graphique d’exécution

Les étapes d’un pipeline peuvent avoir des dépendances sur d’autres étapes. Le service de pipeline Azure ML effectue le travail d’analyse et d’orchestration de ces dépendances. Les nœuds du « graphique d’exécution » résultant sont des étapes de traitement. Chaque étape peut impliquer la création ou la réutilisation d’une combinaison particulière de composants matériels et logiciels, la réutilisation de résultats mis en cache, etc. L’orchestration et l’optimisation du service de ce graphique d’exécution peuvent accélérer considérablement la phase ML et réduire les coûts. 

Étant donné que les étapes s’exécutent indépendamment, les objets qui contiendront les données d’entrée et de sortie circulant entre les étapes doivent être définis en externe. Il s’agit du rôle de [DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py), de [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) et des classes associées. Ces objets de données sont associés à un objet [Datastore](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore%28class%29?view=azure-ml-py) qui encapsule leur configuration de stockage. La classe de base `PipelineStep` est toujours créée avec une chaîne `name`, une liste d’`inputs` et une liste de `outputs`. En règle générale, elle contient également une liste d’`arguments` et elle dispose souvent d’une liste d’`resource_inputs`. Les sous-classes auront également des arguments supplémentaires (par exemple, `PythonScriptStep` requiert le nom de fichier et le chemin d’accès du script à exécuter). 

Le graphique d’exécution est acyclique, mais les pipelines peuvent être exécutés selon une planification récurrente et peuvent exécuter des scripts Python capables d’écrire des informations d’état dans le système de fichiers, ce qui permet de créer des profils complexes. Si vous concevez votre pipeline afin que certaines étapes puissent s’exécuter en parallèle ou de manière asynchrone, Azure Machine Learning gère en toute transparence l’analyse des dépendances et la coordination du fan-out et du fan-in. En général, vous n’avez pas à vous soucier des détails du graphique d’exécution, mais il est disponible via l’attribut [Pipeline.graph](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline.pipeline?view=azure-ml-py#attributes). 


### <a name="a-simple-python-pipeline"></a>Un pipeline Python simple

Cet extrait de code montre les objets et les appels nécessaires à la création et à l’exécution d’un `Pipeline` de base :

```python
ws = Workspace.from_config() 
blob_store = Datastore(ws, "workspaceblobstore")
compute_target = ws.compute_targets["STANDARD_NC6"]
experiment = Experiment(ws, 'MyExperiment') 

input_data = DataReference(
    datastore=Datastore(ws, blob_store),
    data_reference_name="test_data",
    path_on_datastore="20newsgroups/20news.pkl")

output_data = PipelineData(
    "output_data",
    datastore=blob_store,
    output_name="output_data1")

steps = [ PythonScriptStep(
    script_name="train.py",
    arguments=["--input", input_data, "--output", output_data],
    inputs=[input_data],
    outputs=[output_data],
    compute_target=compute_target,
    source_directory="myfolder"
) ]

pipeline = Pipeline(workspace=ws, steps=steps)

pipeline_run = experiment.submit(pipeline)
pipeline_run.wait_for_completion()
```

L’extrait de code commence par les objets Azure Machine Learning communs, un `Workspace`, un `Datastore`, un [ComputeTarget](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py) et un `Experiment`. Ensuite, le code crée les objets pour contenir `input_data` et `output_data`. Le tableau `steps` contient un élément unique, un `PythonScriptStep` qui utilisera les objets de données et s’exécutera sur le `compute_target`. Ensuite, le code instancie l’objet `Pipeline` lui-même, en transmettant l’espace de travail et le tableau d’étapes. L’appel à `experiment.submit(pipeline)` commence l’exécution du pipeline Azure ML. L’appel à `wait_for_completion()` se bloque jusqu’à ce que le pipeline soit terminé. 

## <a name="best-practices-when-using-pipelines"></a>Meilleures pratiques lors de l’utilisation de pipelines

Comme vous pouvez le voir, la création d’un pipeline Azure ML est un peu plus complexe que le démarrage d’un script. Les pipelines nécessitent la configuration et la création de quelques objets Python. 

Voici quelques situations dans lesquelles l’utilisation d’un pipeline est suggérée :

* Dans un environnement d’équipe : divisez les tâches ML en plusieurs étapes indépendantes afin que les développeurs puissent utiliser et faire évoluer leurs programmes indépendamment. 

* Lors d’un déploiement ou d’un déploiement proche : déterminez la configuration et utilisez des opérations planifiées et pilotées par les événements pour rester informé des données modifiées.

* Au cours des premières phases d’un projet ML ou d’un travail autonome : utilisez des pipelines pour automatiser la compilation. Si vous avez commencé à vous soucier de la recréation de la configuration et de l’état de calcul avant d’implémenter une nouvelle idée, c’est le signal que vous pouvez envisager d’utiliser un pipeline pour automatiser le workflow. 

Il est facile de s’enthousiasmer en ce qui concerne la réutilisation des résultats mis en cache, le contrôle affiné des coûts de calcul et l’isolation des processus, mais les pipelines ont des coûts. Voici quelques anti-modèles :

* L’utilisation de pipelines comme seul moyen de séparer les préoccupations. Les fonctions, les objets et les modules intégrés de Python s’avèrent très utiles pour éviter un état de programmation confus. Une étape de pipeline est beaucoup plus coûteuse qu’un appel de fonction.

* Couplage fort entre les étapes du pipeline. Si la refactorisation d’une étape dépendante requiert fréquemment la modification des sorties d’une étape précédente, il est probable que des étapes distinctes soient actuellement plus coûteuses qu’un avantage. Une autre indication que les étapes sont trop couplées sont les arguments d’une étape qui ne sont pas des données mais des indicateurs permettant de contrôler le traitement. 

* Optimisation prématurée des ressources de calcul. Par exemple, il existe souvent plusieurs étapes pour la préparation des données et l’on peut souvent se dire « Oh, voici un endroit où je pourrais utiliser un `MpiStep` pour la programmation parallèle, mais voici un endroit où je pourrais utiliser un `PythonScriptStep` avec une cible de calcul moins puissante », et ainsi de suite. Et peut-être, à long terme, la création d’étapes granulaires comme cela, peut s’avérer utile, en particulier s’il est possible d’utiliser des résultats mis en cache plutôt que de toujours recalculer. Toutefois, les pipelines ne sont pas destinés à remplacer le module `multiprocessing`. 

Tant qu’un projet ne présente pas un déploiement volumineux ou proche, vos pipelines doivent être davantage grossiers plutôt que présenter une granularité fine. Si vous considérez que votre projet ML implique des _phases_ et qu’un pipeline fournit un workflow complet pour vous déplacer au sein d’une phase particulière, vous êtes sur le bon chemin. 

## <a name="key-advantages"></a>Principaux avantages

Voici les principaux avantages de l’utilisation des pipelines pour des workflows de Machine Learning :

|Avantage principal|Description|
|:-------:|-----------|
|**Exécutions sans&nbsp; assistance**|Planifiez des étapes à exécuter en parallèle ou en séquence de façon fiable et sans assistance. La préparation et la modélisation des données peuvent prendre des jours voire des semaines, et les pipelines vous permettent de vous concentrer sur d’autres tâches pendant l’exécution du processus. |
|**Calcul hétérogène**|Utilisez plusieurs pipelines qui sont coordonnés de façon fiable entre des ressources de calcul et des emplacements de stockage hétérogènes et évolutifs. Utilisez efficacement les ressources de calcul disponibles en exécutant des étapes de pipeline individuelles sur différentes cibles de calcul, comme HDInsight, des machines virtuelles DSVM sur GPU et Databricks.|
|**Réutilisabilité**|Créez des modèles de pipeline pour des scénarios précis, comme le réentraînement et le scoring par lots. Déclenchez des pipelines publiés à partir de systèmes externes par de simples appels REST.|
|**Suivi et gestion de version**|Au lieu de suivre manuellement les données et les chemins d’accès aux résultats au fil des itérations, utilisez le kit SDK Pipelines pour nommer vos sources de données, vos entrées et vos sorties et en contrôler les versions. Vous pouvez aussi gérer séparément les scripts et les données pour augmenter la productivité.|
| **Modularité** | La séparation des sujets de préoccupation et l’isolation des modifications permet aux logiciels d’évoluer plus rapidement avec une qualité supérieure. | 
|**Collaboration**|Les pipelines permettent aux scientifiques des données de collaborer dans toutes les phases du processus de conception du Machine Learning tout en travaillant simultanément sur les étapes de pipeline.|

## <a name="next-steps"></a>Étapes suivantes

Les pipelines Azure ML sont une fonctionnalité puissante qui commence à générer de la valeur lors des premières étapes du développement. La valeur augmente à mesure que l’équipe et le projet s’étendent. Cet article a expliqué comment les pipelines sont spécifiés avec le Kit de développement logiciel (SDK) Azure Machine Learning Python et orchestrés sur Azure. Vous avez vu du code source de base et vous avez bénéficié d’une introduction à quelques-unes des classes `PipelineStep` disponibles. Vous devriez à présent savoir quand utiliser les pipelines Azure ML et comment Azure les exécute. 


+ Découvrez comment [créer votre premier pipeline](how-to-create-your-first-pipeline.md).

+ Découvrez comment [exécuter des prédictions par lots sur de grandes quantités de données](tutorial-pipeline-batch-scoring-classification.md ).

+ Consultez les documents de référence du Kit de développement logiciel (SDK) pour le [cœur du pipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) et les [étapes du pipeline](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py).

+ Testez les exemples de notebooks Jupyter illustrant des [pipelines Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines). Découvrez comment [exécuter des notebooks pour explorer ce service](samples-notebooks.md).
