---
title: Utiliser Azure Machine Learning dans la préversion d’Azure Notebooks
description: Un aperçu des exemples de notebooks pour Azure Machine Learning Services que vous pouvez utiliser avec Azure Notebooks.
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: 435abca83255221d438d530b63c237c08bb0b672
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2020
ms.locfileid: "75860569"
---
# <a name="use-azure-machine-learning-in-azure-notebooks-preview"></a>Utiliser Azure Machine Learning dans la préversion d’Azure Notebooks

Azure Notebooks est préconfiguré avec l’environnement nécessaire pour travailler avec [Azure Machine Learning](/azure/machine-learning/). Vous pouvez facilement cloner un exemple de projet dans votre compte Notebooks pour explorer un large éventail de scénarios Machine Learning.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="clone-the-sample-into-your-account"></a>Cloner l’exemple dans votre compte

1. Connectez-vous à [Azure Notebooks](https://notebooks.azure.com/).
1. Sélectionnez **Mes projets** pour accéder au tableau de bord des projets.
1. Sélectionnez le bouton **Charger le référentiel GitHub** (flèche haut) pour ouvrir la fenêtre contextuelle **Charger le référentiel GitHub**.
1. Dans la fenêtre contextuelle, entrez `Azure/MachineLearningNotebooks` dans **Référentiel GitHub**, indiquez un nom de projet dans **Nom du projet**, par exemple « Azure Machine Learning », entrez un identificateur dans **ID de projet**, décochez **Public** si vous le souhaitez, puis sélectionnez **Importer**.

    ![Importer un exemple Azure Machine Learning Notebook dans votre compte Notebooks](media/azureml-import-project.png)

1. Après une minute ou deux, Azure Notebooks vous redirige automatiquement vers le tableau de bord du nouveau projet.

## <a name="run-a-sample-notebook"></a>Exécuter un exemple de notebook

1. Sélectionnez **00 - configuration.ipynb** pour démarrer la section de configuration du notebook et suivez ses instructions pour créer un espace de travail Azure Machine Learning.

    - Étant donné qu’Azure Notebooks contient déjà les packages Python nécessaires, vous pouvez exécuter l’extrait de code de l’étape 2 de la configuration requise pour vérifier la version du SDK Azure ML.

1. Une fois la configuration terminée, sélectionnez **01.getting-started** pour ouvrir un dossier contenant treize exemples de notebooks différents. Notez que chacun d'eux est explicite.

## <a name="next-steps"></a>Étapes suivantes

La documentation Azure Machine Learning contient toutes sortes d’autres ressources qui vous guident tout au long de votre utilisation de Machine Learning dans des notebooks :

- [Démarrage rapide : Utilisation de Python pour démarrer avec Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-configure-environment#local)
- [Tutoriel 1 : Effectuer l'apprentissage d’un modèle de classification d’images avec Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/tutorial-train-models-with-aml)
- [Tutoriel 2 : Déployer un modèle de classification d’images dans Azure Container Instances (ACI)](https://docs.microsoft.com/azure/machine-learning/tutorial-deploy-models-with-aml)
- [Tutoriel : Effectuer l'apprentissage d’un modèle de classification avec Machine Learning automatisé dans Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/tutorial-auto-train-models)

Consultez également la documentation relative à [Azure Machine Learning SDK pour Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
