---
title: Qu’est-il arrivé à Machine Learning Workbench ?
titleSuffix: Azure Machine Learning service
description: Découvrez ce qui est arrivé à l’application Machine Learning Workbench, ce qui a changé dans le service Azure Machine Learning et la chronologie du support.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: overview
ms.reviewer: jmartens
author: j-martens
ms.author: jmartens
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: ff6b61874363bbc869bd509174e58640a2487f56
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/28/2018
ms.locfileid: "53811305"
---
# <a name="whats-happening-to-machine-learning-workbench-in-azure-machine-learning-service"></a>Que se passe-t-il avec Machine Learning Workbench dans le service Azure Machine Learning ?

L’application Azure Machine Learning Workbench et d’autres fonctionnalités d’origine ont été dépréciées et remplacées dans la version de septembre 2018 pour faire place à une [architecture](concept-azure-machine-learning-architecture.md) améliorée. Pour améliorer votre expérience, la version contient de nombreuses mises à jour importantes demandées par les clients dans leurs commentaires. Les fonctionnalités principales qui englobent les exécutions expérimentales jusqu’au déploiement de modèles n’ont pas changé. Toutefois, maintenant, vous pouvez utiliser le <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a> robuste et l’interface [Azure CLI](reference-azure-machine-learning-cli.md) pour accomplir vos tâches et pipelines de machine learning.  

Dans cet article, vous découvrez ce qui a changé et comment cela affecte votre travail préexistant avec Azure Machine Learning Workbench et ses API.

## <a name="what-changed"></a>Qu’est ce qui a changé ?

La dernière version du service Azure Machine Learning comprend les fonctionnalités suivantes :
+ Un [modèle de ressources Azure simplifié](concept-azure-machine-learning-architecture.md).
+ Une [nouvelle interface utilisateur du portail](how-to-track-experiments.md) pour gérer vos expériences et vos cibles de calcul.
+ Un nouveau <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a> Python plus complet.
+ Une nouvelle [extension Azure CLI](reference-azure-machine-learning-cli.md) enrichie pour le machine learning.

