---
title: Hautes performances entre l’inférence de plateforme avec ONNX
titleSuffix: Azure Machine Learning service
description: En savoir plus sur ONNX et le Runtime ONNX pour accélérer la transformation des modèles
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: prasantp
author: prasanthpul
ms.date: 04/24/2019
ms.custom: seodec18
ms.openlocfilehash: 6bda1af8095e7026c79b7d8ffe45b39f6cbb3508
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028694"
---
# <a name="onnx-and-azure-machine-learning-create-and-accelerate-ml-models"></a>ONNX et Azure Machine Learning : Créer et d’accélérer les modèles ML

Découvrez comment l’utilisation du [ouvrir Exchange de réseau neuronal](https://onnx.ai) (ONNX) peut aider à optimiser vos modèles machine learning.

Optimisation des modèles pour l’inférence d’apprentissage sont difficile, car vous devrez paramétrer le modèle et la bibliothèque de l’inférence pour tirer le meilleur parti des fonctionnalités matérielles. Le problème devienne extrêmement difficile si vous souhaitez obtenir des performances optimales sur différents types de plateformes (cloud/edge, UC/GPU, etc.), étant donné que chaque a des caractéristiques et des fonctionnalités différentes. La complexité augmente si vous avez des modèles à partir de diverses infrastructures qui doivent s’exécuter sur diverses plateformes. Il est beaucoup de temps à optimiser les différentes combinaisons d’infrastructures et de matériel. Une solution pour former une seule fois dans votre infrastructure par défaut et exécuter n’importe où sur le cloud ou d’arêtes est nécessaire. Il s’agit là qu’intervient ONNX.

Microsoft et une Communauté de partenaires créé ONNX comme une norme ouverte pour représenter les modèles d’apprentissage automatique. Modèles de [de nombreuses infrastructures](https://onnx.ai/supported-tools) notamment TensorFlow, PyTorch, SciKit-Learn, Keras, Chainer, MXNet et MATLAB peut être exportée ou converti au format ONNX standard. Une fois que les modèles sont au format ONNX, ils peuvent exécuter sur une variété de plateformes et appareils.

[Exécution de ONNX](https://github.com/Microsoft/onnxruntime) est un moteur d’inférence de hautes performances pour le déploiement de modèles ONNX à la production. Il est optimisé pour le cloud et edge et fonctionne sur Linux, Windows et Mac. Écrit C++, il a également C, Python, et C# API. ONNX Runtime prend en charge pour l’ensemble de la spécification ONNX ML et s’intègre également avec les accélérateurs sur différents matériels tels que TensorRT sur des processeurs graphiques NVidia.

Le Runtime ONNX est utilisé dans les services à grande échelle tels que Bing, Office et Cognitive Services. Gains de performances sont interdépendants un certain nombre de facteurs, mais ces services Microsoft ont vu une __moyenne de 2 x gain de performances du processeur__. ONNX Runtime est également utilisé comme partie de ML Windows sur des centaines de millions d’appareils. Vous pouvez utiliser le runtime avec les services Azure Machine Learning. À l’aide de ONNX Runtime, vous pouvez tirer parti les optimisations de qualité production complète, améliorations et de tests en cours.

[![Diagramme de flux ONNX montrant la formation, les convertisseurs et le déploiement](media/concept-onnx/onnx.png) ](./media/concept-onnx/onnx.png#lightbox)

## <a name="get-onnx-models"></a>Obtenir des modèles ONNX

Vous pouvez obtenir des modèles ONNX de plusieurs façons :
+ Former un nouveau modèle ONNX dans le service Azure Machine Learning (voir les exemples en bas de cet article)
+ Convertir ONNX de modèle existant à partir d’un autre format (voir la [didacticiels](https://github.com/onnx/tutorials)) 
+ Obtenir un modèle ONNX préentraîné à partir de la [Zoo de modèle ONNX](https://github.com/onnx/models) (voir les exemples en bas de cet article)
+ Générer un modèle ONNX personnalisé à partir du [service Vision personnalisée Azure](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/) 

De nombreux modèles, y compris la classification d’images, de détection d’objets et de traitement de texte peuvent être représentés en tant que modèles ONNX. Toutefois, certains modèles ne peut-être pas en mesure d’être converti correctement. Si vous rencontrez cette situation, signalez un problème dans GitHub du convertisseur respectif que vous avez utilisé. Vous pouvez continuer à l’aide de votre modèle de format existant jusqu'à ce que le problème est résolu.

## <a name="deploy-onnx-models-in-azure"></a>Déployer des modèles ONNX dans Azure

Avec le service Azure Machine Learning, vous pouvez déployer, gérer et superviser vos modèles ONNX. En utilisant le [workflow de déploiement](concept-model-management-and-deployment.md) standard et ONNX Runtime, vous pouvez créer un point de terminaison REST hébergé dans le cloud. Consultez les exemples de notebooks Jupyter à la fin de cet article pour essayer par vous-même. 

### <a name="install-and-use-onnx-runtime-with-python"></a>Installer et utiliser ONNX Runtime avec Python

Les packages Python pour ONNX Runtime sont disponibles sur [PyPi.org](https://pypi.org) ([processeur](https://pypi.org/project/onnxruntime), [GPU](https://pypi.org/project/onnxruntime-gpu)). Veuillez lire [requise](https://github.com/Microsoft/onnxruntime#system-requirements) avant l’installation. 

 Pour installer le Runtime de ONNX pour Python, utilisez une des commandes suivantes : 
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
results = session.run(["output1", "output2"], {"input1": indata1, "input2": indata2})   
results = session.run([], {"input1": indata1, "input2": indata2})   
``` 

Pour obtenir des informations de référence complètes sur l’API Python, consultez [les documents de référence sur ONNX Runtime](https://aka.ms/onnxruntime-python).    

## <a name="examples"></a>Exemples

Consultez [how-to-utilisation-azureml/déploiement/onnx](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx) pour obtenir des exemples de notebooks qui créent et déploient des modèles ONNX.

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="more-info"></a>En savoir plus

Apprenez-en davantage sur ONNX ou contribuez au projet :
+ [Site web du projet ONNX](https://onnx.ai)
+ [Code ONNX sur GitHub](https://github.com/onnx/onnx)

Découvrez plus d’informations sur ONNX ou contribuez au projet :
+ [Dépôt GitHub d’ONNX Runtime](https://github.com/Microsoft/onnxruntime)


