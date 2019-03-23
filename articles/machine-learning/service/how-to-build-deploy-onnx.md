---
title: Créer et déployer des modèles ONNX interopérables
titleSuffix: Azure Machine Learning service
description: Découvrez ONNX et comment utiliser Azure Machine Learning pour créer et déployer des modèles ONNX.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: prasantp
author: prasanthpul
ms.date: 12/3/2018
ms.custom: seodec18
ms.openlocfilehash: 349f2c4eea743c3e44e492dfa76be4a70f2c37d6
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58362023"
---
# <a name="onnx-and-azure-machine-learning-create-and-deploy-interoperable-ai-models"></a>ONNX et Azure Machine Learning : Créer et déployer des modèles d’intelligence artificielle interopérables

Le format [ONNX (Open Neural Network Exchange)](https://onnx.ai) est une norme ouverte de représentation des modèles Machine Learning. ONNX est pris en charge par une [communauté de partenaires](https://onnx.ai/supported-tools), notamment Microsoft, qui créent des outils et des frameworks compatibles. Microsoft s’est engagé à promouvoir une intelligence artificielle ouverte et interopérable afin de permettre aux scientifiques des données et aux développeurs de :

+ Utiliser le framework de leur choix pour créer et entraîner des modèles.
+ Déployer des modèles multiplateformes avec un travail d’intégration minimal

Microsoft prend en charge ONNX dans toute sa gamme de produits, notamment Azure et Windows, pour vous aider à atteindre ces objectifs.  

## <a name="why-choose-onnx"></a>Pourquoi choisir ONNX ?
L’interopérabilité que vous obtenez avec ONNX permet d’accélérer la mise en production de vos idées. Avec ONNX, les scientifiques des données peuvent choisir leur framework préféré pour effectuer leur travail. De même, les développeurs consacreront moins de temps à préparer les modèles pour la production et à déployer sur le cloud et la périphérie.  

Vous pouvez créer des modèles ONNX à partir de nombreux frameworks, à savoir PyTorch, Chainer, Microsoft Cognitive Toolkit (CNTK), MXNet, ML.Net, TensorFlow, Keras, SciKit-Learn, etc.

Il existe également un écosystème d’outils de visualisation et d’accélération des modèles ONNX. Plusieurs modèles ONNX préentraînés sont également disponibles pour les scénarios courants.

[Les modèles ONNX peuvent être déployés](#deploy) sur le cloud avec Azure Machine Learning et ONNX Runtime. Ils peuvent également être déployés sur des appareils Windows 10 à l’aide de [Windows ML](https://docs.microsoft.com/windows/ai/). Ils peuvent même être déployés sur d’autres plateformes, grâce aux convertisseurs mis à disposition par la communauté ONNX. 

[![Diagramme de flux ONNX montrant la formation, les convertisseurs et le déploiement](media/concept-onnx/onnx.png) ](./media/concept-onnx/onnx.png#lightbox)

## <a name="get-onnx-models"></a>Obtenir des modèles ONNX

Vous pouvez obtenir des modèles ONNX de plusieurs façons :
+ Obtenir un modèle ONNX préentraîné à partir de la collection [ONNX Model Zoo](https://github.com/onnx/models) (voir l’exemple au bas de cet article)
+ Générer un modèle ONNX personnalisé à partir du [service Vision personnalisée Azure](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/) 
+ Convertir le modèle existant d’un autre format vers le format ONNX (voir l’exemple au bas de cet article) 
+ Entraîner un nouveau modèle ONNX dans le service Azure Machine Learning (voir l’exemple au bas de cet article)

## <a name="saveconvert-your-models-to-onnx"></a>Enregistrer/convertir vos modèles au format ONNX

Vous pouvez convertir des modèles existants au format ONNX ou les enregistrer au format ONNX à la fin de votre entraînement.

|Infrastructure pour le modèle|Exemple ou outil de conversion|
|-----|-------|
|PyTorch|[Bloc-notes Jupyter](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb)|
|Microsoft&nbsp;Cognitive&nbsp;Toolkit&nbsp;(CNTK)|[Bloc-notes Jupyter](https://github.com/onnx/tutorials/blob/master/tutorials/CntkOnnxExport.ipynb)|
|TensorFlow|[tensorflow-onnx converter](https://github.com/onnx/tensorflow-onnx)|
|Chainer|[Bloc-notes Jupyter](https://github.com/onnx/tutorials/blob/master/tutorials/ChainerOnnxExport.ipynb)|
|MXNet|[Bloc-notes Jupyter](https://github.com/onnx/tutorials/blob/master/tutorials/MXNetONNXExport.ipynb)|
|Keras, ScitKit-Learn, CoreML<br/>XGBoost, et libSVM|[WinMLTools](https://docs.microsoft.com/windows/ai/convert-model-winmltools)|

Vous trouverez la liste la plus récente des frameworks et des convertisseurs pris en charge sur le [site des tutoriels ONNX](https://github.com/onnx/tutorials).

<a name="deploy"></a>

## <a name="deploy-onnx-models-in-azure"></a>Déployer des modèles ONNX dans Azure

Avec le service Azure Machine Learning, vous pouvez déployer, gérer et superviser vos modèles ONNX. En utilisant le [workflow de déploiement](concept-model-management-and-deployment.md) standard et ONNX Runtime, vous pouvez créer un point de terminaison REST hébergé dans le cloud. Pour essayer par vous-même, consultez l’exemple complet de bloc-notes Jupyter fourni à la fin de cet article. 

### <a name="install-and-configure-onnx-runtime"></a>Installer et configurer ONNX Runtime

ONNX Runtime est un moteur d’inférence open source à hautes performances pour les modèles ONNX. Il fournit une accélération matérielle sur l’UC et le GPU, avec des API disponibles pour Python, C#, et C. ONNX Runtime prend en charge les modèles ONNX 1.2+, et s’exécute sur Linux, Windows et Mac. Des packages Python sont disponibles sur [PyPi.org](https://pypi.org) ([UC](https://pypi.org/project/onnxruntime), [GPU](https://pypi.org/project/onnxruntime-gpu)), et un [package C#](https://www.nuget.org/packages/Microsoft.ML.OnnxRuntime/) se trouve sur [Nuget.org](https://www.nuget.org). Pour plus d’informations, consultez le projet sur [GitHub](https://github.com/Microsoft/onnxruntime). Veuillez lire [requise](https://github.com/Microsoft/onnxruntime#system-requirements) avant l’installation.

Pour installer ONNX Runtime pour Python, utilisez :
```python
pip install onnxruntime
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

### <a name="example-deployment-steps"></a>Exemples d’étapes de déploiement

Voici un exemple de déploiement d’un modèle ONNX :

1. Initialisez votre espace de travail du service Azure Machine Learning. Si vous n’avez pas encore, découvrez comment [créer un espace de travail](setup-create-workspace.md).

   ```python
   from azureml.core import Workspace

   ws = Workspace.from_config()
   print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep = '\n')
   ```

2. Inscrivez le modèle auprès d’Azure Machine Learning.

   ```python
   from azureml.core.model import Model

   model = Model.register(model_path = "model.onnx",
                          model_name = "MyONNXmodel",
                          tags = ["onnx"],
                          description = "test",
                          workspace = ws)
   ```

3. Créez une image avec le modèle et les dépendances.

   ```python
   from azureml.core.image import ContainerImage

   image_config = ContainerImage.image_configuration(execution_script = "score.py",
                                                     runtime = "python",
                                                     conda_file = "myenv.yml",
                                                     description = "test",
                                                     tags = ["onnx"]
                                                    )

   image = ContainerImage.create(name = "myonnxmodelimage",
                                 # this is the model object
                                 models = [model],
                                 image_config = image_config,
                                 workspace = ws)

   image.wait_for_creation(show_output = True)
   ```

   Le fichier `score.py` contient la logique de scoring ; il doit être inclus dans l’image. Ce fichier est utilisé pour exécuter le modèle dans l’image. Consultez ce [tutoriel](tutorial-deploy-models-with-aml.md#create-scoring-script) pour obtenir des instructions sur la création d’un script de scoring. Vous trouverez ci-dessous un exemple de fichier pour un modèle ONNX :

   ```python
   import onnxruntime
   import json
   import numpy as np
   import sys
   from azureml.core.model import Model

   def init():
       global session
       model = Model.get_model_path(model_name = 'MyONNXModel')
       session = onnxruntime.InferenceSession(model)

   def preprocess(input_data_json):
       # convert the JSON data into the tensor input
       return np.array(json.loads(input_data_json)['data']).astype('float32')

   def postprocess(result):
       return np.array(result).tolist()

   def run(input_data_json):
       try:
           start = time.time()   # start timer
           input_data = preprocess(input_data_json)
           input_name = session.get_inputs()[0].name  # get the id of the first input of the model   
           result = session.run([], {input_name: input_data})
           end = time.time()     # stop timer
           return {"result": postprocess(result),
                   "time": end - start}
       except Exception as e:
           result = str(e)
           return {"error": result}
   ```

   Le fichier `myenv.yml` décrit les dépendances nécessaires pour l’image. Consultez ce [tutoriel](tutorial-deploy-models-with-aml.md#create-environment-file) pour obtenir des instructions sur la façon de créer un fichier d’environnement, tel que cet exemple de fichier :

   ```python
   from azureml.core.conda_dependencies import CondaDependencies 

   myenv = CondaDependencies.create(pip_packages=["numpy","onnxruntime","azureml-core"])

   with open("myenv.yml","w") as f:
    f.write(myenv.serialize_to_string())
   ```

4. Pour déployer votre modèle, consultez le document [Comment et où déployer](how-to-deploy-and-where.md).


## <a name="examples"></a>Exemples

Consultez [how-to-utilisation-azureml/déploiement/onnx](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx) pour obtenir des exemples de notebooks qui créent et déploient des modèles ONNX.

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="more-info"></a>En savoir plus

Apprenez-en davantage sur ONNX ou contribuez au projet :
+ [Site web du projet ONNX](https://onnx.ai)

+ [Code ONNX sur GitHub](https://github.com/onnx/onnx)

Découvrez plus d’informations sur ONNX ou contribuez au projet :
+ [Dépôt GitHub d’ONNX Runtime](https://github.com/Microsoft/onnxruntime)


