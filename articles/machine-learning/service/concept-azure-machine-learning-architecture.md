---
title: Architecture et concepts clés
titleSuffix: Azure Machine Learning service
description: En savoir plus sur l’architecture, les termes, les concepts et le workflow qui composent Azure Machine Learning Service.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 07/12/2019
ms.custom: seodec18
ms.openlocfilehash: 59ce6719c117db53b02ed6594de219010ee08ee6
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68828238"
---
# <a name="how-azure-machine-learning-service-works-architecture-and-concepts"></a>Voici comment Azure Machine Learning service fonctionne : Architecture et concepts

En savoir plus sur l’architecture, les concepts et le workflow pour Azure Machine Learning Service. Le diagramme suivant représente les principaux composants du service et illustre le workflow général lors de l’utilisation du service :

![Workflow et architecture Azure Machine Learning Service](./media/concept-azure-machine-learning-architecture/workflow.png)

## <a name="workflow"></a>Workflow

Le workflow du modèle Machine Learning suit généralement cette séquence :

1. **Entraîner**
    + Développez des scripts d’entraînement Machine Learning avec **Python** ou l’interface visuelle.
    + Créez et configurez une **cible de calcul**.
    + **Envoyez les scripts** à la cible de calcul configurée en vue de leur exécution dans cet environnement. Pendant l’entraînement, les scripts peuvent lire ou écrire dans un **magasin de données**. De plus, les enregistrements de l’exécution sont enregistrés en tant que **séries** dans l’**espace de travail** et regroupés dans des **expériences**.

1. **Empaqueter** - Une fois qu’une exécution satisfaisante est trouvée, inscrivez le modèle persistant dans le **registre de modèles**.

1. **Valider** - **Interrogez l’expérience** pour obtenir les métriques journalisées relatives aux exécutions actuelles et passées. Si les métriques ne montrent pas le résultat souhaité, retournez à l’étape 1, puis itérez vos scripts.

1. **Déployer** : développez un script de scoring qui utilise le modèle et **déployez le modèle** en tant que **service web** dans Azure, ou sur un **appareil IoT Edge**.

1. **Surveiller** : surveillez la **dérive de données** entre le jeu de données d’entraînement et les données d’inférence d’un modèle déployé. Lorsque cela est nécessaire, revenez à l’étape 1 pour réentraîner le modèle avec de nouvelles données d’entraînement.

## <a name="tools-for-azure-machine-learning"></a>Outils pour Azure Machine Learning

Utilisez ces outils pour Azure Machine Learning :

