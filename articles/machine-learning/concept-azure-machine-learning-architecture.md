---
title: Architecture et concepts clés
titleSuffix: Azure Machine Learning
description: Découvrez l’architecture, la terminologie et les concepts relatifs à Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 08/20/2020
ms.custom: seoapril2019, seodec18
ms.openlocfilehash: d7bad24510f74a7fadd74328e24ea22855e6fe02
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750863"
---
# <a name="how-azure-machine-learning-works-architecture-and-concepts"></a>Fonctionnement d’Azure Machine Learning : Architecture et concepts

Apprenez-en davantage sur l’architecture et les concepts pour Azure Machine Learning.

> [!NOTE]
> Cet article définit les termes et les concepts utilisés par Azure Machine Learning, et non pas ceux relatifs à la plateforme Azure. Pour plus d’informations sur la terminologie relative à la plateforme Azure, consultez le [glossaire Microsoft Azure](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology).

## <a name="workspace"></a><a name="workspace"></a> Espace de travail

:::image type="content" source="media/concept-azure-machine-learning-architecture/architecture.svg" alt-text="Architecture Azure Machine Learning":::

L’[espace de travail Azure Machine Learning](concept-workspace.md) est la ressource de niveau supérieur pour Azure Machine Learning.  L’espace de travail est l’emplacement centralisé pour :
* Gérer les ressources que vous utilisez pour la formation et le déploiement de modèles, tels que les [calculs](#compute-instance)
* Stocker les ressources que vous créez lorsque vous utilisez Azure Machine Learning, notamment :
  * [Environnements](#environments)
  * [Points](#runs)
  * [Pipelines](#ml-pipelines)
  * [Groupes de données](#datasets-and-datastores)
  * [Modèles](#models)
  * [Points de terminaison](#endpoints)

Un espace de travail comprend d’autres ressources Azure utilisées par l’espace de travail :

+ [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) : Enregistre les conteneurs docker que vous utilisez pendant la formation et lorsque vous déployez un modèle. Pour réduire les coûts, ACR est **chargé en différé** jusqu’à la création des images de déploiement.
+ [Compte Stockage Azure](https://azure.microsoft.com/services/storage/) : Utilisé comme magasin de données par défaut pour l’espace de travail.  Les notebooks Jupyter utilisés avec vos instances de calcul Azure Machine Learning sont également stockés ici.
+ [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) : Stocke les informations de supervision concernant les modèles.
+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) : Stocke les secrets qui sont utilisés par les cibles de calcul, ainsi que d’autres informations sensibles dont a besoin l’espace de travail.

Vous pouvez partager un espace de travail avec d’autres utilisateurs.

## <a name="studio"></a>Studio

[Azure Machine Learning Studio](https://ml.azure.com) fournit une vue Web de tous les artefacts de votre espace de travail.  Ce portail est également l’emplacement où vous accédez aux outils interactifs qui font partie de Azure Machine Learning :

+ [Concepteur Azure Machine Learning (préversion)](concept-designer.md) pour effectuer les étapes de workflow sans écrire de code
+ Expérience Web pour le [Machine Learning automatisé](concept-automated-ml.md)
+ [Projets d’étiquetage des données](how-to-create-labeling-projects.md) pour créer, gérer et surveiller des projets afin d’étiqueter vos données

##  <a name="computes"></a>Calcul

<a name="compute-targets"></a> Une [cible de calcul](concept-compute-target.md) est la machine ou l’ensemble de machines que vous utilisez pour exécuter votre script de formation ou pour héberger votre déploiement de service. Cet emplacement peut être votre machine locale ou une ressource de calcul distante.

Azure Machine Learning introduit deux ressources de calcul entièrement gérées basées dans le cloud qui sont configurées pour les tâches Machine Learning :

* <a name="compute-instance"></a> **Instance de calcul** ([computeinstance](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computeinstance?view=azure-ml-py)) : Une instance de calcul est une machine virtuelle (VM) qui comprend plusieurs outils et environnements installés pour le Machine Learning. Utilisez une instance de calcul comme station de travail de développement pour commencer à exécuter des exemples de bloc-notes sans configuration requise. Peut également être utilisée comme cible de calcul pour les travaux de formation et d’inférence.
* **Clusters de calcul** ([amlcompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py)) : Cluster de machines virtuelles avec des fonctionnalités de mise à l’échelle à plusieurs nœuds. Est automatiquement mise à l’échelle lors de l’envoi d’un travail. Mieux adaptée aux cibles de calcul pour les travaux de grande taille et la production. À utiliser comme cible de calcul de formation ou pour le déploiement de développement/test.

Pour plus d’informations sur les cibles de calcul de formation, consultez [Cibles de calcul de formation](concept-compute-target.md#train).  Pour plus d’informations sur les cibles de calcul de déploiement, consultez [Cibles de déploiement](concept-compute-target.md#deploy).

## <a name="datasets-and-datastores"></a>Jeux de données et magasins de données

Les [**jeux de données Azure Machine Learning**](concept-data.md#datasets) facilitent l’accès aux données et l’utilisation de ces dernières. Les jeux de données gèrent les données dans divers scénarios tels que l’entraînement de modèles et la création de pipelines. À l’aide du SDK Azure Machine Learning, vous pouvez accéder au stockage sous-jacent, explorer des données et gérer le cycle de vie de différentes définitions de jeu de données.

Les jeux de données fournissent des méthodes pour manipuler des données dans des formats courants, comme l’utilisation de `from_delimited_files()` ou `to_pandas_dataframe()`.

Pour plus d’informations, consultez [Créer et inscrire des jeux de données Azure Machine Learning](how-to-create-register-datasets.md).  Pour obtenir plus d’exemples d’utilisation de jeux de données, consultez les [exemples de notebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/datasets-tutorial).

Un [**magasin de données**](concept-data.md#datastores) est une abstraction de stockage d’un compte de stockage Azure. Chaque espace de travail comprend un magasin de données par défaut, et peut inscrire des magasins de données supplémentaires. Utilisez l’API du SDK Python ou l’interface CLI Azure Machine Learning pour stocker et récupérer des fichiers à partir du magasin de données. 

## <a name="models"></a>Modèles

Le modèle le plus simple est un morceau de code qui accepte une entrée et produit une sortie. La création d’un modèle Machine Learning implique la sélection d’un algorithme auquel vous devez fournir des données, ainsi que l’[optimisation des hyperparamètres](how-to-tune-hyperparameters.md). L’entraînement est un processus itératif qui génère un modèle entraîné, lequel encapsule ce qu’il a appris au cours du processus d’entraînement.

Un modèle est généré par l’[exécution](#runs) d’une [expérience](#experiments) effectuée dans Azure Machine Learning. Vous pouvez également utiliser un modèle qui est entraîné en dehors d’Azure Machine Learning. Vous devez ensuite [inscrire le modèle](#register-model) dans l’espace de travail.

Azure Machine Learning est indépendant de tout framework. Lorsque vous créez un modèle, vous pouvez utiliser un des frameworks Machine Learning populaires, tels que Scikit-learn, XGBoost, PyTorch, TensorFlow et Chainer.

Pour obtenir un exemple de formation d’un modèle à l’aide de Scikit-learn, consultez [Didacticiel : Entraîner un modèle de classification d’images avec Azure Machine Learning](tutorial-train-models-with-aml.md)

### <a name="model-registry"></a><a name="register-model"></a> Registre de modèles
[Espace de travail](#workspace) > **Registre du modèle**

Le **registre de modèles** effectue le suivi de tous les modèles de votre espace de travail Azure Machine Learning.

Les modèles sont identifiés par leur nom et par leur version. Chaque fois que vous inscrivez un modèle portant le même nom qu’un modèle existant, le registre suppose qu’il s’agit d’une nouvelle version. La version est incrémentée et le nouveau modèle est inscrit sous le même nom.

Lorsque vous inscrivez le modèle, vous pouvez fournir des étiquettes de métadonnées supplémentaires, puis utiliser les étiquettes lorsque vous recherchez des modèles.

> [!TIP]
> Un modèle inscrit est un conteneur logique pour un ou plusieurs fichiers qui composent votre modèle. Par exemple, si vous avez un modèle qui est stocké dans plusieurs fichiers, vous pouvez inscrire ces derniers en tant que modèle unique dans votre espace de travail Azure Machine Learning. Après l’inscription, vous pouvez ensuite télécharger ou déployer le modèle inscrit et recevoir tous les fichiers qui ont été inscrits.

Vous ne pouvez pas supprimer un modèle inscrit qui est utilisé par un déploiement actif.

Pour obtenir un exemple d’inscription de modèle, consultez [Entraîner un modèle de classification d’images avec Azure Machine Learning](tutorial-train-models-with-aml.md).


### <a name="environments"></a>Environnements

[Espace de travail](#workspace) > **Environnements**

Un [environnement](concept-environments.md) est l’encapsulation de l’environnement dans lequel se produit l’apprentissage ou le score de votre modèle de Machine Learning. L’environnement spécifie les packages, variables d’environnement et paramètres logiciels Python autour de vos scripts d’apprentissage et de scoring.

Pour des exemples de code, consultez la section « Gérer les environnements » de [Comment utiliser les environnements](how-to-use-environments.md#manage-environments).

### <a name="experiments"></a>Expériences

[Espace de travail](#workspace) > **Expériences**

Une expérience est un regroupement d’exécutions provenant d’un script spécifié. Elle appartient toujours à un espace de travail. Lorsque vous envoyez une exécution, vous fournissez un nom pour l’expérience. Les informations concernant l’exécution sont stockées sous cette expérience. Si le nom n’existe pas lorsque vous soumettez une expérience, une nouvelle expérience est automatiquement créée.

Pour obtenir un exemple de l’utilisation d’une expérience, consultez le [Tutoriel : Entraîner votre premier modèle](tutorial-1st-experiment-sdk-train.md).

### <a name="runs"></a>Exécutions

[Espace de travail](#workspace) > [Expériences](#experiments) > **Exécution**

Une exécution est une exécution unique d’un script de formation. Une expérience contient généralement plusieurs exécutions.

Azure Machine Learning enregistre toutes les exécutions et stocke les informations suivantes dans l’expérience :

* Les métadonnées relatives à l’exécution (horodatage, durée, et ainsi de suite)
* Les métriques qui sont journalisées par votre script
* Les fichiers de sortie qui sont collectés automatiquement par l’expérience ou chargés explicitement par l’utilisateur
* Un instantané du répertoire qui contient vos scripts, avant l’exécution

Vous déclenchez une exécution lorsque vous envoyez un script pour entraîner un modèle. Une exécution peut avoir zéro, une ou plusieurs exécutions enfants. Par exemple, l’exécution de niveau supérieur peut avoir deux exécutions enfants, et chacune d’elles peut avoir sa propre exécution enfant.

### <a name="run-configurations"></a>Configurations de séries de tests

[Espace de travai](#workspace)l  > [Expériences](#experiments) > [Exécution](#runs) > **Exécuter la configuration**

Une configuration d’exécution est un ensemble d’instructions qui définit la façon dont un script doit être exécuté dans une cible de calcul spécifiée. La spécification comprend un vaste ensemble de définitions de comportement, par exemple, s’il faut utiliser un environnement Python existant ou utiliser un environnement Conda créé à partir des spécifications.

Une configuration d’exécution peut être rendue persistante dans un fichier du répertoire qui contient votre script d’entraînement.   Elle peut aussi être construite comme un objet en mémoire et utilisée pour envoyer une exécution.

Pour obtenir des exemples de configurations d’exécutions, consultez [Sélectionner et utiliser une cible de calcul pour entraîner votre modèle](how-to-set-up-training-targets.md).

### <a name="estimators"></a>Estimateurs

Pour faciliter la formation de modèle avec des infrastructures populaires, la classe d’estimateurs vous permet de construire facilement des configurations d’exécution. Vous pouvez créer et utiliser un [estimateur](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) générique pour envoyer des scripts d’apprentissage qui utilisent toute infrastructure de formation que vous choisissez (comme scikit-Learn).

Pour les tâches PyTorch, TensorFlow et Chainer, Azure Machine Learning fournit également les estimateurs [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) et [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py), respectivement, qui simplifient l’utilisation de ces infrastructures.

Pour plus d’informations, consultez les articles suivants :

* [Entraîner des modèles ML avec des estimateurs](how-to-train-ml-models.md).
* [Former des modèles de Deep Learning Pytorch à l’échelle avec Azure Machine Learning](how-to-train-pytorch.md).
* [Entraîner et inscrire des modèles TensorFlow à l’échelle avec Azure Machine Learning](how-to-train-tensorflow.md).
* [Entraîner et inscrire des modèles Chainer à l’échelle avec Azure Machine Learning](how-to-train-ml-models.md).

### <a name="snapshots"></a>Instantanés

[Espace de travail](#workspace) > [Expériences](#experiments) > [Exécution](#runs) > **Instantané**

Lorsque vous envoyez une exécution, Azure Machine Learning compresse le répertoire qui contient le script dans un fichier zip, puis l’envoie à la cible de calcul. Le fichier zip est ensuite décompressé, et le script y est exécuté. Azure Machine Learning stocke également le fichier zip en tant qu’instantané dans l’enregistrement d’exécution. Toute personne ayant accès à l’espace de travail peut parcourir un enregistrement d’exécution et télécharger l’instantané.


### <a name="logging"></a>Journalisation

Lorsque vous développez votre solution, utilisez le SDK Python Azure Machine Learning dans votre script Python pour journaliser les métriques arbitraires. Après l’exécution, interrogez les métriques pour vérifier si celle-ci a produit le modèle que vous souhaitez déployer.


> [!NOTE]
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]

### <a name="git-tracking-and-integration"></a>Intégration et suivi Git

Lorsque vous lancez une exécution d’entraînement où le répertoire source est un répertoire Git local, les informations relatives au répertoire sont stockées dans l’historique des exécutions. Cela fonctionne avec des exécutions envoyées à l’aide d’un estimateur, d’un pipeline ML ou d’un script exécuté. Cela fonctionne également pour les exécutions soumises à partir du SDK ou de l’interface CLI Machine Learning.

Pour plus d’informations, consultez [Obtenir une intégration pour Azure Machine Learning](concept-train-model-git-integration.md).

## <a name="deployment"></a>Déploiement

Vous déployez un [modèle inscrit](#register-model) en tant que point de terminaison de service. Vous avez besoin des composants suivants :

* **Environnement**. Cet environnement encapsule les dépendances requises pour exécuter votre modèle pour l’inférence.
* **Code de scoring**. Ce script accepte les requêtes, calcule le score de la requête à l’aide du modèle et retourne les résultats.
* **Configuration de l’inférence**. La configuration de l’inférence spécifie l’environnement, le script d’entrée et d’autres composants nécessaires pour exécuter le modèle en tant que service.

Pour plus d’informations sur ces composants, consultez [Déployer des modèles avec Azure Machine Learning](how-to-deploy-and-where.md).

### <a name="endpoints"></a>Points de terminaison

[Espace de travail](#workspace) > **Points de terminaison**

Un point de terminaison est une instanciation de votre modèle soit dans un service web pouvant être hébergé dans le cloud, soit dans un module IoT pour les déploiements d’appareils intégrés.

#### <a name="web-service-endpoint"></a>Point de terminaison de service Web

Lors du déploiement d’un modèle en tant que service web, le point de terminaison peut être déployé sur Azure Container Instances, Azure Kubernetes Azure ou des FPGA. Vous créez le service à partir d’une image, de votre script et des fichiers associés. Ceux-ci sont placés dans une image de conteneur de base qui contient l’environnement d’exécution du modèle. L’image a un point de terminaison HTTP à charge équilibrée qui reçoit les requêtes de scoring qui sont envoyées au service web.

Vous pouvez activer la télémétrie Application Insights ou la télémétrie de modèle pour surveiller votre service web. Les données de télémétrie ne sont accessibles qu’à vous.  Elles sont stockées dans votre Application Insights et les instances de votre compte de stockage.

Si vous avez activé la mise à l’échelle automatique, Azure met automatiquement à l’échelle votre déploiement.

Pour obtenir un exemple de déploiement de modèle en tant que service, consultez [Déployer un modèle de classification d’images dans Azure Container Instances](tutorial-deploy-models-with-aml.md).

#### <a name="iot-module-endpoints"></a>Points de terminaison de module IoT

Un point de terminaison de module IoT déployé est un conteneur Docker qui inclut votre modèle, ainsi que le script ou l’application associés et toutes les dépendances supplémentaires. Vous déployez ces modules à l’aide d’Azure IoT Edge sur les périphériques en mode Edge.

Si vous avez activé la supervision, Azure collecte les données de télémétrie à partir du modèle qui se trouve dans le module Azure IoT Edge. Vous seul pouvez accéder aux données de télémétrie qui sont stockées dans votre instance de compte de stockage.

Azure IoT Edge garantit l’exécution de votre module et supervise l’appareil qui l’héberge.
. 
## <a name="automation"></a>Automatisation

### <a name="azure-machine-learning-cli"></a>Interface CLI Azure Machine Learning 

L'interface CLI Azure Machine Learning est une extension pour l'[interface Azure](reference-azure-machine-learning-cli.md), une interface de ligne de commande multiplateforme pour la plateforme Azure. Cette extension fournit des commandes pour automatiser vos activités de Machine Learning.

### <a name="ml-pipelines"></a>Pipelines ML

Vous utilisez des [pipelines de machine learning](concept-ml-pipelines.md) pour créer et gérer des flux de travail qui combinent les phases de machine learning. Par exemple, un pipeline peut inclure les phases de préparation des données, d’entraînement du modèle, de déploiement du modèle et d’inférence/scoring. Chaque phase peut englober plusieurs étapes, chacune d’elles pouvant s’exécuter sans assistance dans différentes cibles de calcul. 

Les étapes de pipeline sont réutilisables et peuvent être exécutées sans avoir à réexécuter les étapes précédentes si la sortie de ces étapes n’a pas changé. Par exemple, vous pouvez réentraîner un modèle sans réexécuter les étapes coûteuses de préparation des données si celles-ci n’ont pas été modifiées. Les pipelines permettent également aux scientifiques des données de collaborer tout en travaillant chacun sur une partie différente du workflow de Machine Learning.

## <a name="interacting-with-machine-learning"></a>Interaction avec le Machine Learning

> [!IMPORTANT]
> Les outils marqués (préversion) ci-dessous sont actuellement en préversion publique.
> La préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail en production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

+  Interagissez avec le service dans un environnement Python avec le [SDK Azure Machine Learning pour Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
+ Interagissez avec le service dans un environnement R avec le [Kit de développement logiciel (SDK) Azure Machine Learning pour R](https://azure.github.io/azureml-sdk-for-r/reference/index.html) (préversion).
+ Utilisez le [Concepteur Azure Machine Learning (préversion)](concept-designer.md) pour effectuer les étapes de workflow sans écrire de code. Un [Espace de travail d’entreprise](concept-workspace.md#upgrade) est requis pour utiliser le concepteur.
+ Utiliser l’[interface de ligne de commande d’Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/reference-azure-machine-learning-cli) pour l’automatisation.
+ L’[accélérateur de solution de nombreux modèles](https://aka.ms/many-models) (préversion) s’appuie sur Azure Machine Learning et vous permet d’effectuer l’apprentissage, l’utilisation et la gestion de centaines, voire de milliers de modèles Machine Learning.

## <a name="next-steps"></a>Étapes suivantes

Pour bien démarrer avec Azure Machine Learning, voir :

* [Qu'est-ce que Microsoft Azure Machine Learning ?](overview-what-is-azure-ml.md)
* [Création d’un espace de travail Microsoft Azure Machine Learning](how-to-manage-workspace.md)
* [Tutoriel (partie 1) : Effectuer l'apprentissage d’un modèle](tutorial-train-models-with-aml.md)
