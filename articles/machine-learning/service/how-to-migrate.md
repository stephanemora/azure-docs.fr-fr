---
title: Migrer vers le service Azure Machine Learning
description: Découvrez comment effectuer une mise à niveau ou une migration vers la dernière version du service Azure Machine Learning à partir d’une version antérieure.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: haining
author: haining
ms.date: 09/24/2018
ms.openlocfilehash: 9778d348cf49d4066f034931dc350a1f4a608ad2
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/03/2018
ms.locfileid: "48236582"
---
# <a name="migrate-to-the-latest-version-of-azure-machine-learning-service"></a>Migrer vers la dernière version du service Azure Machine Learning 

**Si vous avez installé l’application Workbench (préversion) et/ou que vous avez des comptes de préversion de gestion des modèles et d’expérimentation, utilisez cet article pour migrer vers la dernière version.**  Si la préversion de Workbench n’est pas installée, ou si vous n’avez pas de compte de gestion des modèles et/ou d’expérimentation, vous n’avez rien à migrer.

## <a name="what-can-i-migrate"></a>Que puis-je migrer ?
La plupart des artefacts créés dans la première préversion du service Azure Machine Learning sont stockés dans votre propre stockage local ou cloud. Ces artefacts ne disparaîtront pas. Pour migrer, réinscrivez les artefacts auprès du service Azure Machine Learning mis à jour. 

