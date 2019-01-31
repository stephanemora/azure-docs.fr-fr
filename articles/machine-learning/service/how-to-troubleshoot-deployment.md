---
title: Guide de résolution des problèmes liés au déploiement
titleSuffix: Azure Machine Learning service
description: Découvrez comment contourner, résoudre et dépanner les erreurs courantes de déploiement Docker avec AKS et ACI à l’aide d’Azure Machine Learning service.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: chris-lauren
ms.author: clauren
ms.reviewer: jmartens
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 83ee548befdc7ef0a4e7ed2d4b4e61b42a217f12
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55247066"
---
# <a name="troubleshooting-azure-machine-learning-service-aks-and-aci-deployments"></a>Résoudre les problèmes de déploiement AKS et ACI d’Azure Machine Learning service

Dans cet article, vous allez découvrir comment contourner et résoudre les erreurs courantes de déploiement Docker avec Azure Container Instances (ACI) et Azure Kubernetes Service (AKS) à l’aide d’Azure Machine Learning service.

Quand vous déployez un modèle dans le service Azure Machine Learning, le système effectue une série de tâches. Il s’agit d’une séquence d’événements complexe au cours de laquelle des problèmes surviennent parfois. Les tâches de déploiement sont les suivantes :

1. Inscrire le modèle dans le registre de modèles de l’espace de travail.

2. Générer une image Docker, notamment :
    1. Télécharger le modèle inscrit à partir du registre. 
    2. Créer un fichier Docker, avec un environnement Python basé sur les dépendances que vous spécifiez dans le fichier d’environnement yaml.
    3. Ajouter vos fichiers de modèle et le script de scoring que vous fournissez dans le fichier Docker.
    4. Créer une image Docker à l’aide du fichier Docker.
    5. Inscrire l’image Docker auprès du registre Azure Container Registry associé à l’espace de travail.

3. Déployer l’image Docker sur le service Azure Container Instance (ACI) ou Azure Kubernetes Service (AKS).

4. Démarrer un nouveau conteneur (ou de nouveaux conteneurs) dans ACI ou AKS. 

Découvrez-en plus sur ce processus dans la présentation de la [gestion des modèles](concept-model-management-and-deployment.md).

## <a name="before-you-begin"></a>Avant de commencer

Si vous rencontrez un problème, la première chose à faire consiste à l’isoler en décomposant la tâche de déploiement (décrite précédemment) en étapes individuelles. 

Cette opération est particulièrement utile si vous utilisez l’API `Webservice.deploy` ou l’API `Webservice.deploy_from_model`, dans la mesure où ces fonctions regroupent les étapes mentionnées précédemment en une seule action. En règle générale, ces API sont très pratiques, mais il est utile de décomposer les étapes durant la résolution des problèmes en les remplaçant par les appels d’API ci-dessous.

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
Si le système ne peut pas générer l’image Docker, l’appel `image.wait_for_creation()` échoue avec certains messages d’erreur qui peuvent offrir certains indices. Vous trouverez également plus d’informations sur les erreurs dans le journal de génération de l’image. Voici un exemple de code qui montre comment découvrir l’URI du journal de génération de l’image.

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


## <a name="service-launch-fails"></a>Échec du lancement du service
Une fois l’image correctement générée, le système tente de démarrer un conteneur dans ACI ou AKS, selon la configuration du déploiement. Il est généralement recommandé d’essayer tout d’abord un déploiement ACI, car il s’agit d’un déploiement monoconteneur plus simple. De cette façon, vous pouvez écarter tout problème lié à AKS.

Dans le cadre du processus de démarrage du conteneur, la fonction `init()` dans votre script de scoring est appelée par le système. Si la fonction `init()` comprend des exceptions non interceptées, l’erreur **CrashLoopBackOff** apparaît peut-être dans le message d’erreur. Voici quelques conseils pour vous aider à résoudre le problème.

