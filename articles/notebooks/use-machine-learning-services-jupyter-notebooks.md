---
title: Utiliser Azure Machine Learning Services dans Azure Notebooks
description: Un aperçu des exemples de notebooks pour Azure Machine Learning Services que vous pouvez utiliser avec Azure Notebooks.
services: app-service
documentationcenter: ''
author: kraigb
manager: barbkess
ms.assetid: 0dc4fc31-ae1c-422c-ac34-7b025e6651b4
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 6eac5d77404c85d5481ded7e58b0cd9fab0de083
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73496647"
---
# <a name="use-azure-machine-learning-in-a-notebook"></a>Utiliser Azure Machine Learning dans un notebook

Azure Notebooks est préconfiguré avec l’environnement nécessaire pour travailler avec [Azure Machine Learning](/azure/machine-learning/service/). Vous pouvez facilement cloner un exemple de projet dans votre compte Notebooks pour explorer un large éventail de scénarios Machine Learning.

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

- [Démarrage rapide : Utilisation de Python pour démarrer avec Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/quickstart-create-workspace-with-python)
- [Tutoriel 1 : Effectuer l'apprentissage d’un modèle de classification d’images avec Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/tutorial-train-models-with-aml)
- [Tutoriel 2 : Déployer un modèle de classification d’images dans Azure Container Instances (ACI)](https://docs.microsoft.com/azure/machine-learning/service/tutorial-deploy-models-with-aml)
- [Tutoriel : Effectuer l'apprentissage d’un modèle de classification avec Machine Learning automatisé dans Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/tutorial-auto-train-models)

Consultez également la documentation relative à [Azure Machine Learning SDK pour Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
