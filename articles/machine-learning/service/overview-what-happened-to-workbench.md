---
title: Que se passe-t-il avec Azure Machine Learning Workbench ? | Microsoft Docs
description: Découvrez ce qui se passe avec l’application Workbench, ce qui a changé dans Azure Machine Learning et quelle est la chronologie de prise en charge.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: overview
ms.reviewer: jmartens
author: j-martens
ms.author: jmartens
ms.date: 09/24/2018
ms.openlocfilehash: b8263c399f287be79590860cce7036207ef2e3f7
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51243741"
---
# <a name="what-is-happening-to-workbench-in-azure-machine-learning-preview"></a>Que se passe-t-il avec Workbench dans Azure Machine Learning (préversion) ?

L’application Workbench et d’autres fonctionnalités d’origine ont été remplacées dans la version de septembre 2018 pour faire place à une [architecture](concept-azure-machine-learning-architecture.md) améliorée. Cette version contient de nombreuses mises à jour importantes demandées par les clients dans leurs commentaires pour améliorer votre expérience. Le fonctionnement intrinsèque allant des exécutions d’expériences au déploiement de modèles n’a pas changé, mais vous pouvez désormais utiliser le <a href="https://aka.ms/aml-sdk" target="_blank">kit SDK</a> et l’[interface CLI](reference-azure-machine-learning-cli.md) pour accomplir vos tâches et vos pipelines de machine learning.  

Dans cet article, vous allez découvrir ce qui a changé et comment cela affecte votre travail pré-existant avec le service Azure Machine Learning.

## <a name="what-changed"></a>Qu’est ce qui a changé ?

La dernière version du service Azure Machine Learning comprend les éléments suivants :
+ Un [modèle de ressources Azure simplifié](concept-azure-machine-learning-architecture.md)
+ Une [nouvelle interface utilisateur du portail](how-to-track-experiments.md) pour gérer vos expériences et vos cibles de calcul
+ Un nouveau kit <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a> Pyhton plus complet
+ Une nouvelle [extension Azure CLI](reference-azure-machine-learning-cli.md) enrichie pour le machine learning.

