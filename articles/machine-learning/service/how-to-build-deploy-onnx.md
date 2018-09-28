---
title: ONNX et Azure Machine Learning | Créer et déployer des modèles
description: Découvrez ONNX et comment utiliser Azure Machine Learning pour créer et déployer des modèles ONNX.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: prasantp
author: prasanthpul
ms.date: 09/24/2018
ms.openlocfilehash: f453fff59abc1441b2fb16049f130d2c19460083
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46970802"
---
# <a name="onnx-and-azure-machine-learning-create-and-deploy-interoperable-ai-models"></a>ONNX et Azure Machine Learning : Créer et déployer des modèles d’intelligence artificielle interopérables

Le format [ONNX (Open Neural Network Exchange)](http://onnx.ai) est une norme ouverte de représentation des modèles Machine Learning. ONNX est pris en charge par une [communauté de partenaires](http://onnx.ai/supported-tools), notamment Microsoft, qui créent des outils et des frameworks compatibles. Microsoft s’est engagé à promouvoir une intelligence artificielle ouverte et interopérable afin de permettre aux scientifiques des données et aux développeurs de :

+ Utiliser le framework de leur choix pour créer et entraîner des modèles.
+ Déployer des modèles multiplateformes avec un travail d’intégration minimal

Microsoft prend en charge ONNX dans toute sa gamme de produits, notamment Azure et Windows, pour vous aider à atteindre ces objectifs.  

## <a name="why-choose-onnx"></a>Pourquoi choisir ONNX ?
L’interopérabilité que vous obtenez avec ONNX permet d’accélérer la mise en production de vos idées. Avec ONNX, les scientifiques des données peuvent choisir leur framework préféré pour effectuer leur travail. De même, les développeurs consacreront moins de temps à préparer les modèles pour la production et à déployer sur le cloud et la périphérie.  

Vous pouvez exporter des modèles ONNX à partir de nombreux frameworks, notamment PyTorch, Chainer, Microsoft Cognitive Toolkit (CNTK), MXNet et ML.Net. Il existe des convertisseurs pour d’autres frameworks tels que TensorFlow, Keras, SciKit-Learn et bien plus encore.

Il existe également un écosystème d’outils de visualisation et d’accélération des modèles ONNX. Plusieurs modèles ONNX préentraînés sont également disponibles pour les scénarios courants.

[Les modèles ONNX peuvent être déployés](#deploy) sur le cloud à l’aide d’Azure Machine Learning et du runtime ONNX. Ils peuvent également être déployés sur des appareils Windows 10 à l’aide de [Windows ML](https://docs.microsoft.com/windows/ai/). Ils peuvent même être déployés sur d’autres plateformes, grâce aux convertisseurs mis à disposition par la communauté ONNX. 

[ ![Organigramme ONNX montrant l’entraînement, les convertisseurs et le déploiement](media/concept-onnx/onnx.png) ] (./media/concept-onnx/onnx.png#lightbox)

## <a name="create-onnx-models-in-azure"></a>Créer des modèles ONNX dans Azure

Vous pouvez créer des modèles ONNX de plusieurs façons :
+ Entraîner un modèle dans le service Azure Machine Learning et le convertir ou l’exporter vers ONNX (voir l’exemple en bas de cet article)

+ Obtenir un modèle ONNX préentraîné à partir du [Zoo de modèles ONNX](https://github.com/onnx/models)

+ Générer un modèle ONNX personnalisé à partir du [service Vision personnalisée Azure](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/)

## <a name="exportconvert-your-models-to-onnx"></a>Exporter/convertir vos modèles ONNX

Vous pouvez également convertir vos modèles existants au format ONNX.
+ Pour les modèles **PyTorch**, essayez [ce bloc-notes Jupyter](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb).

+ Pour les modèles **Microsoft Cognitive Toolkit (CNTK)**, essayez [ce bloc-notes Jupyter](https://github.com/onnx/tutorials/blob/master/tutorials/CntkOnnxExport.ipynb).

+ Pour les modèles **Chainer**, essayez [ce bloc-notes Jupyter](https://github.com/onnx/tutorials/blob/master/tutorials/ChainerOnnxExport.ipynb).

+ Pour les modèles **MXNet**, essayez [ce bloc-notes Jupyter](https://github.com/onnx/tutorials/blob/master/tutorials/MXNetONNXExport.ipynb).

+ Pour les modèles **TensorFlow**, utilisez le convertisseur [tensorflow-onnx](https://github.com/onnx/tensorflow-onnx).

+ Pour les modèles **Keras**, **ScitKit-Learn**, **CoreML**, **XGBoost** et **libSVM**, convertissez au format ONNX à l’aide du package [WinMLTools](https://docs.microsoft.com/windows/ai/convert-model-winmltools).

Vous trouverez la liste la plus récente des frameworks et des convertisseurs pris en charge sur le [site des tutoriels ONNX](https://github.com/onnx/tutorials).

<a name="deploy"></a>

## <a name="deploy-onnx-models-in-azure"></a>Déployer des modèles ONNX dans Azure

Avec le service Azure Machine Learning, vous pouvez déployer, gérer et superviser vos modèles ONNX. À l’aide du [workflow de déploiement](concept-model-management-and-deployment.md) standard et du runtime ONNX, vous pouvez créer un point de terminaison REST hébergé dans le cloud. Pour essayer par vous-même, consultez l’exemple complet de bloc-notes Jupyter fourni à la fin de cet article. 

### <a name="install-and-configure-the-onnx-runtime"></a>Installer et configurer le runtime ONNX

Le runtime ONNX est un moteur d’inférence à hautes performances pour les modèles ONNX. Il est fourni avec une API Python et procure une accélération matérielle sur UC et GPU. Actuellement, il prend en charge les modèles ONNX 1.2 et s’exécute sur Linux Ubuntu 16.04.

Pour installer le runtime ONNX, utilisez :
```python
pip install onnxruntime
```

Pour appeler le runtime ONNX dans votre script Python, utilisez :
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

Pour accéder à la référence d’API complète, consultez la [documentation](https://docs.microsoft.com/en-us/python/api/overview/azure/main?view=azure-onnx-py).

### <a name="example-deployment-steps"></a>Exemples d’étapes de déploiement

Voici un exemple de déploiement d’un modèle ONNX :

1. Initialisez votre espace de travail Azure Machine Learning. Si vous n’en avez pas encore, découvrez comment créer un espace de travail dans [ce guide de démarrage rapide](quickstart-get-started.md).

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
       global model_path
       model_path = Model.get_model_path(model_name = 'MyONNXmodel')

   def run(raw_data):
       try:
           data = json.loads(raw_data)['data']
           data = np.array(data)
        
           sess = onnxruntime.InferenceSession(model_path)
           result = sess.run(["outY"], {"inX": data})
        
           return json.dumps({"result": result.tolist()})
       except Exception as e:
           result = str(e)
           return json.dumps({"error": result})
   ```

   Le fichier `myenv.yml` décrit les dépendances nécessaires pour l’image. Consultez ce [tutoriel](tutorial-deploy-models-with-aml.md#create-environment-file) pour obtenir des instructions sur la façon de créer un fichier d’environnement, tel que cet exemple de fichier :

   ```
   name: myenv
   channels:
     - defaults
   dependencies:
     - pip:
       - onnxruntime
       - azureml-core
   ```

4. Déployez votre modèle ONNX avec Azure Machine Learning sur :
   + Azure Container Instances (ACI) : [Découvrez comment...](how-to-deploy-to-aci.md)

   + Azure Kubernetes Service (AKS) : [Découvrez comment...](how-to-deploy-to-aks.md)


## <a name="examples"></a>Exemples
 
Les blocs-notes suivants illustrent comment déployer des modèles ONNX avec Azure Machine Learning : 
+ `/onnx/onnx-inference-mnist.ipynb`
 
+ `/onnx/onnx-inference-emotion-recognition.ipynb`
 
Consultez ce bloc-notes :
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="more-info"></a>En savoir plus

Apprenez-en davantage sur ONNX ou contribuez au projet :
+ [Site web du projet ONNX](http://onnx.ai)

+ [Code ONNX sur GitHub](https://github.com/onnx/onnx)
