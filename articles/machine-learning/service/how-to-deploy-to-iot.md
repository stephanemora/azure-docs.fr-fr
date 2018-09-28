---
title: Déployer des modèles sur des appareils IoT Edge à partir du service Azure Machine Learning | Microsoft Docs
description: Découvrez comment déployer un modèle à partir du service Azure Machine Learning sur des appareils Azure IoT Edge. Le déploiement d’un modèle sur un appareil de périmètre permet d’effectuer le scoring des données de l’appareil, au lieu de les envoyer vers le cloud et d’attendre une réponse.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: shipatel
author: shivanipatel
manager: cgronlun
ms.reviewer: larryfr
ms.date: 09/24/2018
ms.openlocfilehash: 6ce7a5501c4b294fb6f8a655597122e2a04b4547
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46979241"
---
# <a name="prepare-to-deploy-models-on-iot-edge"></a>Préparer le déploiement des modèles sur IoT Edge

Dans ce document, vous allez voir comment utiliser le service Azure Machine Learning afin de préparer un modèle entraîné pour le déploiement sur un appareil Azure IoT Edge.

Un appareil Azure IoT Edge est un appareil Linux ou Windows qui exécute le runtime Azure IoT Edge. Les modèles Machine Learning peuvent être déployés sur ces appareils comme des modules IoT Edge. Le déploiement d’un modèle sur un appareil IoT Edge permet à celui-ci d’utiliser le modèle directement, au lieu d’avoir à envoyer des données dans le cloud en vue de leur traitement. Les temps de réponse sont alors plus rapides et les transferts de données moins nombreux.

Avant de déployer un modèle sur un appareil de périmètre, utilisez les étapes décrites dans ce document pour préparer le modèle et l’appareil.

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’en avez pas, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

* Un espace de travail de service Microsoft Azure Machine Learning. Pour en créer un, effectuez les étapes décrites dans le document [Bien démarrer avec le service Azure Machine Learning](quickstart-get-started.md).

* Un environnement de développement. Pour plus d’informations, consultez le document [Guide pratique pour configurer un environnement de développement](how-to-configure-environment.md).

* Un [hub Azure IoT](../../iot-hub/iot-hub-create-through-portal.md) dans votre abonnement Azure. 

* Un modèle entraîné. Pour obtenir un exemple d’entraînement de modèle, consultez le document [Entraîner un modèle de classification d’images avec Azure Machine Learning](tutorial-train-models-with-aml.md).

## <a name="prepare-the-iot-device"></a>Préparer l’appareil IoT

Pour savoir comment inscrire votre appareil et installer le runtime IoT, suivez les étapes du document [Démarrage rapide : Déployer votre premier module IoT Edge sur un appareil Linux x64](../../iot-edge/quickstart-linux.md).

## <a name="register-the-model"></a>Inscrire le modèle

Les modules Azure IoT Edge sont basés sur des images conteneur. Pour déployer votre modèle sur un appareil IoT Edge, effectuez les étapes suivantes pour inscrire votre modèle dans un espace de travail Azure Machine Learning et créer une image Docker. 

> [!IMPORTANT]
> Si vous utilisez Azure Machine Learning pour entraîner votre modèle, il se peut qu’il soit déjà inscrit dans votre espace de travail. Si c’est le cas, ignorez l’étape 3.

1. Initialisez l’espace de travail et chargez le fichier config.json :

    ```python
    from azureml.core  import Workspace

    #Load existing workspace from the the config file info.
    ws  = Workspace.from_config()
    ```    

1. Inscrivez le modèle dans votre espace de travail. Remplacez le texte par défaut par le chemin, le nom, les étiquettes et la description du modèle :

    ```python
    from azureml.core.model import Model
    model = Model.register(model_path = "model.pkl", # this path points to the local file
                        model_name = "best_model", # the model gets registered as this name
                        tags = {'attribute': "myattribute", 'classification': "myclassification"},
                        description = "My awesome model",
                        workspace = ws)
    ```    

1. Récupérez le modèle inscrit : 

    ```python
    from azureml.core.model import Model

    model_name = "best_model"
    model = Model(ws, model_name)                     
    ```    

## <a name="create-a-docker-image"></a>Créer une image Docker

1. Créez un **script de scoring** nommé `score.py`. Ce fichier est utilisé pour exécuter le modèle dans l’image. Il doit inclure les fonctions suivantes :

    * La fonction `init()`, qui charge en général le modèle dans un objet global. Cette fonction est exécutée une seule fois lors du démarrage du conteneur Docker. 

    * La fonction `run(input_data)` utilise le modèle pour prédire une valeur sur la base des données d’entrée. Les entrées et les sorties de l’exécution utilisent en général JSON pour la sérialisation et la désérialisation, mais d’autres formats sont pris en charge.

    Pour obtenir un exemple, consultez le [tutoriel sur la classification des images](tutorial-deploy-models-with-aml.md#make-script).

1. Créez un **fichier d’environnement** nommé `myenv.yml`. Ce fichier est une spécification de l’environnement Conda. Il répertorie toutes les dépendances dont ont besoin le script et le modèle. Pour obtenir un exemple, consultez le [tutoriel sur la classification des images](tutorial-deploy-models-with-aml.md#make-myenv).

1. Configurez l’image Docker en utilisant les fichiers `score.py` et `myenv.yml` :
    
    ```python
    from azureml.core.image import Image, ContainerImage
    
    #Image configuration
    image_config = ContainerImage.image_configuration( runtime = "python", 
                           execution_script = "score.py",
                           conda_file = "myenv.yml", 
                           tags = {"attributes", "calssification"},
                           description = "Image that contains my model",
                           
                        )
    ```    

1. Créez l’image à l’aide du modèle et de la configuration de l’image :

    ```python
    image = ContainerImage.create (name = "myimage", 
                           models = [model], #this is the model object
                           image_config = image_config,
                           workspace = ws
                        )
    ```     

    La création d’une image prend environ cinq minutes.

## <a name="get-the-container-registry-credentials"></a>Obtenir les informations d’identification du registre de conteneurs

Azure IoT a besoin des informations d’identification du registre de conteneurs dans lequel le service Azure Machine Learning stocke les images Docker. Pour obtenir les informations d’identification, effectuez les étapes suivantes :

1. Connectez-vous au [Portail Azure](https://portal.azure.com/signin/index).

1. Accédez à votre espace de travail Azure Machine Learning, puis sélectionnez __Vue d’ensemble__. Pour accéder aux paramètres du registre de conteneurs, sélectionnez le lien __Registre__.

    ![Image de l’entrée du registre de conteneurs](./media/how-to-deploy-to-iot/findregisteredcontainer.png)

1. Une fois dans le registre de conteneurs, sélectionnez **Clés d’accès**, puis activez l’utilisateur administrateur.

    ![Image de l’écran Clés d’accès](./media/how-to-deploy-to-iot/findaccesskey.png)

1. Enregistrez les valeurs du serveur de connexion, du nom d’utilisateur et du mot de passe. 

   Ces informations d’identification sont nécessaires pour permettre aux appareils IoT Edge d’accéder aux images de votre registre de conteneurs privé.

## <a name="next-steps"></a>Étapes suivantes

Vous venez de terminer la préparation du déploiement. À présent, utilisez les étapes décrites dans le document [Déployer des modules Azure IoT Edge à partir du portail Azure](../../iot-edge/how-to-deploy-modules-portal.md) pour effectuer le déploiement sur votre appareil de périmètre. Lorsque vous configurez les __paramètres du registre__ pour l’appareil, utilisez les informations d’identification que vous avez configurées précédemment.