L’[architecture](concept-azure-machine-learning-architecture.md) a été repensée pour faciliter l’utilisation. Les ressources et comptes Azure multiples ont laissé la place à un [espace de travail pour le service Azure Machine Learning](concept-azure-machine-learning-architecture.md#workspace).  Vous pouvez rapidement créer des espaces de travail dans le [portail Azure](quickstart-get-started.md).  Un espace de travail peut être utilisé par plusieurs utilisateurs pour stocker les cibles de calcul des entraînements et des déploiements, les expériences des modèles, les images Docker, les modèles déployés, etc.

Bien qu’il existe de nouveaux clients CLI et SDK améliorés dans la version actuelle, l’application Workbench de bureau en elle-même est dépréciée. Vous pouvez désormais superviser vos expériences dans le [tableau de bord de l’espace de travail dans le Portail web Azure](how-to-track-experiments.md#view-the-experiment-in-the-azure-portal). Utilisez le tableau de bord pour obtenir l’historique de vos expériences, gérer les cibles de calcul attachées à votre espace de travail, gérer vos modèles et images Docker, voire déployer des services web.

## <a name="how-do-i-migrate"></a>Comment effectuer la migration ?

La plupart des artefacts créés dans la version précédente du service Azure Machine Learning sont stockés dans votre propre stockage local ou cloud. Ces artefacts ne disparaîtront jamais. Pour migrer, vous devez réinscrire les artefacts auprès du service Azure Machine Learning mis à jour. Découvrez ce que vous pouvez migrer et comment dans ce [article sur la migration](how-to-migrate.md).

<a name="timeline"></a>

## <a name="support-timeline"></a>Chronologie de prise en charge

Vous pourrez continuer à utiliser vos comptes d’expérimentation et de gestion de modèles, ainsi que l’application Workbench pendant un certain temps après le mois de septembre 2018. La prise en charge des ressources suivantes sera progressivement retirée au cours des 3 à 4 mois suivant cette publication. Vous pourrez toujours trouver la documentation sur les anciennes fonctionnalités dans la [section Ressources](../desktop-workbench/tutorial-classifying-iris-part-1.md) située au bas de la table des matières.

|Phase|Détails de la prise en charge des fonctionnalités précédentes|
|:---:|----------------|
|1|La possibilité de créer un _compte Azure Machine Learning - Expérimentation_ et un _compte Gestion des modèles_ dans le portail Azure et à partir de l’interface CLI prend fin. La possibilité de créer des environnements Capacité de calcul Machine Learning à partir de l’interface CLI prend fin également. Si vous disposez d’un compte, l’interface CLI et l’application Workbench de bureau continuent de fonctionner pendant cette phase.|
|2|La prise en charge de tous les autres éléments, dont les API restantes et l’application Workbench de bureau, prend fin pendant cette phase.|

[Commencez la migration](how-to-migrate.md) aujourd’hui. Toutes les fonctionnalités les plus récentes sont disponibles dans le nouveau <a href="https://aka.ms/aml-sdk" target="_blank">kit SDK</a>, la nouvelle [interface CLI](reference-azure-machine-learning-cli.md) et le nouveau [portail](quickstart-get-started.md).

## <a name="what-about-run-histories"></a>Qu’en est-il des historiques des exécutions ?

Les historiques des exécutions resteront accessibles pendant un certain temps. Lorsque vous serez prêt à passer à la version mise à jour du service Azure Machine Learning, vous pourrez exporter ces historiques des exécutions si vous souhaitez conserver une copie.

Les historiques des exécutions sont maintenant dénommées _expériences_ dans la version actuelle. Vous pouvez collecter les expériences de votre modèle et les explorer à l’aide du kit SDK, de l’interface CLI ou du portail web.

Le tableau de bord de l’espace de travail du portail est pris en charge uniquement sur les navigateurs Edge, Chrome et Firefox.

[ ![Portail en ligne](./media/overview-what-happened-to-workbench/image001.png) ] (./media/overview-what-happened-to-workbench/image001.png#lightbox)


## <a name="can-i-still-prep-data"></a>Puis-je toujours préparer les données ?

Vos fichiers de préparation de données préexistants ne sont pas portables vers la dernière version dans la mesure où Workbench n’existe plus. Toutefois, vous pouvez toujours préparer vos données pour la modélisation.  

Avec de petits jeux de données, vous pouvez utiliser le <a href="https://aka.ms/aml-sdk" target="_blank">kit SDK de préparation des données Azure Machine Learning</a> pour préparer rapidement vos données avant la modélisation. 

Vous pouvez utiliser ce même kit <a href="https://aka.ms/aml-sdk" target="_blank">SDK</a> avec des jeux de données plus grands ou utiliser Azure Databricks pour préparer des jeux de données volumineux. 

## <a name="will-projects-persist"></a>Les projets existeront-ils toujours ?

Vous ne perdez aucun code ni travail. Dans la version antérieure, les projets sont des entités de cloud avec un répertoire local. Dans la version la plus récente, vous attachez des répertoires locaux à l’espace de travail du service Azure Machine Learning à l’aide d’un fichier de configuration local. [Consultez un diagramme de l’architecture la plus récente](concept-azure-machine-learning-architecture.md).

Dans la mesure où le contenu du projet se trouvait déjà en grande partie sur votre ordinateur local, vous devez simplement créer un fichier de configuration dans ce répertoire et le référencer dans votre code pour vous connecter à votre espace de travail. [Découvrez comment migrer vos projets existants.](how-to-migrate.md#projects)

Découvrez comment commencer [dans Python avec le kit SDK principal](quickstart-get-started.md).

## <a name="what-about-my-registers-models-and-images"></a>Qu’en est-il des modèles et images du registre ?
 
Les modèles que vous avez enregistrés dans votre ancien registre de modèles doivent être migrés vers votre nouvel espace de travail si vous voulez continuer à les utiliser. Pour ce faire, vous pouvez [télécharger les modèles et les réenregistrer ](how-to-migrate.md) dans votre nouvel espace de travail. 

Les images que vous avez créées dans votre ancien registre d’images doivent être recréées dans le nouvel espace de travail pour continuer à les utiliser. Pour cela, suivez la procédure décrite dans la section [créer une image docker](how-to-deploy-to-aci.md#configure-an-image). 

## <a name="what-about-deployed-web-services"></a>Qu’en est-il des services web déployés ?

Les modèles que vous avez déployés en tant que services web à l’aide de votre compte Gestion des modèles continueront de fonctionner tant qu’Azure Container Service (ACS) est pris en charge. Ces services web fonctionneront même après la fin de la prise en charge des comptes Gestion des modèles. Toutefois, la fin de la prise en charge de l’ancienne interface CLI marque aussi la fin de la possibilité de gérer ces services web.

Dans la version la plus récente, les modèles sont déployés en tant que services web sur des clusters [Azure Container Instances](how-to-deploy-to-aci.md) (ACI) ou [Azure Kubernetes Service](how-to-deploy-to-aks.md) (AKS). Vous pouvez également [déployer les modèles sur des FPGA et sur IoT edge](how-to-deploy-and-where.md). Sans avoir à modifier l’un de vos fichiers de notation, dépendances et schémas, vous pouvez redéployer vos modèles à l’aide du nouveau kit SDK ou de la nouvelle interface CLI. 

## <a name="what-about-the-old-sdk--cli"></a>Qu’en est-il de l’ancien kit SDK et de l’ancienne interface CLI ?

Ils continueront de fonctionner pendant un certain temps (voir la [chronologie](#timeline) ci-dessus). Nous vous recommandons de commencer à créer vos modèles et expériences avec le dernier kit SDK et/ou interface CLI.

Dans la dernière version, le nouveau SDK Python vous permet d’interagir avec le service Azure Machine Learning dans n’importe quel environnement Python. Découvrez comment installer le dernier <a href="https://aka.ms/aml-sdk" target="_blank">kit SDK</a>.  Vous pouvez également utiliser l’[extension de machine learning Azure CLI mise à jour](reference-azure-machine-learning-cli.md) avec les nombreuses commandes `az ml` pour interagir avec le service dans n’importe quel environnement de ligne de commande, dont le service Cloud Shell du portail Azure.

## <a name="what-about-vs-code-tools-for-ai"></a>Qu’en est-il de VS Code Tools for AI ?

Dans cette dernière version, l’extension Visual Studio (VS) Code Tools for AI a été étendue et améliorée de manière à fonctionner avec les nouvelles fonctionnalités ci-dessus.

[ ![Visual Studio Code Tools for AI](./media/overview-what-happened-to-workbench/vscode.png) ] (./media/overview-what-happened-to-workbench/vscode-big.png#lightbox)

## <a name="what-about-domain-packages"></a>Qu’en est-il des packages de domaine ?

Les packages de domaine pour [Vision par ordinateur, Analyse de texte et Prévisions](../desktop-workbench/reference-python-package-overview.md) ne peuvent pas être utilisés avec la dernière version d’Azure Machine Learning. Toutefois, vous pouvez toujours créer et entraîner des modèles de vision par ordinateur, d’analyse de texte et de prévisions avec le dernier <a href="https://aka.ms/aml-sdk" target="_blank">kit SDK</a> Python d’Azure Machine Learning. Pour découvrir comment migrer des modèles pré-existants créés à l’aide des packages Vision par ordinateur, Analyse de texte et Prévisions, contactez-nous à l’adresse [AML-Packages@microsoft.com](mailto:AML-Packages@microsoft.com).

## <a name="next-steps"></a>Étapes suivantes

Découvrez l’[architecture la plus récente du service Azure Machine Learning](concept-azure-machine-learning-architecture.md) et essayez l’un démarrages rapides ou tutoriels :

* [Qu’est-ce que le service Azure Machine Learning ?](overview-what-is-azure-ml.md)
* [Démarrage rapide : créer un espace de travail avec Python](quickstart-get-started.md)
* [Tutoriel : entraîner un modèle](tutorial-train-models-with-aml.md)
