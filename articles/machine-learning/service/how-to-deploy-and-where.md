---
title: Déployer des modèles en tant que services web
titleSuffix: Azure Machine Learning service
description: 'Découvrez comment et où déployer vos modèles de service Azure Machine Learning, notamment : Azure Container Instances, Azure Kubernetes Service, Azure IoT Edge et FPGA (Field-Programmable Gate Arrays).'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: caafd5ac43ca94f8b01298b4e18e48065b7001b9
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/06/2019
ms.locfileid: "55766620"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>Déployer des modèles avec le service Azure Machine Learning

Le service Azure Machine Learning vous permet de déployer votre modèle formé de différentes façons à l'aide du kit de développement logiciel (SDK). Dans ce document, découvrez comment déployer votre modèle en tant que service web dans le cloud Azure ou sur des appareils IoT Edge.

> [!IMPORTANT]
> Le partage des ressources cross-origin (CORS) n’est pas pris en charge pendant le déploiement d’un modèle en tant que service web.

Vous pouvez déployer des modèles sur les cibles de calcul suivantes :

| Cible de calcul | Type de déploiement | Description |
| ----- | ----- | ----- |
| [Azure Container Instances (ACI)](#aci) | Service Web | Déploiement rapide. Convient pour le développement ou le test. |
| [Azure Kubernetes Service (AKS)](#aks) | Service Web | Convient aux déploiements de production à grande échelle. Fournit la mise à l’échelle automatique et des temps de réponse rapides. |
| [Azure IoT Edge](#iotedge) | Module IoT | Déploiement de modèles sur des appareils IoT. Une inférence se produit sur l’appareil. |
| [FPGA (Field-Programmable Gate Array)](#fpga) | Service Web | Latence ultra-faible pour l'inférence en temps réel. |

Le processus de déploiement d'un modèle est similaire pour toutes les cibles de calcul :

1. Entraînez et inscrivez un modèle.
1. Configurez et inscrivez une image qui utilise le modèle.
1. Déployez l'image sur une cible de calcul.
1. test du déploiement

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Kwk3]


Pour plus d’informations sur les concepts impliqués dans le workflow de déploiement, consultez [Déployer, gérer et surveiller des modèles avec le service Azure Machine Learning](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning service](http://aka.ms/AMLFree) dès aujourd’hui.

- Un espace de travail du service Azure Machine Learning et le kit SDK Azure Machine Learning pour Python installé. Découvrez comment obtenir ces prérequis dans le guide de démarrage rapide [Bien démarrer avec Azure Machine Learning](quickstart-get-started.md).

- Un modèle entraîné. Si vous n'avez pas de modèle formé, suivez la procédure du tutoriel [​​Former des modèles](tutorial-train-models-with-aml.md) pour en former et en inscrire un auprès du service Azure Machine Learning.

    > [!NOTE]
    > Bien que le service Azure Machine Learning puisse utiliser n’importe quel modèle générique pouvant être chargé dans Python 3, les exemples dans ce document illustrent l’utilisation d’un modèle stocké dans le format pickle.
    > 
    > Pour plus d'informations sur l'utilisation des modèles ONNX, consultez le document [ONNX et Azure Machine Learning](how-to-build-deploy-onnx.md).

## <a id="registermodel"></a> Inscrire un modèle entraîné

Le registre de modèle est un moyen de stocker et d’organiser vos modèles entraînés dans le cloud Azure. Les modèles sont inscrits dans votre espace de travail de service Azure Machine Learning. Le modèle peut être formé à l'aide d'Azure Machine Learning ou d'un autre service. Pour inscrire un modèle à partir d'un fichier, utilisez le code suivant :

```python
from azureml.core.model import Model

model = Model.register(model_path = "model.pkl",
                       model_name = "Mymodel",
                       tags = {"key": "0.1"},
                       description = "test",
                       workspace = ws)
```

**Durée estimée** : Environ 10 secondes.

Pour plus d'informations, consultez la documentation de référence de la classe [Model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

## <a id="configureimage"></a> Créer et inscrire une image

Les modèles déployés sont empaquetés sous forme d'image. L'image contient les dépendances nécessaires à l'exécution du modèle.

Pour les déploiements **Azure Container Instances**, **Azure Kubernetes Service** et **Azure IoT Edge**, la classe [azureml.core.image.ContainerImage](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py) permet de créer une configuration d’image. La configuration d'image est ensuite utilisée pour créer une image Docker. 

Le code suivant montre comment créer une configuration d'image :

```python
from azureml.core.image import ContainerImage

# Image configuration
image_config = ContainerImage.image_configuration(execution_script = "score.py",
                                                 runtime = "python",
                                                 conda_file = "myenv.yml",
                                                 description = "Image with ridge regression model",
                                                 tags = {"data": "diabetes", "type": "regression"}
                                                 )
```

**Durée estimée** : Environ 10 secondes.

Les paramètres importants de cet exemple sont décrits dans le tableau suivant :

| Paramètre | Description |
| ----- | ----- |
| `execution_script` | Spécifie un script Python qui est utilisé pour recevoir les demandes soumises au service. Dans cet exemple, le script se trouve dans le fichier `score.py`. Pour plus d’informations, consultez la section [Script d’exécution](#script). |
| `runtime` | Indique que l’image utilise Python. L’autre option est `spark-py`, qui utilise Python avec Apache Spark. |
| `conda_file` | Permet de fournir un fichier d’environnement conda. Ce fichier définit l'environnement conda du modèle déployé. Pour plus d'informations sur la création de ce fichier, consultez [Créer un fichier d'environnement (myenv.yml)](tutorial-deploy-models-with-aml.md#create-environment-file). |

Pour plus d'informations, consultez la documentation de référence de la classe [ContainerImage](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py).

### <a id="script"></a> Script d’exécution

Le script d’exécution reçoit des données soumises à une image déployée et les transmet au modèle. Ensuite, il prend la réponse retournée par le modèle et la retourne au client. Le script est spécifique à votre modèle ; il doit comprendre les données que le modèle attend et retourne. Le script contient généralement deux fonctions qui chargent et exécutent le modèle :

* `init()`: en général, cette fonction charge le modèle dans un objet global. Cette fonction est exécutée une seule fois lors du démarrage du conteneur Docker. 

* `run(input_data)`: cette fonction utilise le modèle pour prédire une valeur basée sur les données d'entrée. Les entrées et les sorties de l’exécution utilisent en général JSON pour la sérialisation et la désérialisation. Vous pouvez également utiliser des données binaires brutes. Vous pouvez transformer les données avant de les envoyer au modèle ou avant de les retourner au client. 

#### <a name="working-with-json-data"></a>Utilisation de données JSON

L’exemple de script suivant accepte et retourne des données JSON. La fonction `run` transforme les données à partir du format JSON en un format que le modèle attend, puis transforme la réponse au format JSON avant de les retourner :

```python
# import things required by this script
import json
import numpy as np
import os
import pickle
from sklearn.externals import joblib
from sklearn.linear_model import LogisticRegression

from azureml.core.model import Model

# load the model
def init():
    global model
    # retrieve the path to the model file using the model name
    model_path = Model.get_model_path('sklearn_mnist')
    model = joblib.load(model_path)

# Passes data to the model and returns the prediction
def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    y_hat = model.predict(data)
    return json.dumps(y_hat.tolist())
```

#### <a name="working-with-binary-data"></a>Utilisation de données binaires

Si votre modèle accepte les __données binaires__, utilisez `AMLRequest`, `AMLResponse` et `rawhttp`. L’exemple de script suivant accepte des données binaires et retourne les octets inversés pour les requêtes POST. Pour les requêtes GET, il retourne l’URL complète dans le corps de réponse :

```python
from azureml.contrib.services.aml_request  import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse

def init():
    print("This is init()")

# Accept and return binary data
@rawhttp
def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    # handle GET requests
    if request.method == 'GET':
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    # handle POST requests
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        respBody = bytearray(reqBody)
        respBody.reverse()
        respBody = bytes(respBody)
        return AMLResponse(respBody, 200)
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> L’espace de noms `azureml.contrib` change fréquemment car nous travaillons à améliorer le service. Par conséquent, tout ce qui est compris dans cet espace de noms doit être considéré comme une préversion et n’est pas entièrement pris en charge par Microsoft.
>
> Si vous avez besoin d’effectuer ce test sur votre environnement de développement local, vous pouvez installer les composants dans l’espace de noms à l’aide de la commande suivante : 
> ```shell
> pip install azureml-contrib-services
> ```

### <a id="createimage"></a> Inscrire l’image

Une fois la configuration d’image créée, vous pouvez l’utiliser pour inscrire une image. Cette image est stockée dans le registre de conteneurs de votre espace de travail. Une fois l'image créée, vous pouvez la déployer sur plusieurs services.

```python
# Register the image from the image configuration
image = ContainerImage.create(name = "myimage", 
                              models = [model], #this is the model object
                              image_config = image_config,
                              workspace = ws
                              )
```

**Durée estimée** : environ trois minutes.

Des versions sont automatiquement attribuées aux images lorsque vous en inscrivez plusieurs avec le même nom. Par exemple, la première image inscrite sous le nom `myimage` reçoit l'ID `myimage:1`. La prochaine fois que vous inscrirez une image sous le nom `myimage`, l'ID de la nouvelle image sera `myimage:2`.

Pour plus d'informations, consultez la documentation de référence de la classe [ContainerImage](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py).

## <a id="deploy"></a> Déployer l’image

Le processus de déploiement varie légèrement en fonction de la cible de calcul. Utilisez les informations des sections suivantes pour apprendre à effectuer un déploiement sur :

* [Azure Container Instances](#aci)
* [Azure Kubernetes Services](#aks)
* [Project Brainwave (Field-Programmable Gate Arrays)](#fpga)
* [Appareils Azure IoT Edge](#iotedge)

> [!NOTE]
> Dans le cadre d’un **déploiement en tant que service web**, vous disposez de trois méthodes de déploiement :
>
> | Méthode | Notes |
> | ----- | ----- |
> | [deploy_from_image](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-from-image-workspace--name--image--deployment-config-none--deployment-target-none-) | Avant d’utiliser cette méthode, vous devez inscrire le modèle et créer une image. |
> | [deploy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-workspace--name--model-paths--image-config--deployment-config-none--deployment-target-none-) | avec cette méthode, il n'est pas nécessaire d'inscrire le modèle ni de créer l'image. Cependant, vous n'avez aucun contrôle sur le nom du modèle ou de l'image, ni sur les balises et descriptions associées. |
> | [deploy_from_model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-) | avec cette méthode, il n'est pas nécessaire de créer une image. Mais vous n'avez aucun contrôle sur le nom de l'image créée. |
>
> Les exemples fournis dans ce document utilisent `deploy_from_image`.

### <a id="aci"></a> Procéder à un déploiement sur Azure Container Instances

Utilisez Azure Container Instances pour déployer vos modèles en tant que service web si une ou plusieurs des conditions suivantes sont remplies :

- Vous avez besoin de déployer et de valider rapidement votre modèle. Le déploiement ACI se termine en moins de 5 minutes.
- Vous testez un modèle en cours de développement. Pour plus d'informations sur les quotas et la disponibilité d'ACI en fonction des régions, consultez le document [Disponibilité des régions et quotas pour Azure Container Instances](https://docs.microsoft.com/azure/container-instances/container-instances-quotas).

Pour effectuer un déploiement sur Azure Container Instances, procédez comme suit :

1. Définissez la configuration du déploiement. L'exemple suivant définit une configuration qui utilise un seul cœur de processeur et 1 Go de mémoire :

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-deploy-to-aci/how-to-deploy-to-aci.py?name=configAci)]

2. Pour déployer l'image créée dans la section [Créer l'image](#createimage) de ce document, utilisez le code suivant :

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-deploy-to-aci/how-to-deploy-to-aci.py?name=option3Deploy)]

    **Durée estimée** : environ trois minutes.

Pour plus d'informations, consultez la documentation de référence des classes [AciWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py) et [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py).

### <a id="aks"></a> Procéder à un déploiement sur Azure Kubernetes Service

Pour déployer votre modèle en tant que service web de production à grande échelle, utilisez Azure Kubernetes Service (AKS). Vous pouvez utiliser un cluster AKS existant ou en créer un en utilisant le kit SDK Azure Machine Learning, l’interface CLI ou le portail Azure.

La création d’un cluster AKS est un processus qui n’est à effectuer qu’une seule fois pour votre espace de travail. Vous pouvez le réutiliser pour vos autres déploiements. Si vous supprimez le cluster, vous devrez créer un cluster lors du prochain déploiement.

Azure Kubernetes Service offre les fonctionnalités suivantes :

* Mise à l’échelle automatique
* Journalisation
* Collection de données de modèle
* Temps de réponse rapides pour vos services web

#### <a name="create-a-new-cluster"></a>Créer un cluster

Pour créer un cluster Azure Kubernetes Service, utilisez le code suivant :

> [!IMPORTANT]
> La création du cluster AKS est un processus qui n’est à effectuer qu’une seule fois pour votre espace de travail. Une fois créé, vous pouvez le réutiliser pour vos autres déploiements. Si vous supprimez le cluster ou le groupe de ressources dans lequel il se trouve, vous devrez créer un cluster lors du prochain déploiement.
> Pour [`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py), si vous choisissez des valeurs personnalisées pour agent_count et vm_size, vous devez vous assurer que agent_count multiplié par vm_size est supérieur ou égal à 12 processeurs virtuels. Par exemple, si vous définissez vm_size sur « Standard_D3_v2 », qui comporte 4 processeurs virtuels, vous devez définir agent_count sur un nombre supérieur ou égal à 3.

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'aml-aks-1' 
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws, 
                                    name = aks_name, 
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```

**Durée estimée** : environ 20 minutes.

#### <a name="use-an-existing-cluster"></a>Utiliser un cluster existant

Si vous disposez déjà d'un cluster AKS dans le cadre de votre abonnement Azure, et qu'il s'agit de la version 1.11.*, vous pouvez l'utiliser pour déployer votre image. Le code suivant montre comment associer un cluster existant à votre espace de travail :

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Set the resource group that contains the AKS cluster and the cluster name
resource_group = 'myresourcegroup'
cluster_name = 'mycluster'

# Attach the cluster to your workgroup
attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
aks_target = ComputeTarget.attach(ws, 'mycompute', attach_config)

# Wait for the operation to complete
aks_target.wait_for_completion(True)
```

**Durée estimée** : environ trois minutes.

#### <a name="deploy-the-image"></a>Déployer l'image

Pour déployer sur le cluster Azure Kubernetes Service l’image créée dans la section [Créer l’image](#createimage) de ce document, utilisez le code suivant :

```python
from azureml.core.webservice import Webservice, AksWebservice

# Set configuration and service name
aks_config = AksWebservice.deploy_configuration()
aks_service_name ='aks-service-1'
# Deploy from image
service = Webservice.deploy_from_image(workspace = ws, 
                                            name = aks_service_name,
                                            image = image,
                                            deployment_config = aks_config,
                                            deployment_target = aks_target)
# Wait for the deployment to complete
service.wait_for_deployment(show_output = True)
print(service.state)
```

**Durée estimée** : environ trois minutes.

Pour plus d'informations, consultez la documentation de référence des classes [AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py) et [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py).

### <a id="fpga"></a> Procéder à un déploiement sur FPGA (Field-Programmable Gate Arrays)

Project Brainwave permet d'atteindre une très faible latence pour les requêtes d'inférence en temps réel. Project Brainwave accélère les réseaux neuronaux profonds déployés sur des tableaux FPGA dans le cloud Azure. Les réseaux neuronaux profonds fréquemment utilisés sont disponibles en tant que caractériseurs pour l’apprentissage par transfert ou personnalisables avec des pondérations entraînées à partir de vos propres données.

Pour déployer un modèle à l'aide de Project Brainwave, consultez le document [Procéder à un déploiement sur FPGA (Field-Programmable Gate Arrays)](how-to-deploy-fpga-web-service.md).

### <a id="iotedge"></a> Procéder à un déploiement sur Azure IoT Edge

Un appareil Azure IoT Edge est un appareil Linux ou Windows qui exécute le runtime Azure IoT Edge. Les modèles Machine Learning peuvent être déployés sur ces appareils comme des modules IoT Edge. Le déploiement d’un modèle sur un appareil IoT Edge permet à celui-ci d’utiliser le modèle directement, au lieu d’avoir à envoyer des données dans le cloud en vue de leur traitement. Les temps de réponse sont alors plus rapides et les transferts de données moins nombreux.

Les modules Azure IoT Edge sont déployés sur votre appareil à partir d'un registre de conteneurs. Lorsque vous créez une image à partir de votre modèle, elle est stockée dans le registre de conteneurs de votre espace de travail.

#### <a name="set-up-your-environment"></a>Configurer votre environnement

* Un environnement de développement. Pour plus d’informations, consultez le document [Guide pratique pour configurer un environnement de développement](how-to-configure-environment.md).

* Un [hub Azure IoT](../../iot-hub/iot-hub-create-through-portal.md) dans votre abonnement Azure. 

* Un modèle entraîné. Pour obtenir un exemple d’entraînement de modèle, consultez le document [Entraîner un modèle de classification d’images avec Azure Machine Learning](tutorial-train-models-with-aml.md). Un modèle préentraîné est disponible sur le [dépôt GitHub AI Toolkit pour Azure IoT Edge](https://github.com/Azure/ai-toolkit-iot-edge/tree/master/IoT%20Edge%20anomaly%20detection%20tutorial).

#### <a name="prepare-the-iot-device"></a>Préparer l’appareil IoT
Vous devez créer un hub IoT et inscrire un appareil ou en réutiliser un à l'aide de [ce script](https://raw.githubusercontent.com/Azure/ai-toolkit-iot-edge/master/amliotedge/createNregister).

``` bash
ssh <yourusername>@<yourdeviceip>
sudo wget https://raw.githubusercontent.com/Azure/ai-toolkit-iot-edge/master/amliotedge/createNregister
sudo chmod +x createNregister
sudo ./createNregister <The Azure subscriptionID you want to use> <Resourcegroup to use or create for the IoT hub> <Azure location to use e.g. eastus2> <the Hub ID you want to use or create> <the device ID you want to create>
```

Enregistrez la chaîne de connexion obtenue après « cs":"{copier cette chaîne} ».

Initialisez votre appareil en téléchargeant [ce script](https://raw.githubusercontent.com/Azure/ai-toolkit-iot-edge/master/amliotedge/installIoTEdge) sur un nœud IoT Edge UbuntuX64 ou sur une DSVM afin d’exécuter les commandes suivantes :

```bash
ssh <yourusername>@<yourdeviceip>
sudo wget https://raw.githubusercontent.com/Azure/ai-toolkit-iot-edge/master/amliotedge/installIoTEdge
sudo chmod +x installIoTEdge
sudo ./installIoTEdge
```

Le nœud IoT Edge est prêt à recevoir la chaîne de connexion de votre hub IoT. Recherchez la ligne ```device_connection_string:``` et collez la chaîne de connexion précédente entre les guillemets.

Vous pouvez également apprendre à inscrire votre appareil et à installer le runtime IoT en suivant le document [Démarrage rapide : Déployer votre premier module IoT Edge sur un appareil Linux x64](../../iot-edge/quickstart-linux.md).


#### <a name="get-the-container-registry-credentials"></a>Obtenir les informations d’identification du registre de conteneurs
Pour déployer un module IoT Edge sur votre appareil, Azure IoT a besoin des informations d'identification du registre de conteneurs dans lequel le service Azure Machine Learning stocke les images Docker.

Vous pouvez récupérer les informations d'identification du registre de conteneurs de deux manières :

+ **À partir du portail Azure** :

  1. Connectez-vous au [Portail Azure](https://portal.azure.com/signin/index).

  1. Accédez à votre espace de travail du service Azure Machine Learning, puis sélectionnez __Vue d’ensemble__. Pour accéder aux paramètres du registre de conteneurs, sélectionnez le lien __Registre__.

     ![Image de l’entrée du registre de conteneurs](./media/how-to-deploy-and-where/findregisteredcontainer.png)

  1. Une fois dans le registre de conteneurs, sélectionnez **Clés d’accès**, puis activez l’utilisateur administrateur.
 
     ![Image de l’écran Clés d’accès](./media/how-to-deploy-and-where/findaccesskey.png)

  1. Enregistrez les valeurs du **serveur de connexion**, du **nom d'utilisateur** et du **mot de passe**. 

+ **Avec un script Python** :

  1. Utilisez le script Python suivant après le code que vous avez exécuté précédemment pour créer un conteneur :

     ```python
     # Getting your container details
     container_reg = ws.get_details()["containerRegistry"]
     reg_name=container_reg.split("/")[-1]
     container_url = "\"" + image.image_location + "\","
     subscription_id = ws.subscription_id
     from azure.mgmt.containerregistry import ContainerRegistryManagementClient
     from azure.mgmt import containerregistry
     client = ContainerRegistryManagementClient(ws._auth,subscription_id)
     result= client.registries.list_credentials(resource_group_name, reg_name, custom_headers=None, raw=False)
     username = result.username
     password = result.passwords[0].value
     print('ContainerURL{}'.format(image.image_location))
     print('Servername: {}'.format(reg_name))
     print('Username: {}'.format(username))
     print('Password: {}'.format(password))
     ```
  1. Enregistrez les valeurs de l'URL du conteneur, du nom du serveur, du nom d'utilisateur et du mot de passe. 

     Ces informations d'identification sont nécessaires pour permettre à l'appareil IoT Edge d'accéder aux images de votre registre de conteneurs privé.

#### <a name="deploy-the-model-to-the-device"></a>Déployer le modèle sur l'appareil

Vous pouvez facilement déployer un modèle en exécutant [ce script](https://raw.githubusercontent.com/Azure/ai-toolkit-iot-edge/master/amliotedge/deploymodel) et en fournissant les informations suivantes obtenues lors des étapes précédentes : nom du registre de conteneurs, nom d'utilisateur, mot de passe, URL de l'emplacement de l'image, nom souhaité pour le déploiement, nom du hub IoT et ID de l'appareil que vous avez créé. Pour ce faire, sur la machine virtuelle, procédez comme suit : 

```bash 
wget https://raw.githubusercontent.com/Azure/ai-toolkit-iot-edge/master/amliotedge/deploymodel
sudo chmod +x deploymodel
sudo ./deploymodel <ContainerRegistryName> <username> <password> <imageLocationURL> <DeploymentID> <IoTHubname> <DeviceID>
```

Vous pouvez également suivre la procédure décrite dans le document [Déployer des modules Azure IoT Edge à partir du portail Azure](../../iot-edge/how-to-deploy-modules-portal.md) pour déployer l'image sur votre appareil. Lors de la configuration des __Paramètres de Registre__ de l'appareil, utilisez le __serveur de connexion__, le __nom d'utilisateur__ et le __mot de passe__ du registre de conteneurs de votre espace de travail.

> [!NOTE]
> Si vous ne connaissez pas Azure IoT, consultez les documents suivants pour savoir comment utiliser le service :
>
> * [Démarrage rapide : Déployer votre premier module IoT Edge sur un appareil Linux](../../iot-edge/quickstart-linux.md)
> * [Démarrage rapide : Déployer votre premier module IoT Edge sur un appareil Windows](../../iot-edge/quickstart.md)


## <a name="testing-web-service-deployments"></a>Tester des déploiements de services web

Pour tester un déploiement de service web, vous pouvez utiliser la méthode `run` de l'objet Webservice. Dans l'exemple suivant, un document JSON est défini sur un service web et le résultat est affiché. Les données envoyées doivent correspondre aux attentes du modèle. Dans cet exemple, le format de données correspond à l'entrée attendue par le modèle diabetes.

```python
import json

test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10], 
    [10,9,8,7,6,5,4,3,2,1]
]})
test_sample = bytes(test_sample,encoding = 'utf8')

prediction = service.run(input_data = test_sample)
print(prediction)
```

Le service web étant une API REST, vous pouvez créer des applications clientes dans un large éventail de langages de programmation. Pour plus d’informations, consultez [Créer des applications clientes pour utiliser des services web](how-to-consume-web-service.md).

## <a id="update"></a> Mettre à jour le service web

Lorsque vous créez une image, vous devez manuellement mettre à jour chacun des services pour lesquels vous souhaitez utiliser la nouvelle image. Pour effectuer une mise à jour du service web, utilisez la méthode `update`. Le code suivant montre comment effectuer une mise à jour du service web pour utiliser une nouvelle image :

```python
from azureml.core.webservice import Webservice
from azureml.core.image import Image

service_name = 'aci-mnist-3'
# Retrieve existing service
service = Webservice(name = service_name, workspace = ws)

# point to a different image
new_image = Image(workspace = ws, id="myimage2:1")

# Update the image used by the service
service.update(image = new_image)
print(service.state)
```

Pour plus d’informations, consultez la documentation de référence de la classe [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py).

## <a name="clean-up"></a>Nettoyer

Pour supprimer un service web déployé, utilisez `service.delete()`.

Pour supprimer une image, utilisez `image.delete()`.

Pour supprimer un modèle inscrit, utilisez `model.delete()`.

Pour plus d’informations, consultez la documentation de référence de [WebService.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--), [Image.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.image(class)?view=azure-ml-py#delete--) et [Model.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--).

## <a name="troubleshooting"></a>Résolution de problèmes

* __Si des erreurs se produisent au cours du déploiement__, utilisez `service.get_logs()` pour afficher les journaux du service. Les informations journalisées peuvent indiquer la cause de l’erreur.

* Les journaux peuvent contenir une erreur qui vous indique de __définir le niveau de journalisation sur DÉBOGAGE__. Pour définir le niveau de journalisation, ajoutez les lignes suivantes à votre script de scoring, créez l’image, puis créez un service utilisant l’image :

    ```python
    import logging
    logging.basicConfig(level=logging.DEBUG)
    ```

    Cette modification permet une journalisation supplémentaire et peut retourner des informations complémentaires sur la raison pour laquelle l’erreur se produit.

## <a name="next-steps"></a>Étapes suivantes

* [Sécuriser les services web Azure Machine Learning avec SSL](how-to-secure-web-service.md)
* [Utiliser un modèle ML déployé en tant que service web](how-to-consume-web-service.md)
* [Exécuter des prédictions par lots](how-to-run-batch-predictions.md)
* [Superviser vos modèles Azure Machine Learning avec Application Insights](how-to-enable-app-insights.md)
* [Collecter des données pour des modèles en production](how-to-enable-data-collection.md)
* [SDK du service Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Utiliser Azure Machine Learning service avec des réseaux virtuels Azure](how-to-enable-virtual-network.md)
* [Bonnes pratiques pour la création de systèmes de recommandation](https://github.com/Microsoft/Recommenders)
* [Générer une API de recommandation en temps réel sur Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
