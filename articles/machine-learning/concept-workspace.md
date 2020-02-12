---
title: Qu’est-ce qu’un espace de travail ?
titleSuffix: Azure Machine Learning
description: L’espace de travail est la ressource de niveau supérieur du service Azure Machine Learning. Il conserve un historique de toutes les exécutions d’entraînement, y compris les journaux d’activité, les métriques, les sorties et un instantané de vos scripts. Vous utilisez ces informations pour déterminer l’exécution d’entraînement produisant le meilleur modèle.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 12/27/2019
ms.openlocfilehash: f3c2b1aa8f41b382bea96b33108d23bf09850c78
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76984759"
---
# <a name="what-is-an-azure-machine-learning-workspace"></a>Qu’est-ce qu’un espace de travail Azure Machine Learning ?

L’espace de travail est la ressource de niveau supérieur pour Azure Machine Learning. Il fournit un emplacement centralisé dans lequel exploiter tous les artefacts que vous créez lorsque vous utilisez Azure Machine Learning.  L’espace de travail conserve un historique de toutes les exécutions d’entraînement, y compris les journaux d’activité, les métriques, les sorties et un instantané de vos scripts. Vous utilisez ces informations pour déterminer quelle exécution d’entraînement produit le meilleur modèle.  

Une fois que vous disposez d’un modèle qui vous convient, inscrivez-le avec l’espace de travail. Ainsi, grâce au modèle inscrit et aux scripts de scoring, vous pouvez déployer sur Azure Container Instances, Azure Kubernetes Service ou sur un tableau FPGA (field programmable gate array) comme point de terminaison HTTP basé sur REST. Vous pouvez également déployer le modèle en tant que module sur un appareil Azure IoT Edge.

