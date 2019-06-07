---
title: Architecture et les principaux concepts
titleSuffix: Azure Machine Learning service
description: En savoir plus sur l’architecture, les termes du contrat, les concepts et les flux de travail qui composent le service Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 04/15/2019
ms.custom: seodec18
ms.openlocfilehash: f369f899d4a383205ad124e4fcd8dabf9f92f63f
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/07/2019
ms.locfileid: "66753190"
---
# <a name="how-azure-machine-learning-service-works-architecture-and-concepts"></a>Voici comment Azure Machine Learning service fonctionne : Architecture et concepts

En savoir plus sur l’architecture, les concepts et les flux de travail pour le service Azure Machine Learning. Le diagramme suivant représente les principaux composants du service et illustre le workflow général lors de l’utilisation du service :

[![Architecture et workflow du service Azure Machine Learning](./media/concept-azure-machine-learning-architecture/workflow.png)](./media/concept-azure-machine-learning-architecture/workflow.png#lightbox)

## <a name="workflow"></a>Workflow

Le flux de travail machine learning suit généralement cette séquence :

1. Développer l’apprentissage, formation de scripts dans **Python** ou avec l’interface visuelle.
1. Créez et configurez une **cible de calcul**.
1. **Envoyez les scripts** à la cible de calcul configurée en vue de leur exécution dans cet environnement. Pendant l’entraînement, les scripts peuvent lire ou écrire dans un **magasin de données**. De plus, les enregistrements de l’exécution sont enregistrés en tant que **séries** dans l’**espace de travail** et regroupés dans des **expériences**.
1. **Interrogez cette expérience** pour obtenir les métriques journalisées relatives aux exécutions actuelles et passées. Si les métriques ne montrent pas le résultat souhaité, retournez à l’étape 1, puis itérez vos scripts.
1. Une fois qu’une exécution satisfaisante est trouvée, inscrivez le modèle persistant dans le **registre de modèles**.
1. Développer un script de notation qui utilise le modèle et **déployer le modèle** comme un **service web** dans Azure, ou à un **appareil IoT Edge**.

Vous effectuez ces étapes avec les éléments suivants :
+ [SDK Azure Machine Learning pour Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
+ [Azure Machine Learning CLI](https://docs.microsoft.com/azure/machine-learning/service/reference-azure-machine-learning-cli)
+ [Extension de Machine Learning VS Code Azure](how-to-vscode-tools.md)
+  Le [interface visuelle (version préliminaire) pour le service Azure Machine Learning](ui-concept-visual-interface.md)

> [!NOTE]
> Cet article définit les termes et les concepts utilisés par le service Azure Machine Learning, et non pas ceux relatifs à la plateforme Azure. Pour plus d’informations sur la terminologie relative à la plateforme Azure, consultez le [glossaire Microsoft Azure](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology).

## <a name="workspace"></a>Espace de travail

[L’espace de travail](concept-workspace.md) est la ressource de niveau supérieur pour le service Azure Machine Learning. Il fournit un emplacement centralisé dans lequel utiliser tous les artefacts que vous créez lorsque vous utilisez le service Azure Machine Learning.

Le diagramme suivant représente une taxonomie de l’espace de travail :

[![Taxonomie de l’espace de travail](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png)](./media/concept-azure-machine-learning-architecture/azure-machine-learning-taxonomy.png#lightbox)

Pour plus d’informations sur les espaces de travail, consultez [qu’est un espace de travail Azure Machine Learning ?](concept-workspace.md).

## <a name="experiment"></a>Expérience

Une expérience est un regroupement d’exécutions provenant d’un script spécifié. Elle appartient toujours à un espace de travail. Lorsque vous envoyez une exécution, vous fournissez un nom pour l’expérience. Les informations concernant l’exécution sont stockées sous cette expérience. Si vous envoyez une exécution et spécifiez un nom d’expérience qui n’existe pas, une nouvelle expérience portant ce nom nouvellement spécifié est automatiquement créée.

Pour obtenir un exemple de l’utilisation d’une expérience, consultez le [Guide de démarrage rapide : Prise en main d’Azure Machine Learning service](quickstart-run-cloud-notebook.md).

## <a name="model"></a>Modèle

Le modèle le plus simple est un morceau de code qui accepte une entrée et produit une sortie. La création d’un modèle Machine Learning implique la sélection d’un algorithme auquel vous devez fournir des données, ainsi que l’optimisation des hyperparamètres. L’entraînement est un processus itératif qui génère un modèle entraîné, lequel encapsule ce qu’il a appris au cours du processus d’entraînement.

Un modèle est généré par une exécution effectuée dans Azure Machine Learning. Vous pouvez également utiliser un modèle qui est entraîné en dehors d’Azure Machine Learning. Vous pouvez inscrire un modèle dans un espace de travail Azure Machine Learning service.

Le service Azure Machine Learning est indépendant de l’architecture. Lorsque vous créez un modèle, vous pouvez utiliser toute infrastructure d’apprentissage machine populaires, tels que Scikit-learn, XGBoost, PyTorch, TensorFlow et programme de chaînage.

Pour obtenir un exemple d’apprentissage d’un modèle, consultez [didacticiel : Effectuer l’apprentissage d’un modèle de classification d’images avec Azure Machine Learning Service](tutorial-train-models-with-aml.md).

### <a name="model-registry"></a>Registre de modèles

Le registre de modèles garde une trace de tous les modèles de l’espace de travail correspondant au service Azure Machine Learning.

Les modèles sont identifiés par leur nom et par leur version. Chaque fois que vous inscrivez un modèle portant le même nom qu’un modèle existant, le registre suppose qu’il s’agit d’une nouvelle version. La version est incrémentée et le nouveau modèle est inscrit sous le même nom.

Lorsque vous inscrivez le modèle, vous pouvez fournir des étiquettes de métadonnées supplémentaires, puis utiliser les étiquettes lorsque vous recherchez des modèles.

Vous ne pouvez pas supprimer les modèles qui sont utilisés par un déploiement actif.

Pour obtenir un exemple d’inscription de modèle, consultez [Entraîner un modèle de classification d’images avec Azure Machine Learning](tutorial-train-models-with-aml.md).

## <a name="run-configuration"></a>Configuration de série de tests

Une configuration d’exécution est un ensemble d’instructions qui définit la façon dont un script doit être exécuté dans une cible de calcul spécifiée. La spécification comprend un vaste ensemble de définitions de comportement, par exemple, s’il faut utiliser un environnement Python existant ou utiliser un environnement Conda créé à partir des spécifications.

Une configuration d’exécution peut être rendue persistante dans un fichier du répertoire qui contient votre script d’entraînement, ou peut être construite comme un objet en mémoire et utilisée pour envoyer une exécution.

Pour obtenir des exemples de configurations d’exécutions, consultez [Sélectionner et utiliser une cible de calcul pour entraîner votre modèle](how-to-set-up-training-targets.md).

## <a name="dataset"></a>Jeu de données

Azure Machine Learning jeux de données (version préliminaire) facilitent l’accéder et travailler avec vos données. Jeux de données gérer les données dans divers scénarios tels que la formation de modèle et la création du pipeline. À l’aide du Kit de développement logiciel Azure Machine Learning, vous pouvez accéder au stockage sous-jacent, Explorer et préparer des données, gérer le cycle de vie de différentes définitions de jeu de données et comparer entre les jeux de données utilisés dans la formation et de production.

Jeux de données fournit des méthodes pour manipuler des données dans des formats courants, comme l’utilisation de `from_delimited_files()` ou `to_pandas_dataframe()`.

Pour plus d’informations, consultez [créer et inscrire les jeux de données Azure Machine Learning](how-to-create-register-datasets.md).

Pour obtenir un exemple d’utilisation de jeux de données, consultez le [exemples de blocs-notes](https://aka.ms/dataset-tutorial).

## <a name="datastore"></a>Magasin de données

Un magasin de données est une abstraction de stockage d’un compte de stockage Azure. Le magasin de données peut utiliser un conteneur d’objets blob Azure ou un partage de fichiers Azure en tant que stockage backend. Chaque espace de travail comprend un magasin de données par défaut, et peut inscrire des magasins de données supplémentaires.

Utilisez l’API du SDK Python ou l’interface CLI Azure Machine Learning pour stocker et récupérer des fichiers à partir du magasin de données.

## <a name="compute-target"></a>Cible de calcul

Un [cible de calcul](concept-compute-target.md) vous permet de pour spécifier la ressource de calcul où vous exécutez votre script de formation ou d’un hôte de votre déploiement de service. Cet emplacement peut être votre ordinateur local ou une ressource de calcul basé sur le cloud. Cibles de calcul facilitent la modification de votre environnement de calcul sans modifier votre code. 

## <a name="training-script"></a>Script d’entraînement

Pour entraîner un modèle, vous devez spécifier le répertoire qui contient le script d’entraînement et les fichiers associés. Vous pouvez également spécifier un nom pour l’expérience, qui est utilisée pour stocker les informations qui sont collectées au cours de l’entraînement. Pendant l’entraînement, l’intégralité du répertoire est copiée dans l’environnement d’entraînement (la cible de calcul) et le script qui est spécifié par la configuration d’exécution est démarré. Un instantané du répertoire est également stocké sous l’expérience, dans l’espace de travail.

Pour obtenir un exemple, consultez [Didacticiel : Effectuer l’apprentissage d’un modèle de classification d’images avec Azure Machine Learning Service](tutorial-train-models-with-aml.md).

## <a name="run"></a>Exécuter

Une exécution est un enregistrement qui contient les informations suivantes :

* Les métadonnées relatives à l’exécution (horodatage, durée, et ainsi de suite)
* Les métriques qui sont journalisées par votre script
* Les fichiers de sortie qui sont collectés automatiquement par l’expérience ou chargés explicitement par l’utilisateur
* Un instantané du répertoire qui contient vos scripts, avant l’exécution

Vous déclenchez une exécution lorsque vous envoyez un script pour entraîner un modèle. Une exécution peut avoir zéro, une ou plusieurs exécutions enfants. Par exemple, l’exécution de niveau supérieur peut avoir deux exécutions enfants, et chacune d’elles peut avoir sa propre exécution enfant.

Pour obtenir un exemple de l’affichage des exécutions qui sont produites par l’apprentissage d’un modèle, consultez le [Guide de démarrage rapide : Prise en main d’Azure Machine Learning service](quickstart-run-cloud-notebook.md).

## <a name="github-tracking-and-integration"></a>Intégration et suivi de GitHub

Lorsque vous démarrez une formation exécutée où le répertoire source est un référentiel Git local, les informations relatives au référentiel sont stockées dans l’historique des exécutions. Par exemple, l’ID de validation en cours pour le dépôt est consignée dans le cadre de l’historique. Cela fonctionne avec des séries envoyées à l’aide d’un estimateur, le pipeline ML ou le script exécuté. Elle fonctionne également pour les exécutions soumises à partir du Kit de développement logiciel ou la CLI Machine Learning.

## <a name="snapshot"></a>Instantané

Lorsque vous envoyez une exécution, Azure Machine Learning compresse le répertoire qui contient le script dans un fichier zip, puis l’envoie à la cible de calcul. Le fichier zip est ensuite décompressé, et le script y est exécuté. Azure Machine Learning stocke également le fichier zip en tant qu’instantané dans l’enregistrement d’exécution. Toute personne ayant accès à l’espace de travail peut parcourir un enregistrement d’exécution et télécharger l’instantané.

> [!NOTE]
> Pour empêcher que les fichiers inutiles soient inclus dans l’instantané, rendre un fichier ignoré (.gitignore ou .amlignore). Placez ce fichier dans le répertoire des captures instantanées et ajouter les noms de fichiers à ignorer qu’il contient. Le fichier .amlignore utilise le même [syntaxe et les modèles en tant que le fichier .gitignore](https://git-scm.com/docs/gitignore). Si les deux fichiers existent, le fichier .amlignore est prioritaire.

## <a name="activity"></a>Activité

Une activité représente une opération de longue durée. Les opérations suivantes sont des exemples d’activités :

* Création ou suppression d’une cible de calcul
* Exécution d’un script sur une cible de calcul

Les activités peuvent envoyer des notifications via le SDK ou l’interface utilisateur web, ce qui vous permet de superviser facilement la progression de ces opérations.

## <a name="image"></a>Image

Les images constituent un moyen fiable de déployer un modèle et tous les composants dont vous avez besoin pour son utilisation. Une image contient les éléments suivants :

* Un modèle
* Un script ou une application de scoring. Vous utilisez le script pour passer l’entrée au modèle et retourner la sortie du modèle.
* Les dépendances dont ont besoin le modèle ou l’application/le script de scoring. Par exemple, vous pouvez inclure un fichier d’environnement Conda qui répertorie les dépendances du package Python.

Azure Machine Learning peut créer deux types d’image :

* **Image FPGA** : Utilisée lorsque vous effectuez un déploiement vers un tableau FPGA (field programmable gate array) dans Azure.
* **Image Docker** : Utilisée lorsque vous effectuez un déploiement vers des cibles de calcul autres que les tableaux FPGA. Il peut s’agir, par exemple, d’Azure Container Instances et d’Azure Kubernetes Service.

Le service Azure Machine Learning fournit une image de base, qui est utilisée par défaut. Vous pouvez également fournir vos propres images personnalisées.

Pour obtenir un exemple de création d’image, consultez [Déployer un modèle de classification d’images dans Azure Container Instances](tutorial-deploy-models-with-aml.md).

### <a name="image-registry"></a>Registre d’images

Le registre d’images effectue le suivi des images qui sont créées à partir de vos modèles. Vous pouvez fournir des étiquettes de métadonnées supplémentaires lorsque vous créez l’image. Les étiquettes de métadonnées sont stockées par le registre d’images, et vous pouvez les interroger pour trouver votre image.

## <a name="deployment"></a>Déploiement

Un déploiement est une instanciation de votre modèle dans un service web qui peut être hébergé dans le cloud ou un module IoT pour les déploiements de l’appareil intégré.

### <a name="web-service"></a>Service Web

Un service web déployé peut utiliser Azure Container Instances, Azure Kubernetes Service ou des tableaux FPGA. Vous créez le service à partir de votre modèle, les scripts et les fichiers associés. Ceux-ci sont encapsulées dans une image, qui fournit l’environnement d’exécution pour le service web. L’image a un point de terminaison HTTP à charge équilibrée qui reçoit les requêtes de scoring qui sont envoyées au service web.

Azure vous permet de superviser le déploiement de votre service web en collectant les données de télémétrie Application Insights ou les données de télémétrie des modèles, si vous avez choisi d’activer cette fonctionnalité. Vous seul pouvez accéder aux données de télémétrie qui sont stockées dans votre instance Application Insights et votre instance de compte de stockage.

Si vous avez activé la mise à l’échelle automatique, Azure met automatiquement à l’échelle votre déploiement.

Pour obtenir un exemple de déploiement de modèle en tant que service, consultez [Déployer un modèle de classification d’images dans Azure Container Instances](tutorial-deploy-models-with-aml.md).

### <a name="iot-module"></a>Module IoT

Un module IoT déployé est un conteneur Docker qui inclut votre modèle, ainsi que le script ou l’application associés et toutes les dépendances supplémentaires. Vous déployez ces modules à l’aide d’Azure IoT Edge sur périphériques de périmètre.

Si vous avez activé la supervision, Azure collecte les données de télémétrie à partir du modèle qui se trouve dans le module Azure IoT Edge. Vous seul pouvez accéder aux données de télémétrie qui sont stockées dans votre instance de compte de stockage.

Azure IoT Edge garantit l’exécution de votre module et supervise l’appareil qui l’héberge.

## <a name="pipeline"></a>Pipeline

Vous utilisez des pipelines de machine learning pour créer et gérer des flux de travail qui combinent les phases de machine learning. Par exemple, un pipeline peut inclure des données phases de préparation, apprentissage du modèle, déploiement de modèle et l’inférence/notation. Chaque phase peut englober plusieurs étapes, chacune d’elles pouvant s’exécuter sans assistance dans différentes cibles de calcul.

Pour plus d’informations sur les pipelines Machine Learning disposant de ce service, consultez [Pipelines et Azure Machine Learning](concept-ml-pipelines.md).

## <a name="logging"></a>Journalisation

Lorsque vous développez votre solution, utilisez le SDK Python Azure Machine Learning dans votre script Python pour journaliser les métriques arbitraires. Après l’exécution, interrogez les métriques pour vérifier si celle-ci a produit le modèle que vous souhaitez déployer.

## <a name="next-steps"></a>Étapes suivantes

Pour prendre en main le service Azure Machine Learning, consultez :

* [Qu’est-ce que le service Azure Machine Learning ?](overview-what-is-azure-ml.md)
* [Créer un espace de travail du service Azure Machine Learning](setup-create-workspace.md)
* [Tutoriel : Effectuer l'apprentissage d’un modèle](tutorial-train-models-with-aml.md)
* [Créer un espace de travail avec un modèle Resource Manager](how-to-create-workspace-template.md)