L’[architecture](concept-azure-machine-learning-architecture.md) a été repensée pour faciliter l’utilisation. Les ressources et comptes Azure multiples ont laissé la place à un [espace de travail pour le service Azure Machine Learning](concept-azure-machine-learning-architecture.md#workspace). Vous pouvez rapidement créer des espaces de travail dans le [portail Azure](quickstart-get-started.md). Avec un espace de travail, plusieurs utilisateurs peuvent stocker des cibles de calcul d’entraînement et de déploiement, des expériences de modèle, des images Docker, des modèles déployés, etc.

Bien que la version actuelle comprenne de nouveaux clients CLI et SDK améliorés, l’application Workbench de bureau elle-même est dépréciée. Vous pouvez désormais superviser vos expériences dans le [tableau de bord d’espace de travail dans le portail Azure](how-to-track-experiments.md#view-the-experiment-in-the-azure-portal). Utilisez le tableau de bord pour obtenir l’historique de vos expériences, gérer les cibles de calcul attachées à votre espace de travail, gérer vos modèles et images Docker, voire déployer des services web.

## <a name="how-do-i-migrate"></a>Comment effectuer la migration ?

La plupart des artefacts créés dans la version précédente du service Azure Machine Learning sont stockés dans votre propre stockage local ou cloud. Ces artefacts ne disparaîtront jamais. Pour migrer, vous devez réinscrire les artefacts auprès du service Azure Machine Learning mis à jour. Découvrez ce que vous pouvez migrer et comment dans ce [article sur la migration](how-to-migrate.md).

<a name="timeline"></a>

## <a name="support-timeline"></a>Chronologie de prise en charge

Vous pouvez toujours utiliser vos comptes Machine Learning - Expérimentation et Gestion des modèles, ainsi que l’application Machine Learning Workbench après septembre 2018. Le support des ressources suivantes sera progressivement retiré au cours des 3 à 4 mois suivant cette publication. Vous pourrez toujours trouver la documentation sur les anciennes fonctionnalités dans la [section Ressources](../desktop-workbench/tutorial-classifying-iris-part-1.md) située au bas de la table des matières.

|Phase de mise&nbsp;hors service|Détails de la prise en charge des fonctionnalités précédentes|
|:---:|----------------|
|4 décembre 2018|La possibilité de créer un compte Azure Machine Learning - Expérimentation et un compte Gestion des modèles dans le portail Azure et à partir de l’interface CLI est terminée. La possibilité de créer des environnements Capacité de calcul Machine Learning à partir de l’interface CLI est également terminée. Si vous avez un compte, l’interface CLI et l’application de bureau Machine Learning Workbench continuent de fonctionner pendant cette phase.|
|9 janvier 2019|Le support de tout le reste se termine à cette date. Par exemple : les API restantes et l’application de bureau Machine Learning Workbench.|

[Commencez la migration](how-to-migrate.md) aujourd’hui. Toutes les fonctionnalités les plus récentes sont disponibles dans le nouveau <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a>, la nouvelle [interface CLI](reference-azure-machine-learning-cli.md) et le nouveau [portail](quickstart-get-started.md).

## <a name="what-about-run-histories"></a>Qu’en est-il des historiques des exécutions ?

Les historiques d’exécution restent accessibles pendant un certain temps. Quand vous êtes prêt à passer à la version mise à jour du service Azure Machine Learning, vous pouvez exporter ces historiques d’exécution pour en garder une copie.

Les historiques d’exécution sont appelés **expériences** dans la version actuelle. Vous pouvez collecter les expériences de votre modèle et les explorer à l’aide du SDK, de l’interface CLI ou du portail Azure.

Le tableau de bord d’espace de travail du portail est pris en charge uniquement sur les navigateurs Microsoft Edge, Chrome et Firefox :

[ ![Portail en ligne](./media/overview-what-happened-to-workbench/image001.png)] (./media/overview-what-happened-to-workbench/image001.png#lightbox)


## <a name="can-i-still-prep-data"></a>Puis-je toujours préparer les données ?

Vos fichiers de préparation de données préexistants ne sont pas portables vers la dernière version dans la mesure où Machine Learning Workbench n’existe plus. Toutefois, vous pouvez toujours préparer vos données pour la modélisation.  

Avec des jeux de données de toute taille, vous pouvez utiliser le [SDK de préparation des données Azure Machine Learning](http://aka.ms/data-prep-sdk) pour préparer rapidement vos données avant la modélisation en écrivant du code Python. 

Vous pouvez suivre [ce tutoriel](tutorial-data-prep.md) pour en savoir plus sur l’utilisation du SDK de préparation des données Azure Machine Learning.

## <a name="will-projects-persist"></a>Les projets existeront-ils toujours ?

Vous ne perdez aucun code ni travail. Dans la version antérieure, les projets sont des entités de cloud avec un répertoire local. Dans la dernière version, vous attachez des répertoires locaux à l’espace de travail du service Azure Machine Learning en utilisant un fichier config local. Consultez un [diagramme de l’architecture la plus récente](concept-azure-machine-learning-architecture.md).

La majorité du contenu de projet est déjà sur votre ordinateur local. Vous devez simplement créer un fichier config dans ce répertoire et le référencer dans votre code pour vous connecter à votre espace de travail. Découvrez comment [migrer vos projets existants](how-to-migrate.md#projects).

Découvrez comment commencer dans [Python avec le SDK principal](quickstart-create-workspace-with-python.md) ou en utilisant le [portail Azure](quickstart-get-started.md).

## <a name="what-about-my-registered-models-and-images"></a>Qu’en est-il de mes modèles et images inscrits ?
 
Les modèles que vous avez enregistrés dans votre ancien registre de modèles doivent être migrés vers votre nouvel espace de travail si vous voulez continuer à les utiliser. Pour migrer vos modèles, [téléchargez les modèles et réinscrivez-les](how-to-migrate.md) dans votre nouvel espace de travail. 

Les images que vous avez créées dans votre ancien registre d’images doivent être recréées dans le nouvel espace de travail pour continuer à les utiliser. Vous pouvez recréer ces images en suivant les sections [Configurer et créer une image](how-to-deploy-and-where.md#configureimage). 

## <a name="what-about-deployed-web-services"></a>Qu’en est-il des services web déployés ?

Les modèles que vous avez déployés sous forme de services web à l’aide de votre compte Gestion des modèles Machine Learning continuent de fonctionner tant qu’Azure Container Service est pris en charge. Ces services web fonctionnent même après la fin de support des comptes Gestion des modèles Machine Learning. Toutefois, la fin de la prise en charge de l’ancienne interface CLI marque aussi la fin de la possibilité de gérer ces services web.

Dans la dernière version, les modèles sont déployés sous forme de services web sur des clusters Azure Container Instances ou Azure Kubernetes Service (AKS). Vous pouvez également déployer les modèles sur des FPGA et sur Azure IoT Edge. Pour plus d’informations, consultez l’article qui explique [comment et où effectuer le déploiement](how-to-deploy-and-where.md). Sans avoir à changer l’un de vos fichiers de scoring, dépendances et schémas, vous pouvez redéployer vos modèles à l’aide du nouveau SDK ou de la nouvelle interface CLI. 

## <a name="what-about-the-old-sdk-and-cli"></a>Qu’en est-il de l’ancien kit SDK et de l’ancienne interface CLI ?

Oui, ils continuent de fonctionner jusqu’en janvier. Consultez la [chronologie](#timeline) précédente. Nous vous recommandons de commencer à créer vos modèles et expériences avec le dernier SDK et/ou la dernière interface CLI.

En utilisant le nouveau SDK Python de la dernière version, vous pouvez interagir avec le service Azure Machine Learning dans n’importe quel environnement Python. Découvrez comment installer le dernier <a href="https://aka.ms/aml-sdk" target="_blank">kit SDK</a>. Vous pouvez également utiliser l’[extension CLI d’Azure Machine Learning](reference-azure-machine-learning-cli.md) mise à jour avec les nombreuses commandes `az ml` pour interagir avec le service dans n’importe quel environnement de ligne de commande, dont Azure Cloud Shell.

## <a name="what-about-azure-machine-learning-for-visual-studio-code"></a>Qu’en est-il d’Azure Machine Learning pour Visual Studio Code ?

Dans cette dernière version, Azure Machine Learning pour Visual Studio Code a été développé et amélioré de manière à fonctionner avec les nouvelles fonctionnalités précédentes.

[ ![Azure Machine Learning pour Visual Studio Code](./media/overview-what-happened-to-workbench/vscode.png)] (./media/overview-what-happened-to-workbench/vscode-big.png#lightbox)

## <a name="what-about-domain-packages"></a>Qu’en est-il des packages de domaine ?

Les packages de domaine pour la [vision par ordinateur, l’analyse de texte et les prévisions](../desktop-workbench/reference-python-package-overview.md) ne peuvent pas être utilisés avec la dernière version d’Azure Machine Learning. Toutefois, vous pouvez toujours créer et entraîner des modèles de vision par ordinateur, d’analyse de texte et de prévisions avec le dernier <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a> Python d’Azure Machine Learning. Pour découvrir comment migrer des modèles préexistants créés à l’aide des packages Vision par ordinateur, Analyse de texte et Prévisions, contactez [AML-Packages@microsoft.com](mailto:AML-Packages@microsoft.com).

## <a name="next-steps"></a>Étapes suivantes

Découvrez plus d’informations sur la [dernière architecture du service Azure Machine Learning](concept-azure-machine-learning-architecture.md). Essayez un des tutoriels ou démarrages rapides :

* [Qu’est-ce que le service Azure Machine Learning ?](overview-what-is-azure-ml.md)
* [Démarrage rapide : Créer un espace de travail avec Python](quickstart-get-started.md)
* [Tutoriel : Effectuer l'apprentissage d’un modèle](tutorial-train-models-with-aml.md)