### <a name="inspect-the-docker-log"></a>Examiner le journal Docker
Vous pouvez afficher les messages détaillés du journal du moteur Docker à partir de l’objet de service.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```

### <a name="debug-the-docker-image-locally"></a>Déboguer l’image Docker localement
Le journal Docker n’émet parfois pas suffisamment d’informations sur le problème en cours. Vous pouvez aller plus loin en extrayant l’image Docker générée, en démarrant un conteneur local et en effectuant le débogage directement dans le conteneur actif de manière interactive. Pour démarrer un conteneur local, vous devez disposer d’un moteur Docker en cours d’exécution localement ; votre tâche serait grandement facilitée si l’[interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) est également installé.

Nous devons d’abord déterminer l’emplacement de l’image :

```python
# print image location
print(image.image_location)
```

Voici le format de l’emplacement de l’image : `<acr-name>.azurecr.io/<image-name>:<version-number>`, par exemple `myworkpaceacr.azurecr.io/myimage:3`. 

Accédez maintenant à votre fenêtre de ligne de commande. Si l’interface de ligne de commande Azure est installée, vous pouvez taper les commandes suivantes pour vous connecter à l’ACR (Azure Container Registry) associé à l’espace de travail où l’image est stockée. 

```sh
# log on to Azure first if you haven't done so before
$ az login

# make sure you set the right subscription in case you have access to multiple subscriptions
$ az account set -s <subscription_name_or_id>

# now let's log in to the workspace ACR
# note the acr-name is the domain name WITHOUT the ".azurecr.io" postfix
# e.g.: az acr login -n myworkpaceacr
$ az acr login -n <acr-name>
```
Si l’interface de ligne de commande Azure n’est pas installée, vous pouvez utiliser la commande `docker login` pour vous connecter au registre ACR. Mais vous devez récupérer tout d’abord le nom d’utilisateur et le mot de passe du registre ACR à partir du portail Azure.

Une fois connecté au registre ACR, vous pouvez extraire l’image Docker et démarrer un conteneur localement, puis lancer une session bash de débogage à l’aide de la commande `docker run` :

```sh
# note the image_id is <acr-name>.azurecr.io/<image-name>:<version-number>
# for example: myworkpaceacr.azurecr.io/myimage:3
$ docker run -it <image_id> /bin/bash
```

Une fois que vous avez lancé une session bash sur le conteneur en cours d’exécution, vous pouvez trouver vos scripts de scoring dans le dossier `/var/azureml-app`. Vous pouvez ensuite lancer une session Python pour déboguer vos scripts de scoring. 

```sh
# enter the directory where scoring scripts live
cd /var/azureml-app

# find what Python packages are installed in the python environment
pip freeze

# sanity-check on score.py
# you might want to edit the score.py to trigger init().
# as most of the errors happen in init() when you are trying to load the model.
python score.py
```
Si vous avez besoin d’un éditeur de texte pour modifier vos scripts, vous pouvez installer vim, nano, Emacs ou votre éditeur de prédilection.

```sh
# update package index
apt-get update

# install a text editor of your choice
apt-get install vim
apt-get install nano
apt-get install emacs

# launch emacs (for example) to edit score.py
emacs score.py

# exit the container bash shell
exit
```

Vous pouvez également démarrer le service web localement et lui envoyer du trafic HTTP. Le serveur Flask dans le conteneur Docker s’exécute sur le port 5001. Vous pouvez effectuer un mappage à tous les ports disponibles sur la machine hôte.
```sh
# you can find the scoring API at: http://localhost:8000/score
$ docker run -p 8000:5001 <image_id>
```

## <a name="function-fails-getmodelpath"></a>Échec de la fonction : get_model_path()
Souvent, dans la fonction `init()` du script de scoring, la fonction `Model.get_model_path()` est appelée pour rechercher un fichier de modèle ou un dossier de fichiers de modèle dans le conteneur. Il s’agit souvent d’une source de défaillance si le fichier de modèle ou le dossier est introuvable. Le moyen le plus simple de déboguer cette erreur consiste à exécuter le code Python ci-dessous dans l’interpréteur de commandes du conteneur :

```python
from azureml.core.model import Model
print(Model.get_model_path(model_name='my-best-model'))
```

Ce code affiche le chemin local (relatif à `/var/azureml-app`) sur le conteneur où votre script de scoring est censé trouver le fichier de modèle ou le dossier. Vous pouvez ensuite vérifier si le fichier ou dossier se trouve effectivement à l’emplacement indiqué.


## <a name="function-fails-runinputdata"></a>Échec de la fonction : run(input_data)
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
**Remarque**: Retournez les messages d’erreur à partir de l’appel `run(input_data)` exclusivement à des fins de débogage. Pour des raisons de sécurité, évitez cette approche dans un environnement de production.


## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur le déploiement : 
* [Où et comment effectuer un déploiement](how-to-deploy-and-where.md)

* [Tutoriel : Effectuer l'apprentissage de modèles et les déployer](tutorial-train-models-with-aml.md)
