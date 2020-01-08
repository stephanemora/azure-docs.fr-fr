---
title: 'ONNX : inférence haute performance et multiplateforme'
titleSuffix: Azure Machine Learning
description: Découvrez comment Open Neural Network Exchange (ONNX) peut optimiser l’inférence de votre modèle Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: prasantp
author: prasanthpul
ms.date: 08/15/2019
ms.custom: seodec18
ms.openlocfilehash: 98aebb4733c2aa2a6d0b0217f1f437bcea1992e9
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/28/2019
ms.locfileid: "75535485"
---
# <a name="onnx-and-azure-machine-learning-create-and-accelerate-ml-models"></a>ONNX et Azure Machine Learning : Créer et accélérer des modèles ML

Découvrez comment [Open Neural Network Exchange](https://onnx.ai) (ONNX) peut optimiser l’inférence de votre modèle Machine Learning. L’inférence, ou notation du modèle, est la phase où le modèle déployé est utilisé pour la prédiction, généralement sur des données de production. 

Il est difficile d’optimiser les modèles Machine Learning pour l’inférence (ou le scoring du modèle), car vous devez paramétrer le modèle et la bibliothèque d’inférence pour tirer le meilleur parti des fonctionnalités matérielles. Le problème devient extrêmement complexe lorsque vous souhaitez obtenir des performances optimales sur différents types de plateformes (cloud/edge, UC/GPU, etc.), étant donné que chaque plateforme a des caractéristiques et des fonctionnalités différentes. La complexité s’accroît si des modèles issus de diverses infrastructures doivent s’exécuter sur diverses plateformes. Il est très chronophage d’optimiser les différentes combinaisons d’infrastructures et de matériel. Il faut une solution de formation unique dans votre infrastructure par défaut et l’exécuter partout sur le cloud ou les appareils Edge. C’est là qu’entre en scène ONNX.

Microsoft et une communauté de partenaires ont créé ONNX : cette norme ouverte représente les modèles Machine Learning. Les modèles de [nombreuses infrastructures](https://onnx.ai/supported-tools) notamment TensorFlow, PyTorch, SciKit-Learn, Keras, Chainer, MXNet et MATLAB peuvent être exportés ou convertis au format ONNX standard. Une fois que les modèles sont au format ONNX, ils peuvent s’exécuter sur une multitude de plateformes et d’appareils.

[ONNX Runtime](https://github.com/Microsoft/onnxruntime) est un moteur d’inférence haute performance pour le déploiement de modèles ONNX en production. Il est optimisé pour le cloud et les appareils Edge, et il fonctionne sur Linux, Windows et Mac. Écrit en C++, il contient également des API C, Python et C#. ONNX Runtime prend en charge toutes les spécifications ML ONNX et s’intègre avec les accélérateurs sur divers matériels, comme TensorRT sur les GPU NVIDIA.

Runtime ONNX est utilisé dans les services Microsoft à grande échelle, tels que Bing, Office et Cognitive Services. Les gains de performances dépendent d’un certain nombre de facteurs, mais ces services Microsoft ont enregistré __en moyenne un doublement des performances de l’UC__. ONNX Runtime est également utilisé dans le cadre de Windows ML sur des centaines de millions d’appareils. Vous pouvez utiliser le runtime avec Azure Machine Learning. Grâce à ONNX Runtime, vous pouvez tirer parti de vastes optimisations à l’échelle de la production, des tests et des améliorations en cours.

[![Organigramme ONNX montrant la formation, les convertisseurs et le déploiement](./media/concept-onnx/onnx.png)](././media/concept-onnx/onnx.png#lightbox)

## <a name="get-onnx-models"></a>Obtenir des modèles ONNX

Vous pouvez obtenir des modèles ONNX de plusieurs façons :
+ Entraînez un nouveau modèle ONNX dans Azure Machine Learning (voir les exemples au bas de cet article)
+ Convertissez un modèle existant au format ONNX (voir les [tutoriels](https://github.com/onnx/tutorials)) 
+ Obtenez un modèle ONNX préformé à partir de la collection [ONNX Model Zoo](https://github.com/onnx/models) (voir exemples au bas de cet article)
+ Générer un modèle ONNX personnalisé à partir du [service Vision personnalisée Azure](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/) 

De nombreux modèles, y compris la classification d’images, la détection d’objets et le traitement de texte, peuvent être représentés en tant que modèles ONNX. Toutefois, il n’est pas toujours possible de convertir certains modèles. Dans ce cas, signalez un problème dans le référentiel GitHub du convertisseur que vous avez utilisé. Vous pouvez continuer à utiliser le modèle de format existant jusqu’à ce que le problème soit résolu.

## <a name="deploy-onnx-models-in-azure"></a>Déployer des modèles ONNX dans Azure

Avec Azure Machine Learning, vous pouvez déployer, gérer et superviser vos modèles ONNX. En utilisant le [workflow de déploiement](concept-model-management-and-deployment.md) standard et ONNX Runtime, vous pouvez créer un point de terminaison REST hébergé dans le cloud. Pour essayer par vous-même, consultez l’exemple complet de notebooks Jupyter fourni à la fin de cet article. 

### <a name="install-and-use-onnx-runtime-with-python"></a>Installer et utiliser ONNX Runtime avec Python

Les packages Python pour ONNX Runtime sont disponibles sur [PyPi.org](https://pypi.org) ([UC](https://pypi.org/project/onnxruntime), [GPU](https://pypi.org/project/onnxruntime-gpu)). Veuillez lire la [configuration requise](https://github.com/Microsoft/onnxruntime#system-requirements) avant l’installation. 

 Pour installer ONNX Runtime pour Python, utilisez l’une des commandes suivantes : 
```python   
pip install onnxruntime       # CPU build
pip install onnxruntime-gpu   # GPU build
```

Pour appeler ONNX Runtime dans votre script Python, utilisez :    
```python
import onnxruntime
session = onnxruntime.InferenceSession("path to model")
```

La documentation qui accompagne le modèle indique généralement les entrées et sorties pour l’utilisation du modèle. Vous pouvez également utiliser un outil de visualisation tel que [Netron](https://github.com/lutzroeder/Netron) pour afficher le modèle. Le runtime ONNX vous permet également d’interroger les métadonnées, entrées et sorties du modèle :    
```python
session.get_modelmeta()
first_input_name = session.get_inputs()[0].name
first_output_name = session.get_outputs()[0].name
```

Pour déduire votre modèle, utilisez `run` et passez la liste des sorties à retourner (laissez vide si vous souhaitez toutes les retourner) et un mappage des valeurs d’entrée. Le résultat est une liste des sorties.  
```python
results = session.run(["output1", "output2"], {
                      "input1": indata1, "input2": indata2})
results = session.run([], {"input1": indata1, "input2": indata2})
```

Pour obtenir des informations de référence complètes sur l’API Python, consultez [les documents de référence sur ONNX Runtime](https://aka.ms/onnxruntime-python).    

## <a name="examples"></a>Exemples

Consultez [how-to-utilisation-azureml/déploiement/onnx](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx) pour obtenir des exemples de notebooks qui créent et déploient des modèles ONNX.

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="more-info"></a>En savoir plus

Apprenez-en davantage sur ONNX ou contribuez au projet :
+ [Site web du projet ONNX](https://onnx.ai)
+ [Code ONNX sur GitHub](https://github.com/onnx/onnx)

Découvrez plus d’informations sur ONNX ou contribuez au projet :
+ [Dépôt GitHub d’ONNX Runtime](https://github.com/Microsoft/onnxruntime)


