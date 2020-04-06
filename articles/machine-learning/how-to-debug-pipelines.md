---
title: Déboguer et résoudre les problèmes de pipelines de machine learning
titleSuffix: Azure Machine Learning
description: Déboguez et résolvez les problèmes de pipelines de machine learning dans le SDK Azure Machine Learning pour Python. Découvrez les écueils habituels du développement de pipelines ainsi que des conseils pour vous aider à déboguer les scripts avant et pendant l’exécution à distance. Découvrez comment utiliser Visual Studio Code pour déboguer de manière interactive vos pipelines de Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 03/18/2020
ms.openlocfilehash: b68efbb64e9634ade001373e8cd9d61355bf786f
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2020
ms.locfileid: "80388982"
---
# <a name="debug-and-troubleshoot-machine-learning-pipelines"></a>Déboguer et résoudre les problèmes de pipelines de machine learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Dans cet article, vous allez découvrir comment déboguer et résoudre les problèmes de [pipelines de machine learning](concept-ml-pipelines.md) dans le [SDK Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) et le [concepteur Azure Machine Learning (préversion)](https://docs.microsoft.com/azure/machine-learning/concept-designer). Vous trouverez des informations sur la manière d’effectuer les opérations suivantes :

* Déboguer à l’aide du kit de développement logiciel (SDK) Azure Machine Learning
* Déboguer à l’aide du concepteur Azure Machine Learning
* Déboguer à l’aide d’Application Insights
* Déboguer de manière interactive à l’aide de Visual Studio Code (VS Code) et du plug-in Python Tools pour Visual Studio (PTVS)

## <a name="debug-and-troubleshoot-in-the-azure-machine-learning-sdk"></a>Déboguer et résoudre les problèmes dans le SDK Azure Machine Learning
Les sections suivantes offrent une vue d’ensemble des écueils habituels de la création de pipelines et exposent différentes stratégies pour déboguer votre code qui s’exécute dans un pipeline. Servez-vous des conseils suivants quand vous avez des difficultés à exécuter un pipeline comme prévu.

### <a name="testing-scripts-locally"></a>Tester les scripts localement

Parmi les échecs les plus communément observés dans un pipeline figurent l’exécution incongrue d’un script attaché (script de suppression de données, script de scoring, etc.) ou la présence d’erreurs d’exécution dans le contexte de calcul distant qu’il est difficile de déboguer dans votre espace de travail Azure Machine Learning Studio. 

Les pipelines eux-mêmes ne peuvent pas être exécutés localement, mais l’exécution de scripts en isolation sur votre ordinateur local vous permet de déboguer plus rapidement dans la mesure où vous n’avez pas besoin d’attendre le processus de génération de calcul et d’environnement. Cela demande un peu de travail de développement :

* Si vos données se trouvent dans un magasin de données cloud, vous devez les télécharger et les rendre accessibles à votre script. Utiliser un petit échantillon de données est un bon moyen de réduire la durée d’exécution et d’être rapidement renseigné sur le comportement du script.
* Si vous tentez de simuler une étape de pipeline intermédiaire, vous devrez peut-être créer manuellement les types d’objet que le script attend de l’étape précédente.
* Vous devrez aussi définir votre propre environnement et répliquer les dépendances définies dans votre environnement de calcul distant.

Une fois que vous avez configuré un script pour qu’il s’exécute dans un environnement local, il est beaucoup plus facile d’effectuer certaines tâches de débogage, notamment :

* Attacher une configuration de débogage personnalisée
* Suspendre l’exécution et inspecter l’état des objets
* Intercepter les erreurs de type ou les erreurs logiques qui n’apparaîtront pas avant l’exécution

> [!TIP] 
> Une fois que vous pouvez vérifier que votre script s’exécute comme prévu, nous vous recommandons d’exécuter le script dans un pipeline à une seule étape avant de tenter de l’exécuter dans un pipeline à plusieurs étapes.

### <a name="debugging-scripts-from-remote-context"></a>Déboguer les scripts à partir du contexte distant

