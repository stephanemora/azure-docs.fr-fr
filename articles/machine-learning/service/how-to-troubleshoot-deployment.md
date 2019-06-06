---
title: Guide de résolution des problèmes liés au déploiement
titleSuffix: Azure Machine Learning service
description: Découvrez comment contourner, résoudre et dépanner les erreurs courantes de déploiement Docker avec AKS et ACI à l’aide du service Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: chris-lauren
ms.author: clauren
ms.reviewer: jmartens
ms.date: 05/02/2018
ms.custom: seodec18
ms.openlocfilehash: 9d173409fc9f270f13d688999a88f9480c3eb40f
ms.sourcegitcommit: 7042ec27b18f69db9331b3bf3b9296a9cd0c0402
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66742923"
---
# <a name="troubleshooting-azure-machine-learning-service-aks-and-aci-deployments"></a>Résoudre les problèmes de déploiement AKS et ACI d’Azure Machine Learning service

Apprenez à contourner ou résoudre des erreurs courantes de déploiement de Docker avec Azure Container Instances (ACI) et Azure Kubernetes Service (AKS) à l’aide du service Azure Machine Learning.

Quand vous déployez un modèle dans le service Azure Machine Learning, le système effectue une série de tâches. Les tâches de déploiement sont les suivantes :

1. Inscrire le modèle dans le registre de modèles de l’espace de travail.

2. Générer une image Docker, notamment :
    1. Télécharger le modèle inscrit à partir du registre. 
    2. Créer un fichier Docker, avec un environnement Python basé sur les dépendances que vous spécifiez dans le fichier d’environnement yaml.
    3. Ajouter vos fichiers de modèle et le script de scoring que vous fournissez dans le fichier Docker.
    4. Créer une image Docker à l’aide du fichier Docker.
    5. Inscrire l’image Docker auprès du registre Azure Container Registry associé à l’espace de travail.

    > [!IMPORTANT]
    > En fonction de votre code, la création d’images se produire automatiquement sans votre intervention.

3. Déployer l’image Docker sur le service Azure Container Instance (ACI) ou Azure Kubernetes Service (AKS).

4. Démarrer un nouveau conteneur (ou de nouveaux conteneurs) dans ACI ou AKS. 

Découvrez-en plus sur ce processus dans la présentation de la [gestion des modèles](concept-model-management-and-deployment.md).

## <a name="before-you-begin"></a>Avant de commencer

Si vous rencontrez un problème, la première chose à faire consiste à l’isoler en décomposant la tâche de déploiement (décrite précédemment) en étapes individuelles.

