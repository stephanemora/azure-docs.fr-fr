---
title: Classification d’images avec le package Azure Machine Learning (LAM) de la vision par ordinateur (AMLPCV) et le processus TDSP (Team Data Science Process) | Microsoft Docs
description: Décrit l’utilisation des processus TDSP et AMLPCV pour la classification d’image
services: machine-learning, team-data-science-process
documentationcenter: ''
author: xibingao
manager: deguhath
editor: cgronlun
ms.assetid: b8fbef77-3e80-4911-8e84-23dbf42c9bee
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 18/05/2018
ms.author: xibingao
ms.openlocfilehash: cfb49f08a245fc08ba3fe78333e801ea2d358c4a
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34367941"
---
# <a name="skin-cancer-image-classification-with-azure-machine-learning-aml-package-for-computer-vision-amlpcv-and-team-data-science-process-tdsp"></a>Classification d’images de cancer de la peau avec le package Azure Machine Learning (LAM) de la vision par ordinateur (AMLPCV) et le processus TDSP (Team Data Science Process)

## <a name="introduction"></a>Introduction

Cet article vous montre comment utiliser le [package Azure Machine Learning de la vision par ordinateur (AMLPCV)](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest) pour former, tester et déployer un modèle de **classification d’images**. Cet exemple utilise la structure et les modèles TDSP dans [Azure Machine Learning Workbench](https://docs.microsoft.com/en-us/azure/machine-learning/service/quickstart-installation). L’exemple complet est fourni dans cette procédure pas-à-pas. Il utilise [CNTK](https://www.microsoft.com/en-us/cognitive-toolkit/) comme infrastructure d’apprentissage approfondie et la formation est effectuée sur une machine GPU [Data Science VM](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview). Le déploiement utilise l’interface CLI Azure ML Operationalization.

Beaucoup d’applications dans le domaine de la vision par ordinateur peuvent être chargées comme des problèmes de classification d’images. Celles-ci incluent la création de modèles répondant à des questions telles que « Est-ce qu’un objet est présent dans l’image ? » (l’objet peut être un *chien*, une *voiture* ou un *bateau*) et des questions plus complexes, par exemple : « Quel est le degré de gravité de la maladie oculaire mise en évidence par le scanner de la rétine de ce patient ? » AMLPCV rationalise le traitement des données de classification d’images et le pipeline de modélisation. 

## <a name="link-to-github-repository"></a>Lien vers le dépôt GitHub
Vous trouverez des documents récapitulatifs sur l’exemple ici. Une documentation plus complète est également disponible sur le [site GitHub](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification).

## <a name="team-data-science-process-tdsp-walkthrough-with-amlpcv"></a>Procédure pas-à-pas du processus TDSP (Team Data Science Process) avec AMLPCV

Cette procédure pas-à-pas utilise le cycle de vie du [processus TDSP (Team Data Science Process)](https://docs.microsoft.com/en-us/azure/machine-learning/team-data-science-process/overview).

La procédure pas-à-pas couvre les étapes suivantes du cycle de vie :

### <a name="1-data-acquisionhttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode01dataacquisitionandunderstanding"></a>[1. Acquisition de données](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/01_data_acquisition_and_understanding)
Le jeu de données CITI est utilisé pour la tâche de classification d’image. La CITI (International Skin Imaging Collaboration) est un partenariat entre le milieu universitaire et l’industrie visant à faciliter l’application de l’imagerie cutanée numérique pour étudier et aider à réduire la mortalité par mélanome. L’[archive CITI](https://isic-archive.com/#images) contient plus de 13 000 images de lésions cutanées avec des étiquettes indiquant « bénigne » ou « maligne ». Nous téléchargeons un exemple d’images à partir de l’archive CITI.

### <a name="2-modelinghttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode02modeling"></a>[2. Modélisation](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/02_modeling)
À l’étape de modélisation, les sous-étapes suivantes sont effectuées. 

<b>2.1 Création d’un jeu de données</b><br>

Afin de générer un objet dataset dans AMLPCV, fournissez un répertoire racine des images sur le disque local. 

<b>2.2 Visualisation et annotation d’images</b><br>

Visualisez les images dans l’objet dataset et corrigez certaines étiquettes si nécessaire.

<b>2.3 Augmentation d’images</b><br>

Augmenter un objet dataset en utilisant toutes les transformations décrites dans la bibliothèque [imgaug](https://github.com/aleju/imgaug).

<b>2.4 Définition du modèle DNN</b><br>

Définissez l’architecture de modèle utilisé à l’étape de formation. Six modèles différents de réseau de neurones profond sont pris en charge dans AMLPCV : AlexNet, Resnet-18, 34-Resnet et Resnet à 50, Resnet-101 et Resnet-152.

<b>2.5 Formation du classifieur</b><br>

Formez les réseaux neuronaux avec des paramètres personnalisés ou par défaut.

<b>2.6 Évaluation et visualisation</b><br>

La sous-étape permet d’évaluer les performances du modèle formé sur un jeu de données de test indépendant. Les mesures d’évaluation incluent l’exactitude, la précision et le rappel, ainsi que la courbe ROC.

Ces sous-étapes sont décrites en détail dans le Jupyter Notebook correspondant. Nous avons également fourni des instructions afin de modifier les paramètres tels que la vitesse d’apprentissage, la taille du mini-lot et le taux de décrochage pour améliorer les performances du modèle.

### <a name="3-deploymenthttpsgithubcomazuremachinelearningsamples-amlvisionpackage-isicimageclassificationblobmastercode03deployment"></a>[3. Déploiement](https://github.com/Azure/MachineLearningSamples-AMLVisionPackage-ISICImageClassification/blob/master/code/03_deployment)

Cette étape met en œuvre le modèle produit depuis l’étape de modélisation. Elle présente les conditions préalables requises pour la mise en œuvre et l’installation. Enfin, la consommation du service web y est également expliquée. Dans ce didacticiel, vous pouvez apprendre à générer des modèles d’apprentissage approfondi avec AMLPCV et à mettre en œuvre le modèle dans Azure.

## <a name="next-steps"></a>Étapes suivantes
Lisez plus de documents sur le [Package Azure Machine Learning de la vision par ordinateur (AMLPCV)](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest) et le [processus TDSP (Team Data Science Process)](https://aka.ms/tdsp) pour commencer.


## <a name="references"></a>Références

* [Package Azure Machine Learning de la vision par ordinateur (AMLPCV)](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/computer-vision?view=azure-ml-py-latest)
* [Azure Machine Learning Workbench](https://docs.microsoft.com/en-us/azure/machine-learning/service/quickstart-installation)
* [Machine virtuelle Science des données](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview)