Tester les scripts localement est un excellent moyen de déboguer des fragments de code importants et une logique complexe avant de commencer à créer un pipeline. Mais à un moment donné, vous devrez probablement déboguer des scripts pendant l’exécution effective du pipeline, surtout quand il s’agira de diagnostiquer le comportement que les étapes du pipeline interagiront. Nous vous recommandons d’utiliser à loisir les instructions `print()` dans les scripts d’étapes pour voir l’état des objets et les valeurs attendues pendant l’exécution distante, comme vous le feriez pour déboguer du code JavaScript.

Le fichier journal `70_driver_log.txt` contient : 

* Toutes les instructions imprimées pendant l’exécution de votre script
* La rapport des appels de procédure du script 

Pour trouver ce fichier journal (et d’autres) sur le portail, commencez par cliquer sur l’exécution de pipeline dans votre espace de travail.

![Page listant les exécutions de pipeline](./media/how-to-debug-pipelines/pipelinerun-01.png)

Accédez à la page des détails d’exécutions de pipeline.

![Page des détails d’exécutions de pipeline](./media/how-to-debug-pipelines/pipelinerun-02.png)

Cliquez sur le module correspondant à l’étape. Accédez à l’onglet **Journaux**. D’autres journaux renseignent sur le processus de génération d’images de votre environnement et les scripts de préparation d’étape.

![Onglet Journal dans la page des détails d’exécutions de pipeline](./media/how-to-debug-pipelines/pipelinerun-03.png)

> [!TIP]
> Les exécutions des *pipelines publiés* se trouvent sous l’onglet **Points de terminaison** dans votre espace de travail. Les exécutions des *pipelines non publiés* se trouvent sous **Expériences** ou **Pipelines**.

### <a name="troubleshooting-tips"></a>Conseils de dépannage

Le tableau suivant présente les problèmes courants qui se produisent pendant le développement de pipelines ainsi que les solutions possibles.

