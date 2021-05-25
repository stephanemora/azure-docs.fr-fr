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
ms.date: 10/02/2020
ms.openlocfilehash: 9cb46ef11ab7cc86efa0842fe5952b92170aa648
ms.sourcegitcommit: eda26a142f1d3b5a9253176e16b5cbaefe3e31b3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/11/2021
ms.locfileid: "109737399"
---
# <a name="what-is-an-azure-machine-learning-compute-instance"></a>Qu’est-ce qu’une instance de calcul Azure Machine Learning ?

Une instance de calcul Azure Machine Learning est une station de travail cloud managée pour les scientifiques des données.

Les instances de calcul facilitent le démarrage du développement avec Azure Machine Learning. Elles fournissent aux administrateurs informatiques des fonctionnalités de gestion et de préparation de l’entreprise.  

Utilisez une instance de calcul comme environnement de développement complètement configuré et managé dans le cloud pour l’apprentissage automatique. Elle peut également être utilisée comme cible de calcul pour l’apprentissage et l’inférence à des fins de développement et de test.  

Pour un entraînement de modèle de niveau production, utilisez un [cluster de calcul Azure Machine Learning](how-to-create-attach-compute-cluster.md) avec des fonctionnalités de mise à l’échelle multinœud. Pour le déploiement d’un modèle de niveau production, utilisez un [cluster Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md).

Pour que la fonctionnalité d’instance de calcul Jupyter fonctionne, vérifiez que la communication avec le socket web n’est pas désactivée. Vérifiez que votre réseau autorise les connexions WebSocket à *.instances.azureml.net et *.instances.azureml.ms.

## <a name="why-use-a-compute-instance"></a>Pourquoi créer une instance de calcul ?

Une instance de calcul est une station de travail cloud complètement managée qui est optimisée pour votre environnement de développement Machine Learning. Elle vous permet de bénéficier des avantages suivants :

