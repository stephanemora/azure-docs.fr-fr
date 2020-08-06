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
ms.date: 07/27/2020
ms.openlocfilehash: 4ac95fa81fdbee237cacaa1541e333bb70c370fa
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87323296"
---
# <a name="what-is-an-azure-machine-learning-compute-instance"></a>Qu’est-ce qu’une instance de calcul Azure Machine Learning ?

Une instance de calcul Azure Machine Learning est une station de travail cloud managée pour les scientifiques des données.

Les instances de calcul facilitent le démarrage du développement avec Azure Machine Learning. Elles fournissent aux administrateurs informatiques des fonctionnalités de gestion et de préparation de l’entreprise.  

Utilisez une instance de calcul comme environnement de développement complètement configuré et managé dans le cloud pour l’apprentissage automatique. Elle peut également être utilisée comme cible de calcul pour l’apprentissage et l’inférence à des fins de développement et de test.  

Pour un entraînement de modèle de niveau production, utilisez un [cluster de calcul Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute) avec des fonctionnalités de mise à l’échelle à nœuds multiples. Pour le déploiement d’un modèle de niveau production, utilisez un [cluster Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md).

## <a name="why-use-a-compute-instance"></a>Pourquoi créer une instance de calcul ?

Une instance de calcul est une station de travail cloud complètement managée qui est optimisée pour votre environnement de développement Machine Learning. Elle vous permet de bénéficier des avantages suivants :

