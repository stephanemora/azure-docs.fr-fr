---
title: Créer des jeu de données avec Azure Open Datasets
titleSuffix: Azure Open Datasets
description: Découvrez comment créer des jeux de données Azure Machine Learning à partir de Azure Open Datasets.
ms.service: open-datasets
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.date: 08/05/2020
ms.custom: how-to, tracking-python
ms.openlocfilehash: db2e493f04d74308b21e8a63222f26b1e2af8546
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88038107"
---
# <a name="create-azure-machine-learning-datasets-from-azure-open-datasets"></a>Créer des jeux de données Azure Machine Learning à partir de Azure Open Datasets
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Dans cet article, vous allez apprendre à créer un jeu de données Azure Machine Learning à partir de [Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/) pour accéder aux données de vos expériences locales ou distantes. 

En créant un [jeu de données Azure Machine Learning](../machine-learning/how-to-create-register-datasets.md), vous créez une référence à l’emplacement de la source de données, ainsi qu’une copie de ses métadonnées. Étant donné que les données restent à leur emplacement existant, vous n’avez aucun coût de stockage supplémentaire et vous ne risquez pas de modifier involontairement vos sources de données d’origine. Les jeux de données sont également évalués tardivement, ce qui contribue aux vitesses de performances de flux de travail.
 
