---
title: Classification d’images avec le package Azure Machine Learning de la vision par ordinateur et le processus TDSP (Team Data Science Process) | Microsoft Docs
description: Cet article décrit l’utilisation du processus TDSP et du package Azure Machine Learning de la vision par ordinateur pour la classification d’images.
services: machine-learning, team-data-science-process
documentationcenter: ''
author: xibingao
manager: deguhath
editor: cgronlun
ms.assetid: b8fbef77-3e80-4911-8e84-23dbf42c9bee
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2018
ms.author: xibingao
ms.openlocfilehash: f9e88cfb7185845e96f287b39bebaaa24320f537
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/21/2018
ms.locfileid: "36300789"
---
# <a name="skin-cancer-image-classification-with-the-azure-machine-learning-package-for-computer-vision-and-team-data-science-process"></a>Classification d’images de cancer de la peau avec le package Azure Machine Learning de la vision par ordinateur et le processus TDSP

Cet article vous montre comment utiliser le [package Azure Machine Learning de la vision par ordinateur](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest) pour former, tester et déployer un modèle de *classification d’images*. Cet exemple utilise la structure et les modèles TDSP dans [Azure Machine Learning Workbench](https://docs.microsoft.com/en-us/azure/machine-learning/service/quickstart-installation). L’exemple complet est fourni dans cette procédure pas à pas. Il utilise le kit de ressources [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/) comme infrastructure d’apprentissage profond, et la formation est effectuée sur une machine GPU [Machine virtuelle de science des données](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview). Le déploiement utilise l’interface CLI d’opérationnalisation d’Azure Machine Learning.

Beaucoup d’applications dans le domaine de la vision par ordinateur peuvent être chargées comme des problèmes de classification d’images. Celles-ci incluent la création de modèles répondant à des questions simples telles que « Est-ce qu’un objet est présent dans l’image ? » où l’objet peut être un chien, une voiture ou un navire. Cela comprend également des réponses à des questions plus complexes, par exemple : « Quel est le degré de gravité de la maladie oculaire mise en évidence par le scanner de la rétine de ce patient ? ». Le package Azure Machine Learning de la vision par ordinateur rationalise le traitement des données de classification d’images ainsi que le pipeline de modélisation. 

## <a name="link-to-the-github-repository"></a>Lien vers le référentiel GitHub
Cet article est un document de synthèse relatif à l’exemple. Une documentation plus complète se trouve sur le [site GitHub](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification).

## <a name="team-data-science-process-walkthrough"></a>Procédure pas à pas du processus TDSP

Cette procédure pas à pas utilise le cycle de vie du [processus TDSP](https://docs.microsoft.com/en-us/azure/machine-learning/team-data-science-process/overview). La procédure pas à pas couvre les étapes du cycle de vie indiquées ci-dessous.

### <a name="1-data-acquisitionhttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode01dataacquisitionandunderstanding"></a>[1. Acquisition de données](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/01_data_acquisition_and_understanding)
Le jeu de données CITI (International Skin Imaging Collaboration) est utilisé pour la classification d’images. La CITI est un partenariat entre le milieu universitaire et l’industrie visant à faciliter l’application de l’imagerie cutanée numérique pour étudier et aider à réduire la mortalité par mélanome. [L’archive CITI](https://isic-archive.com/#images) contient plus de 13 000 images de lésions cutanées étiquetées bénignes ou malignes. Téléchargez un exemple d’images à partir de l’archive CITI.

### <a name="2-modelinghttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode02modeling"></a>[2. Modélisation](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/02_modeling)
À l’étape de la modélisation, les sous-étapes suivantes sont effectuées.

#### <a name="dataset-creation"></a>Création d’un jeu de données

Pour générer un objet DataSet dans le package Azure Machine Learning de la vision par ordinateur, indiquez un répertoire racine des images sur le disque local. 

#### <a name="image-visualization-and-annotation"></a>Visualisation et annotation d’images

Visualisez les images dans l’objet DataSet et corrigez les étiquettes si nécessaire.

#### <a name="image-augmentation"></a>Augmentation d’images

Augmentez un objet DataSet en utilisant les transformations décrites dans la bibliothèque [imgaug](https://github.com/aleju/imgaug).

#### <a name="dnn-model-definition"></a>Définition du modèle DNN

Définissez l’architecture de modèle utilisée à l’étape de formation. Six modèles détaillés de réseau neuronal préentraînés sont pris en charge dans le package Azure Machine Learning de la vision par ordinateur : AlexNet, Resnet-18, Resnet-34, Resnet-50, Resnet-101 et Resnet-152.

#### <a name="classifier-training"></a>Formation du classifieur

Formez les réseaux neuronaux avec des paramètres personnalisés ou par défaut.

#### <a name="evaluation-and-visualization"></a>Évaluation et visualisation

Fournit les fonctionnalités permettant d’évaluer les performances du modèle formé sur un jeu de données de test indépendant. Les mesures d’évaluation incluent l’exactitude, la précision, le rappel et la courbe ROC.

Les sous-étapes sont décrites en détail dans l’application Jupyter Notebook correspondante. Le bloc-notes contient également des instructions afin de modifier les paramètres tels que la vitesse d’apprentissage, la taille du mini-lot et le taux de décrochage pour améliorer les performances du modèle.

### <a name="3-deploymenthttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode03deployment"></a>[3. Déploiement](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/03_deployment)

Cette étape met en œuvre le modèle qui a été produit depuis l’étape de modélisation. Elle présente la configuration et les conditions préalables requises. La consommation du service web y est également expliquée. Dans ce didacticiel, vous allez apprendre à générer des modèles d’apprentissage profond avec le package Azure Machine Learning pour la vision de l’ordinateur et à les mettre en œuvre dans Azure.

## <a name="next-steps"></a>Étapes suivantes
- Consultez les documents complémentaires sur le [package Azure Machine Learning de la vision par ordinateur](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest).
- Consultez la documentation de prise en main du [processus TDSP (Team Data Science Process)](https://aka.ms/tdsp).


## <a name="references"></a>Références

* [Package Azure Machine Learning de la vision par ordinateur](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest)
* [Azure Machine Learning Workbench](https://docs.microsoft.com/en-us/azure/machine-learning/service/quickstart-installation)
* [Machine virtuelle de science des données](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview)

