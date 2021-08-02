---
title: Résolution des problèmes de ParallelRunStep
titleSuffix: Azure Machine Learning
description: Conseils pour résoudre les erreurs liées à l’utilisation de ParallelRunStep dans les pipelines de Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: troubleshooting
ms.custom: troubleshooting
ms.reviewer: larryfr, vaidyas, laobri, tracych
ms.author: pansav
author: psavdekar
ms.date: 09/23/2020
ms.openlocfilehash: aaacc12f6a577fd0a2ff0150d22902bb6e7d6cc1
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111753392"
---
# <a name="troubleshooting-the-parallelrunstep"></a>Résolution des problèmes de ParallelRunStep

Dans cet article, vous allez découvrir comment résoudre les erreurs liées à l’utilisation de la classe [ParallelRunStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallel_run_step.parallelrunstep) à partir du [Kit de développement logiciel (SDK) Azure Machine Learning](/python/api/overview/azure/ml/intro).

Pour obtenir des conseils généraux sur la résolution des problèmes liés à un pipeline, consultez [Résolution des problèmes de pipeline Machine Learning](how-to-debug-pipelines.md).

## <a name="testing-scripts-locally"></a>Tester les scripts localement

 ParallelRunStep s’exécute en tant qu’étape dans les pipelines ML. Vous souhaiterez peut-être [tester vos scripts localement](how-to-debug-visual-studio-code.md#debug-and-troubleshoot-machine-learning-pipelines) en tant que première étape.

##  <a name="script-requirements"></a>Configuration requise pour le script

Le script d'une étape `ParallelRunStep` *doit contenir* deux fonctions :
- `init()` : utilisez cette fonction pour toute préparation coûteuse ou commune en vue d’un traitement ultérieur. Par exemple, utilisez-la pour charger le modèle dans un objet global. Cette fonction est appelée une seule fois au début du processus.
    > [!NOTE]
    > Si votre méthode `init` crée un répertoire de sortie, spécifiez que `exist_ok=True`. La méthode `init` est appelée à partir de chaque processus Worker sur chaque nœud sur lequel le travail est en cours d’exécution.
-  `run(mini_batch)`: cette fonction s’exécute pour chaque instance de `mini_batch`.
    -  `mini_batch` : `ParallelRunStep` va appeller la méthode d’exécution, et passer une liste ou un `DataFrame` Pandas en tant qu’argument à la méthode. Chaque entrée de mini_batch sera un chemin de fichier si l’entrée est un `FileDataset`, ou un `DataFrame` Pandas si l’entrée est un `TabularDataset`.
    -  `response` : la méthode run() doit retourner un `DataFrame` Pandas ou un tableau. Pour append_row output_action, les éléments retournés sont ajoutés au fichier de sortie commun. Pour summary_only, le contenu des éléments est ignoré. Pour toutes les actions de sortie, chaque élément de sortie retourné indique la réussite de l’exécution d’une entrée dans le mini-lot d’entrée. Vérifiez que suffisamment de données sont incluses dans le résultat de l’exécution pour mapper l’entrée au résultat de la sortie de l’exécution. La sortie de l’exécution sera écrite dans le fichier de sortie, mais pas nécessairement dans l’ordre. Vous devez utiliser une clé dans la sortie pour la mapper à l’entrée.
        > [!NOTE]
        > Un élément de sortie est attendu pour un élément d’entrée.  

```python
%%writefile digit_identification.py
# Snippets from a sample script.
# Refer to the accompanying digit_identification.py
# (https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines/parallel-run)
# for the implementation script.

import os
import numpy as np
import tensorflow as tf
from PIL import Image
from azureml.core import Model


def init():
    global g_tf_sess

    # Pull down the model from the workspace
    model_path = Model.get_model_path("mnist")

    # Construct a graph to execute
    tf.reset_default_graph()
    saver = tf.train.import_meta_graph(os.path.join(model_path, 'mnist-tf.model.meta'))
    g_tf_sess = tf.Session()
    saver.restore(g_tf_sess, os.path.join(model_path, 'mnist-tf.model'))


def run(mini_batch):
    print(f'run method start: {__file__}, run({mini_batch})')
    resultList = []
    in_tensor = g_tf_sess.graph.get_tensor_by_name("network/X:0")
    output = g_tf_sess.graph.get_tensor_by_name("network/output/MatMul:0")

    for image in mini_batch:
        # Prepare each image
        data = Image.open(image)
        np_im = np.array(data).reshape((1, 784))
        # Perform inference
        inference_result = output.eval(feed_dict={in_tensor: np_im}, session=g_tf_sess)
        # Find the best probability, and add it to the result list
        best_result = np.argmax(inference_result)
        resultList.append("{}: {}".format(os.path.basename(image), best_result))

    return resultList
```

Si vous avez un autre fichier ou dossier dans le même répertoire que votre script d’inférence, vous pouvez le référencer en recherchant le répertoire de travail actuel.

```python
script_dir = os.path.realpath(os.path.join(__file__, '..',))
file_path = os.path.join(script_dir, "<file_name>")
```

### <a name="parameters-for-parallelrunconfig"></a>Paramètres de ParallelRunConfig

`ParallelRunConfig` est la configuration principale de l’instance `ParallelRunStep` dans le pipeline Azure Machine Learning. Elle permet de wrapper votre script et de configurer les paramètres nécessaires, dont toutes les entrées suivantes :
- `entry_script`: script utilisateur utilisé comme un chemin de fichier local qui sera exécuté en parallèle sur plusieurs nœuds. Si `source_directory` est présent, utilisez un chemin relatif. Dans le cas contraire, utilisez un chemin accessible sur la machine.
- `mini_batch_size`: taille du mini-lot passé à un appel `run()` unique (Facultatif ; la valeur par défaut est `10` fichiers pour `FileDataset` et `1MB` pour `TabularDataset`.)
    - Pour `FileDataset`, il s’agit du nombre de fichiers avec une valeur minimale de `1`. Vous pouvez combiner plusieurs fichiers dans un mini-lot.
    - Pour `TabularDataset`, il s’agit de la taille des données. Par exemple, il peut s’agir des valeurs `1024`, `1024KB`, `10MB` ou `1GB`. `1MB` est la valeur recommandée. Le mini-lot de `TabularDataset` ne franchira jamais les limites du fichier. Par exemple, si vous avez des fichiers .csv de différentes tailles, le plus petit fichier aura une taille de 100 Ko et le plus grand une taille de 10 Mo. Si vous définissez `mini_batch_size = 1MB`, les fichiers dont la taille est inférieure à 1 Mo seront traités ensemble comme un mini-lot. Les fichiers dont la taille est supérieure à 1 Mo seront répartis dans plusieurs mini-lots.
        > [!NOTE]
        > Impossible de partitionner les TabularDatasets sauvegardés par SQL. Les TabularDatasets provenant d’un seul fichier Parquet et d’un seul groupe de lignes ne peuvent pas être partitionnés.

- `error_threshold`: nombre d’échecs d’enregistrement pour `TabularDataset` et d’échecs de fichiers pour `FileDataset` qui doivent être ignorés pendant le traitement. Si le nombre d’erreurs présentes dans la totalité de l’entrée dépasse cette valeur, le travail est annulé. Le seuil d’erreur concerne la totalité de l’entrée et non le mini-lot envoyé à la méthode `run()`. La plage est la suivante : `[-1, int.max]`. La partie `-1` indique qu’il faut ignorer tous les échecs au cours du traitement.
- `output_action`: l’une des valeurs suivantes indique comment la sortie sera organisée :
    - `summary_only`: le script utilisateur va stocker la sortie. `ParallelRunStep` utilisera la sortie uniquement pour le calcul du seuil d’erreurs.
    - `append_row`: pour toutes les entrées, un seul fichier sera créé dans le dossier de sortie. Vous y ajouterez toutes les sorties séparées par une ligne.
- `append_row_file_name`: permet de personnaliser le nom du fichier de sortie pour append_row output_action (facultatif ; la valeur par défaut est `parallel_run_step.txt`).
- `source_directory`: chemins des dossiers qui contiennent tous les fichiers à exécuter sur la cible de calcul (facultatif).
- `compute_target`: Seul `AmlCompute` est pris en charge.
- `node_count`: nombre de nœuds de calcul à utiliser pour l’exécution du script utilisateur.
- `process_count_per_node` : le nombre de processus Worker par nœud pour exécuter le script d’entrée en parallèle. Pour un ordinateur GPU, la valeur par défaut est 1. Pour un ordinateur UC, la valeur par défaut est le nombre de cœurs par nœud. Un processus Worker appellera `run()` de manière répétée en transmettant le mini lot qu’il obtient. Le nombre total de processus Worker dans votre travail est `process_count_per_node * node_count`, qui détermine le nombre maximal de `run()` à exécuter en parallèle.  
- `environment`: définition de l’environnement Python. Vous pouvez la configurer de manière à utiliser un environnement Python existant ou un environnement temporaire. La définition est également chargée de définir les dépendances d’application nécessaires (facultatif).
- `logging_level`: Verbosité du journal. Les valeurs permettant d’augmenter le niveau de verbosité sont les suivantes : `WARNING`, `INFO` et `DEBUG`. (Facultatif ; la valeur par défaut est `INFO`.)
- `run_invocation_timeout`: délai d’attente de l’appel de la méthode `run()`, en secondes. (Facultatif ; la valeur par défaut est `60`.)
- `run_max_try`: nombre maximal de tentatives de `run()` pour un mini-lot. `run()` a échoué si une exception est levée, ou si rien n’est retourné lorsque `run_invocation_timeout` est atteint (facultatif ; la valeur par défaut est `3`). 

Vous pouvez spécifier `mini_batch_size`, `node_count`, `process_count_per_node`, `logging_level`, `run_invocation_timeout` et `run_max_try` en tant que `PipelineParameter` ; ainsi, lorsque vous soumettez à nouveau une exécution de pipeline, vous pouvez ajuster les valeurs des paramètres. Dans cet exemple, vous utilisez `PipelineParameter` pour `mini_batch_size` et `Process_count_per_node`, puis vous modifiez ces valeurs quand vous soumettez à nouveau une exécution. 

#### <a name="cuda-devices-visibility"></a>Visibilité des appareils CUDA
Pour les cibles de calcul équipées de GPU, la variable d’environnement `CUDA_VISIBLE_DEVICES` est définie dans les processus Worker. Dans AmlCompute, vous pouvez trouver le nombre total de périphériques GPU dans la variable d’environnement `AZ_BATCHAI_GPU_COUNT_FOUND`, qui est définie automatiquement. Si vous souhaitez que chaque processus Worker ait un GPU dédié, définissez `process_count_per_node` égal au nombre de périphériques GPU sur un ordinateur. Chaque processus Worker attribuera un index unique à `CUDA_VISIBLE_DEVICES`. Si un processus Worker s’arrête pour une raison quelconque, le prochain processus Worker qui démarre utilisera l’index GPU libéré.

Si le nombre total de périphériques GPU est inférieur à `process_count_per_node`, les processus Worker se voient attribuer un index GPU jusqu’à ce que tous aient été utilisés. 

Si le nombre total de périphériques GPU est 2 et que `process_count_per_node = 4`, par exemple, le processus 0 et le processus 1 auront les index 0 et 1. Les processus 2 et 3 n’auront pas de variable d’environnement. Pour une bibliothèque qui utilise cette variable d’environnement pour l’attribution des GPU, les processus 2 et 3 n’auront pas de GPU et n’essaieront pas d’acquérir des périphériques GPU. Si le processus 0 s’arrête, il libère l’index GPU 0. Le processus suivant, à savoir le processus 4, se verra attribuer l’index GPU 0.

Pour plus d’informations, consultez [CUDA Pro Tip: Control GPU Visibility with CUDA_VISIBLE_DEVICES](https://developer.nvidia.com/blog/cuda-pro-tip-control-gpu-visibility-cuda_visible_devices/).

### <a name="parameters-for-creating-the-parallelrunstep"></a>Paramètres de création de l'étape ParallelRunStep

Créez ParallelRunStep à l’aide du script, de la configuration de l’environnement et des paramètres. Spécifiez la cible de calcul que vous avez déjà jointe à votre espace de travail en tant que cible d’exécution de votre script d’inférence. Utilisez `ParallelRunStep` pour créer l’étape de pipeline d’inférence par lots, qui accepte tous les paramètres suivants :
- `name`: nom de l’étape, avec les restrictions de nommage suivantes : unique, de 3 à 32 caractères et expression régulière ^\[a-z\]([-a-z0-9]*[a-z0-9])?$.
- `parallel_run_config`: objet `ParallelRunConfig`, comme défini précédemment.
- `inputs`: un ou plusieurs jeux de données Azure Machine Learning à un seul type, à partitionner pour un traitement parallèle.
- `side_inputs`: une ou plusieurs données, ou jeux de données de référence utilisés comme entrées supplémentaires sans avoir besoin d’être partitionnés.
- `output` : objet `OutputFileDatasetConfig` qui représente le chemin d’accès au répertoire dans lequel les données de sortie seront stockées.
- `arguments`: liste d’arguments passés au script utilisateur. Utilisez unknown_args pour les récupérer dans votre script d’entrée (facultatif).
- `allow_reuse`: indique si l’étape doit réutiliser les résultats précédents lorsqu’elle est exécutée avec les mêmes paramètres ou entrées. Si ce paramètre a la valeur `False`, une nouvelle exécution sera toujours générée pour cette étape pendant l’exécution du pipeline (Facultatif ; la valeur par défaut est `True`.)

```python
from azureml.pipeline.steps import ParallelRunStep

parallelrun_step = ParallelRunStep(
    name="predict-digits-mnist",
    parallel_run_config=parallel_run_config,
    inputs=[input_mnist_ds_consumption],
    output=output_dir,
    allow_reuse=True
)
```

## <a name="debugging-scripts-from-remote-context"></a>Déboguer les scripts à partir du contexte distant

La transition du débogage d’un script de scoring localement au débogage d’un script de scoring dans un pipeline réel peut être difficile. Pour plus d’informations sur la recherche de vos journaux dans le portail, consultez la [section Pipelines de Machine Learning sur le débogage de scripts à partir d’un contexte distant](how-to-debug-pipelines.md). Les informations contenues dans cette section s’appliquent également à un ParallelRunStep.

Par exemple, le fichier journal `70_driver_log.txt` contient des informations provenant du contrôleur qui lance le code ParallelRunStep.

En raison de la nature distribuée des travaux ParallelRunStep, les journaux peuvent provenir de plusieurs sources différentes. Toutefois, deux fichiers consolidés sont créés et fournissent des informations de haut niveau :

- `~/logs/job_progress_overview.txt`: Ce fichier fournit des informations de haut niveau sur le nombre de mini-lots (également appelés tâches) créés jusqu’à présent et le nombre de mini-lots traités jusqu’à présent. À cette fin, il affiche le résultat du travail. Si le travail a échoué, le message d’erreur s’affiche et indique où démarrer la résolution des problèmes.

- `~/logs/sys/master_role.txt`: Ce fichier fournit la vue du nœud principal (également connu sous le nom d’orchestrateur) du travail en cours d’exécution. Comprend la création de tâches, la supervision de la progression et le résultat de l’exécution.

Les journaux générés à partir du script d’entrée avec l’assistance EntryScript et les instructions print se trouvent dans les fichiers suivants :

- `~/logs/user/entry_script_log/<node_id>/<process_name>.log.txt`: Ces fichiers sont les journaux écrits à partir d’entry_script avec le helper EntryScript.

- `~/logs/user/stdout/<node_id>/<process_name>.stdout.txt` : ces fichiers sont les journaux de stdout (par exemple, l’instruction print) d’entry_script.

- `~/logs/user/stderr/<node_id>/<process_name>.stderr.txt` : ces fichiers sont les journaux de stderr d’entry_script.

Pour une compréhension concise des erreurs contenues dans votre script :

- `~/logs/user/error.txt`: Ce fichier tentera de résumer les erreurs contenues dans votre script.

Pour plus d’informations sur les erreurs dans votre script :

- `~/logs/user/error/` : contient des traces de la pile complète d’exceptions levées pendant le chargement et l’exécution du script d’entrée.

Lorsque vous avez besoin de comprendre en détail la façon dont chaque nœud a exécuté le script de score, examinez les journaux de processus individuels pour chaque nœud. Les journaux de processus se trouvent dans le dossier `sys/node`, regroupés par nœuds Worker :

- `~/logs/sys/node/<node_id>/<process_name>.txt`: Ce fichier fournit des informations détaillées sur chaque mini-lot au fur et à mesure qu’il est sélectionné ou traité par un Worker. Pour chaque mini-lot, ce fichier comprend les éléments suivants :

    - L’adresse IP et le PID du processus Worker. 
    - Le nombre total d’éléments, le nombre d’éléments traités avec succès et le nombre d’éléments ayant échoué.
    - L’heure de début, la durée, le temps de traitement et la durée de la méthode d’exécution.

Vous pouvez également afficher les résultats de vérifications périodiques de l’utilisation de ressources pour chaque nœud. Les fichiers journaux et les fichiers d’installation se trouvent dans ce dossier :

- `~/logs/perf` : Définissez `--resource_monitor_interval` pour modifier l’intervalle de vérification en secondes. La valeur définir pour l’intervalle par défaut est `600`, soit environ 10 minutes. Pour arrêter la surveillance, définissez la valeur sur `0`. Chaque dossier `<node_id>` comprend les éléments suivants :

    - `os/` : informations sur tous les processus en cours d’exécution dans le nœud. Une vérification exécute une commande du système d’exploitation et enregistre le résultat dans un fichier. Sous Linux, la commande est `ps`. Sous Windows, utilisez la commande `tasklist`.
        - `%Y%m%d%H` : le nom du sous-dossier correspond à l’heure.
            - `processes_%M` : le nom du fichier se termine par la minute à laquelle la vérification a été effectuée.
    - `node_disk_usage.csv` : utilisation détaillée du disque du nœud.
    - `node_resource_usage.csv` : vue d’ensemble de l’utilisation des ressources du nœud.
    - `processes_resource_usage.csv` : vue d’ensemble de l’utilisation des ressources de chaque processus.

### <a name="how-do-i-log-from-my-user-script-from-a-remote-context"></a>Comment se connecter à son script utilisateur depuis un contexte distant ?

ParallelRunStep peut exécuter plusieurs processus sur un nœud en fonction de process_count_per_node. Pour organiser les journaux de chaque processus sur un nœud et combiner les instructions print et log, nous vous recommandons d’utiliser l’enregistreur d’événements ParallelRunStep comme indiqué ci-dessous. Vous recevez d’EntryScript un enregistreur d’événements et affichez les journaux dans le dossier **logs/user** dans le portail.

**exemple de script d’entrée utilisant l’enregistreur d’événements :**
```python
from azureml_user.parallel_run import EntryScript

def init():
    """Init once in a worker process."""
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug("This will show up in files under logs/user on the Azure portal.")


def run(mini_batch):
    """Call once for a mini batch. Accept and return the list back."""
    # This class is in singleton pattern and will return same instance as the one in init()
    entry_script = EntryScript()
    logger = entry_script.logger
    logger.debug(f"{__file__}: {mini_batch}.")
    ...

    return mini_batch
```

### <a name="where-does-the-message-from-python-logging-sink-to"></a>Où le message de Python `logging` est-il reçu ?
ParallelRunStep définit un gestionnaire sur le journaliseur racine, qui reçoit le message dans `logs/user/stdout/<node_id>/processNNN.stdout.txt`.

`logging` a par défaut le niveau `WARNING`. Par défaut, les niveaux inférieurs à `WARNING` ne s’affichent pas, tels que `INFO` ou `DEBUG`.

### <a name="where-is-the-message-from-subprocess-created-with-popen"></a>Où le message du sous-processus est-il créé avec Popen() ?
Si aucun `stdout` ou `stderr` n’est spécifié, un sous-processus hérite du paramètre du processus Worker.

`stdout` écrit dans `logs/sys/node/<node_id>/processNNN.stdout.txt` et `stderr` dans `logs/sys/node/<node_id>/processNNN.stderr.txt`.

### <a name="how-could-i-write-to-a-file-to-show-up-in-the-portal"></a>Comment écrire dans un fichier pour qu’il apparaisse dans le portail ?
Les fichiers dans le dossier `logs` sont chargés et s’affichent dans le portail.
Vous pouvez accéder au dossier `logs/user/entry_script_log/<node_id>` comme ci-dessous et composer le chemin du fichier en vue de l’écriture :

```python
from pathlib import Path
def init():
    """Init once in a worker process."""
    entry_script = EntryScript()
    folder = entry_script.log_dir

    fil_path = Path(folder) / "<file_name>"
```

### <a name="how-do-i-write-a-file-to-the-output-directory-and-then-view-it-in-the-portal"></a>Comment puis-je écrire un fichier dans le répertoire de sortie, puis l’afficher dans le portail ?

Vous pouvez obtenir le répertoire de sortie à l’aide de la classe `EntryScript` et y écrire. Pour afficher les fichiers écrits, dans l’affichage Exécution de l’étape du portail Azure Machine Learning, sélectionnez l’onglet **Sorties + journaux d’activité**. Sélectionnez le lien **Sorties de données**, puis effectuez les étapes décrites dans la boîte de dialogue. 

Utilisez `EntryScript` dans votre script d’entrée comme dans l’exemple suivant :

```python
from pathlib import Path
from azureml_user.parallel_run import EntryScript

def run(mini_batch):
    output_dir = Path(entry_script.output_dir)
    (Path(output_dir) / res1).write...
    (Path(output_dir) / res2).write...
```

### <a name="how-can-i-pass-a-side-input-such-as-a-file-or-files-containing-a-lookup-table-to-all-my-workers"></a>Comment puis-je transmettre une entrée supplémentaire, par exemple un ou des fichiers contenant une table de choix, à tous mes collaborateurs ?

L’utilisateur peut passer des données de référence au script à l’aide du paramètre side_inputs de ParalleRunStep. Tous les jeux de données fournis en tant que side_inputs seront montés sur chaque nœud Worker. L’utilisateur peut récupérer l’emplacement du montage en passant l’argument.

Construisez un [Jeu de données](/python/api/azureml-core/azureml.core.dataset.dataset) contenant les données de référence, spécifiez un chemin de montage local et inscrivez-le auprès de votre espace de travail. Transmettez-le au paramètre `side_inputs` de votre `ParallelRunStep`. En outre, vous pouvez ajouter son chemin d’accès dans la section `arguments` pour accéder facilement à son chemin monté.

> [!NOTE]
> Utilisez FileDatasets uniquement pour side_inputs. 

```python
local_path = "/tmp/{}".format(str(uuid.uuid4()))
label_config = label_ds.as_named_input("labels_input").as_mount(local_path)
batch_score_step = ParallelRunStep(
    name=parallel_step_name,
    inputs=[input_images.as_named_input("input_images")],
    output=output_dir,
    arguments=["--labels_dir", label_config],
    side_inputs=[label_config],
    parallel_run_config=parallel_run_config,
)
```

Après cela, vous pouvez y accéder dans votre script d’inférence, par exemple dans votre méthode init(), comme suit :

```python
parser = argparse.ArgumentParser()
parser.add_argument('--labels_dir', dest="labels_dir", required=True)
args, _ = parser.parse_known_args()

labels_path = args.labels_dir
```

### <a name="how-to-use-input-datasets-with-service-principal-authentication"></a>Comment utiliser des jeux de données d’entrée avec l’authentification du principal du service ?
L’utilisateur peut passer des jeux de données d’entrée avec l’authentification du principal du service utilisée dans l’espace de travail. L’utilisation d’un tel jeu de données dans ParallelRunStep nécessite que le jeu de données soit inscrit pour pouvoir construire une configuration ParallelRunStep.

```python
service_principal = ServicePrincipalAuthentication(
    tenant_id="***",
    service_principal_id="***",
    service_principal_password="***")
 
ws = Workspace(
    subscription_id="***",
    resource_group="***",
    workspace_name="***",
    auth=service_principal
    )
 
default_blob_store = ws.get_default_datastore() # or Datastore(ws, '***datastore-name***') 
ds = Dataset.File.from_files(default_blob_store, '**path***')
registered_ds = ds.register(ws, '***dataset-name***', create_new_version=True)
```

## <a name="how-to-check-progress-and-analyze-it"></a>Comment vérifier la progression et l’analyser
Cette section explique comment vérifier la progression d’un travail ParallelRunStep et rechercher la cause d’un comportement inattendu.

### <a name="how-to-check-job-progress"></a>Comment vérifier la progression d’un travail ?
Outre l’examen de l’état général du StepRun, vous pouvez consulter le nombre de mini-lots planifiés ou traités et la progression de la génération de la sortie dans `~/logs/job_progress_overview.<timestamp>.txt`. Le fichier est mis à jour quotidiennement : vous pouvez consulter celui dont le timestamp est le plus élevé pour obtenir les informations les plus récentes.

### <a name="what-should-i-check-if-there-is-no-progress-for-a-while"></a>Que dois-je vérifier s’il n’y a aucune progression pendant un certain temps ?
Vous pouvez accéder à `~/logs/sys/errror` pour voir s’il y a une exception. S’il n’y en a pas, il est probable que votre script d’entrée prenne beaucoup de temps. Vous pouvez imprimer les informations relatives à la progression dans votre code pour localiser la partie qui prend du temps ou vous pouvez ajouter `"--profiling_module", "cProfile"` à la section `arguments` de `ParallelRunStep` pour générer un fichier de profil nommé `<process_name>.profile` sous le dossier `~/logs/sys/node/<node_id>`.

### <a name="when-will-a-job-stop"></a>Quand un travail s’arrête-t-il  ?
S’il n’est pas annulé, le travail s’arrêtera avec l’état :
- Terminé. Si tous les mini-lots ont été traités et que la sortie a été générée pour le mode `append_row`.
- Échec. Si `error_threshold` dans [`Parameters for ParallelRunConfig`](#parameters-for-parallelrunconfig) est dépassé ou si une erreur système s’est produite pendant le travail.

### <a name="where-to-find-the-root-cause-of-failure"></a>Où trouver la cause racine de l’échec ?
Vous pouvez suivre la piste dans `~logs/job_result.txt` pour rechercher la cause et le journal détaillé des erreurs.

### <a name="will-node-failure-impact-the-job-result"></a>La défaillance d’un nœud aura-t-elle un impact sur le résultat du travail ?
Non, à condition que d’autres nœuds soient disponibles dans le cluster de calcul désigné. L’orchestrateur démarrera un nouveau nœud en remplacement, et ParallelRunStep est résilient à une telle opération.

### <a name="what-happens-if-init-function-in-entry-script-fails"></a>Que se passe-t-il si la fonction `init` du script d’entrée échoue ?
ParallelRunStep dispose d’un mécanisme de nouvelle tentative pendant un certain temps afin de permettre la récupération en cas de problèmes temporaires sans retarder trop longtemps l’échec du travail. Le mécanisme est le suivant :
1. Si, après le démarrage d’un nœud, `init` sur tous les agents continue d’échouer, nous arrêterons les tentatives après `3 * process_count_per_node` échecs.
2. Si, après le démarrage du travail, `init` sur tous les agents de tous les nœuds continue d’échouer, nous arrêterons les tentatives si le travail dure plus de deux minutes et qu’il y a `2 * node_count * process_count_per_node` échecs.
3. Si tous les agents sont bloqués sur `init` pendant plus de `3 * run_invocation_timeout + 30` secondes, le travail échoue en raison d’une absence de progression sur une période trop longue.

### <a name="what-will-happen-on-outofmemory-how-can-i-check-the-cause"></a>Que se passe-t-il en cas d’erreur OutOfMemory ? Comment puis-je en vérifier la cause ?
ParallelRunStep indique que la tentative actuelle de traitement du mini-lot est en échec et tente de redémarrer le processus qui a échoué. Vous pouvez vérifier `~logs/perf/<node_id>` pour rechercher le processus gourmand en mémoire.

### <a name="why-do-i-have-a-lot-of-processnnn-files"></a>Pourquoi ai-je un grand nombre de fichiers processNNN ?
ParallelRunStep démarre de nouveaux processus Worker en remplacement de ceux qui se sont arrêtés anormalement, et chaque processus génère un fichier `processNNN` comme journal. Toutefois, si le processus a échoué en raison d’une exception survenue pendant la fonction `init` du script utilisateur et que l’erreur s’est répétée continuellement sur `3 * process_count_per_node` tentatives, aucun nouveau processus Worker ne sera démarré.

## <a name="next-steps"></a>Étapes suivantes

* Consultez ces [Notebooks Jupyter illustrant des pipelines Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines).

* Consultez les informations de référence sur le SDK pour obtenir de l’aide sur le package [azureml-pipeline-Steps](/python/api/azureml-pipeline-steps/azureml.pipeline.steps). Consultez la [documentation](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunstep) de référence pour la classe ParallelRunStep.

* Suivez le [tutoriel avancé](tutorial-pipeline-batch-scoring-classification.md) sur l’utilisation des pipelines avec ParallelRunStep. Le tutoriel montre comment passer un autre fichier en tant qu’entrée de côté.
