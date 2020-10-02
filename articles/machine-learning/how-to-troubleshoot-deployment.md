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
ms.date: 08/06/2020
ms.topic: conceptual
ms.custom: troubleshooting, contperfq4, devx-track-python
ms.openlocfilehash: 22f9c709ced1069caa39ba2145981efa353caadf
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/16/2020
ms.locfileid: "90602631"
---
# <a name="troubleshoot-docker-deployment-of-models-with-azure-kubernetes-service-and-azure-container-instances"></a>Résolution des problèmes de déploiement Docker des modèles avec Azure Kubernetes Service et Azure Container Instances 

Découvrez comment contourner et résoudre les erreurs courantes de déploiement Docker avec Azure Container Instances (ACI) et Azure Kubernetes Service (AKS) à l’aide d’Azure Machine Learning.

## <a name="prerequisites"></a>Prérequis

* Un **abonnement Azure**. Essayez la [version gratuite ou payante d’Azure Machine Learning](https://aka.ms/AMLFree).
* Le [Kit de développement logiciel (SDK) Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true).
* [Interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* [Extension CLI pour Azure Machine Learning](reference-azure-machine-learning-cli.md).
* Pour déboguer localement, vous devez avoir une installation opérationnelle de Docker sur votre système local.

    Pour vérifier votre installation de Docker, utilisez la commande `docker run hello-world` à partir d’un terminal ou d’une invite de commandes. Pour obtenir des informations sur l’installation de Docker ou sur la résolution des erreurs Docker, consultez la [documentation Docker](https://docs.docker.com/).

## <a name="steps-for-docker-deployment-of-machine-learning-models"></a>Étapes pour le déploiement Docker des modèles Machine Learning

Lorsque vous déployez un modèle dans Azure Machine Learning, vous utilisez l'API [Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) et un objet [Environment](how-to-use-environments.md). Le service crée une image Docker de base pendant la phase de déploiement, et monte les modèles requis en un seul appel. Les tâches de déploiement de base sont les suivantes :

1. Inscrire le modèle dans le registre de modèles de l’espace de travail.

2. Définir la configuration de l’inférence :
    1. Créez un objet [Environment](how-to-use-environments.md). Cet objet peut utiliser les dépendances d'un fichier yaml d'environnement, l'un de nos environnements organisés.
    2. Créez une configuration d’inférence (objet InferenceConfig) basée sur l’environnement et le script de notation.

3. Déployer le modèle sur le service Azure Container Instance (ACI) ou Azure Kubernetes Service (AKS).

Découvrez-en plus sur ce processus dans la présentation de la [gestion des modèles](concept-model-management-and-deployment.md).

## <a name="before-you-begin"></a>Avant de commencer

Si vous rencontrez un problème, la première chose à faire consiste à l’isoler en décomposant la tâche de déploiement (décrite précédemment) en étapes individuelles.

Lorsque vous utilisez [Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) avec un objet [Environment](how-to-use-environments.md) comme paramètre d'entrée, votre code peut être divisé en trois étapes principales :

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

En divisant le processus de déploiement en tâches individuelles, il est plus facile d'identifier certaines des erreurs les plus courantes.

## <a name="debug-locally"></a>Déboguer en local

Si vous rencontrez des problèmes lors du déploiement d'un modèle sur ACI ou AKS, déployez-le en tant que service web local. L’utilisation d’un service web local facilite la résolution des problèmes.

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

Si vous définissez votre propre YAML de spécification Conda, faites figurer une version supérieure ou égale à la version 1.0.45 d'azureml-defaults comme dépendance pip. Ce package est nécessaire pour héberger le modèle en tant que service web.

À ce stade, vous pouvez travailler avec le service comme d’habitude. Le code suivant illustre l'envoi des données au service :

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
> La méthode `reload` est disponible uniquement pour les déploiements locaux. Pour en savoir plus sur la mise à jour d’un déploiement vers une autre cible de calcul, consultez la [procédure de mise à jour de vos services web](how-to-deploy-update-web-service.md).

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> Le script est rechargé à partir de l’emplacement spécifié par l’objet `InferenceConfig` utilisé par le service.

Pour modifier le modèle, les dépendances Conda ou la configuration de déploiement, utilisez [update()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#&preserve-view=trueupdate--args-). L’exemple suivant met à jour le modèle utilisé par le service :

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>Supprimer le service

Pour supprimer le service, utilisez [delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#&preserve-view=truedelete--).

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

Lorsque vous déployez un service sur une cible de calcul Azure Kubernetes Service, Azure Machine Learning tente de planifier le service avec la quantité de ressources demandée. Si, au bout de cinq minutes, aucun nœud n'est disponible dans le cluster avec suffisamment de ressources, le déploiement échoue. Le message d'échec est le suivant : `Couldn't Schedule because the kubernetes cluster didn't have available resources after trying for 00:05:00`. Vous pouvez corriger cette erreur en ajoutant des nœuds supplémentaires, en modifiant la référence SKU de vos nœuds ou en changeant les besoins en ressources de votre service. 

Le message d’erreur indique généralement la ressource insuffisante. Par exemple, un message d’erreur qui indique `0/3 nodes are available: 3 Insufficient nvidia.com/gpu` signifie que le service réclame des GPU et qu’il y a trois nœuds dans le cluster qui n’ont pas de GPU disponibles. Pour résoudre cette erreur, vous pouvez ajouter d’autres nœuds si vous utilisez une référence SKU de GPU, basculer sur une référence SKU compatible GPU sinon ou modifier votre environnement de sorte qu’il ne réclame pas de GPU.  

## <a name="service-launch-fails"></a>Échec du lancement du service

Une fois l’image correctement générée, le système tente de démarrer un conteneur à l’aide de la configuration du déploiement. Dans le cadre du processus de démarrage du conteneur, la fonction `init()` dans votre script de scoring est appelée par le système. Si la fonction `init()` comprend des exceptions non interceptées, l’erreur **CrashLoopBackOff** apparaît peut-être dans le message d’erreur.

Utilisez les informations de la section [Examiner le journal Docker](#dockerlog) pour vérifier les journaux.

## <a name="function-fails-get_model_path"></a>Échec de la fonction : get_model_path()

Souvent, dans la fonction `init()` du script de scoring, la fonction [Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#&preserve-view=trueget-model-path-model-name--version-none---workspace-none-) est appelée pour rechercher un fichier de modèle ou un dossier de fichiers de modèle dans le conteneur. Si le fichier ou le dossier de modèle est introuvable, la fonction échoue. Le moyen le plus simple de déboguer cette erreur consiste à exécuter le code Python ci-dessous dans l’interpréteur de commandes du conteneur :

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

Les déploiements Azure Kubernetes Service prennent en charge la mise à l’échelle automatique, ce qui permet d’ajouter des réplicas pour gérer une charge supplémentaire. L'Autoscaler est conçu pour gérer des changements **progressifs** de charge. Si vous recevez des pics importants de demandes par seconde, les clients peuvent recevoir un code d’état HTTP 503. Même si l'Autoscaler réagit rapidement, il faut beaucoup de temps à AKS pour créer des conteneurs supplémentaires.

La décision d’effectuer un scale-up/down est basée sur l’utilisation des réplicas de conteneur actuels. Le nombre de réplicas occupés (qui traitent une demande) divisé par le nombre total de réplicas actuels donne l’utilisation en cours. Si ce nombre dépasse `autoscale_target_utilization`, d’autres réplicas sont créés. S’il est inférieur, des réplicas sont supprimés. La décision d’ajouter des réplicas est hâtive et rapide (environ 1 seconde). La décision de supprimer des réplicas est prudente (environ 1 minute). Par défaut, l'utilisation de la cible de mise à l'échelle automatique est définie sur **70 %** , ce qui signifie que le service peut gérer les pics de demandes par seconde (RPS) **jusqu'à 30 %** .

Il existe deux mesures permettant d’empêcher les codes d’état 503 :

> [!TIP]
> Ces deux approches peuvent être utilisées individuellement ou conjointement.

* Modifiez le niveau d’utilisation auquel la mise à l’échelle automatique crée de nouveaux réplicas. Vous pouvez ajuster la cible de l’utilisation en définissant `autoscale_target_utilization` sur une valeur inférieure.

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

Pour plus d’informations sur la configuration de `autoscale_target_utilization`, `autoscale_max_replicas`, et `autoscale_min_replicas`, consultez les informations de référence sur le module [AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py&preserve-view=true).

## <a name="http-status-code-504"></a>Code d’état HTTP 504

Un code d’état 504 indique que la requête a expiré. Le délai d’expiration par défaut est de une minute.

Vous pouvez augmenter le délai d’expiration ou essayer d’accélérer le service en modifiant le fichier score.py pour supprimer les appels inutiles. Si ces actions ne corrigent pas le problème, utilisez les informations de cet article pour déboguer le fichier score.py. Le code peut être dans un état sans réponse ou une boucle infinie.

## <a name="advanced-debugging"></a>Débogage avancé

Vous pouvez être amené à déboguer interactivement le code Python contenu dans votre modèle de déploiement. Par exemple, si le script d’entrée échoue pour une raison ne pouvant pas être déterminée par une journalisation supplémentaire. À l’aide de Visual Studio Code et de debugpy, vous pouvez attacher au code qui s’exécute dans le conteneur Docker.

Pour plus d’informations, consultez le [guide de débogage interactif dans VS Code](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-deployments).

## <a name="model-deployment-user-forum"></a>[Forum utilisateurs sur le déploiement de modèle](https://docs.microsoft.com/answers/topics/azure-machine-learning-inference.html)

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur le déploiement :

* [Où et comment effectuer un déploiement](how-to-deploy-and-where.md)
* [Tutoriel : Effectuer l'apprentissage de modèles et les déployer](tutorial-train-models-with-aml.md)
