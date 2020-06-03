---
title: Architecture et concepts clés
titleSuffix: Azure Machine Learning
description: Apprenez-en davantage sur l’architecture, les termes, les concepts et le workflow qui composent Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 05/13/2020
ms.custom: seoapril2019, seodec18
ms.openlocfilehash: 49c23774fe16c24ba90daa02cdda1688b79b12d3
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83683045"
---
# <a name="how-azure-machine-learning-works-architecture-and-concepts"></a>Fonctionnement d’Azure Machine Learning : Architecture et concepts

Apprenez-en davantage sur l’architecture, les concepts et le workflow pour Azure Machine Learning. Le diagramme suivant représente les principaux composants du service et illustre le workflow général lors de l’utilisation du service :

![Architecture et workflow d’Azure Machine Learning](./media/concept-azure-machine-learning-architecture/workflow.png)

## <a name="workflow"></a>Workflow

Le workflow du modèle Machine Learning suit généralement cette séquence :

1. **Entraîner**
    + Développez des scripts d’entraînement Machine Learning dans **Python**, **R** ou le concepteur visuel.
    + Créez et configurez une **cible de calcul**.
    + **Envoyez les scripts** à une cible de calcul configurée en vue de leur exécution dans cet environnement. Pendant l’entraînement, les scripts peuvent lire ou écrire dans des **magasins de données**. Les journaux et la sortie produits pendant l’entraînement sont enregistrés sous forme d’**exécution** dans l’**espace de travail** et regroupés dans des **expériences**.

1. **Empaqueter** - Une fois qu’une exécution satisfaisante est trouvée, inscrivez le modèle persistant dans le **registre de modèles**.

1. **Valider** - **Interrogez l’expérience** pour obtenir les métriques journalisées relatives aux exécutions actuelles et passées. Si les métriques ne montrent pas le résultat souhaité, retournez à l’étape 1, puis itérez vos scripts.

1. **Déployer** : développez un script de scoring qui utilise le modèle et **déployez le modèle** en tant que **service web** dans Azure, ou sur un **appareil IoT Edge**.

1. **Surveiller** : surveillez la **dérive de données** entre le jeu de données d’entraînement et les données d’inférence d’un modèle déployé. Lorsque cela est nécessaire, revenez à l’étape 1 pour réentraîner le modèle avec de nouvelles données d’entraînement.

## <a name="tools-for-azure-machine-learning"></a>Outils pour Azure Machine Learning

Utilisez ces outils pour Azure Machine Learning :