|Principaux avantages|Description|
|----|----|
|Productivité|Vous pouvez créer et déployer des modèles à l’aide des blocs-notes intégrés et des outils suivants dans Azure Machine Learning Studio :<br/>-  Jupyter<br/>-  JupyterLab<br/>-  VS Code (préversion)<br/>-  RStudio (préversion)<br/>L’instance de calcul est entièrement intégrée avec l’espace de travail et le studio Azure Machine Learning. Vous pouvez partager des blocs-notes et des données avec d’autres scientifiques des données dans l’espace de travail.<br/> Vous pouvez également utiliser [VS Code](https://techcommunity.microsoft.com/t5/azure-ai/power-your-vs-code-notebooks-with-azml-compute-instances/ba-p/1629630) avec des instances de calcul.
|Managée et sécurisée|Réduisez votre empreinte de sécurité et ajoutez la conformité aux exigences de sécurité de l’entreprise. Les instances de calcul fournissent des stratégies de gestion robustes et des configurations de mise en réseau sécurisées telles que :<br/><br/>- Provisionnement automatique par les modèles Resource Manager ou le SDK Azure Machine Learning<br/>- [Contrôle d’accès en fonction du rôle Azure (Azure RBAC)](../role-based-access-control/overview.md)<br/>- [Prise en charge des réseaux virtuels](./how-to-secure-training-vnet.md#compute-instance)<br/>- Stratégie SSH pour activer/désactiver l’accès SSH<br/>TLS 1.2 activé |
|Préconfiguré&nbsp;pour&nbsp;ML|Gagnez du temps sur les tâches d’installation grâce à des packages ML préconfigurés et à jour, des infrastructures de Deep Learning et des pilotes GPU.|
|Entièrement personnalisable|Les scénarios avancés deviennent un jeu d’enfant grâce à la prise en charge étendue des types de machines virtuelles Azure, y compris les GPU et la personnalisation de bas niveau persistante, comme l’installation de packages et de pilotes. |

Vous pouvez [créer une instance de calcul](how-to-create-manage-compute-instance.md?tabs=python#create) vous-même ou un administrateur peut **[créer une instance de calcul en votre nom](how-to-create-manage-compute-instance.md?tabs=python#on-behalf)** .

Vous pouvez également **[utiliser un script de configuration (préversion)](how-to-create-manage-compute-instance.md#setup-script)** pour une méthode automatisée de personnalisation et de configuration de l’instance de calcul en fonction de vos besoins.

## <a name="tools-and-environments"></a><a name="contents"></a>Outils et environnements

> [!IMPORTANT]
> Les éléments marqués (préversion) dans cet article sont actuellement en préversion publique.
> La préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail en production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

L’instance de calcul Azure Machine Learning vous permet de créer, d’effectuer l’apprentissage et de déployer des modèles dans une expérience de notebook entièrement intégrée dans votre espace de travail.

Vous pouvez exécuter des blocs-notes Jupyter dans [VS Code](https://techcommunity.microsoft.com/t5/azure-ai/power-your-vs-code-notebooks-with-azml-compute-instances/ba-p/1629630) à l’aide de l’instance de calcul en tant que serveur distant sans avoir besoin de SSH. Vous pouvez également activer l’intégration de VS Code via l’[extension SSH à distance](https://devblogs.microsoft.com/python/enhance-your-azure-machine-learning-experience-with-the-vs-code-extension/).

Vous pouvez [installer des packages](how-to-access-terminal.md#install-packages) et [ajouter des noyaux](how-to-access-terminal.md#add-new-kernels) à votre instance de calcul.  

Les outils et environnements suivants sont déjà installés sur l’instance de calcul : 

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
[SDK Azure Machine Learning pour Python](/python/api/overview/azure/ml/intro)</br>de PyPI|Comprend la plupart des packages supplémentaires azureml.  Pour afficher la liste complète, [ouvrez une fenêtre de terminal sur votre instance de calcul](how-to-access-terminal.md), puis exécutez <br/> `conda list -n azureml_py36 azureml*` |
|Autres packages PyPI|`jupytext`</br>`tensorboard`</br>`nbconvert`</br>`notebook`</br>`Pillow`|
|Packages Conda|`cython`</br>`numpy`</br>`ipykernel`</br>`scikit-learn`</br>`matplotlib`</br>`tqdm`</br>`joblib`</br>`nodejs`</br>`nb_conda_kernels`|
|Packages Deep learning|`PyTorch`</br>`TensorFlow`</br>`Keras`</br>`Horovod`</br>`MLFlow`</br>`pandas-ml`</br>`scrapbook`|
|Packages ONNX|`keras2onnx`</br>`onnx`</br>`onnxconverter-common`</br>`skl2onnx`</br>`onnxmltools`|
|Exemples de SDK Azure Machine Learning pour Python et R||

Les packages Python sont tous installés dans l’environnement **Python 3.8 - AzureML**. L’instance de calcul dispose d’Ubuntu 18.04 comme système d’exploitation de base.

## <a name="accessing-files"></a>Accès aux fichiers

Les notebooks et les scripts R sont stockés dans le compte de stockage par défaut de votre espace de travail dans le partage de fichiers Azure.  Ces fichiers se trouvent dans le répertoire « Fichiers utilisateur ». Grâce à ce stockage, il est facile de partager des notebooks entre des instances de calcul. Le compte de stockage conserve également vos notebooks en toute sécurité lorsque vous arrêtez ou supprimez une instance de calcul.

Le compte de partage de fichiers Azure de votre espace de travail est monté en tant que lecteur sur l’instance de calcul. Ce lecteur est le répertoire de travail par défaut pour Jupyter, Jupyter Labs et RStudio. Cela signifie que les blocs-notes et les autres fichiers que vous créez dans Jupyter, JupyterLab ou RStudio sont automatiquement stockés dans le partage de fichiers, et disponibles pour être utilisés dans d’autres instances de calcul également.

Les fichiers qui se trouvent dans le partage de fichiers sont accessibles à partir de toutes les instances de calcul du même espace de travail. Toutes les modifications apportées à ces fichiers sur l’instance de calcul seront conservées de manière fiable dans le partage de fichiers.

Vous pouvez également cloner les exemples les plus récents d’Azure Machine Learning dans votre dossier dans le répertoire des fichiers utilisateur du partage de fichiers de l’espace de travail.

L’écriture de petits fichiers peut être plus lente sur des lecteurs réseau que sur le disque local de l’instance de calcul.  Si vous écrivez de nombreux petits fichiers, essayez d’utiliser un répertoire directement sur l’instance de calcul, par exemple un répertoire `/tmp`. Notez que ces fichiers ne seront pas accessibles à partir d’autres instances de calcul. 

Ne stockez pas de données de formation sur le partage de fichiers de notebook. Vous pouvez utiliser le répertoire `/tmp` sur l’instance de calcul pour vos données temporaires.  Toutefois, n’écrivez pas de fichiers de données très volumineux sur le disque du système d’exploitation de l’instance de calcul. Le disque du système d’exploitation sur l’instance de calcul a une capacité de 128 Go. Vous pouvez également stocker des données de formation temporaires sur un disque temporaire monté sur /mnt. La taille du disque temporaire peut être configurée en fonction de la taille de la machine virtuelle choisie, et peut stocker d’importantes quantités de données si une machine virtuelle de taille supérieure est sélectionnée. Vous pouvez également monter [des magasins de données et des jeux de données](concept-azure-machine-learning-architecture.md#datasets-and-datastores). 

## <a name="managing-a-compute-instance"></a>Gestion d’une instance de calcul

Dans votre espace de travail dans Azure Machine Learning Studio, sélectionnez **Calcul**, puis sélectionnez **Instance de calcul** en haut.

![Gérer une instance de calcul](./media/concept-compute-instance/manage-compute-instance.png)

Pour plus d’informations sur la gestion de l’instance de calcul, consultez [Créer et gérer une instance de calcul Azure Machine Learning](how-to-create-manage-compute-instance.md).

### <a name="create-a-compute-instance"></a><a name="create"></a>Créer une instance de calcul

En tant qu’administrateur, vous pouvez **[créer une instance de calcul pour d’autres utilisateurs de l’espace de travail (préversion)](how-to-create-manage-compute-instance.md#on-behalf)** .  

Vous pouvez également **[utiliser un script de configuration (préversion)](how-to-create-manage-compute-instance.md#setup-script)** pour personnaliser et configurer automatiquement l’instance de calcul.

Pour créer votre instance de calcul, utilisez votre espace de travail dans Azure Machine Learning Studio, [créez une instance de calcul](how-to-create-attach-compute-studio.md#compute-instance) à partir de la section **Calcul** ou de la section **Notebooks** lorsque vous êtes prêt à exécuter l’un de vos notebooks. 

Vous pouvez également créer une instance
* Directement à partir de l’[expérience de blocs-notes intégrés](tutorial-train-models-with-aml.md#azure)
* Dans le portail Azure
* À partir d’un modèle Azure Resource Manager. Pour un exemple de modèle, consultez [Créer un modèle d’instance de calcul Azure Machine Learning](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-computeinstance).
* Avec le [Kit de développement logiciel (SDK) Azure Machine Learning](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/machine-learning/concept-compute-instance.md)
* À partir de l’[extension CLI pour Azure Machine Learning](reference-azure-machine-learning-cli.md#computeinstance)

Le quota de cœurs dédiés par région par famille de machine virtuelle et le quota régional total, qui s’appliquent à la création d’une instance de calcul, sont unifiés et partagés avec le quota de clusters de calcul d’entraînement Azure Machine Learning. L’arrêt de l’instance de calcul n’a pas pour effet de libérer le quota pour s’assurer que vous puissiez redémarrer l’instance de calcul.

L’instance de calcul est fournie avec le disque du système d’exploitation P10. Le type de disque temporaire dépend de la taille de machine virtuelle choisie. Actuellement, il n’est pas possible de modifier le type de disque du système d’exploitation.


## <a name="compute-target"></a>Cible de calcul

Les instances de calcul peuvent être utilisées comme [cible de calcul d’entraînement](concept-compute-target.md#train), comme les clusters d’entraînement pour le calcul Azure Machine Learning. 

Une instance de calcul :
* a une file d’attente de travaux ;
* exécute des travaux en toute sécurité dans un environnement de réseau virtuel, sans qu’il soit nécessaire d’ouvrir un port SSH. Le travail s’exécute dans un environnement conteneurisé et empaquette les dépendances de votre modèle dans un conteneur Docker.
* peut exécuter plusieurs petits travaux en parallèle (préversion).  Deux travaux par cœur peuvent s’exécuter en parallèle, tandis que les autres travaux sont mis en file d’attente.
* Prend en charge les travaux d’entraînement distribués sur un seul nœud équipé de plusieurs GPU

Vous pouvez utiliser une instance de calcul en tant que cible de déploiement d’inférence locale dans des scénarios de test ou de débogage.

> [!TIP]
> L’instance de calcul a un disque de système d’exploitation de 120 Go. Si vous ne disposez pas de suffisamment d’espace disque et qu’il en découle un état inutilisable, effacez au moins 5 Go d’espace sur le disque du système d’exploitation (monté sur /) via le terminal d’instance de calcul en supprimant des fichiers/dossiers, puis en effectuant un `sudo reboot`. Pour accéder au terminal, accédez à la page des listes de calcul ou à la page des détails de l’instance de calcul, puis cliquez sur le lien **Terminal** . Vous pouvez vérifier l’espace disque disponible en exécutant `df -h` sur le terminal. Libérez au moins 5 Go d’espace avant d’effectuer `sudo reboot`. N’arrêtez pas ou ne redémarrez pas l’instance de calcul via Studio tant qu’un espace disque de 5 Go n’a pas été libéré.

## <a name="next-steps"></a>Étapes suivantes

* [Créer et gérer une instance de calcul](how-to-create-manage-compute-instance.md)
* [Tutoriel : Effectuer l’apprentissage de votre premier modèle ML](tutorial-1st-experiment-sdk-train.md) montre comment utiliser une instance de calcul avec un notebook intégré.
