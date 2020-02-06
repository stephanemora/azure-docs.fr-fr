---
title: Qu’est-ce qu’une instance de calcul Azure Machine Learning ?
titleSuffix: Azure Machine Learning
description: En savoir plus sur l’instance de calcul Azure Machine Learning, une station de travail informatique entièrement managée.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 12/13/2019
ms.openlocfilehash: 7f7e0c3eef96970c1820783225a4ff59be8113b3
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/28/2020
ms.locfileid: "76769114"
---
# <a name="what-is-an-azure-machine-learning-compute-instance"></a>Qu’est-ce qu’une instance de calcul Azure Machine Learning ?

Une instance de calcul Azure Machine Learning (préversion) est une station de travail basée dans le cloud et complètement managée qui est destinée aux scientifiques des données. 

Les instances de calcul facilitent le démarrage du développement avec Azure Machine Learning. Elles fournissent aux administrateurs informatiques des fonctionnalités de gestion et de préparation de l’entreprise.  

Utilisez une instance de calcul comme environnement de développement complètement configuré et managé dans le cloud.

Les instances de calcul sont généralement utilisées comme environnements de développement.  Elles peuvent également être utilisées comme cible de calcul pour la formation et l’inférence à des fins de développement et de test.  Pour les tâches volumineuses, un [cluster de calcul Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute) avec des fonctionnalités de mise à l’échelle à plusieurs nœuds est un meilleur choix de cible de calcul.