| Problème | Solution possible |
|--|--|
| Impossible de transmettre les données au répertoire `PipelineData` | Vérifiez que vous avez créé un répertoire dans le script qui correspond à l’emplacement où votre pipeline attend les données de sortie de l’étape. Dans la plupart des cas, un argument d’entrée définit le répertoire de sortie, puis crée le répertoire explicitement. Utilisez `os.makedirs(args.output_dir, exist_ok=True)` pour créer le répertoire de sortie. Pour obtenir un exemple de script de scoring qui illustre ce modèle de conception, consultez ce [tutoriel](tutorial-pipeline-batch-scoring-classification.md#write-a-scoring-script). |
| Bogues de dépendance | Si vous avez développé et testé des scripts localement, mais que vous détectez des problèmes de dépendance pendant leur exécution sur une cible de calcul distante dans le pipeline, vérifiez que les dépendances et les versions de votre environnement de calcul correspondent à celles de votre environnement de test. (Voir [Création, mise en cache et réutilisation d’environnement](https://docs.microsoft.com/azure/machine-learning/concept-environments#environment-building-caching-and-reuse))|
| Erreurs ambiguës liées aux cibles de calcul | La suppression et la recréation des cibles de calcul peuvent résoudre certains problèmes liés aux cibles de calcul. |
| Le pipeline ne réutilise pas les étapes | La réutilisation d’étape est activée par défaut, mais vérifiez que vous ne l’avez pas désactivée dans une étape du pipeline. Si la réutilisation est désactivée, le paramètre `allow_reuse` de l’étape est défini sur `False`. |
| Le pipeline se réexécute inutilement | Pour faire en sorte que les étapes ne se réexécutent que lorsque leurs données sous-jacents ou leurs scripts changent, découplez vos répertoires pour chaque étape. Si vous utilisez le même répertoire source pour plusieurs étapes, des réexécutions inutiles peuvent se produire. Utilisez le paramètre `source_directory` sur un objet d’étape de pipeline pour pointer vers votre répertoire isolé pour cette étape, et vérifiez que vous n’utilisez pas le même chemin `source_directory` pour plusieurs étapes. |

### <a name="logging-options-and-behavior"></a>Options et comportement de journalisation

Le tableau ci-dessous fournit des informations sur les différentes options de débogage pour les pipelines. Cette liste n’est pas exhaustive, car il existe d’autres options que les solutions zure Machine Learning, Python ou OpenCensus présentées ici.

| Bibliothèque                    | Type   | Exemple                                                          | Destination                                  | Ressources                                                                                                                                                                                                                                                                                                                    |
|----------------------------|--------|------------------------------------------------------------------|----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Kit de développement logiciel (SDK) Azure Machine Learning | Métrique | `run.log(name, val)`                                             | Interface utilisateur du portail Azure Machine Learning             | [Comment suivre les expériences](how-to-track-experiments.md#available-metrics-to-track)<br>[azureml.core.Run class](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=experimental)                                                                                                                                                 |
| Impression/Journalisation pour Python    | Journal    | `print(val)`<br>`logging.info(message)`                          | Journaux du pilote, concepteur Azure Machine Learning | [Comment suivre les expériences](how-to-track-experiments.md#available-metrics-to-track)<br><br>[Journalisation pour Python](https://docs.python.org/2/library/logging.html)                                                                                                                                                                       |
| OpenCensus pour Python          | Journal    | `logger.addHandler(AzureLogHandler())`<br>`logging.log(message)` | Application Insights – Traces                | [Déboguer des pipelines dans Application Insights](how-to-debug-pipelines-application-insights.md)<br><br>[Exportateurs OpenCensus Azure Monitor](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)<br>[Fiches pratiques concernant la journalisation pour Python](https://docs.python.org/3/howto/logging-cookbook.html) |

#### <a name="logging-options-example"></a>Exemple d’options du journalisation

```python
import logging

from azureml.core.run import Run
from opencensus.ext.azure.log_exporter import AzureLogHandler

run = Run.get_context()

# Azure ML Scalar value logging
run.log("scalar_value", 0.95)

# Python print statement
print("I am a python print statement, I will be sent to the driver logs.")

# Initialize python logger
logger = logging.getLogger(__name__)
logger.setLevel(args.log_level)

# Plain python logging statements
logger.debug("I am a plain debug statement, I will be sent to the driver logs.")
logger.info("I am a plain info statement, I will be sent to the driver logs.")

handler = AzureLogHandler(connection_string='<connection string>')
logger.addHandler(handler)

# Python logging with OpenCensus AzureLogHandler
logger.warning("I am an OpenCensus warning statement, find me in Application Insights!")
logger.error("I am an OpenCensus error statement with custom dimensions", {'step_id': run.id})
``` 

## <a name="debug-and-troubleshoot-in-azure-machine-learning-designer-preview"></a>Déboguer et résoudre les problèmes dans le concepteur Azure Machine Learning (préversion)

Cette section fournit une vue d’ensemble de la résolution des problèmes des pipelines dans le concepteur.
Pour les pipelines créés dans le concepteur, vous trouverez les **fichiers journaux** dans la page de création ou dans la page des détails d’exécutions de pipeline.

### <a name="access-logs-from-the-authoring-page"></a>Accéder aux journaux à partir de la page de création

Quand vous publiez une exécution de pipeline en restant dans la page de création, vous voyez les fichiers journaux générés pour chaque module.

1. Sélectionnez un module dans le canevas de création.
1. Dans le volet droit du module, accédez à l’onglet **Sorties + journaux**.
1. Sélectionnez le fichier journal `70_driver_log.txt`.

    ![Journaux des modules dans la page de création](./media/how-to-debug-pipelines/pipelinerun-05.png)

### <a name="access-logs-from-pipeline-runs"></a>Accéder aux journaux à partir des exécutions de pipeline

Vous pouvez également trouver les fichiers journaux d’exécutions spécifiques dans la page des détails d’exécutions de pipeline, dans la section **Pipelines** ou la section **Expériences**.

1. Sélectionnez une exécution de pipeline créée dans le concepteur.
    ![Page d’exécutions de pipeline](./media/how-to-debug-pipelines/pipelinerun-04.png)
1. Sélectionnez un module dans le volet d’aperçu.
1. Dans le volet droit du module, accédez à l’onglet **Sorties + journaux**.
1. Sélectionnez le fichier journal `70_driver_log.txt`.

## <a name="debug-and-troubleshoot-in-application-insights"></a>Déboguez et détectez des problèmes dans Application Insights
Pour en savoir plus sur l’utilisation de la bibliothèque Python OpenCensus de cette manière, consultez ce guide : [Déboguer et résoudre les problèmes de pipelines de Machine Learning dans Application Insights](how-to-debug-pipelines-application-insights.md)

## <a name="debug-and-troubleshoot-in-visual-studio-code"></a>Déboguer et détecter un problème dans Visual Studio Code

Dans certains cas, vous devrez peut-être déboguer interactivement le code Python utilisé dans votre pipeline ML. Visual Studio Code (VS Code) et le plug-in Python Tools pour Visual Studio (PTVS) pouvez attacher au code en cours d’exécution dans l’environnement d’apprentissage.

### <a name="prerequisites"></a>Prérequis

* Un __espace de travail Azure Machine Learning__ configuré pour utiliser un __réseau virtuel Azure__.
* un __pipeline Azure Machine Learning__ utilisant des scripts Python dans le cadre de ses étapes. Par exemple, PythonScriptStep.
* Un cluster de Capacité de calcul Machine Learning se trouvant __dans le réseau virtuel__, que __le pipeline utilise pour effectuer l’apprentissage__.
* Un __environnement de développement__ se trouvant __dans le réseau virtuel__. L’environnement de développement peut être l’une des ressources suivantes :

    * une machine virtuelle Azure se trouvant dans le réseau virtuel ;
    * une instance de calcul de machine virtuelle Notebook se trouvant dans le réseau virtuel ;
    * un ordinateur client connecté au réseau virtuel via un réseau privé virtuel (VPN).

Pour plus d’informations sur l’utilisation d’un réseau virtuel Azure avec Azure Machine Learning, voir [Sécuriser l’expérimentation Azure Machine Learning et les travaux d’inférence au sein d’un réseau virtuel Azure](how-to-enable-virtual-network.md).

### <a name="how-it-works"></a>Fonctionnement

Les étapes de votre pipeline ML exécutent des scripts Python. Ces scripts sont modifiés pour effectuer les actions suivantes :
    
1. journaliser l’adresse IP de l’hôte sur lequel ils s’exécutent. Vous utilisez l’adresse IP pour connecter le débogueur au script.

2. Démarrer le composant de débogage PTVS et attendre qu’un débogueur se connecte.

3. À partir de votre environnement de développement, vous surveillez les journaux créés par le processus d’apprentissage pour trouver l’adresse IP où le script est en cours d’exécution.

4. Vous indiquez à VS Code l’adresse IP à laquelle connecter le débogueur à l’aide d’un fichier `launch.json`.

5. Vous attachez le débogueur et parcourez le script de manière interactive.

### <a name="configure-python-scripts"></a>Configurer des scripts Python

Pour activer le débogage, apportez les modifications suivantes au(x) script(s) Python utilisé(s) dans les étapes de votre pipeline ML :

1. Ajoutez les instructions d’importation suivantes :

    ```python
    import ptvsd
    import socket
    from azureml.core import Run
    ```

1. Ajoutez les arguments suivants : Ces arguments vous permettent d’activer le débogueur en fonction des besoins, et de définir le délai d’attente pour l’attachement du débogueur :

    ```python
    parser.add_argument('--remote_debug', action='store_true')
    parser.add_argument('--remote_debug_connection_timeout', type=int,
                    default=300,
                    help=f'Defines how much time the Azure ML compute target '
                    f'will await a connection from a debugger client (VSCODE).')
    ```

1. Ajoutez les instructions suivantes. Ces instructions chargent le contexte d’exécution actuel afin que vous puissiez journaliser l’adresse IP du nœud sur lequel le code s’exécute :

    ```python
    global run
    run = Run.get_context()
    ```

1. Ajoutez une instruction `if` qui démarre PTVS et attend qu’un débogueur s’attache. Si aucun débogueur ne s’attache avant le délai d’expiration, le script continue à s’exécuter normalement.

    ```python
    if args.remote_debug:
        print(f'Timeout for debug connection: {args.remote_debug_connection_timeout}')
        # Log the IP and port
        ip = socket.gethostbyname(socket.gethostname())
        print(f'ip_address: {ip}')
        ptvsd.enable_attach(address=('0.0.0.0', 5678),
                            redirect_output=True)
        # Wait for the timeout for debugger to attach
        ptvsd.wait_for_attach(timeout=args.remote_debug_connection_timeout)
        print(f'Debugger attached = {ptvsd.is_attached()}')
    ```

L’exemple Python suivant montre un fichier `train.py` de base qui active le débogage :

```python
# Copyright (c) Microsoft. All rights reserved.
# Licensed under the MIT license.

import argparse
import os
import ptvsd
import socket
from azureml.core import Run

print("In train.py")
print("As a data scientist, this is where I use my training code.")

parser = argparse.ArgumentParser("train")

parser.add_argument("--input_data", type=str, help="input data")
parser.add_argument("--output_train", type=str, help="output_train directory")

# Argument check for remote debugging
parser.add_argument('--remote_debug', action='store_true')
parser.add_argument('--remote_debug_connection_timeout', type=int,
                    default=300,
                    help=f'Defines how much time the AML compute target '
                    f'will await a connection from a debugger client (VSCODE).')
# Get run object, so we can find and log the IP of the host instance
global run
run = Run.get_context()

args = parser.parse_args()

# Start debugger if remote_debug is enabled
if args.remote_debug:
    print(f'Timeout for debug connection: {args.remote_debug_connection_timeout}')
    # Log the IP and port
    ip = socket.gethostbyname(socket.gethostname())
    print(f'ip_address: {ip}')
    ptvsd.enable_attach(address=('0.0.0.0', 5678),
                        redirect_output=True)
    # Wait for the timeout for debugger to attach
    ptvsd.wait_for_attach(timeout=args.remote_debug_connection_timeout)
    print(f'Debugger attached = {ptvsd.is_attached()}')

print("Argument 1: %s" % args.input_data)
print("Argument 2: %s" % args.output_train)

if not (args.output_train is None):
    os.makedirs(args.output_train, exist_ok=True)
    print("%s created" % args.output_train)
```

### <a name="configure-ml-pipeline"></a>Configurer un pipeline ML

Pour fournir les packages Python nécessaires pour démarrer PTVS et obtenir le contexte d’exécution, créez un environnement et définissez `pip_packages=['ptvsd', 'azureml-sdk==1.0.83']`. Modifiez la version du kit de développement logiciel (SDK) pour qu’elle corresponde à celle que vous utilisez. L’extrait de code suivant montre comment créer un environnement :

```python
# Use a RunConfiguration to specify some additional requirements for this step.
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_CPU_IMAGE

# create a new runconfig object
run_config = RunConfiguration()

# enable Docker 
run_config.environment.docker.enabled = True

# set Docker base image to the default CPU-based image
run_config.environment.docker.base_image = DEFAULT_CPU_IMAGE

# use conda_dependencies.yml to create a conda environment in the Docker image for execution
run_config.environment.python.user_managed_dependencies = False

# specify CondaDependencies obj
run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'],
                                                                           pip_packages=['ptvsd', 'azureml-sdk==1.0.83'])
```

Dans la section [Configurer des scripts Python](#configure-python-scripts), deux nouveaux arguments ont été ajoutés aux scripts qu’utilisent les étapes de votre pipeline ML. L’extrait de code suivant montre comment utiliser ces arguments pour activer le débogage pour le composant et définir un délai d’expiration. Il montre également comment utiliser l’environnement créé précédemment en définissant `runconfig=run_config` :

```python
# Use RunConfig from a pipeline step
step1 = PythonScriptStep(name="train_step",
                         script_name="train.py",
                         arguments=['--remote_debug', '--remote_debug_connection_timeout', 300],
                         compute_target=aml_compute, 
                         source_directory=source_directory,
                         runconfig=run_config,
                         allow_reuse=False)
```

Lorsque le pipeline s’exécute, chaque étape crée une exécution enfant. Si le débogage est activé, le script modifié journalise des informations similaires au texte suivant dans le fichier `70_driver_log.txt` pour l’exécution enfant :

```text
Timeout for debug connection: 300
ip_address: 10.3.0.5
```

Enregistrez la valeur `ip_address`. Elles seront utilisées dans la section suivante.

> [!TIP]
> Vous pouvez également trouver l’adresse IP dans les journaux de l’exécution enfant pour cette étape de pipeline. Pour plus d’informations sur l’affichage de ces informations, voir [Surveiller les exécutions et les métriques des expériences Azure Machine Learning](how-to-track-experiments.md).

### <a name="configure-development-environment"></a>Configurer l’environnement de développement

1. Pour installer Python Tools pour Visual Studio (PTVS) sur votre environnement de développement VS Code, utilisez la commande suivante :

    ```
    python -m pip install --upgrade ptvsd
    ```

    Pour plus d’informations sur l’utilisation de PTVSD avec VS Code, consultez [Débogage à distance](https://code.visualstudio.com/docs/python/debugging#_remote-debugging).

1. Pour configurer VS Code afin de communiquer avec la Capacité de calcul Azure Machine Learning qui exécute le débogueur, créez une nouvelle configuration de débogage :

    1. Dans VS Code, sélectionnez le menu __Déboguer__, puis sélectionnez __Ouvrir les configurations__. Un fichier nommé __launch.json__ s’ouvre.

    1. Dans le fichier __launch.json__, recherchez la ligne contenant `"configurations": [` et insérez le texte suivant après celle-ci : Remplacez l’entrée `"host": "10.3.0.5"` par l’adresse IP renvoyée dans vos journaux de la section précédente. Remplacez l’entrée `"localRoot": "${workspaceFolder}/code/step"` par un répertoire local contenant une copie du script en cours de débogage :

        ```json
        {
            "name": "Azure Machine Learning Compute: remote debug",
            "type": "python",
            "request": "attach",
            "port": 5678,
            "host": "10.3.0.5",
            "redirectOutput": true,
            "pathMappings": [
                {
                    "localRoot": "${workspaceFolder}/code/step1",
                    "remoteRoot": "."
                }
            ]
        }
        ```

        > [!IMPORTANT]
        > Si la section des configurations contient déjà d’autres entrées, ajoutez une virgule (,) après le code que vous avez inséré.

        > [!TIP]
        > La meilleure pratique consiste à conserver les ressources des scripts dans des répertoires distincts. C’est pourquoi la valeur de l’exemple `localRoot` fait référence à `/code/step1`.
        >
        > Si vous déboguez plusieurs scripts, dans des répertoires différents, créez une section de configuration distincte pour chaque script.

    1. Enregistrez le fichier __launch.json__.

### <a name="connect-the-debugger"></a>Connectez le débogueur.

1. Ouvrez VS Code et ouvrez une copie locale du script.
2. Définissez des points d’arrêt là où vous souhaitez que le script s’arrête une fois que vous l’avez attaché.
3. Pendant que le processus enfant exécute le script et que le `Timeout for debug connection` s’affiche dans les journaux, utilisez la touche F5 ou sélectionnez __Déboguer__. Lorsque vous y êtes invité, sélectionnez la configuration __Capacité de calcul Azure Machine Learning : débogage à distance__. Vous pouvez également sélectionner l’icône de débogage dans la barre latérale, l’entrée __Azure Machine Learning : débogage à distance__ dans le menu déroulant Débogage, puis utiliser la flèche verte pour attacher le débogueur.

    À ce stade, VS Code se connecte à PTVS sur le nœud de calcul et s’arrête au point d’arrêt que vous avez préalablement défini. Vous pouvez maintenant parcourir le code au fur et à mesure de son exécution, afficher des variables etc.

    > [!NOTE]
    > Si le journal contient une entrée indiquant `Debugger attached = False`, cela signifie que le délai d’expiration a expiré et que l’exécution du script a continué sans le débogueur. Soumettez à nouveau le pipeline et connectez le débogueur après le message `Timeout for debug connection`, et avant la fin du délai d’expiration.

## <a name="next-steps"></a>Étapes suivantes

* Consultez les informations de référence sur le SDK pour obtenir de l’aide sur les packages [azureml-pipelines-core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) et [azureml-pipelines-steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py).

* Consultez la liste des [exceptions et codes d’erreur du concepteur](algorithm-module-reference/designer-error-codes.md).