+  Interagissez avec le service dans un environnement Python avec le [SDK Azure Machine Learning pour Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
+ Automatisez vos activités Machine Learning avec l’[interface CLI Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/reference-azure-machine-learning-cli).
+ Écrivez du code dans Visual Studio Code avec l’[extension Azure Machine Learning VS Code](how-to-vscode-tools.md)
+ Utilisez l’[interface visuelle (préversion) pour Azure Machine Learning Service](ui-concept-visual-interface.md) pour effectuer les étapes de workflow sans écrire de code.

## <a name="glossary-of-concepts"></a>Glossaire des concepts

+ <a href="#workspaces">Espace de travail</a>
+ <a href="#experiments">Expériences</a>
+ <a href="#models">Modèles</a>
+ <a href="#run-configurations">Configuration de série de tests</a>
+ <a href="#datasets-and-datastores">Jeu de données et magasins de données</a>
+ <a href="#compute-targets">Cibles de calcul</a>
+ <a href="#training-scripts">Script d’entraînement</a>
+ <a href="#runs">Run</a>
+ <a href="#github-tracking-and-integration">Suivi Git</a>
+ <a href="#snapshots">Instantané</a>
+ <a href="#activities">Activité</a>
+ <a href="#images">Image</a>
+ <a href="#deployment">Déploiement</a>
+ <a href="#web-service-deployments">Services web</a>
+ <a href="#iot-module-deployments">Modules IoT</a>
+ <a href="#ml-pipelines">Pipelines ML</a>
+ <a href="#logging">Logging</a>

> [!NOTE]
> Cet article définit les termes et les concepts utilisés par le service Azure Machine Learning, et non pas ceux relatifs à la plateforme Azure. Pour plus d’informations sur la terminologie relative à la plateforme Azure, consultez le [glossaire Microsoft Azure](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology).


### <a name="workspaces"></a>Workspaces

L’[espace de travail](concept-workspace.md) est la ressource de niveau supérieur du service Azure Machine Learning. Il fournit un emplacement centralisé dans lequel utiliser tous les artefacts que vous créez lorsque vous utilisez le service Azure Machine Learning.

Le diagramme suivant représente une taxonomie de l’espace de travail :

[![Taxonomie de l’espace de travail](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png)](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png#lightbox)

Pour plus d’informations sur les espaces de travail, consultez [Présentation d’un espace de travail Azure Machine Learning](concept-workspace.md).

### <a name="experiments"></a>Expériences

Une expérience est un regroupement d’exécutions provenant d’un script spécifié. Elle appartient toujours à un espace de travail. Lorsque vous envoyez une exécution, vous fournissez un nom pour l’expérience. Les informations concernant l’exécution sont stockées sous cette expérience. Si vous envoyez une exécution et spécifiez un nom d’expérience qui n’existe pas, une nouvelle expérience portant ce nom nouvellement spécifié est automatiquement créée.

Pour obtenir un exemple de l’utilisation d’une expérience, consultez le [Tutoriel : Entraîner votre premier modèle](tutorial-1st-experiment-sdk-train.md).

### <a name="models"></a>Modèles

Le modèle le plus simple est un morceau de code qui accepte une entrée et produit une sortie. La création d’un modèle Machine Learning implique la sélection d’un algorithme auquel vous devez fournir des données, ainsi que l’optimisation des hyperparamètres. L’entraînement est un processus itératif qui génère un modèle entraîné, lequel encapsule ce qu’il a appris au cours du processus d’entraînement.

Un modèle est généré par une exécution effectuée dans Azure Machine Learning. Vous pouvez également utiliser un modèle qui est entraîné en dehors d’Azure Machine Learning. Vous pouvez inscrire un modèle dans un espace de travail Azure Machine Learning service.

Le service Azure Machine Learning est indépendant de l’architecture. Lorsque vous créez un modèle, vous pouvez utiliser un des frameworks Machine Learning populaires, tels que Scikit-learn, XGBoost, PyTorch, TensorFlow et Chainer.

Pour obtenir un exemple d’entraînement d’un modèle, consultez le [Tutoriel : Effectuer l’apprentissage d’un modèle de classification d’images avec Azure Machine Learning Service](tutorial-train-models-with-aml.md).

Le **registre de modèles** garde une trace de tous les modèles de votre espace de travail Azure Machine Learning Service.

Les modèles sont identifiés par leur nom et par leur version. Chaque fois que vous inscrivez un modèle portant le même nom qu’un modèle existant, le registre suppose qu’il s’agit d’une nouvelle version. La version est incrémentée et le nouveau modèle est inscrit sous le même nom.

Lorsque vous inscrivez le modèle, vous pouvez fournir des étiquettes de métadonnées supplémentaires, puis utiliser les étiquettes lorsque vous recherchez des modèles.

> [!TIP]
> Un modèle inscrit est un conteneur logique pour un ou plusieurs fichiers qui composent votre modèle. Par exemple, si vous avez un modèle qui est stocké dans plusieurs fichiers, vous pouvez inscrire ces derniers en tant que modèle unique dans votre espace de travail Azure Machine Learning. Après l’inscription, vous pouvez ensuite télécharger ou déployer le modèle inscrit et recevoir tous les fichiers qui ont été inscrits.

Vous ne pouvez pas supprimer un modèle inscrit qui est utilisé par un déploiement actif.

Pour obtenir un exemple d’inscription de modèle, consultez [Entraîner un modèle de classification d’images avec Azure Machine Learning](tutorial-train-models-with-aml.md).

### <a name="run-configurations"></a>Configurations de séries de tests

Une configuration d’exécution est un ensemble d’instructions qui définit la façon dont un script doit être exécuté dans une cible de calcul spécifiée. La spécification comprend un vaste ensemble de définitions de comportement, par exemple, s’il faut utiliser un environnement Python existant ou utiliser un environnement Conda créé à partir des spécifications.

Une configuration d’exécution peut être rendue persistante dans un fichier du répertoire qui contient votre script d’entraînement, ou peut être construite comme un objet en mémoire et utilisée pour envoyer une exécution.

Pour obtenir des exemples de configurations d’exécutions, consultez [Sélectionner et utiliser une cible de calcul pour entraîner votre modèle](how-to-set-up-training-targets.md).

### <a name="datasets-and-datastores"></a>Jeux de données et magasins de données

Les **jeux de données Azure Machine Learning** (préversion) facilitent l’accès aux données et l’utilisation de ces dernières. Les jeux de données gèrent les données dans divers scénarios tels que l’entraînement de modèles et la création de pipelines. À l’aide du SDK Azure Machine Learning, vous pouvez accéder au stockage sous-jacent, explorer et préparer des données, gérer le cycle de vie de différentes définitions de jeu de données et comparer les jeux de données utilisés dans l’entraînement et en production.

Les jeux de données fournissent des méthodes pour manipuler des données dans des formats courants, comme l’utilisation de `from_delimited_files()` ou `to_pandas_dataframe()`.

Pour plus d’informations, consultez [Créer et inscrire des jeux de données Azure Machine Learning](how-to-create-register-datasets.md).  Pour obtenir plus d’exemples d’utilisation de jeux de données, consultez les [exemples de notebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/work-with-data/datasets).

Un **magasin de données** est une abstraction de stockage d’un compte de stockage Azure. Le magasin de données peut utiliser un conteneur d’objets blob Azure ou un partage de fichiers Azure en tant que stockage backend. Chaque espace de travail comprend un magasin de données par défaut, et peut inscrire des magasins de données supplémentaires. Utilisez l’API du SDK Python ou l’interface CLI Azure Machine Learning pour stocker et récupérer des fichiers à partir du magasin de données.

### <a name="compute-targets"></a>Cibles de calcul

Une [cible de calcul](concept-compute-target.md) permet de spécifier la ressource de calcul que vous utilisez pour exécuter votre script d’entraînement ou pour héberger votre déploiement de service. Cet emplacement peut être votre machine locale ou une ressource de calcul basée sur le cloud. Les cibles de calcul facilitent la modification de votre environnement de calcul sans modifier votre code.

En savoir plus sur les [cibles de calcul disponibles pour l’entraînement et le déploiement](concept-compute-target.md).

### <a name="training-scripts"></a>Scripts d’entraînement

Pour entraîner un modèle, vous devez spécifier le répertoire qui contient le script d’entraînement et les fichiers associés. Vous pouvez également spécifier un nom pour l’expérience, qui est utilisée pour stocker les informations qui sont collectées au cours de l’entraînement. Pendant l’entraînement, l’intégralité du répertoire est copiée dans l’environnement d’entraînement (la cible de calcul) et le script qui est spécifié par la configuration d’exécution est démarré. Un instantané du répertoire est également stocké sous l’expérience, dans l’espace de travail.

Pour obtenir un exemple, consultez [Didacticiel : Effectuer l’apprentissage d’un modèle de classification d’images avec Azure Machine Learning Service](tutorial-train-models-with-aml.md).

### <a name="runs"></a>Exécutions

Une exécution est un enregistrement qui contient les informations suivantes :

* Les métadonnées relatives à l’exécution (horodatage, durée, et ainsi de suite)
* Les métriques qui sont journalisées par votre script
* Les fichiers de sortie qui sont collectés automatiquement par l’expérience ou chargés explicitement par l’utilisateur
* Un instantané du répertoire qui contient vos scripts, avant l’exécution

Vous déclenchez une exécution lorsque vous envoyez un script pour entraîner un modèle. Une exécution peut avoir zéro, une ou plusieurs exécutions enfants. Par exemple, l’exécution de niveau supérieur peut avoir deux exécutions enfants, et chacune d’elles peut avoir sa propre exécution enfant.


### <a name="github-tracking-and-integration"></a>Intégration et suivi GitHub

Lorsque vous lancez une exécution d’entraînement où le répertoire source est un répertoire Git local, les informations relatives au répertoire sont stockées dans l’historique des exécutions. Par exemple, l’ID de validation en cours pour le dépôt est consigné au sein de l’historique. Cela fonctionne avec des exécutions envoyées à l’aide d’un estimateur, d’un pipeline ML ou d’un script exécuté. Cela fonctionne également pour les exécutions soumises à partir du SDK ou de l’interface CLI Machine Learning.

### <a name="snapshots"></a>Instantanés

Lorsque vous envoyez une exécution, Azure Machine Learning compresse le répertoire qui contient le script dans un fichier zip, puis l’envoie à la cible de calcul. Le fichier zip est ensuite décompressé, et le script y est exécuté. Azure Machine Learning stocke également le fichier zip en tant qu’instantané dans l’enregistrement d’exécution. Toute personne ayant accès à l’espace de travail peut parcourir un enregistrement d’exécution et télécharger l’instantané.

> [!NOTE]
> Pour empêcher que les fichiers inutiles soient inclus dans l’instantané, créez un fichier « ignore» (.gitignore ou .amlignore). Placez ce fichier dans le répertoire des instantanés et ajoutez les noms de fichiers à ignorer qu’il contient. Le fichier .amlignore utilise les mêmes modèles et syntaxe [ que le fichier .gitignore](https://git-scm.com/docs/gitignore). Si les deux fichiers existent, le fichier .amlignore est prioritaire.

### <a name="activities"></a>Activités

Une activité représente une opération de longue durée. Les opérations suivantes sont des exemples d’activités :

* Création ou suppression d’une cible de calcul
* Exécution d’un script sur une cible de calcul

Les activités peuvent envoyer des notifications via le SDK ou l’interface utilisateur web, ce qui vous permet de superviser facilement la progression de ces opérations.

### <a name="images"></a>Images

Les images constituent un moyen fiable de déployer un modèle et tous les composants dont vous avez besoin pour son utilisation. Une image contient les éléments suivants :

* Un modèle
* Un script ou une application de scoring. Vous utilisez le script pour passer l’entrée au modèle et retourner la sortie du modèle.
* Les dépendances dont ont besoin le modèle ou l’application/le script de scoring. Par exemple, vous pouvez inclure un fichier d’environnement Conda qui répertorie les dépendances du package Python.

Azure Machine Learning peut créer deux types d’image :

* **Image FPGA** : Utilisée lorsque vous effectuez un déploiement vers un tableau FPGA (field programmable gate array) dans Azure.
* **Image Docker** : Utilisée lorsque vous effectuez un déploiement vers des cibles de calcul autres que les tableaux FPGA. Il peut s’agir, par exemple, d’Azure Container Instances et d’Azure Kubernetes Service.

Azure Machine Learning Service fournit une image de base, qui est utilisée par défaut. Vous pouvez également fournir vos propres images personnalisées.

### <a name="image-registry"></a>Registre d’images

Les images sont cataloguées dans le **registre d’images** dans votre espace de travail. Vous pouvez fournir des catégories de métadonnées supplémentaires lorsque vous créez l’image. Vous pouvez ensuite les interroger plus tard pour rechercher votre image.

Pour obtenir un exemple de création d’image, consultez [Déployer un modèle de classification d’images dans Azure Container Instances](tutorial-deploy-models-with-aml.md).

Pour obtenir un exemple de déploiement d’un modèle à l’aide d’une image personnalisée, consultez [Déployer un modèle à l’aide d’une image Docker personnalisée](how-to-deploy-custom-docker-image.md).

### <a name="deployment"></a>Déploiement

Un déploiement est une instanciation de votre modèle soit dans un service web pouvant être hébergé dans le cloud, soit dans un module IoT pour les déploiements d’appareils intégrés.

#### <a name="web-service-deployments"></a>Déploiement de services web

Un service web déployé peut utiliser Azure Container Instances, Azure Kubernetes Service ou des tableaux FPGA. Vous créez le service à partir d’une image, de votre script et des fichiers associés. Ceux-ci sont encapsulés dans une image, qui fournit l’environnement d’exécution pour le service web. L’image a un point de terminaison HTTP à charge équilibrée qui reçoit les requêtes de scoring qui sont envoyées au service web.

Azure vous permet de superviser le déploiement de votre service web en collectant les données de télémétrie Application Insights ou les données de télémétrie des modèles, si vous avez choisi d’activer cette fonctionnalité. Vous seul pouvez accéder aux données de télémétrie qui sont stockées dans votre instance Application Insights et votre instance de compte de stockage.

Si vous avez activé la mise à l’échelle automatique, Azure met automatiquement à l’échelle votre déploiement.

Pour obtenir un exemple de déploiement de modèle en tant que service, consultez [Déployer un modèle de classification d’images dans Azure Container Instances](tutorial-deploy-models-with-aml.md).

#### <a name="iot-module-deployments"></a>Déploiements de module IoT

Un module IoT déployé est un conteneur Docker qui inclut votre modèle, ainsi que le script ou l’application associés et toutes les dépendances supplémentaires. Vous déployez ces modules à l’aide d’Azure IoT Edge sur les périphériques en mode Edge.

Si vous avez activé la supervision, Azure collecte les données de télémétrie à partir du modèle qui se trouve dans le module Azure IoT Edge. Vous seul pouvez accéder aux données de télémétrie qui sont stockées dans votre instance de compte de stockage.

Azure IoT Edge garantit l’exécution de votre module et supervise l’appareil qui l’héberge.

### <a name="ml-pipelines"></a>Pipelines ML

Vous utilisez des pipelines de machine learning pour créer et gérer des flux de travail qui combinent les phases de machine learning. Par exemple, un pipeline peut inclure les phases de préparation des données, d’entraînement du modèle, de déploiement du modèle et d’inférence/scoring. Chaque phase peut englober plusieurs étapes, chacune d’elles pouvant s’exécuter sans assistance dans différentes cibles de calcul. 

Les étapes de pipeline sont réutilisables et peuvent être exécutées sans avoir à réexécuter les étapes suivantes si la sortie de l’étape en question n’a pas changé. Par exemple, vous pouvez réentraîner un modèle sans réexécuter les étapes coûteuses de préparation des données si celles-ci n’ont pas été modifiées. Les pipelines permettent également aux scientifiques des données de collaborer tout en travaillant chacun sur une partie différente du workflow de Machine Learning.

Pour plus d’informations sur les pipelines Machine Learning disposant de ce service, consultez [Pipelines et Azure Machine Learning](concept-ml-pipelines.md).

### <a name="logging"></a>Journalisation

Lorsque vous développez votre solution, utilisez le SDK Python Azure Machine Learning dans votre script Python pour journaliser les métriques arbitraires. Après l’exécution, interrogez les métriques pour vérifier si celle-ci a produit le modèle que vous souhaitez déployer.

### <a name="next-steps"></a>Étapes suivantes

Pour prendre en main le service Azure Machine Learning, consultez :

* [Qu’est-ce que le service Azure Machine Learning ?](overview-what-is-azure-ml.md)
* [Créer un espace de travail Azure Machine Learning service](setup-create-workspace.md)
* [Tutoriel (partie 1) : Effectuer l'apprentissage d’un modèle](tutorial-train-models-with-aml.md)
