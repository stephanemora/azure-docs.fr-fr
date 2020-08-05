---
title: Résolution des problèmes liés au déploiement Docker
titleSuffix: Azure Machine Learning
description: Découvrez comment contourner et résoudre les erreurs courantes de déploiement Docker avec Azure Kubernetes Service et Azure Container Instances à l’aide d’Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: clauren42
ms.author: clauren
ms.reviewer: jmartens
ms.date: 03/05/2020
ms.topic: conceptual
ms.custom: troubleshooting, contperfq4, tracking-python
ms.openlocfilehash: dcb2a50a91bec70dfe5d9adda7518f3510a8c973
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87373196"
---
# <a name="troubleshoot-docker-deployment-of-models-with-azure-kubernetes-service-and-azure-container-instances"></a>Résolution des problèmes de déploiement Docker des modèles avec Azure Kubernetes Service et Azure Container Instances 

Découvrez comment contourner et résoudre les erreurs courantes de déploiement Docker avec Azure Container Instances (ACI) et Azure Kubernetes Service (AKS) à l’aide d’Azure Machine Learning.

## <a name="prerequisites"></a>Prérequis

* Un **abonnement Azure**. Si vous n’en avez pas, essayez la [version gratuite ou payante d’Azure Machine Learning](https://aka.ms/AMLFree).
* Le [Kit de développement logiciel (SDK) Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
* [Interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* [Extension CLI pour Azure Machine Learning](reference-azure-machine-learning-cli.md).
* Pour déboguer localement, vous devez avoir une installation opérationnelle de Docker sur votre système local.

    Pour vérifier votre installation de Docker, utilisez la commande `docker run hello-world` à partir d’un terminal ou d’une invite de commandes. Pour obtenir des informations sur l’installation de Docker ou sur la résolution des erreurs Docker, consultez la [documentation Docker](https://docs.docker.com/).

## <a name="steps-for-docker-deployment-of-machine-learning-models"></a>Étapes pour le déploiement Docker des modèles Machine Learning

Lorsque vous déployez un modèle dans Azure Machine Learning, le système effectue une série de tâches.

L’approche recommandée pour le modèle de déploiement consiste à utiliser l’API [Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) avec un objet [Environment](how-to-use-environments.md) comme paramètre d’entrée. Dans ce cas, le service crée une image Docker de base pendant la phase de déploiement, et monte les modèles requis en un seul appel. Les tâches de déploiement de base sont les suivantes :

1. Inscrire le modèle dans le registre de modèles de l’espace de travail.

2. Définir la configuration de l’inférence :
    1. Créez un objet [Environment](how-to-use-environments.md) basé sur les dépendances que vous spécifiez dans le fichier yaml de l’environnement, ou utilisez l’un de nos environnements provisionnés.
    2. Créez une configuration d’inférence (objet InferenceConfig) basée sur l’environnement et le script de notation.

3. Déployer le modèle sur le service Azure Container Instance (ACI) ou Azure Kubernetes Service (AKS).

Découvrez-en plus sur ce processus dans la présentation de la [gestion des modèles](concept-model-management-and-deployment.md).

## <a name="before-you-begin"></a>Avant de commencer

Si vous rencontrez un problème, la première chose à faire consiste à l’isoler en décomposant la tâche de déploiement (décrite précédemment) en étapes individuelles.

En supposant que vous appliquez la méthode de déploiement nouvelle/recommandée à l’aide de l’API [Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) avec un objet [Environment](how-to-use-environments.md) comme paramètre d’entrée, votre code peut être divisé en trois étapes principales :

1. Inscrire le modèle. Voici un exemple de code :

    ```python
    from azureml.core.model import Model


    # register a model out of a run record
    model = best_run.register_model(model_name='my_best_model', model_path='outputs/my_model.pkl')

    # or, you can register a file or a folder of files as a model
    model = Model.register(model_path='my_model.pkl', model_name='my_best_model', workspace=ws)
    ```

2. Définissez la configuration de l’inférence pour le déploiement :

    ```python
    from azureml.core.model import InferenceConfig
    from azureml.core.environment import Environment


    # create inference configuration based on the requirements defined in the YAML
    myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
    inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
    ```

3. Déployez le modèle à l’aide de la configuration d’inférence créée à l’étape précédente :

    ```python
    from azureml.core.webservice import AciWebservice


    # deploy the model
    aci_config = AciWebservice.deploy_configuration(cpu_cores=1, memory_gb=1)
    aci_service = Model.deploy(workspace=ws,
                           name='my-service',
                           models=[model],
                           inference_config=inference_config,
                           deployment_config=aci_config)
    aci_service.wait_for_deployment(show_output=True)
    ```

Une fois que vous avez décomposé le processus de déploiement en tâches distinctes, nous pouvons examiner certaines des erreurs les plus courantes.

## <a name="debug-locally"></a>Déboguer en local

Si vous rencontrez des problèmes de déploiement d’un modèle sur ACI ou AKS, essayez de le déployer comme un service web local. L’utilisation d’un service web local facilite la résolution des problèmes. L’image Docker contenant le modèle est téléchargée et démarrée sur votre système local.

Vous trouverez un exemple exécutable de [notebook de déploiement local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/deploy-to-local/register-model-deploy-local.ipynb) dans le référentiel [MachineLearningNotebooks](https://github.com/Azure/MachineLearningNotebooks).

> [!WARNING]
> Les déploiements de service web locaux ne sont pas pris en charge pour les scénarios de production.

Pour déployer en local, modifiez votre code pour utiliser `LocalWebservice.deploy_configuration()` afin de créer une configuration de déploiement. Puis, utilisez `Model.deploy()` pour déployer le service. L’exemple suivant déploie un modèle (contenu dans la variable model) en tant que service web local :

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import LocalWebservice


# Create inference configuration based on the environment definition and the entry script
myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
# Create a local deployment, using port 8890 for the web service endpoint
deployment_config = LocalWebservice.deploy_configuration(port=8890)
# Deploy the service
service = Model.deploy(
    ws, "mymodel", [model], inference_config, deployment_config)
# Wait for the deployment to complete
service.wait_for_deployment(True)
# Display the port that the web service is available on
print(service.port)
```

Si vous définissez votre propre YAML de spécification Conda, vous devez faire figurer azureml-defaults avec une version supérieure ou égale à 1.0.45 comme dépendance pip. Ce package contient les fonctionnalités nécessaires pour héberger le modèle en tant que service web.

À ce stade, vous pouvez travailler avec le service comme d’habitude. Par exemple, le code suivant illustre l’envoi des données au service :

```python
import json

test_sample = json.dumps({'data': [
    [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
]})

test_sample = bytes(test_sample, encoding='utf8')

prediction = service.run(input_data=test_sample)
print(prediction)
```

Pour plus d’informations sur la personnalisation de votre environnement Python, consultez [Créer et gérer des environnements pour l’entraînement et le déploiement](how-to-use-environments.md). 

### <a name="update-the-service"></a>Mettre à jour le service

Pendant le test local, vous devrez peut-être mettre à jour le fichier `score.py` pour ajouter la journalisation ou tenter de résoudre les problèmes détectés. Pour recharger les modifications apportées au fichier `score.py`, utilisez `reload()`. Par exemple, le code suivant recharge le script pour le service et lui envoie des données. Les données sont calculées à l’aide du fichier `score.py` mis à jour :

> [!IMPORTANT]
> La méthode `reload` est disponible uniquement pour les déploiements locaux. Pour plus d’informations sur la mise à jour d’un déploiement vers une autre cible de calcul, consultez la section mise à jour de [Déployer des modèles](how-to-deploy-and-where.md#update).

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> Le script est rechargé à partir de l’emplacement spécifié par l’objet `InferenceConfig` utilisé par le service.

Pour modifier le modèle, les dépendances Conda ou la configuration de déploiement, utilisez [update()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#update--args-). L’exemple suivant met à jour le modèle utilisé par le service :

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>Supprimer le service

Pour supprimer le service, utilisez [delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#delete--).

### <a name="inspect-the-docker-log"></a><a id="dockerlog"></a> Examiner le journal Docker

Vous pouvez afficher les messages détaillés du journal du moteur Docker à partir de l’objet de service. Vous pouvez afficher le journal pour les déploiements ACI, AKS et locaux. L’exemple suivant illustre l’impression des journaux.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```
Si la ligne `Booting worker with pid: <pid>` s’affiche plusieurs fois dans les journaux, cela signifie qu’il n’y a pas assez de mémoire pour démarrer le Worker.
Vous pouvez résoudre l’erreur en augmentant la valeur de `memory_gb` dans `deployment_config`.
 
## <a name="container-cannot-be-scheduled"></a>Impossible de planifier le conteneur

Lorsque vous déployez un service sur une cible de calcul Azure Kubernetes Service, Azure Machine Learning tente de planifier le service avec la quantité de ressources demandée. Si, au bout de cinq minutes, aucun nœud n’est disponible dans le cluster avec suffisamment de ressources, le déploiement échouera avec le message `Couldn't Schedule because the kubernetes cluster didn't have available resources after trying for 00:05:00`. Vous pouvez résoudre cette erreur en ajoutant des nœuds supplémentaires, en modifiant la référence SKU de vos nœuds ou en changeant les besoins en ressources de votre service. 

Le message d’erreur indique généralement la ressource insuffisante. Par exemple, un message d’erreur qui indique `0/3 nodes are available: 3 Insufficient nvidia.com/gpu` signifie que le service réclame des GPU et qu’il y a trois nœuds dans le cluster qui n’ont pas de GPU disponibles. Pour résoudre cette erreur, vous pouvez ajouter d’autres nœuds si vous utilisez une référence SKU de GPU, basculer sur une référence SKU compatible GPU sinon ou modifier votre environnement de sorte qu’il ne réclame pas de GPU.  

## <a name="service-launch-fails"></a>Échec du lancement du service

Une fois l’image correctement générée, le système tente de démarrer un conteneur à l’aide de la configuration du déploiement. Dans le cadre du processus de démarrage du conteneur, la fonction `init()` dans votre script de scoring est appelée par le système. Si la fonction `init()` comprend des exceptions non interceptées, l’erreur **CrashLoopBackOff** apparaît peut-être dans le message d’erreur.

Utilisez les informations de la section [Examiner le journal Docker](#dockerlog) pour vérifier les journaux.

## <a name="function-fails-get_model_path"></a>Échec de la fonction : get_model_path()

Souvent, dans la fonction `init()` du script de scoring, la fonction [Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) est appelée pour rechercher un fichier de modèle ou un dossier de fichiers de modèle dans le conteneur. Si le fichier ou le dossier de modèle est introuvable, la fonction échoue. Le moyen le plus simple de déboguer cette erreur consiste à exécuter le code Python ci-dessous dans l’interpréteur de commandes du conteneur :

```python
from azureml.core.model import Model
import logging
logging.basicConfig(level=logging.DEBUG)
print(Model.get_model_path(model_name='my-best-model'))
```

Cet exemple affiche le chemin local (relatif à `/var/azureml-app`) sur le conteneur où votre script de scoring est censé trouver le fichier de modèle ou le dossier. Vous pouvez ensuite vérifier si le fichier ou dossier se trouve effectivement à l’emplacement indiqué.

La définition du niveau de journalisation sur DEBUG peut entraîner l’enregistrement d’informations supplémentaires, qui peuvent être utiles pour identifier l’échec.

## <a name="function-fails-runinput_data"></a>Échec de la fonction : run(input_data)

Si le service est déployé avec succès, mais qu’il plante quand vous publiez des données sur le point de terminaison de scoring, vous pouvez ajouter une instruction d’interception d’erreur dans votre fonction `run(input_data)` afin qu’elle retourne un message d’erreur détaillé. Par exemple :

```python
def run(input_data):
    try:
        data = json.loads(input_data)['data']
        data = np.array(data)
        result = model.predict(data)
        return json.dumps({"result": result.tolist()})
    except Exception as e:
        result = str(e)
        # return error message back to the client
        return json.dumps({"error": result})
```

**Remarque** : Retournez les messages d’erreur à partir de l’appel `run(input_data)` exclusivement à des fins de débogage. Pour des raisons de sécurité, vous ne devez pas renvoyer des messages d’erreur de cette façon dans un environnement de production.

## <a name="http-status-code-502"></a>Code d’état HTTP 502

Un code d’état 502 indique que le service a levé une exception ou s’est planté dans la méthode `run()` du fichier score.py. Utilisez les informations de cet article pour déboguer le fichier.

## <a name="http-status-code-503"></a>Code d’état HTTP 503

Les déploiements Azure Kubernetes Service prennent en charge la mise à l’échelle automatique, ce qui permet d’ajouter des réplicas pour gérer une charge supplémentaire. Toutefois, l’autoscaler est conçu pour gérer des changements **progressifs** de la charge. Si vous recevez des pics importants de demandes par seconde, les clients peuvent recevoir un code d’état HTTP 503.

Il existe deux mesures permettant d’empêcher les codes d’état 503 :

* Modifiez le niveau d’utilisation auquel la mise à l’échelle automatique crée de nouveaux réplicas.
    
    Par défaut, l’utilisation de la cible de mise à l’échelle automatique est définie sur 70 %, ce qui signifie que le service peut gérer les pics de demandes par seconde (RPS) jusqu’à 30 %. Vous pouvez ajuster la cible de l’utilisation en définissant `autoscale_target_utilization` sur une valeur inférieure.

    > [!IMPORTANT]
    > Cette modification n’entraîne pas la création de réplicas *plus rapidement*. Ils sont créés à un seuil d’utilisation inférieur. Au lieu d’attendre jusqu’à ce que le service soit utilisé à 70 %, la modification de la valeur sur 30 % provoque la création de réplicas lors d’une utilisation à 30 %.
    
    Si le service web utilise déjà les réplicas maximum actuels et que des codes d’état 503 subsistent, augmentez la valeur `autoscale_max_replicas` pour augmenter le nombre maximal de réplicas.

* Modifiez le nombre minimal de réplicas. L’augmentation du nombre minimal de réplicas offre un plus grand pool pour gérer les pics entrants.

    Pour augmenter le nombre minimal de réplicas, définissez `autoscale_min_replicas` sur une valeur plus élevée. Vous pouvez calculer les réplicas requis en utilisant le code suivant, en remplaçant les valeurs par des valeurs spécifiques de votre projet :

    ```python
    from math import ceil
    # target requests per second
    targetRps = 20
    # time to process the request (in seconds)
    reqTime = 10
    # Maximum requests per container
    maxReqPerContainer = 1
    # target_utilization. 70% in this example
    targetUtilization = .7

    concurrentRequests = targetRps * reqTime / targetUtilization

    # Number of container replicas
    replicas = ceil(concurrentRequests / maxReqPerContainer)
    ```

    > [!NOTE]
    > Si vous recevez des pics de demande supérieurs au nouveau nombre minimal pouvant être géré par les réplicas, il se peut que des codes d’état 503 réapparaissent. Par exemple, si le trafic vers votre service augmente, vous devrez peut-être augmenter le nombre minimal de réplicas.

Pour plus d’informations sur la configuration de `autoscale_target_utilization`, `autoscale_max_replicas`, et `autoscale_min_replicas`, consultez les informations de référence sur le module [AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py).

## <a name="http-status-code-504"></a>Code d’état HTTP 504

Un code d’état 504 indique que la requête a expiré. Le délai d’expiration par défaut est de une minute.

Vous pouvez augmenter le délai d’expiration ou essayer d’accélérer le service en modifiant le fichier score.py pour supprimer les appels inutiles. Si ces actions ne corrigent pas le problème, utilisez les informations de cet article pour déboguer le fichier score.py. Le code peut être dans un état sans réponse ou une boucle infinie.

## <a name="advanced-debugging"></a>Débogage avancé

Dans certains cas, vous devrez peut-être déboguer interactivement le code Python contenu dans votre modèle de déploiement. Par exemple, si le script d’entrée échoue pour une raison ne pouvant pas être déterminée par une journalisation supplémentaire. À l’aide de Visual Studio Code et de Python Tools pour Visual Studio (PTVSD), vous pouvez attacher au code qui s’exécute dans le conteneur Docker.

> [!IMPORTANT]
> Cette méthode de débogage ne fonctionne pas lorsque `Model.deploy()` et `LocalWebservice.deploy_configuration` sont utilisés pour déployer un modèle localement. Au lieu de cela, vous devez créer une image à l’aide de la méthode [Model.package()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#package-workspace--models--inference-config-none--generate-dockerfile-false-).

Les déploiements de service web locaux nécessitent l’installation d’un Docker de travail sur votre système local. Pour plus d’informations sur l’utilisation de Docker, consultez la [documentation Docker](https://docs.docker.com/).

### <a name="configure-development-environment"></a>Configurer l’environnement de développement

1. Pour installer Python Tools pour Visual Studio (PTVSD) sur votre environnement de développement VS Code local, utilisez la commande suivante :

    ```
    python -m pip install --upgrade ptvsd
    ```

    Pour plus d’informations sur l’utilisation de PTVSD avec VS Code, consultez [Débogage à distance](https://code.visualstudio.com/docs/python/debugging#_remote-debugging).

1. Pour configurer VS Code pour communiquer avec l’image Docker, créez une nouvelle configuration de débogage :

    1. Dans VS Code, sélectionnez le menu __Déboguer__, puis sélectionnez __Ouvrir les configurations__. Un fichier nommé __launch.json__ s’ouvre.

    1. Dans le fichier __launch.json__, recherchez la ligne qui contient `"configurations": [` et insérez le texte suivant après celle-ci :

        ```json
        {
            "name": "Azure Machine Learning: Docker Debug",
            "type": "python",
            "request": "attach",
            "port": 5678,
            "host": "localhost",
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}",
                    "remoteRoot": "/var/azureml-app"
                }
            ]
        }
        ```

        > [!IMPORTANT]
        > Si la section des configurations contient déjà d’autres entrées, ajoutez une virgule (,) après le code que vous avez inséré.

        Cette section est attachée au conteneur Docker à l’aide du port 5678.

    1. Enregistrez le fichier __launch.json__.

### <a name="create-an-image-that-includes-ptvsd"></a>Créez une image qui inclut PTVSD

1. Modifiez l’environnement Conda pour votre déploiement afin qu’il inclue PTVSD. L’exemple suivant illustre son ajout à l’aide du paramètre `pip_packages` :

    ```python
    from azureml.core.conda_dependencies import CondaDependencies 


    # Usually a good idea to choose specific version numbers
    # so training is made on same packages as scoring
    myenv = CondaDependencies.create(conda_packages=['numpy==1.15.4',            
                                'scikit-learn==0.19.1', 'pandas==0.23.4'],
                                 pip_packages = ['azureml-defaults==1.0.45', 'ptvsd'])

    with open("myenv.yml","w") as f:
        f.write(myenv.serialize_to_string())
    ```

1. Pour démarrer PTVSD et attendre une connexion au démarrage du service, ajoutez le code suivant au début de votre fichier `score.py` :

    ```python
    import ptvsd
    # Allows other computers to attach to ptvsd on this IP address and port.
    ptvsd.enable_attach(address=('0.0.0.0', 5678), redirect_output = True)
    # Wait 30 seconds for a debugger to attach. If none attaches, the script continues as normal.
    ptvsd.wait_for_attach(timeout = 30)
    print("Debugger attached...")
    ```

1. Créez une image basée sur la définition de l’environnement et extrayez l’image dans le registre local. Pendant le débogage, vous souhaiterez peut-être apporter des modifications aux fichiers de l’image sans avoir à la recréer. Pour installer un éditeur de texte (vim) dans l’image Docker, utilisez les propriétés `Environment.docker.base_image` et `Environment.docker.base_dockerfile` :

    > [!NOTE]
    > Cet exemple suppose que `ws` pointe vers votre espace de travail Azure Machine Learning et que `model` est le modèle en cours de déploiement. Le fichier `myenv.yml` contient les dépendances Conda créées à l’étape 1.

    ```python
    from azureml.core.conda_dependencies import CondaDependencies
    from azureml.core.model import InferenceConfig
    from azureml.core.environment import Environment


    myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
    myenv.docker.base_image = None
    myenv.docker.base_dockerfile = "FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04\nRUN apt-get update && apt-get install vim -y"
    inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
    package = Model.package(ws, [model], inference_config)
    package.wait_for_creation(show_output=True)  # Or show_output=False to hide the Docker build logs.
    package.pull()
    ```

    Une fois l’image créée et téléchargée, le chemin de l’image (y compris le référentiel, le nom et la balise, qui dans ce cas est également son code de hachage) s’affiche dans un message semblable au suivant :

    ```text
    Status: Downloaded newer image for myregistry.azurecr.io/package@sha256:<image-digest>
    ```

1. Pour faciliter le travail sur l’image, utilisez la commande suivante pour ajouter une balise. Remplacez `myimagepath` par la valeur d’emplacement de l’étape précédente.

    ```bash
    docker tag myimagepath debug:1
    ```

    Pour le reste des étapes, vous pouvez faire référence à l’image locale en tant que `debug:1` au lieu de la valeur du chemin complet de l’image.

### <a name="debug-the-service"></a>Déboguer le service

> [!TIP]
> Si vous définissez un délai d’expiration pour la connexion PTVSD dans le fichier `score.py`, vous devez connecter VS Code à la session de débogage avant l’expiration du délai. Démarrez VS Code, ouvrez la copie locale de `score.py`, définissez un point d’arrêt et préparez-le avant d’utiliser les étapes de cette section.
>
> Pour plus d’informations sur le débogage et la définition de points d’arrêt, consultez [Débogage](https://code.visualstudio.com/Docs/editor/debugging).

1. Pour démarrer un conteneur Docker à l’aide de l’image, utilisez la commande suivante :

    ```bash
    docker run --rm --name debug -p 8000:5001 -p 5678:5678 debug:1
    ```

1. Pour attacher VS Code à PTVSD à l’intérieur du conteneur, ouvrez VS Code et utilisez la touche F5, ou sélectionnez __Déboguer__. Lorsque vous y êtes invité, sélectionnez __Azure Machine Learning : Configuration du débogage Docker__. Vous pouvez également sélectionner l’icône de débogage dans la barre latérale, __Azure Machine Learning : entrée de débogage__ Docker dans le menu déroulant Déboguer, puis utiliser la flèche verte pour attacher le débogueur.

    ![Icône de débogage, bouton Démarrer le débogage et sélecteur de configuration](./media/how-to-troubleshoot-deployment/start-debugging.png)

À ce stade, VS Code se connecte à PTVSD à l’intérieur du conteneur Docker et s’arrête au point d’arrêt que vous avez préalablement défini. Vous pouvez maintenant parcourir le code au fur et à mesure de son exécution, afficher des variables etc.

Pour plus d’informations sur l’utilisation de VS Code pour déboguer Python, consultez [Déboguer votre code Python](https://docs.microsoft.com/visualstudio/python/debugging-python-in-visual-studio?view=vs-2019).

<a id="editfiles"></a>
### <a name="modify-the-container-files"></a>Modifier les fichiers de conteneur

Pour apporter des modifications aux fichiers de l’image, vous pouvez les attacher au conteneur en cours d’exécution et exécuter un interpréteur de commandes bash. À partir de là, vous pouvez utiliser vim pour modifier les fichiers :

1. Pour vous connecter au conteneur en cours d’exécution et lancer un interpréteur de commandes bash dans le conteneur, utilisez la commande suivante :

    ```bash
    docker exec -it debug /bin/bash
    ```

1. Pour rechercher les fichiers utilisés par le service, utilisez la commande suivante à partir de l’interpréteur de commandes bash du conteneur si le répertoire par défaut est différent de `/var/azureml-app` :

    ```bash
    cd /var/azureml-app
    ```

    À partir de là, vous pouvez utiliser vim pour modifier le fichier `score.py`. Pour plus d’informations sur l’utilisation de vim, consultez [Utilisation de l’éditeur Vim](https://www.tldp.org/LDP/intro-linux/html/sect_06_02.html).

1. Les modifications apportées à un conteneur ne sont normalement pas persistantes. Pour enregistrer des modifications que vous apportez, utilisez la commande suivante avant de quitter l’interpréteur de commandes démarré à l’étape précédente (autrement dit, dans un autre interpréteur de commandes) :

    ```bash
    docker commit debug debug:2
    ```

    Cette commande crée une image nommée `debug:2`, qui contient vos modifications.

    > [!TIP]
    > Vous devez arrêter le conteneur actuel et commencer à utiliser la nouvelle version avant que les modifications prennent effet.

1. Veillez à ce que les modifications apportées aux fichiers dans le conteneur restent synchronisées avec les fichiers locaux utilisés par VS Code. Dans le cas contraire, l’expérience du débogueur ne fonctionnera pas comme prévu.

### <a name="stop-the-container"></a>Arrêter le conteneur

Pour arrêter un conteneur, utilisez la commande suivante :

```bash
docker stop debug
```

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur le déploiement :

* [Où et comment effectuer un déploiement](how-to-deploy-and-where.md)
* [Tutoriel : Effectuer l'apprentissage de modèles et les déployer](tutorial-train-models-with-aml.md)