Pour comprendre où figurent les jeux de données dans le flux de travail global d’accès aux données d’Azure Machine Learning, consultez l’article [Sécuriser l’accès aux données](../machine-learning/concept-data.md#data-workflow).


Les jeux de données ouverts se trouvent dans le Cloud sur Microsoft Azure et sont inclus à la fois dans le [kit de développement logiciel (SDK) Python Azure Machine Learning](#create-datasets-with-the-sdk) et dans le [studio Azure Machine Learning](#create-datasets-with-the-studio).

## <a name="why-use-azure-open-datasets"></a>Pourquoi utiliser Azure Open Datasets ? 

Les jeux de données Azure Open Datasets sont des jeux de données publics organisés que vous pouvez utiliser pour ajouter des fonctionnalités spécifiques à des scénarios à des solutions de Machine Learning afin d'obtenir des modèles plus précis. Les jeux de données englobent des données du domaine public portant sur la météo, le recensement, les jours fériés, la sécurité publique et la localisation et vous aident à former des modèles Machine Learning et à enrichir des solutions prédictives. 

Pour plus d’informations, consultez [Que sont les jeux de données ouverts ?](overview-what-are-open-datasets.md)

## <a name="prerequisites"></a>Prérequis

Pour créer et utiliser des jeux de données, vous avez besoin des éléments suivants :

* Un abonnement Azure. Si vous n’en avez pas, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning](https://aka.ms/AMLFree).

* Un [espace de travail Azure Machine Learning](../machine-learning/how-to-manage-workspace.md).

* Le [SDK Azure Machine Learning pour Python installé](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), qui inclut le paquet azureml-datasets.

    * Créez une [instance de calcul Azure Machine Learning](../machine-learning/concept-compute-instance.md#managing-a-compute-instance), qui est un environnement de développement entièrement configuré et géré qui comprend des notebooks intégrés et le kit de développement logiciel (SDK) déjà installé.

    **OR**

    * Travaillez sur votre propre notebook Jupyter et installez vous-même le kit de développement logiciel (SDK) à l’aide de [ces instructions](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

> [!NOTE]
> Certaines classes de jeu de données ont des dépendances avec le package [azureml-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py), qui n’est compatible qu’avec Python 64 bits. Pour les utilisateurs Linux, ces classes sont uniquement prises en charge dans les distributions suivantes :  Red Hat Enterprise Linux (7, 8), Ubuntu (14.04, 16.04, 18.04), Fedora (27, 28), Debian (8, 9) et CentOS (7).

## <a name="create-datasets-with-the-sdk"></a>Créer des jeux de données avec le kit de développement logiciel (SDK)

Pour créer des jeux de données à l’aide des classes Open Datasets dans le kit de développement logiciel (SDK) Python Azure Machine Learning, assurez-vous que vous avez installé le package avec `pip install azureml-opendatasets`. Chaque jeu de données discret est représenté par sa propre classe dans le SDK, et certaines classes sont disponibles en tant que `TabularDataset`, `FileDataset` ou les deux. Pour obtenir la liste complète des classes, consultez la [documentation de référence](https://docs.microsoft.com/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py).

Vous pouvez récupérer certaines classes en tant que `TabularDataset` ou `FileDataset`, ce qui vous permet de manipuler et/ou de télécharger les fichiers directement. D’autres classes peuvent obtenir un jeu de données **seulement** avec l’une des fonctions `get_tabular_dataset()` ou `get_file_dataset()`. L’exemple de code suivant montre des exemples de ces types de classes.

```python
from azureml.opendatasets import MNIST

# MNIST class can return either TabularDataset or FileDataset
tabular_dataset = MNIST.get_tabular_dataset()
file_dataset = MNIST.get_file_dataset()

from azureml.opendatasets import Diabetes

# Diabetes class can return ONLY TabularDataset and must be called from the static function
diabetes_tabular = Diabetes.get_tabular_dataset()
```

Lorsque vous inscrivez un jeu de données créé à partir d’Open Datasets, aucune donnée n’est immédiatement téléchargée, mais les données seront accessibles ultérieurement à la demande (pour l’apprentissage, par exemple) à partir d’un emplacement de stockage central.

## <a name="create-datasets-with-the-studio"></a>Créer des jeux de données avec le studio

Vous pouvez également créer des jeux de données à partir d’Open Datasets avec le [studio Azure Machine Learning ](https://ml.azure.com). Dans votre espace de travail, sélectionnez l’onglet **Jeux de données** sous **Ressources**. Dans le menu déroulant **Créer un jeu de données**, sélectionnez **À partir d’Open Datasets**.

![Ouvrir un jeu de données avec l’interface utilisateur](./media/how-to-create-dataset-from-open-dataset/open-datasets-1.png)

Choisissez un jeu de données en sélectionnant sa vignette. (Vous avez la possibilité de filtrer en utilisant la barre de recherche.) Sélectionnez **Suivant**.

![Choisir un jeu de données](./media/how-to-create-dataset-from-open-dataset/open-datasets-2.png)

Choisissez ensuite un nom sous lequel inscrire le jeu de données, puis filtrez éventuellement les données en utilisant les filtres disponibles. Dans cet exemple, pour le jeu de données des jours fériés, vous filtrez la période sur une année et le code pays sur États-Unis seulement. Sélectionnez **Create** (Créer).

![Définir les paramètres du jeu de données et créer le jeu de données](./media/how-to-create-dataset-from-open-dataset/open-datasets-3.png)

Le jeu de données est maintenant disponible dans votre espace de travail sous **Jeux de données**. Vous pouvez l’utiliser de la même façon que les autres jeux de données que vous avez créés.


## <a name="access-datasets-for-your-experiments"></a>Accédez aux jeux de données pour vos expériences

Utilisez vos jeux de données dans vos expériences d’apprentissage automatique pour la formation de modèles ML. [Découvrez-en plus sur l’entraînement avec des jeux de données](../machine-learning/how-to-train-with-datasets.md).

## <a name="example-notebooks"></a>Exemples de notebooks

Pour obtenir des exemples et des démonstrations des fonctionnalités d’Open Datasets, consultez ces [notebooks](https://github.com/Azure/OpenDatasetsNotebooks/tree/master/tutorials).

## <a name="next-steps"></a>Étapes suivantes

* [Effectuer l’apprentissage d’un modèle avec des jeux de données](../machine-learning/how-to-train-with-datasets.md).

* [Créer un jeu de données Azure Machine Learning](../machine-learning/how-to-create-register-datasets.md).