> [!NOTE]
> Les instances de calcul sont actuellement disponibles uniquement pour les espaces de travail dans les régions **USA Centre Nord** ou **Royaume-Uni Sud**, mais d’autres régions seront bientôt prises en charge.
>Si votre espace de travail se trouve dans une autre région, vous pouvez continuer à créer et à utiliser une [machine virtuelle Notebook](concept-compute-instance.md#notebookvm) à la place. 

## <a name="why-use-a-compute-instance"></a>Pourquoi créer une instance de calcul ?

Une instance de calcul est une station de travail cloud complètement managée qui est optimisée pour votre environnement de développement Machine Learning. Elle vous permet de bénéficier des avantages suivants :

|Principaux avantages||
|----|----|
|Productivité|Les scientifiques des données peuvent créer et déployer des modèles à l’aide des notebooks intégrés et des outils suivants dans leur navigateur web :<br/>-  Jupyter<br/>-  JupyterLab<br/>-  RStudio|
|Managée et sécurisée|Réduisez votre empreinte de sécurité et ajoutez la conformité aux exigences de sécurité de l’entreprise. Les instances de calcul fournissent des stratégies de gestion robustes et des configurations de mise en réseau sécurisées telles que :<br/><br/>- Provisionnement automatique par les modèles Resource Manager ou le kit SDK Azure Machine Learning<br/>- [Contrôle d’accès en fonction du rôle (RBAC)](/azure/role-based-access-control/overview)<br/>- [Prise en charge des réseaux virtuels](how-to-enable-virtual-network.md#compute-instance)<br/>- Stratégie SSH pour activer/désactiver l’accès SSH|
|Préconfigurée&nbsp;ou&nbsp;ML|Gagnez du temps sur les tâches d’installation grâce à des packages ML préconfigurés et à jour, des infrastructures de Deep Learning et des pilotes GPU.|
|Entièrement personnalisable|Les scénarios avancés deviennent un jeu d’enfant grâce à la prise en charge étendue des types de machines virtuelles Azure, y compris les GPU et la personnalisation de bas niveau persistante, comme l’installation de packages et de pilotes. |

## <a name="contents"></a>Outils et environnements

L’instance de calcul Azure Machine Learning vous permet de créer, d’effectuer l’apprentissage et de déployer des modèles dans une expérience de notebook entièrement intégrée dans votre espace de travail.


Ces outils et environnements sont installés sur l’instance de calcul : 

|Outils et environnements généraux|Détails|
|----|:----:|
|Pilotes|`CUDA`</br>`cuDNN`</br>`NVIDIA`</br>`Blob FUSE` |
|Bibliothèque Intel MPI||
|Azure CLI ||
|Exemples Azure Machine Learning ||
|Moteur EDAT Azure Machine Learning ||
|Docker||
|Nginx||
|NCCL 2.0 ||
|Protobuf|| 

|Outils et environnements **R**|Détails|
|----|:----:|
|Édition open source du serveur RStudio||
|Noyau R||
|SDK Azure Machine Learning pour R|[azuremlsdk](https://azure.github.io/azureml-sdk-for-r/reference/index.html)</br>Exemples du Kit de développement logiciel (SDK)|

|Outils et environnements **PYTHON**|Détails|
|----|----|
|Anaconda Python||
|Jupyter et extensions||
|Jupyterlab et extensions||
|Visual Studio Code ||
[SDK Azure Machine Learning pour Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)</br>de PyPI|`azureml-sdk[notebooks,contrib,automl,explain]`</br>`azureml-contrib-datadrift`</br>`azureml-telemetry`</br>`azureml-tensorboard`</br>`azureml-contrib-opendatasets`</br>`azureml-opendatasets`</br>`azureml-contrib-reinforcementlearning`</br>`azureml-mlflow`</br>`azureml-contrib-interpret` |
|Autres packages PyPI|`jupytext`</br>`jupyterlab-git`</br>`tensorboard`</br>`nbconvert`</br>`notebook`</br>`Pillow`|
|Packages Conda|`cython`</br>`numpy`</br>`ipykernel`</br>`scikit-learn`</br>`matplotlib`</br>`tqdm`</br>`joblib`</br>`nodejs`</br>`nb_conda_kernels`|
|Packages Deep learning|`PyTorch`</br>`TensorFlow`</br>`Keras`</br>`Horovod`</br>`MLFlow`</br>`pandas-ml`</br>`scrapbook`|
|Packages ONNX|`keras2onnx`</br>`onnx`</br>`onnxconverter-common`</br>`skl2onnx`</br>`onnxmltools`|
|Exemples de SDK Azure Machine Learning pour Python et R||

Les packages Python sont tous installés dans l’environnement **Python 3.6 – AzureML**.  

Les instances de calcul sont généralement utilisées comme environnements de développement.  Elles peuvent également être utilisées comme cible de calcul pour la formation et l’inférence à des fins de développement et de test.  Pour les tâches volumineuses, un [cluster de calcul Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute) avec des fonctionnalités de mise à l’échelle à plusieurs nœuds est un meilleur choix de cible de calcul.

### <a name="installing-packages"></a>Installation des packages

Vous pouvez installer des packages directement dans un bloc-notes Jupyter ou RStudio :

* RStudio utilise l’onglet **Packages** en bas à droite, ou l’onglet **Console** en haut à gauche.  
* Python : Ajoutez le code d’installation et l’exécutez dans une cellule de bloc-notes Jupyter.

Ou vous pouvez accéder à une fenêtre de terminal de l’une des manières suivantes :

* RStudio : Sélectionnez l’onglet **Terminal** en haut à gauche.
* Lab Jupyter :  Sélectionnez la vignette **Terminal** sous le titre **Autre** de l’onglet Lanceur.
* Jupyter :  Sélectionnez **Nouveau > Terminal** en haut à droite dans l’onglet Fichiers.
* SSH sur la machine.  Installez ensuite les packages Python dans l’environnement **Python 3.6 – AzureML**.  Installez les packages R dans l’environnement **R**.

## <a name="accessing-files"></a>Accès aux fichiers

Les notebooks et les scripts R sont stockés dans le compte de stockage par défaut de votre espace de travail dans le partage de fichiers Azure.  Ces fichiers se trouvent dans le répertoire « Fichiers utilisateur ». Grâce à ce stockage, il est facile de partager des notebooks entre des instances de calcul. Le compte de stockage conserve également vos notebooks en toute sécurité lorsque vous arrêtez ou supprimez une instance de calcul.

Le compte de partage de fichiers Azure de votre espace de travail est monté en tant que lecteur sur l’instance de calcul. Ce lecteur est le répertoire de travail par défaut pour Jupyter, Jupyter Labs et RStudio.

Les fichiers qui se trouvent dans le partage de fichiers sont accessibles à partir de toutes les instances de calcul du même espace de travail. Toutes les modifications apportées à ces fichiers sur l’instance de calcul seront conservées de manière fiable dans le partage de fichiers.

Vous pouvez également cloner les exemples les plus récents d’Azure Machine Learning dans votre dossier dans le répertoire des fichiers utilisateur du partage de fichiers de l’espace de travail.

L’écriture de petits fichiers peut être plus lente sur les lecteurs réseau que l’écriture sur la machine virtuelle elle-même.  Si vous écrivez de nombreux petits fichiers, essayez d’utiliser un répertoire directement sur l’instance de calcul, par exemple un répertoire `/tmp`. Veuillez noter que ces fichiers ne seront pas accessibles à partir d’autres instances de calcul dans l’espace de travail.

## <a name="managing-a-compute-instance"></a>Gestion d’une instance de calcul

Dans votre espace de travail dans Azure Machine Learning Studio, sélectionnez **Calcul**, puis sélectionnez **Instance de calcul** en haut.

![Gérer une instance de calcul](./media/concept-compute-instance/manage-compute-instance.png)

Vous pouvez effectuer les actions suivantes :

* Créer une instance de calcul. Spécifier le nom et le type de machine virtuelle Azure, y compris les GPU (veuillez noter que le type de machine virtuelle ne peut pas être modifié après la création), activer/désactiver l’accès SSH et configurer des paramètres du réseau virtuel en option. Vous pouvez également créer une instance directement à partir de notebooks intégrés, du Portail Azure, du modèle Resource Manager ou du Kit de développement logiciel (SDK) Azure Machine Learning. Le quota de cœurs dédiés par région qui s’applique à la création d’une instance de calcul est unifié et partagé avec le quota de clusters de calcul Azure Machine Learning.
* Actualiser l’onglet des instances de calcul.
* Démarrer, arrêter et redémarrer une instance de calcul
* Supprimer une instance de calcul

Pour chaque instance de calcul dans votre espace de travail, vous pouvez :

* Accéder aux URI de Jupyter, JupyterLab, RStudio et VS Code sur l’instance de calcul.
* Ajouter un accès SSH à une instance de calcul. L’accès SSH est désactivé par défaut, mais il peut être activé au moment de la création de l’instance de calcul. L’accès SSH s’effectue par le biais d’un mécanisme de clé publique/privée. L’onglet vous donne des informations sur la connexion SSH, telles que l’adresse IP, le nom d’utilisateur et le numéro de port.
* Obtenir des informations sur une instance de calcul spécifique, telles que l’adresse IP et la région.

Le [contrôle d’accès en fonction du rôle (RBAC)](/azure/role-based-access-control/overview) vous permet de contrôler quels utilisateurs dans l’espace de travail peuvent créer, supprimer, démarrer ou arrêter une instance de calcul. Tous les utilisateurs ayant les rôles Contributeur et Propriétaire dans l’espace de travail sont autorisés à créer, supprimer, démarrer, arrêter et redémarrer des instances de calcul dans tout l’espace de travail. Toutefois, seul le créateur d’une instance de calcul spécifique est autorisé à accéder à Jupyter, JupyterLab et RStudio sur cette instance de calcul. Le créateur de l’instance de calcul possède l’instance de calcul dédiée, dispose d’un accès racine et peut accéder au terminal par le biais de Jupyter. L’instance de calcul utilisera une connexion d’utilisateur unique de l’utilisateur créateur et toutes les actions utiliseront l’identité de cet utilisateur pour le contrôle RBAC et l’attribution d’exécutions d’expériences. L’accès SSH est contrôlé par le biais d’un mécanisme de clé publique/privée.

Vous pouvez également créer une instance
* Directement à partir de l’expérience de notebooks intégrés
* Dans le portail Azure
* À partir d’un modèle Azure Resource Manager
* Grâce au Kit de développement logiciel (SDK) Azure Machine Learning

Le quota de cœurs dédiés par région qui s’applique à la création d’une instance de calcul est unifié et partagé avec le quota de clusters de formation Azure Machine Learning. 

## <a name="compute-target"></a>Cible de calcul

Les instances de calcul peuvent être utilisées comme [cible de calcul d’entraînement](concept-compute-target.md#train), comme les clusters d’entraînement pour le calcul Azure Machine Learning. Configurez une machine virtuelle multi-GPU pour exécuter des travaux de formation distribués à l’aide d’estimateurs TensorFlow/PyTorch. Vous pouvez également créer une configuration de série de tests et l’utiliser pour exécuter votre expérience sur l’instance de calcul. Vous pouvez utiliser l’instance de calcul comme cible de déploiement d’inférence local dans les scénarios de test/débogage.

## <a name="notebookvm"></a>Qu’est-il arrivé à la machine virtuelle Notebook ?

Les instances de calcul remplacent la machine virtuelle Notebook.  Dans les régions où les instances de calcul ne sont pas encore disponibles, vous pouvez continuer à utiliser des machines virtuelles Notebook avec toutes leurs fonctionnalités et à en créer d’autres.

Tous les fichiers notebook stockés dans le partage de fichiers de l’espace de travail et les données dans les banques de données de l’espace de travail sont accessibles à partir d’une instance de calcul. Toutefois, tous les packages personnalisés qui ont été précédemment installés sur une machine virtuelle Notebook devront être réinstallés sur l’instance de calcul. Les limitations de quota qui s’appliquent à la création des clusters de calcul s’appliquent aussi à la création des instances de calcul. 

Dans les régions où les instances de calcul sont disponibles, il n’est pas possible de créer de machines virtuelles Notebook. Toutefois, vous pouvez toujours accéder aux machines virtuelles Notebook que vous avez créées et les utiliser, avec toutes leurs fonctionnalités. Des instances de calcul peuvent être créées dans le même espace de travail que les machines virtuelles Notebook existantes. 


## <a name="next-steps"></a>Étapes suivantes

 * [Tutoriel : Effectuer l’apprentissage de votre premier modèle ML](tutorial-1st-experiment-sdk-train.md) montre comment utiliser une instance de calcul avec un notebook intégré.