|Principaux avantages|Description|
|----|----|
|Productivité|Vous pouvez créer et déployer des modèles à l’aide des blocs-notes intégrés et des outils suivants dans Azure Machine Learning Studio :<br/>-  Jupyter<br/>-  JupyterLab<br/>-  RStudio (préversion)<br/>L’instance de calcul est entièrement intégrée avec l’espace de travail et le studio Azure Machine Learning. Vous pouvez partager des blocs-notes et des données avec d’autres scientifiques des données dans l’espace de travail. Vous pouvez également configurer le développement à distance VS Code à l’aide du protocole [SSH](how-to-set-up-vs-code-remote.md) |
|Managée et sécurisée|Réduisez votre empreinte de sécurité et ajoutez la conformité aux exigences de sécurité de l’entreprise. Les instances de calcul fournissent des stratégies de gestion robustes et des configurations de mise en réseau sécurisées telles que :<br/><br/>- Provisionnement automatique par les modèles Resource Manager ou le kit SDK Azure Machine Learning<br/>- [Contrôle d’accès en fonction du rôle (RBAC)](/azure/role-based-access-control/overview)<br/>- [Prise en charge des réseaux virtuels](how-to-enable-virtual-network.md#compute-instance)<br/>- Stratégie SSH pour activer/désactiver l’accès SSH<br/>TLS 1.2 activé |
|Préconfiguré&nbsp;pour&nbsp;ML|Gagnez du temps sur les tâches d’installation grâce à des packages ML préconfigurés et à jour, des infrastructures de Deep Learning et des pilotes GPU.|
|Entièrement personnalisable|Les scénarios avancés deviennent un jeu d’enfant grâce à la prise en charge étendue des types de machines virtuelles Azure, y compris les GPU et la personnalisation de bas niveau persistante, comme l’installation de packages et de pilotes. |

## <a name="tools-and-environments"></a><a name="contents"></a>Outils et environnements

> [!IMPORTANT]
> Les outils marqués (préversion) ci-dessous sont actuellement en préversion publique.
> La préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail en production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

L’instance de calcul Azure Machine Learning vous permet de créer, d’effectuer l’apprentissage et de déployer des modèles dans une expérience de notebook entièrement intégrée dans votre espace de travail.

Ces outils et environnements sont installés sur l’instance de calcul : 

|Outils et environnements généraux|Détails|
|----|:----:|
|Pilotes|`CUDA`</br>`cuDNN`</br>`NVIDIA`</br>`Blob FUSE` |
|Bibliothèque Intel MPI||
|Azure CLI ||
|Exemples Azure Machine Learning ||
|Docker||
|Nginx||
|NCCL 2.0 ||
|Protobuf|| 

|Outils et environnements **R**|Détails|
|----|:----:|
|Édition open source du serveur RStudio (préversion)||
|Noyau R||
|SDK Azure Machine Learning pour R|[azuremlsdk](https://azure.github.io/azureml-sdk-for-r/reference/index.html)</br>Exemples du Kit de développement logiciel (SDK)|

|Outils et environnements **PYTHON**|Détails|
|----|----|
|Anaconda Python||
|Jupyter et extensions||
|Jupyterlab et extensions||
[SDK Azure Machine Learning pour Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)</br>de PyPI|Comprend la plupart des packages supplémentaires azureml.  Pour afficher la liste complète, [ouvrez une fenêtre de terminal sur votre instance de calcul](how-to-run-jupyter-notebooks.md#terminal), puis exécutez <br/> `conda list -n azureml_py36 azureml*` |
|Autres packages PyPI|`jupytext`</br>`tensorboard`</br>`nbconvert`</br>`notebook`</br>`Pillow`|
|Packages Conda|`cython`</br>`numpy`</br>`ipykernel`</br>`scikit-learn`</br>`matplotlib`</br>`tqdm`</br>`joblib`</br>`nodejs`</br>`nb_conda_kernels`|
|Packages Deep learning|`PyTorch`</br>`TensorFlow`</br>`Keras`</br>`Horovod`</br>`MLFlow`</br>`pandas-ml`</br>`scrapbook`|
|Packages ONNX|`keras2onnx`</br>`onnx`</br>`onnxconverter-common`</br>`skl2onnx`</br>`onnxmltools`|
|Exemples de SDK Azure Machine Learning pour Python et R||

Les packages Python sont tous installés dans l’environnement **Python 3.6 – AzureML**.  

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

Le compte de partage de fichiers Azure de votre espace de travail est monté en tant que lecteur sur l’instance de calcul. Ce lecteur est le répertoire de travail par défaut pour Jupyter, Jupyter Labs et RStudio. Cela signifie que les blocs-notes et les autres fichiers que vous créez dans Jupyter, JupyterLab ou RStudio sont automatiquement stockés dans le partage de fichiers, et disponibles pour être utilisés dans d’autres instances de calcul également.

Les fichiers qui se trouvent dans le partage de fichiers sont accessibles à partir de toutes les instances de calcul du même espace de travail. Toutes les modifications apportées à ces fichiers sur l’instance de calcul seront conservées de manière fiable dans le partage de fichiers.

Vous pouvez également cloner les exemples les plus récents d’Azure Machine Learning dans votre dossier dans le répertoire des fichiers utilisateur du partage de fichiers de l’espace de travail.

L’écriture de petits fichiers peut être plus lente sur des lecteurs réseau que sur le disque local de l’instance de calcul.  Si vous écrivez de nombreux petits fichiers, essayez d’utiliser un répertoire directement sur l’instance de calcul, par exemple un répertoire `/tmp`. Veuillez noter que ces fichiers ne seront pas accessibles à partir d’autres instances de calcul. 

Vous pouvez utiliser le répertoire `/tmp` sur l’instance de calcul pour vos données temporaires.  Toutefois, n’écrivez pas de fichiers de données volumineux sur le disque du système d’exploitation de l’instance de calcul.  Utilisez plutôt des [magasin de données](concept-azure-machine-learning-architecture.md#datasets-and-datastores). Si vous avez installé l’extension git JupyterLab, cela peut également entraîner un ralentissement des performances de l’instance de calcul.

## <a name="managing-a-compute-instance"></a>Gestion d’une instance de calcul

Dans votre espace de travail dans Azure Machine Learning Studio, sélectionnez **Calcul**, puis sélectionnez **Instance de calcul** en haut.

![Gérer une instance de calcul](./media/concept-compute-instance/manage-compute-instance.png)

Vous pouvez effectuer les actions suivantes :

* [Créer une instance de calcul](#create). 
* Actualiser l’onglet des instances de calcul.
* Démarrer, arrêter et redémarrer une instance de calcul  Vous payez l’instance chaque fois qu’elle s’exécute. Quand vous n’utilisez pas l’instance de calcul, arrêtez-la pour réduire les coûts. L’arrêt d’une instance de calcul a pour effet de la libérer. Ensuite, redémarrez-la quand vous en avez besoin. 
* Supprimer une instance de calcul.
* Filtrez la liste des instances de calcul en pour la limiter à celles que vous avez créées.  Il s’agit des instances de calcul auxquelles vous pouvez accéder.

Pour chaque instance de calcul à laquelle vous avez accès dans votre espace de travail, vous pouvez :

* Accéder à Jupyter, JupyterLab et RStudio sur l’instance de calcul.
* Ajouter un accès SSH à une instance de calcul. L’accès SSH est désactivé par défaut, mais il peut être activé au moment de la création de l’instance de calcul. L’accès SSH s’effectue par le biais d’un mécanisme de clé publique/privée. L’onglet vous donne des informations sur la connexion SSH, telles que l’adresse IP, le nom d’utilisateur et le numéro de port.
* Obtenir des informations sur une instance de calcul spécifique, telles que l’adresse IP et la région.

Le [contrôle d’accès en fonction du rôle (RBAC)](/azure/role-based-access-control/overview) vous permet de contrôler quels utilisateurs dans l’espace de travail peuvent créer, supprimer, démarrer ou arrêter une instance de calcul. Tous les utilisateurs ayant les rôles Contributeur et Propriétaire dans l’espace de travail sont autorisés à créer, supprimer, démarrer, arrêter et redémarrer des instances de calcul dans tout l’espace de travail. Toutefois, seul le créateur d’une instance de calcul spécifique est autorisé à accéder à Jupyter, JupyterLab et RStudio sur cette instance de calcul. Le créateur de l’instance de calcul possède l’instance de calcul qui lui est dédiée, dispose d’un accès racine et peut accéder au terminal via Jupyter/JupyterLab/RStudio. L’instance de calcul utilisera une connexion d’utilisateur unique de l’utilisateur créateur et toutes les actions utiliseront l’identité de cet utilisateur pour le contrôle RBAC et l’attribution d’exécutions d’expériences. L’accès SSH est contrôlé par le biais d’un mécanisme de clé publique/privée.

Ces actions peuvent être contrôlées par RBAC :
* *Microsoft.MachineLearningServices/workspaces/computes/read*
* *Microsoft.MachineLearningServices/workspaces/computes/write*
* *Microsoft.MachineLearningServices/workspaces/computes/delete*
* *Microsoft.MachineLearningServices/workspaces/computes/start/action*
* *Microsoft.MachineLearningServices/workspaces/computes/stop/action*
* *Microsoft.MachineLearningServices/workspaces/computes/restart/action*

### <a name="create-a-compute-instance"></a><a name="create"></a>Créer une instance de calcul

Dans votre espace de travail dans Azure Machine Learning Studio, créez une instance de calcul à partir de la section **Calcul** ou de la section **Blocs-notes** lorsque vous êtes prêt à exécuter l’un de vos blocs-notes.

:::image type="content" source="media/concept-compute-instance/create-compute-instance.png" alt-text="Créer une instance de calcul":::


|Champ  |Description  |
|---------|---------|
|Nom du calcul     |  <li>Le nom est obligatoire et doit comprendre entre 3 et 24 caractères.</li><li>Les caractères valides sont les lettres majuscules et minuscules, les chiffres et le caractère **-** .</li><li>Le nom doit commencer par une lettre.</li><li>Le nom doit être unique parmi tous les calculs existants au sein d’une région Azure. Si le nom que vous choisissez n’est pas unique, une alerte s’affiche.</li><li>Si le caractère **-** est utilisé, il doit être suivi d’au moins une lettre plus loin dans le nom.</li>     |
|Type de machine virtuelle |  Choisissez UC ou GPU. Vous ne pouvez pas modifier ce type après la création.     |
|Taille de la machine virtuelle     |  Il se peut que les tailles de machines virtuelles prises en charge soient restreintes dans votre région. Vérifier la [liste de disponibilité](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)     |
|Activer/désactiver l’accès SSH     |   L’accès SSH est désactivé par défaut.  Il n’est pas possible de modifier l’accès SSH après sa création. Veillez à activer l’accès si vous envisagez de déboguer de manière interactive avec [VS Code Remote](how-to-set-up-vs-code-remote.md)   |
|Paramètres avancés     |  facultatif. Configurez un réseau virtuel. Pour créer l’instance de calcul à l’intérieur d’un réseau virtuel Azure (vnet), spécifiez le **Groupe de ressources**, le **Réseau virtuel** et le **Sous-réseau**. Pour plus d’informations, consultez la [configuration requise](how-to-enable-virtual-network.md#compute-instance) pour le réseau virtuel.        |

Vous pouvez également créer une instance
* Directement à partir de l’[expérience de blocs-notes intégrés](tutorial-1st-experiment-sdk-setup.md#azure)
* Dans le portail Azure
* À partir d’un modèle Azure Resource Manager. Pour un exemple de modèle, consultez [Créer un modèle d’instance de calcul Azure Machine Learning](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-computeinstance).
* Avec le [Kit de développement logiciel (SDK) Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-computeinstance/train-on-computeinstance.ipynb)
* À partir de l’[extension CLI pour Azure Machine Learning](reference-azure-machine-learning-cli.md#computeinstance)

Les cœurs dédiés par région, le quota par famille de machines virtuelles et le quota régional total qui s’appliquent à la création de l’instance de calcul sont unifié et partagés avec le quota de cluster de calcul d’apprentissage de Azure Machine Learning. L’arrêt de l’instance de calcul n’a pas pour effet de libérer le quota pour s’assurer que vous puissiez redémarrer l’instance de calcul.

## <a name="compute-target"></a>Cible de calcul

Les instances de calcul peuvent être utilisées comme [cible de calcul d’entraînement](concept-compute-target.md#train), comme les clusters d’entraînement pour le calcul Azure Machine Learning. 

Une instance de calcul :
* a une file d’attente de travaux ;
* exécute des travaux en toute sécurité dans un environnement de réseau virtuel, sans qu’il soit nécessaire d’ouvrir un port SSH. Le travail s’exécute dans un environnement conteneurisé et empaquette les dépendances de votre modèle dans un conteneur Docker.
* peut exécuter plusieurs petits travaux en parallèle (préversion).  Deux travaux par cœur peuvent s’exécuter en parallèle, tandis que les autres travaux sont mis en file d’attente.

Vous pouvez utiliser une instance de calcul en tant que cible de déploiement d’inférence locale dans des scénarios de test ou de débogage.

> [!NOTE]
> Une instance de calcul ne prend pas en charge les travaux d’apprentissage distribué.  Pour l’apprentissage distribué, utilisez (clusters de calcul](how-to-set-up-training-targets.md#amlcompute).

Pour plus d’informations, consultez le bloc-notes [train-on-computeinstance](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-computeinstance/train-on-computeinstance.ipynb). Ce bloc-notes est également disponible dans le dossier **Samples** de studio, dans *training/train-on-computeinstance*.

## <a name="what-happened-to-notebook-vm"></a><a name="notebookvm"></a>Qu’est-il arrivé à la machine virtuelle Notebook ?

Les instances de calcul remplacent la machine virtuelle Notebook.  

Tous les fichiers notebook stockés dans le partage de fichiers de l’espace de travail et les données dans les banques de données de l’espace de travail sont accessibles à partir d’une instance de calcul. Toutefois, tous les packages personnalisés qui ont été précédemment installés sur une machine virtuelle Notebook devront être réinstallés sur l’instance de calcul. Les limitations de quota qui s’appliquent à la création des clusters de calcul s’appliquent aussi à la création des instances de calcul.

Impossible de créer des machines virtuelles de notebooks. Toutefois, vous pouvez toujours accéder aux machines virtuelles Notebook que vous avez créées et les utiliser, avec toutes leurs fonctionnalités. Des instances de calcul peuvent être créées dans le même espace de travail que les machines virtuelles Notebook existantes.


## <a name="next-steps"></a>Étapes suivantes

 * [Tutoriel : Effectuer l’apprentissage de votre premier modèle ML](tutorial-1st-experiment-sdk-train.md) montre comment utiliser une instance de calcul avec un notebook intégré.