Diviser le déploiement en tâches est utile si vous utilisez le [Webservice.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#deploy-workspace--name--model-paths--image-config--deployment-config-none--deployment-target-none-) API, ou [Webservice.deploy_from_model()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-) API, en tant que ces deux fonctions effectuer les étapes mentionnées précédemment comme un action unique. En général, ces API sont commodes, mais il est utile de diviser les étapes lors de la résolution des problèmes en les remplaçant par le ci-dessous les appels d’API.

1. Inscrire le modèle. Voici un exemple de code :

    ```python
    # register a model out of a run record
    model = best_run.register_model(model_name='my_best_model', model_path='outputs/my_model.pkl')

    # or, you can register a file or a folder of files as a model
    model = Model.register(model_path='my_model.pkl', model_name='my_best_model', workspace=ws)
    ```

2. Générer l’image. Voici un exemple de code :

    ```python
    # configure the image
    image_config = ContainerImage.image_configuration(runtime="python",
                                                      execution_script="score.py",
                                                      conda_file="myenv.yml")

    # create the image
    image = Image.create(name='myimg', models=[model], image_config=image_config, workspace=ws)

    # wait for image creation to finish
    image.wait_for_creation(show_output=True)
    ```

3. Déployer l’image en tant que service. Voici un exemple de code :

    ```python
    # configure an ACI-based deployment
    aci_config = AciWebservice.deploy_configuration(cpu_cores=1, memory_gb=1)

    aci_service = Webservice.deploy_from_image(deployment_config=aci_config, 
                                               image=image, 
                                               name='mysvc', 
                                               workspace=ws)
    aci_service.wait_for_deployment(show_output=True)    
    ```

Une fois que vous avez décomposé le processus de déploiement en tâches distinctes, nous pouvons examiner certaines des erreurs les plus courantes.

## <a name="image-building-fails"></a>Échec de la génération de l’image

Si l’image Docker ne peut pas être générée, le [image.wait_for_creation()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.image(class)?view=azure-ml-py#wait-for-creation-show-output-false-) ou [service.wait_for_deployment()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#wait-for-deployment-show-output-false-) appel échoue avec certains messages d’erreur qui peuvent offrir certains indices. Vous trouverez également plus d’informations sur les erreurs dans le journal de génération de l’image. Voici un exemple de code qui montre comment découvrir l’URI du journal de génération de l’image.

```python
# if you already have the image object handy
print(image.image_build_log_uri)

# if you only know the name of the image (note there might be multiple images with the same name but different version number)
print(ws.images['myimg'].image_build_log_uri)

# list logs for all images in the workspace
for name, img in ws.images.items():
    print (img.name, img.version, img.image_build_log_uri)
```

L’URI du journal de l’image est une URL SAP pointant vers un fichier journal stocké dans votre stockage blob Azure. Copiez et collez simplement l’URI dans une fenêtre de navigateur ; vous pouvez alors télécharger et afficher le fichier journal.

### <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Stratégie d’accès Azure Key Vault et les modèles Azure Resource Manager

La génération d’image peut également échouer en raison d’un problème avec la stratégie d’accès sur Azure Key Vault. Cette situation peut se produire lorsque vous utilisez un modèle Azure Resource Manager pour créer l’espace de travail et les ressources associées (y compris Azure Key Vault), plusieurs fois. Par exemple, à l’aide du modèle plusieurs fois avec les mêmes paramètres dans le cadre d’une intégration continue et le pipeline de déploiement.

La plupart des opérations de création de ressources via des modèles sont idempotentes, mais Key Vault efface les stratégies d’accès chaque fois que le modèle est utilisé. Effacement de l’accès de sauts de stratégies accès à Key Vault pour n’importe quel espace de travail existant qui est à l’aide. Cette condition entraîne des erreurs lorsque vous tentez de créer de nouvelles images. Voici quelques exemples d’erreurs que vous pouvez recevoir :

__Portail__ :
```text
Create image "myimage": An internal server error occurred. Please try again. If the problem persists, contact support.
```

__KIT DE DÉVELOPPEMENT LOGICIEL__:
```python
image = ContainerImage.create(name = "myimage", models = [model], image_config = image_config, workspace = ws)
Creating image
Traceback (most recent call last):
  File "C:\Python37\lib\site-packages\azureml\core\image\image.py", line 341, in create
    resp.raise_for_status()
  File "C:\Python37\lib\site-packages\requests\models.py", line 940, in raise_for_status
    raise HTTPError(http_error_msg, response=self)
requests.exceptions.HTTPError: 500 Server Error: Internal Server Error for url: https://eastus.modelmanagement.azureml.net/api/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>/images?api-version=2018-11-19

Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "C:\Python37\lib\site-packages\azureml\core\image\image.py", line 346, in create
    'Content: {}'.format(resp.status_code, resp.headers, resp.content))
azureml.exceptions._azureml_exception.WebserviceException: Received bad response from Model Management Service:
Response Code: 500
Headers: {'Date': 'Tue, 26 Feb 2019 17:47:53 GMT', 'Content-Type': 'application/json', 'Transfer-Encoding': 'chunked', 'Connection': 'keep-alive', 'api-supported-versions': '2018-03-01-preview, 2018-11-19', 'x-ms-client-request-id': '3cdcf791f1214b9cbac93076ebfb5167', 'x-ms-client-session-id': '', 'Strict-Transport-Security': 'max-age=15724800; includeSubDomains; preload'}
Content: b'{"code":"InternalServerError","statusCode":500,"message":"An internal server error occurred. Please try again. If the problem persists, contact support"}'
```

__CLI__ :
```text
ERROR: {'Azure-cli-ml Version': None, 'Error': WebserviceException('Received bad response from Model Management Service:\nResponse Code: 500\nHeaders: {\'Date\': \'Tue, 26 Feb 2019 17:34:05
GMT\', \'Content-Type\': \'application/json\', \'Transfer-Encoding\': \'chunked\', \'Connection\': \'keep-alive\', \'api-supported-versions\': \'2018-03-01-preview, 2018-11-19\', \'x-ms-client-request-id\':
\'bc89430916164412abe3d82acb1d1109\', \'x-ms-client-session-id\': \'\', \'Strict-Transport-Security\': \'max-age=15724800; includeSubDomains; preload\'}\nContent:
b\'{"code":"InternalServerError","statusCode":500,"message":"An internal server error occurred. Please try again. If the problem persists, contact support"}\'',)}
```

Pour éviter ce problème, nous vous recommandons d’une des approches suivantes :

* Ne déployez pas le modèle plusieurs fois pour les mêmes paramètres. Ou supprimer les ressources existantes avant d’utiliser le modèle au pour recréer.
* Examiner les stratégies d’accès Key Vault et ensuite utiliser ces stratégies pour définir le `accessPolicies` propriété du modèle.
* Vérifiez si la ressource de coffre de clés existe déjà. Le cas échéant, ne pas recréer un il via le modèle. Par exemple, ajoutez un paramètre qui vous permet de désactiver la création de la ressource Key Vault si elle existe déjà.

## <a name="debug-locally"></a>Déboguer localement

Si vous rencontrez des problèmes de déploiement d’un modèle ACI ou AKS, essayez de le déployer comme un service web local. À l’aide d’un service web local facilite la résolution des problèmes. L’image Docker contenant le modèle est téléchargé et démarré sur votre système local.

> [!IMPORTANT]
> Déploiements de service web local nécessitent un travail installation Docker sur votre système local. Docker doit être en cours d’exécution avant de déployer un service web local. Pour plus d’informations sur l’installation et à l’aide de Docker, consultez [ https://www.docker.com/ ](https://www.docker.com/).

> [!WARNING]
> Déploiements de service web local ne sont pas pris en charge pour les scénarios de production.

Pour déployer en local, modifiez votre code pour utiliser `LocalWebservice.deploy_configuration()` pour créer une configuration de déploiement. Utilisez ensuite `Model.deploy()` pour déployer le service. L’exemple suivant déploie un modèle (contenus dans le `model` variable) comme un service web local :

```python
from azureml.core.model import InferenceConfig,Model
from azureml.core.webservice import LocalWebservice

# Create inference configuration. This creates a docker image that contains the model.
inference_config = InferenceConfig(runtime= "python", 
                                   entry_script="score.py",
                                   conda_file="myenv.yml")

# Create a local deployment, using port 8890 for the web service endpoint
deployment_config = LocalWebservice.deploy_configuration(port=8890)
# Deploy the service
service = Model.deploy(ws, "mymodel", [model], inference_config, deployment_config)
# Wait for the deployment to complete
service.wait_for_deployment(True)
# Display the port that the web service is available on
print(service.port)
```

À ce stade, vous pouvez travailler avec le service comme d’habitude. Par exemple, le code suivant illustre le service envoie des données :

```python
import json

test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10], 
    [10,9,8,7,6,5,4,3,2,1]
]})

test_sample = bytes(test_sample,encoding = 'utf8')

prediction = service.run(input_data=test_sample)
print(prediction)
```

### <a name="update-the-service"></a>Mettre à jour le service

Pendant le test local, vous devrez peut-être mettre à jour le `score.py` fichier pour ajouter la journalisation ou tenter de résoudre les problèmes que vous avez découvertes. Pour recharger les modifications apportées à la `score.py` , utilisez `reload()`. Par exemple, le code suivant recharge le script pour le service et lui envoie des données. Les données sont calculées à l’aide de la mise à jour `score.py` fichier :

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> Le script est rechargé à partir de l’emplacement spécifié par le `InferenceConfig` objet utilisé par le service.

Pour modifier le modèle, les dépendances Conda ou configuration de déploiement, utilisez [update()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#update--args-). L’exemple suivant met à jour le modèle utilisé par le service :

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>Supprimer le service

Pour supprimer le service, utilisez [delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#delete--).

### <a id="dockerlog"></a> Examiner le journal de Docker

Vous pouvez afficher les messages détaillés du journal du moteur Docker à partir de l’objet de service. Vous pouvez afficher le journal pour les déploiements locaux ACI et AKS. L’exemple suivant montre comment imprimer les journaux.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```

## <a name="service-launch-fails"></a>Échec du lancement du service

Une fois que l’image est créée avec succès, le système tente de démarrer un conteneur à l’aide de la configuration de votre déploiement. Dans le cadre du processus de démarrage du conteneur, la fonction `init()` dans votre script de scoring est appelée par le système. Si la fonction `init()` comprend des exceptions non interceptées, l’erreur **CrashLoopBackOff** apparaît peut-être dans le message d’erreur.

Utilisez les informations dans le [examiner le journal de Docker](#dockerlog) section pour vérifier les journaux.

## <a name="function-fails-getmodelpath"></a>Échec de la fonction : get_model_path()

Souvent, dans le `init()` fonction dans le script de notation, [Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) fonction est appelée pour rechercher un fichier de modèle ou un dossier de fichiers de modèle dans le conteneur. Si le fichier de modèle ou le dossier est introuvable, la fonction échoue. Le moyen le plus simple de déboguer cette erreur consiste à exécuter le code Python ci-dessous dans l’interpréteur de commandes du conteneur :

```python
import logging
logging.basicConfig(level=logging.DEBUG)
from azureml.core.model import Model
print(Model.get_model_path(model_name='my-best-model'))
```

Cet exemple imprime le chemin d’accès local (relatif à `/var/azureml-app`) dans le conteneur où les votre script de notation s’attend à trouver le fichier de modèle ou le dossier. Vous pouvez ensuite vérifier si le fichier ou dossier se trouve effectivement à l’emplacement indiqué.

Le niveau de journalisation DEBUG risque d’être connecté, des informations supplémentaires qui peuvent être utiles pour identifier l’échec.

## <a name="function-fails-runinputdata"></a>Échec de la fonction : run(input_data)

Si le service est déployé avec succès, mais qu’il plante quand vous publiez des données sur le point de terminaison de scoring, vous pouvez ajouter une instruction d’interception d’erreur dans votre fonction `run(input_data)` afin qu’elle retourne un message d’erreur détaillé. Exemple :

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

**Remarque**: Retournez les messages d’erreur à partir de l’appel `run(input_data)` exclusivement à des fins de débogage. Pour des raisons de sécurité, vous ne devez pas retourner des messages d’erreur de cette façon dans un environnement de production.

## <a name="http-status-code-503"></a>Code d’état HTTP 503

Les déploiements de Kubernetes Service Azure prend en charge la mise à l’échelle, ce qui permet des réplicas à ajouter pour prendre en charge de la charge supplémentaire. Toutefois, l’autoscaler est conçu pour gérer **progressive** modifications de la charge. Si vous recevez des pics importants dans les demandes par seconde, les clients peuvent recevoir un code d’état HTTP 503.

Il existe deux choses qui peuvent aider à empêcher les codes d’état 503 :

* Modifier le niveau d’utilisation à quels à l’échelle automatique crée de nouveaux réplicas.
    
    Par défaut, l’utilisation de cible de mise à l’échelle est définie à 70 %, ce qui signifie que le service peut gérer les pics de demandes par seconde (RPS) jusqu'à 30 %. Vous pouvez ajuster la cible de l’utilisation en définissant le `autoscale_target_utilization` sur une valeur inférieure.

    > [!IMPORTANT]
    > Cette modification n’entraîne pas la création de répliques *plus rapidement*. Au lieu de cela, ils sont créés à un seuil inférieur de l’utilisation. Au lieu d’attendre jusqu'à ce que le service est utilisé à 70 %, modification de la valeur à 30 % provoque des réplicas à créer lors de l’utilisation de 30 %.
    
    Si le service web utilise déjà les réplicas actuels maximales et codes d’état 503 persiste, augmentez la `autoscale_max_replicas` valeur pour augmenter le nombre maximal de réplicas.

* Modifier le nombre minimal de réplicas. Augmenter les réplicas minimale fournit un plus grand pool pour gérer les pics entrants.

    Pour augmenter le nombre minimal de réplicas, définissez `autoscale_min_replicas` sur une valeur plus élevée. Vous pouvez calculer les réplicas requis en utilisant le code suivant, en remplaçant les valeurs par des valeurs spécifiques à votre projet :

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
    > Si vous recevez des pics de demande plus volumineux que les nouveaux réplicas minimale ne peuvent gérer, vous pouvez recevoir 503s à nouveau. Par exemple, en tant que le trafic vers votre augmente de service, vous devrez peut-être augmenter les réplicas minimale.

Pour plus d’informations sur la configuration `autoscale_target_utilization`, `autoscale_max_replicas`, et `autoscale_min_replicas` pour, consultez le [AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py) référence de module.


## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur le déploiement :

* [Où et comment effectuer un déploiement](how-to-deploy-and-where.md)
* [Tutoriel : Effectuer l'apprentissage de modèles et les déployer](tutorial-train-models-with-aml.md)
