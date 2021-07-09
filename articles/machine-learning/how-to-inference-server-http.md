---
title: Serveur HTTP d’inférence Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Découvrez comment activer le développement local avec le serveur HTTP d’inférence Azure Machine Learning.
author: shivanissambare
ms.author: ssambare
ms.reviewer: larryfr
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.custom: inference server, local development, local debugging
ms.date: 05/14/2021
ms.openlocfilehash: d54195829c4f4734d135e3468897711bdaa0421f
ms.sourcegitcommit: 9ad20581c9fe2c35339acc34d74d0d9cb38eb9aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110538446"
---
# <a name="azure-machine-learning-inference-http-server-preview"></a>Serveur HTTP d’inférence Azure Machine Learning (préversion)

Le serveur HTTP d’inférence Azure Machine Learning [(préversion)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) est un package Python qui vous permet de valider facilement votre script d’entrée (`score.py`) dans un environnement de développement local. En cas de problème avec le script de scoring, le serveur retourne une erreur. Il retourne également l’emplacement où l’erreur s’est produite.

Le serveur peut également être utilisé lors de la création de portes de validation dans un pipeline d’intégration et de déploiement continus. Par exemple, démarrez le serveur avec le script candidat et exécutez la suite de tests sur le point de terminaison local.

## <a name="prerequisites"></a>Prérequis

- Version de Python : 3.7

## <a name="installation"></a>Installation

> [!NOTE]
> Pour éviter les conflits de packages, installez le serveur dans un environnement virtuel.

Pour installer le `azureml-inference-server-http package`, exécutez la commande suivante à votre invite de commande ou dans votre terminal :

```bash
python -m pip install azureml-inference-server-http
```

## <a name="use-the-server"></a>Utiliser le serveur

1. Créez un répertoire pour stocker vos fichiers :

    ```bash
    mkdir server_quickstart
    cd server_quickstart
    ```

1. Pour éviter les conflits de package, créez un environnement virtuel et activez-le :

    ```bash
    virtualenv myenv
    source myenv/bin/activate
    ```

1. Installez le package `azureml-inference-server-http` à partir du flux [pypi](https://pypi.org/) :

    ```bash
    python -m pip install azureml-inference-server-http
    ```

1. Créez votre script d’entrée (`score.py`). L’exemple suivant crée un script d’entrée de base :

    ```bash
    echo '
    import time

    def init():
        time.sleep(1)

    def run(input_data):
        return {"message":"Hello, World!"}
    ' > score.py
    ```

1. Démarrez le serveur et définissez `score.py` comme script d’entrée :

    ```bash
    azmlinfsrv --entry_script score.py
    ```

    > [!NOTE]
    > Le serveur est hébergé à 0.0.0.0, ce qui signifie qu’il doit écouter toutes les adresses IP de l’ordinateur hôte.

    Le serveur est à l’écoute sur le port 5001 à ces routes.

    | Nom | Route|
    | --- | --- |
    | Diagnostic Probe Liveness | 127.0.0.1:5001/|
    | Score | 127.0.0.1:5001/score|

1. Envoyez une requête de scoring au serveur à l’aide de `curl` :

    ```bash
    curl -p 127.0.0.1:5001/score
    ```

    Le serveur doit répondre comme ceci.

    ```bash
    {"message": "Hello, World!"}
    ```

Vous pouvez maintenant modifier le script de scoring et tester vos modifications en réexécutant le serveur.

## <a name="server-parameters"></a>Paramètres de serveur

Le tableau suivant contient les paramètres acceptés par le serveur :

| Paramètre | Obligatoire | Default | Description |
| ---- | --- | ---- | ----|
| entry_script | Vrai | N/A | Chemin relatif ou absolu du script de scoring.|
| model_dir | Faux | N/A | Chemin relatif ou absolu du répertoire contenant le modèle utilisé pour l’inférence.
| port | False | 5001 | Port de service du serveur.|
| worker_count | False | 1 | Nombre de threads de travail qui traiteront les requêtes simultanées. |

## <a name="request-flow"></a>Flux de demande

Les étapes suivantes expliquent comment le serveur HTTP d’inférence Azure Machine Learning gère les requêtes entrantes :

1. Un wrapper de l’interface CLI Python réside autour de la pile réseau du serveur, et est utilisé pour démarrer le serveur.
1. Un client envoie une requête au serveur.
1. Lorsqu’une requête est reçue, elle passe par le serveur [WSGI](https://www.fullstackpython.com/wsgi-servers.html) et est ensuite distribuée à l’un des workers.
    - [Gunicorn](https://docs.gunicorn.org/) est utilisé sur __Linux__.
    - [Waitress](https://docs.pylonsproject.org/projects/waitress/) est utilisé sur __Windows__.
1. Les requêtes sont ensuite gérées par une application [Flask](https://flask.palletsprojects.com/), qui charge le script d’entrée et les éventuelles dépendances.
1. Pour finir, la requête est envoyée à votre script d’entrée. Le script d’entrée effectue ensuite un appel d’inférence au modèle chargé, et retourne une réponse.

:::image type="content" source="./media/how-to-inference-server-http/inference-server-architecture.png" alt-text="Diagramme du processus de serveur HTTP":::
## <a name="frequently-asked-questions"></a>Forum aux questions

### <a name="do-i-need-to-reload-the-server-when-changing-the-score-script"></a>Dois-je recharger le serveur lors de la modification du script de score ?

Après avoir modifié votre script de scoring (`score.py`), arrêtez le serveur avec `ctrl + c`. Ensuite, redémarrez-le avec `azmlinfsrv --entry_script score.py`.

### <a name="which-os-is-supported"></a>Quels sont les systèmes d’exploitation pris en charge ?

Le serveur d’inférence Azure Machine Learning s’exécute sur les systèmes d’exploitation Windows et Linux.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la création d’un script d’entrée et le déploiement de modèles, consultez [Guide pratique pour déployer un modèle à l’aide d’Azure Machine Learning](how-to-deploy-and-where.md).