+  Interagissez avec le service dans un environnement Python avec le [SDK Azure Machine Learning pour Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
+ Interagissez avec le service dans un environnement R avec le [Kit de développement logiciel (SDK) Azure Machine Learning pour R](https://azure.github.io/azureml-sdk-for-r/reference/index.html).
+ Automatisez vos activités Machine Learning avec l’[interface CLI Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/reference-azure-machine-learning-cli).
+ Utilisez le [Concepteur Azure Machine Learning (préversion)](concept-designer.md) pour effectuer les étapes de workflow sans écrire de code.
+ L’[accélérateur de solution de nombreux modèles](https://aka.ms/many-models) (préversion) s’appuie sur Azure Machine Learning et vous permet d’effectuer la formation, l’utilisation et la gestion de centaines, voire de milliers de modèles Machine Learning.

> [!NOTE]
> Cet article définit les termes et les concepts utilisés par Azure Machine Learning, et non pas ceux relatifs à la plateforme Azure. Pour plus d’informations sur la terminologie relative à la plateforme Azure, consultez le [glossaire Microsoft Azure](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology).

## <a name="glossary"></a>Glossaire

* [Activité](#activities)
* [Espace de travail](#workspaces)
    * [Expériences](#experiments)
        * [Exécuter](#runs) 
            * [Configuration d’exécution](#run-configurations)
            * [Instantané](#snapshots)
            * [Suivi Git](#github-tracking-and-integration)
            * [Logging](#logging)
    * [Pipelines ML](#ml-pipelines)
    * [Modèles](#models)
        * [Environnements](#environments)
        * [Script d’entraînement](#training-scripts)
        * [Estimateurs](#estimators)
    * [Points de terminaison](#endpoints)
        * [Service web](#web-service-endpoint)
        * [Modules IoT](#iot-module-endpoints)
    * [Jeu de données et magasins de données](#datasets-and-datastores)
    * [Cibles de calcul](#compute-targets)

### <a name="activities"></a>Activités

Une activité représente une opération de longue durée. Les opérations suivantes sont des exemples d’activités :

* Création ou suppression d’une cible de calcul
* Exécution d’un script sur une cible de calcul

Les activités peuvent envoyer des notifications via le SDK ou l’interface utilisateur web, ce qui vous permet de superviser facilement la progression de ces opérations.

### <a name="workspaces"></a>Workspaces

L’[espace de travail](concept-workspace.md) est la ressource de niveau supérieur d’Azure Machine Learning. Il fournit un emplacement centralisé dans lequel utiliser tous les artefacts que vous créez quand vous utilisez Azure Machine Learning. Vous pouvez partager un espace de travail avec d’autres utilisateurs. Pour une description détaillée des espaces de travail, consultez [Présentation d’un espace de travail Azure Machine Learning](concept-workspace.md).

### <a name="experiments"></a>Expériences

Une expérience est un regroupement d’exécutions provenant d’un script spécifié. Elle appartient toujours à un espace de travail. Lorsque vous envoyez une exécution, vous fournissez un nom pour l’expérience. Les informations concernant l’exécution sont stockées sous cette expérience. Si vous envoyez une exécution et spécifiez un nom d’expérience qui n’existe pas, une nouvelle expérience portant ce nom nouvellement spécifié est automatiquement créée.

Pour obtenir un exemple de l’utilisation d’une expérience, consultez le [Tutoriel : Entraîner votre premier modèle](tutorial-1st-experiment-sdk-train.md).

### <a name="runs"></a>Exécutions

Une exécution est une exécution unique d’un script de formation. Une expérience contient généralement plusieurs exécutions.

Azure Machine Learning enregistre toutes les exécutions et stocke les informations suivantes dans l’expérience :

* Les métadonnées relatives à l’exécution (horodatage, durée, et ainsi de suite)
* Les métriques qui sont journalisées par votre script
* Les fichiers de sortie qui sont collectés automatiquement par l’expérience ou chargés explicitement par l’utilisateur
* Un instantané du répertoire qui contient vos scripts, avant l’exécution

Vous déclenchez une exécution lorsque vous envoyez un script pour entraîner un modèle. Une exécution peut avoir zéro, une ou plusieurs exécutions enfants. Par exemple, l’exécution de niveau supérieur peut avoir deux exécutions enfants, et chacune d’elles peut avoir sa propre exécution enfant.

### <a name="run-configurations"></a>Configurations de séries de tests

Une configuration d’exécution est un ensemble d’instructions qui définit la façon dont un script doit être exécuté dans une cible de calcul spécifiée. La spécification comprend un vaste ensemble de définitions de comportement, par exemple, s’il faut utiliser un environnement Python existant ou utiliser un environnement Conda créé à partir des spécifications.

Une configuration d’exécution peut être rendue persistante dans un fichier du répertoire qui contient votre script d’entraînement, ou peut être construite comme un objet en mémoire et utilisée pour envoyer une exécution.

Pour obtenir des exemples de configurations d’exécutions, consultez [Sélectionner et utiliser une cible de calcul pour entraîner votre modèle](how-to-set-up-training-targets.md).

### <a name="snapshots"></a>Instantanés

Lorsque vous envoyez une exécution, Azure Machine Learning compresse le répertoire qui contient le script dans un fichier zip, puis l’envoie à la cible de calcul. Le fichier zip est ensuite décompressé, et le script y est exécuté. Azure Machine Learning stocke également le fichier zip en tant qu’instantané dans l’enregistrement d’exécution. Toute personne ayant accès à l’espace de travail peut parcourir un enregistrement d’exécution et télécharger l’instantané.

> [!NOTE]
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]

### <a name="github-tracking-and-integration"></a>Intégration et suivi GitHub

Lorsque vous lancez une exécution d’entraînement où le répertoire source est un répertoire Git local, les informations relatives au répertoire sont stockées dans l’historique des exécutions. Cela fonctionne avec des exécutions envoyées à l’aide d’un estimateur, d’un pipeline ML ou d’un script exécuté. Cela fonctionne également pour les exécutions soumises à partir du SDK ou de l’interface CLI Machine Learning.

Pour plus d’informations, consultez [Obtenir une intégration pour Azure Machine Learning](concept-train-model-git-integration.md).

### <a name="logging"></a>Journalisation

Lorsque vous développez votre solution, utilisez le SDK Python Azure Machine Learning dans votre script Python pour journaliser les métriques arbitraires. Après l’exécution, interrogez les métriques pour vérifier si celle-ci a produit le modèle que vous souhaitez déployer.

### <a name="ml-pipelines"></a>Pipelines ML

Vous utilisez des pipelines de machine learning pour créer et gérer des flux de travail qui combinent les phases de machine learning. Par exemple, un pipeline peut inclure les phases de préparation des données, d’entraînement du modèle, de déploiement du modèle et d’inférence/scoring. Chaque phase peut englober plusieurs étapes, chacune d’elles pouvant s’exécuter sans assistance dans différentes cibles de calcul. 

Les étapes de pipeline sont réutilisables et peuvent être exécutées sans avoir à réexécuter les étapes précédentes si la sortie de ces étapes n’a pas changé. Par exemple, vous pouvez réentraîner un modèle sans réexécuter les étapes coûteuses de préparation des données si celles-ci n’ont pas été modifiées. Les pipelines permettent également aux scientifiques des données de collaborer tout en travaillant chacun sur une partie différente du workflow de Machine Learning.

Pour plus d’informations sur les pipelines Machine Learning disposant de ce service, consultez [Pipelines et Azure Machine Learning](concept-ml-pipelines.md).

### <a name="models"></a>Modèles

Le modèle le plus simple est un morceau de code qui accepte une entrée et produit une sortie. La création d’un modèle Machine Learning implique la sélection d’un algorithme auquel vous devez fournir des données, ainsi que l’optimisation des hyperparamètres. L’entraînement est un processus itératif qui génère un modèle entraîné, lequel encapsule ce qu’il a appris au cours du processus d’entraînement.

Un modèle est généré par une exécution effectuée dans Azure Machine Learning. Vous pouvez également utiliser un modèle qui est entraîné en dehors d’Azure Machine Learning. Vous pouvez inscrire un modèle dans un espace de travail Azure Machine Learning.

Azure Machine Learning est indépendant de tout framework. Lorsque vous créez un modèle, vous pouvez utiliser un des frameworks Machine Learning populaires, tels que Scikit-learn, XGBoost, PyTorch, TensorFlow et Chainer.

Pour obtenir un exemple de formation d’un modèle à l’aide de Scikit-learn et d’un estimateur, consultez [Didacticiel : Entraîner un modèle de classification d’images avec Azure Machine Learning](tutorial-train-models-with-aml.md)

Le **registre de modèles** effectue le suivi de tous les modèles de votre espace de travail Azure Machine Learning.

Les modèles sont identifiés par leur nom et par leur version. Chaque fois que vous inscrivez un modèle portant le même nom qu’un modèle existant, le registre suppose qu’il s’agit d’une nouvelle version. La version est incrémentée et le nouveau modèle est inscrit sous le même nom.

Lorsque vous inscrivez le modèle, vous pouvez fournir des étiquettes de métadonnées supplémentaires, puis utiliser les étiquettes lorsque vous recherchez des modèles.

> [!TIP]
> Un modèle inscrit est un conteneur logique pour un ou plusieurs fichiers qui composent votre modèle. Par exemple, si vous avez un modèle qui est stocké dans plusieurs fichiers, vous pouvez inscrire ces derniers en tant que modèle unique dans votre espace de travail Azure Machine Learning. Après l’inscription, vous pouvez ensuite télécharger ou déployer le modèle inscrit et recevoir tous les fichiers qui ont été inscrits.

Vous ne pouvez pas supprimer un modèle inscrit qui est utilisé par un déploiement actif.

Pour obtenir un exemple d’inscription de modèle, consultez [Entraîner un modèle de classification d’images avec Azure Machine Learning](tutorial-train-models-with-aml.md).

### <a name="environments"></a>Environnements

Les environnements Azure ML sont utilisés pour spécifier la configuration (Docker, Python, Spark, etc.) qui permet de créer un environnement reproductible pour la préparation des données, l’entraînement des modèles et le déploiement des modèles. Il s’agit d’entités gérées et avec version dans votre espace de travail Azure Machine Learning qui permettent des workflows Machine Learning reproductibles, pouvant être audités et portables sur différentes cibles de calcul.

Vous pouvez utiliser un objet d’environnement sur votre calcul local pour développer votre script d’entraînement, réutiliser le même environnement sur la Capacité de calcul Azure Machine Learning pour l’entraînement de modèle à grande échelle, et même déployer votre modèle dans ce même environnement. 

Découvrez [comment créer et gérer un environnement ML réutilisable](how-to-use-environments.md) pour l’entraînement et l’inférence.

### <a name="training-scripts"></a>Scripts d’entraînement

Pour entraîner un modèle, vous devez spécifier le répertoire qui contient le script d’entraînement et les fichiers associés. Vous pouvez également spécifier un nom pour l’expérience, qui est utilisée pour stocker les informations qui sont collectées au cours de l’entraînement. Pendant l’entraînement, l’intégralité du répertoire est copiée dans l’environnement d’entraînement (la cible de calcul) et le script qui est spécifié par la configuration d’exécution est démarré. Un instantané du répertoire est également stocké sous l’expérience, dans l’espace de travail.

Pour obtenir un exemple, consultez [Tutoriel : Entraîner un modèle de classification d’images avec Azure Machine Learning](tutorial-train-models-with-aml.md)

### <a name="estimators"></a>Estimateurs

Pour faciliter la formation de modèle avec des infrastructures populaires, la classe d’estimateurs vous permet de construire facilement des configurations d’exécution. Vous pouvez créer et utiliser un [estimateur](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) générique pour envoyer des scripts d’apprentissage qui utilisent toute infrastructure de formation que vous choisissez (comme scikit-Learn).

Pour les tâches PyTorch, TensorFlow et Chainer, Azure Machine Learning fournit également les estimateurs [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) et [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py), respectivement, qui simplifient l’utilisation de ces infrastructures.

Pour plus d’informations, consultez les articles suivants :

* [Entraîner des modèles ML avec des estimateurs](how-to-train-ml-models.md).
* [Former des modèles de Deep Learning Pytorch à l’échelle avec Azure Machine Learning](how-to-train-pytorch.md).
* [Entraîner et inscrire des modèles TensorFlow à l’échelle avec Azure Machine Learning](how-to-train-tensorflow.md).
* [Entraîner et inscrire des modèles Chainer à l’échelle avec Azure Machine Learning](how-to-train-ml-models.md).

### <a name="endpoints"></a>Points de terminaison

Un point de terminaison est une instanciation de votre modèle soit dans un service web pouvant être hébergé dans le cloud, soit dans un module IoT pour les déploiements d’appareils intégrés.

#### <a name="web-service-endpoint"></a>Point de terminaison de service Web

Lors du déploiement d’un modèle en tant que service web, le point de terminaison peut être déployé sur Azure Container Instances, Azure Kubernetes Azure ou des FPGA. Vous créez le service à partir d’une image, de votre script et des fichiers associés. Ceux-ci sont placés dans une image de conteneur de base qui contient l’environnement d’exécution du modèle. L’image a un point de terminaison HTTP à charge équilibrée qui reçoit les requêtes de scoring qui sont envoyées au service web.

Azure vous permet de surveiller votre service web en collectant les données de télémétrie Application Insights ou les données de télémétrie des modèles, si vous avez choisi d’activer cette fonctionnalité. Vous seul pouvez accéder aux données de télémétrie qui sont stockées dans votre instance Application Insights et votre instance de compte de stockage.

Si vous avez activé la mise à l’échelle automatique, Azure met automatiquement à l’échelle votre déploiement.

Pour obtenir un exemple de déploiement de modèle en tant que service web, consultez [Déployer un modèle de classification d’images dans Azure Container Instances](tutorial-deploy-models-with-aml.md).

#### <a name="iot-module-endpoints"></a>Points de terminaison de module IoT

Un point de terminaison de module IoT déployé est un conteneur Docker qui inclut votre modèle, ainsi que le script ou l’application associés et toutes les dépendances supplémentaires. Vous déployez ces modules à l’aide d’Azure IoT Edge sur les périphériques en mode Edge.

Si vous avez activé la supervision, Azure collecte les données de télémétrie à partir du modèle qui se trouve dans le module Azure IoT Edge. Vous seul pouvez accéder aux données de télémétrie qui sont stockées dans votre instance de compte de stockage.

Azure IoT Edge garantit l’exécution de votre module et supervise l’appareil qui l’héberge.


### <a name="compute-instance-preview"></a><a name="compute-instance"></a>Instance de calcul (préversion)

Une **instance de calcul Azure Machine Learning** (ancienne machine virtuelle Notebook) est une station de travail cloud complètement managée qui comprend plusieurs outils et environnements installés pour Machine Learning. Les instances de calcul peuvent être utilisées comme cible de calcul pour les travaux de formation et d’inférence. Pour les tâches volumineuses, les [clusters de calcul Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute) avec des fonctionnalités de mise à l’échelle à plusieurs nœuds constituent un meilleur choix de cible de calcul.

En savoir plus sur les [instances de calcul](concept-compute-instance.md).

### <a name="datasets-and-datastores"></a>Jeux de données et magasins de données

Les **jeux de données Azure Machine Learning** (préversion) facilitent l’accès aux données et l’utilisation de ces dernières. Les jeux de données gèrent les données dans divers scénarios tels que l’entraînement de modèles et la création de pipelines. À l’aide du SDK Azure Machine Learning, vous pouvez accéder au stockage sous-jacent, explorer des données et gérer le cycle de vie de différentes définitions de jeu de données.

Les jeux de données fournissent des méthodes pour manipuler des données dans des formats courants, comme l’utilisation de `from_delimited_files()` ou `to_pandas_dataframe()`.

Pour plus d’informations, consultez [Créer et inscrire des jeux de données Azure Machine Learning](how-to-create-register-datasets.md).  Pour obtenir plus d’exemples d’utilisation de jeux de données, consultez les [exemples de notebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/datasets-tutorial).

Un **magasin de données** est une abstraction de stockage d’un compte de stockage Azure. Le magasin de données peut utiliser un conteneur d’objets blob Azure ou un partage de fichiers Azure en tant que stockage backend. Chaque espace de travail comprend un magasin de données par défaut, et peut inscrire des magasins de données supplémentaires. Utilisez l’API du SDK Python ou l’interface CLI Azure Machine Learning pour stocker et récupérer des fichiers à partir du magasin de données.

### <a name="compute-targets"></a>Cibles de calcul

Une [cible de calcul](concept-compute-target.md) permet de spécifier la ressource de calcul que vous utilisez pour exécuter votre script de formation ou pour héberger votre déploiement de service. Cet emplacement peut être votre machine locale ou une ressource de calcul basée sur le cloud.

En savoir plus sur les [cibles de calcul disponibles pour l’entraînement et le déploiement](concept-compute-target.md).

### <a name="next-steps"></a>Étapes suivantes

Pour bien démarrer avec Azure Machine Learning, voir :

* [Qu'est-ce que Microsoft Azure Machine Learning ?](overview-what-is-azure-ml.md)
* [Création d’un espace de travail Microsoft Azure Machine Learning](how-to-manage-workspace.md)
* [Tutoriel (partie 1) : Effectuer l'apprentissage d’un modèle](tutorial-train-models-with-aml.md)