Le tableau et l’article suivants expliquent ce que vous pouvez faire avec vos ressources existantes avant ou après être passé à la dernière version du service Azure Machine Learning. Vous pouvez également continuer à utiliser la version précédente et vos ressources pendant un certain temps ([voir la chronologie de prise en charge de transition](overview-what-happened-to-workbench.md#timeline)).

|Ressource de l’ancienne version|Migration possible ?|Actions|
|-----------------|:-------------:|-------------|
|Modèles Machine Learning (en tant que fichiers locaux)|Oui|Aucune. Fonctionne comme avant.|
|Dépendances de modèles & schémas (en tant que fichiers locaux)|Oui|Aucune. Fonctionne comme avant.|
|Projets|Oui|[Attacher le dossier local au nouvel espace de travail](#projects).|
|Historiques des exécutions|Non |[Téléchargeables](#history) pendant un certain temps.|
|Fichiers de préparation des données|Non |[Préparer un jeu de données de taille quelconque](#dataprep) pour la modélisation à l’aide du nouveau SDK de préparation des données Azure Machine Learning ou utiliser Azure Databricks.|
|Cibles de calcul|Non |Les inscrire dans le nouvel espace de travail.|
|Modèles inscrits|Non |Réinscrire le modèle sous un nouvel espace de travail.|
|Manifestes inscrits|Non |Aucune. Les manifestes n’existent plus en tant que concept dans le nouvel espace de travail.|
|Images inscrites|Non |Recréer l’image Docker de déploiement sous un nouvel espace de travail.|
|Services web déployés|Non |Aucune. Ils fonctionneront toujours tels quels <br/>ou [les redéployer à l’aide de la version la plus récente](#services).|
|Comptes de <br/>gestion des modèles et d’expérimentation|Non |[Créer un espace de travail](#resources) à la place.|
|Interface CLI et SDK Machine Learning|Non |Utiliser la nouvelle interface [CLI](reference-azure-machine-learning-cli.md) et le nouveau [SDK](http://aka.ms/aml-sdk) pour les nouveaux travaux.|


Apprenez-en davantage sur [ce qui a changé dans cette version](overview-what-happened-to-workbench.md).

>[!Warning]
>Cet article ne concerne pas les utilisateurs d’Azure Machine Learning Studio. Il est destiné aux clients du service Azure Machine Learning qui ont installé l’application Workbench (préversion) et/ou qui ont des comptes de préversion de gestion des modèles et d’expérimentation.

<a name="resources"></a>

## <a name="azure-resources"></a>Ressources Azure

Les ressources telles que vos comptes d’expérimentation, comptes de gestion des modèles et environnements de calcul Machine Learning ne peuvent pas être migrés vers la dernière version du service Azure Machine Learning. Consultez la [chronologie](overview-what-happened-to-workbench.md#timeline) pour savoir pendant combien de temps vos ressources continueront à fonctionner.

Pour bien démarrer avec la dernière version, commencez par créer un espace de travail Azure Machine Learning dans le [Portail Microsoft Azure](quickstart-get-started.md). Le tableau de bord de l’espace de travail du portail est pris en charge uniquement sur les navigateurs Edge, Chrome et Firefox.

Ce nouvel espace de travail constitue la ressource de service de niveau supérieur. Il vous permet d’utiliser toutes les fonctionnalités les plus récentes du service Azure Machine Learning. Découvrez plus d’informations sur [cet espace de travail et cette architecture](concept-azure-machine-learning-architecture.md).

<a name="projects"></a>

## <a name="projects"></a>Projets

Au lieu d’être dans un espace de travail dans le cloud, dans la dernière version vos projets sont désormais des répertoires sur votre ordinateur local. Consultez un diagramme de [l’architecture la plus récente](concept-azure-machine-learning-architecture.md). 

Pour continuer à utiliser le répertoire local contenant vos fichiers et scripts, spécifiez le nom du répertoire dans la commande Python [« experiment.submit »](http://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py) ou à l’aide de la commande CLI « az ml project attach ».

Par exemple : 
```python
run = exp.submit(source_directory = script_folder, script = 'train.py', run_config = run_config_system_managed)
```

<a name="services"></a>

## <a name="deployed-web-services"></a>Services web déployés

Pour migrer les services web, redéployez vos modèles à l’aide du nouveau SDK ou de la nouvelle interface CLI sur les nouvelles cibles de déploiement. Il est inutile de changer votre fichier de scoring, vos fichiers de dépendances de fichiers de modèles, votre fichier d’environnement et vos fichiers de schémas d’origine. 

Dans la dernière version, les modèles sont déployés en tant que services web sur des clusters [Azure Container Instances](how-to-deploy-to-aci.md) (ACI) ou [Azure Kubernetes Service](how-to-deploy-to-aks.md) (AKS). 

Pour en savoir plus, consultez les articles suivants :
+ [Déployer sur ACI](how-to-deploy-to-aci.md)
+ [Déployer sur AKS](how-to-deploy-to-aks.md)
+ [Tutoriel : Déployer des modèles avec le service Azure Machine Learning](tutorial-deploy-models-with-aml.md)

Quand la [prise en charge de l’interface CLI précédente prendra fin](overview-what-happened-to-workbench.md#timeline), vous ne pourrez plus gérer les services web que vous avez déployés à l’origine avec votre compte de gestion des modèles. Toutefois, ces services web continueront à fonctionner tant qu’Azure Container Service (ACS) sera pris en charge.

<a name="history"></a>

## <a name="run-history-records"></a>Enregistrements de l’historique des exécutions

Bien que vous ne puissiez pas continuer à ajouter des entrées à vos historiques d’exécutions existants sous l’ancien espace de travail, vous pouvez exporter vos historiques à l’aide de l’interface CLI précédente. Quand la [prise en charge de l’interface CLI précédente prendra fin](overview-what-happened-to-workbench.md#timeline), vous ne pourrez plus exporter ces historiques d’exécutions.

Commencez à entraîner vos modèles et à effectuer le suivi des historiques d’exécutions à l’aide de la nouvelle interface CLI et du nouveau SDK. Pour découvrir comment faire, consultez le [Tutoriel : Entraîner des modèles avec le service Azure Machine Learning](tutorial-train-models-with-aml.md).

Pour exporter l’historique d’exécutions avec l’interface CLI précédente :

```azurecli
#list all runs
az ml history list

#get details about a particular run
az ml history info

# download all artifacts of a run
az ml history download
```

<a name="dataprep"></a>

## <a name="data-preparation-files"></a>Fichiers de préparation des données
Les fichiers de préparation des données ne sont pas portables sans Workbench. Mais vous pouvez encore préparer un jeu de données de taille quelconque pour la modélisation à l’aide du nouveau SDK de préparation des données Azure Machine Learning ou utiliser Azure Databricks pour les jeux de données volumineux.  [Découvrez comment obtenir le SDK de préparation des données](how-to-data-prep.md). 

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir un guide de démarrage rapide illustrant comment créer un espace de travail, créer un projet, exécuter un script et explorer l’historique d’exécutions du script avec la dernière version du service Azure Machine Learning, consultez l’article [Bien démarrer avec le service Azure Machine Learning](quickstart-get-started.md).

Pour obtenir une expérience plus approfondie de ce workflow, suivez le tutoriel complet qui contient des étapes détaillées pour l’entraînement et le déploiement de modèles avec le service Azure Machine Learning. 

> [!div class="nextstepaction"]
> [Tutoriel : Entraîner et déployer des modèles](tutorial-train-models-with-aml.md)