La tarification et les fonctionnalités disponibles varient selon que l’édition [De base ou Enterprise](overview-what-is-azure-ml.md#sku) est sélectionnée pour l’espace de travail. Vous sélectionnez l’édition quand vous [créer l’espace de travail](#create-workspace).  Vous pouvez également [faire une mise à niveau](#upgrade) de De base à Enterprise.

## <a name="taxonomy"></a>Taxonomie 

Le diagramme suivant représente une taxonomie de l’espace de travail :

[![Taxonomie de l’espace de travail](./media/concept-workspace/azure-machine-learning-taxonomy.png)](./media/concept-workspace/azure-machine-learning-taxonomy.png#lightbox)

Le diagramme montre les composants d’un espace de travail suivants :

+ Un espace de travail peut contenir des [instances de calcul Azure Machine Learning](concept-compute-instance.md), des ressources cloud configurées avec l’environnement Python nécessaire pour exécuter Azure Machine Learning.

+ Les [rôles d’utilisateur](how-to-assign-roles.md) vous permettent de partager votre espace de travail avec d’autres utilisateurs, équipes ou projets.
+ Les [cibles de calcul](concept-azure-machine-learning-architecture.md#compute-targets) vous permettent d’exécuter vos expériences.
+ Lorsque vous créez l’espace de travail, les [ressources associées](#resources) sont également créées à votre place.
+ Les [expériences](concept-azure-machine-learning-architecture.md#experiments) sont des exécutions d’entraînement qui vous permettent de créer vos modèles.  
+ Les [pipelines](concept-azure-machine-learning-architecture.md#ml-pipelines) sont des flux de travail réutilisables destinés à l’entraînement et la reformation de votre modèle.
+ Les [jeux de données](concept-azure-machine-learning-architecture.md#datasets-and-datastores) facilitent la gestion des données que vous utilisez pour l’entraînement des modèles et la création de pipelines.
+ Une fois que vous avez un modèle que vous souhaitez déployer, vous créez un modèle inscrit.
+ Utilisez le modèle inscrit et un script de scoring pour créer un [point de terminaison de déploiement](concept-azure-machine-learning-architecture.md#endpoints).

## <a name="tools-for-workspace-interaction"></a>Outils pour l’interaction avec l’espace de travail

Vous pouvez interagir avec votre espace de travail comme suit :

+ Sur le web :
    + [Azure Machine Learning studio ](https://ml.azure.com) 
    + [Le concepteur Azure Machine Learning (préversion)](concept-designer.md) - disponible uniquement dans les espaces de travail [édition Enterprise](overview-what-is-azure-ml.md#sku).
+ Dans un environnement Python avec le [kit de développement logiciel (SDK) Azure Machine Learning pour Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
+ Dans un environnement R avec le [kit de développement logiciel (SDK) Azure Machine Learning pour R](https://azure.github.io/azureml-sdk-for-r/reference/index.html).
+ Sur la ligne de commande avec l’[extension CLI](https://docs.microsoft.com/azure/machine-learning/reference-azure-machine-learning-cli) Azure Machine Learning :

## <a name="machine-learning-with-a-workspace"></a>Machine Learning avec un espace de travail

Les tâches de Machine Learning lisent et/ou écrivent des artefacts sur votre espace de travail.

+ Exécuter une expérience pour entraîner un modèle : écrit les résultats de l’exécution d’expérience sur l’espace de travail.
+ Utiliser le Machine Learning automatisé pour entraîner un modèle : écrit les résultats de l’entraînement sur l’espace de travail.
+ Inscrivez un modèle dans l’espace de travail.
+ Déployer un modèle : utilise le modèle inscrit pour créer un déploiement.
+ Créez et exécutez des flux de travail réutilisables.
+ Affichez les artefacts de Machine Learning (expériences, pipelines, modèles, déploiements, etc.).
+ Suivez et supervisez les modèles.

## <a name="workspace-management"></a>Gestion de l’espace de travail

Vous pouvez également effectuer les tâches de gestion de l’espace de travail suivantes :

| Tâche de gestion de l’espace de travail   | Portail              | Studio | Kit SDK Python / Kit SDK R       | Interface de ligne de commande        |
|---------------------------|---------|---------|------------|------------|
| Créer un espace de travail        | **&check;**     | | **&check;** | **&check;** |
| Gérer les accès à l’espace de travail    | **&check;**   || |  **&check;**    |
| Mise à jour vers l’édition Enterprise    | **&check;** | **&check;**  | |     |
| Créer et gérer des ressources de calcul    | **&check;**   | **&check;** | **&check;** |  **&check;**   |
| Créer une machine virtuelle de notebooks |   | **&check;** | |     |

> [!NOTE]
> Les instances de calcul sont disponibles uniquement pour les espaces de travail dans les régions **USA Centre Nord**, **USA Est 2**, **Europe Nord** ou **Royaume-Uni Sud**, mais d’autres régions seront bientôt prises en charge.
>Si votre espace de travail se trouve dans une autre région, vous pouvez continuer à créer et à utiliser une [machine virtuelle Notebook](concept-compute-instance.md#notebookvm) à la place.

## <a name='create-workspace'></a> Créer un espace de travail

Lorsque vous créez un espace de travail, vous décidez de le créer avec [l’édition De base ou Enterprise](overview-what-is-azure-ml.md#sku). L’édition détermine les fonctionnalités disponibles dans l’espace de travail. Parmi d’autres fonctionnalités, l’édition Enterprise vous permet d’accéder au [concepteur Azure Machine Learning](concept-designer.md) et à la version studio de la génération [d’expériences Machine Learning automatisées](tutorial-first-experiment-automated-ml.md).  Pour plus de détails et pour obtenir des informations sur la tarification, consultez [Tarifs Azure Machine Learning](https://azure.microsoft.com/pricing/details/machine-learning/).

Il existe plusieurs moyens de créer un espace de travail :  

* Utilisez le [portail Azure](how-to-manage-workspace.md) comme interface de type pointer-cliquer pour vous guider dans chaque étape.
* Utilisez le [kit SDK Azure Machine Learning pour Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) pour créer un espace de travail à la volée à partir de scripts Python ou de notebooks Jupyter
* Utilisez un [modèle Azure Resource Manager](how-to-create-workspace-template.md) ou l’[interface CLI Azure Machine Learning](reference-azure-machine-learning-cli.md) lorsque vous devez automatiser ou personnaliser la création avec des normes de sécurité d’entreprise.
* Si vous travaillez dans Visual Studio Code, utilisez l'[extension VS Code](tutorial-setup-vscode-extension.md).

> [!NOTE]
> Le nom de l’espace de travail n’est pas sensible à la casse.

## <a name="upgrade"></a> Mise à jour vers l’édition Enterprise

Vous pouvez [mettre à niveau votre espace de travail depuis l’édition De base vers l’édition Enterprise](how-to-manage-workspace.md#upgrade) à l’aide du portail Azure. Vous ne pouvez pas rétrograder un espace de travail Édition Entreprise vers une édition De base. 

## <a name="resources"></a> Ressources associées

Lorsque vous créez un nouvel espace de travail, celui-ci crée automatiquement plusieurs ressources Azure qui sont utilisées par l’espace de travail :

+ [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) : Enregistre les conteneurs docker que vous utilisez pendant la formation et lorsque vous déployez un modèle. Pour réduire les coûts, ACR est **chargé en différé** jusqu’à la création des images de déploiement.
+ [Compte Stockage Azure](https://azure.microsoft.com/services/storage/) : Utilisé comme magasin de données par défaut pour l’espace de travail.  Les notebooks Jupyter utilisés avec vos instances de calcul Azure Machine Learning sont également stockés ici.
+ [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) : Stocke les informations de supervision concernant les modèles.
+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) : Stocke les secrets qui sont utilisés par les cibles de calcul, ainsi que d’autres informations sensibles dont a besoin l’espace de travail.

> [!NOTE]
> En plus de créer de nouvelles versions, vous pouvez utiliser les services Azure existants.

## <a name="next-steps"></a>Étapes suivantes

Pour bien démarrer avec Azure Machine Learning, voir :

+ [Vue d’ensemble d’Azure Machine Learning](overview-what-is-azure-ml.md)
+ [Créer un espace de travail](how-to-manage-workspace.md)
+ [Gérer un espace de travail](how-to-manage-workspace.md)
+ [Tutoriel : Bien démarrer avec la création de votre première expérience ML avec le SDK Python](tutorial-1st-experiment-sdk-setup.md)
+ [Tutoriel : Bien démarrer avec Azure Machine Learning et le SDK R](tutorial-1st-r-experiment.md)
+ [Tutoriel : Créer votre premier modèle de classification avec Machine Learning automatisé](tutorial-first-experiment-automated-ml.md) (disponible uniquement dans les espaces de travail [d’édition Enterprise](overview-what-is-azure-ml.md#sku))
+ [Tutoriel : Prédire le prix des véhicules à l’aide du concepteur](tutorial-designer-automobile-price-train-score.md) (disponible uniquement dans les espaces de travail [d’édition Enterprise](overview-what-is-azure-ml.md#sku))
