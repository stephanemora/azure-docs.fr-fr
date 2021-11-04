---
title: Résolution des problèmes de points de terminaison de traitement de lots (préversion)
titleSuffix: Azure Machine Learning
description: Conseils pour utiliser efficacement les points de terminaison de traitement de lots.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.topic: troubleshooting
ms.custom: troubleshooting, devplatv2
ms.reviewer: laobri
ms.author: tracych
author: tracych
ms.date: 10/21/2021
ms.openlocfilehash: 97b235e45e1f988fde94272252e738d38b0a53e6
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131564667"
---
# <a name="troubleshooting-batch-endpoints-preview"></a>Résolution des problèmes de points de terminaison de traitement de lots (préversion)

Découvrez comment résoudre ou contourner les erreurs courantes liées à l’utilisation de [points de terminaison de traitement de lots](how-to-use-batch-endpoint.md) (préversion) pour le scoring par lot.

 [!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

Le tableau suivant présente les problèmes courants liés au développement et à la consommation de points de terminaison de traitement de lots et les solutions possibles.

| Problème | Solution possible |
|--|--|
| Configuration de code ou environnement manquant. | Veillez à fournir le script de scoring et une définition d’environnement si vous utilisez un modèle non-MLflow. Le déploiement sans code est pris en charge pour le modèle MLflow uniquement. Pour plus d’informations, consultez [Suivre des modèles ML avec MLflow et Azure Machine Learning](how-to-use-mlflow.md)|
| Données d’entrée non prises en charge. | Le point de terminaison de traitement de lots accepte les données d’entrée sous trois formes : 1) données inscrites 2) données dans le cloud 3) données locales. Vérifiez que vous utilisez le format approprié. Pour plus d’informations, consultez [Utiliser des points de terminaison de traitement de lots (préversion) pour le scoring par lot](how-to-use-batch-endpoint.md)|
| La sortie existe déjà. | Si vous configurez votre propre emplacement de sortie, veillez à spécifier une nouvelle sortie pour chaque appel de point de terminaison. |

## <a name="understanding-logs-of-a-batch-scoring-job"></a>Comprendre les journaux d’un travail de scoring par lot

### <a name="get-logs"></a>Obtenir des journaux d’activité

Quand vous appelez un point de terminaison de lot à l’aide d’Azure CLI ou de l’API REST, le travail de scoring par lot s’exécute de façon asynchrone. Il existe deux options pour obtenir les journaux d’un travail de scoring par lot.

Option 1 : Streamer les journaux à la console locale

Vous pouvez exécuter la commande suivante pour streamer les journaux générés par le système à votre console. Seuls les journaux dans le dossier `azureml-logs` sont streamés.

```bash
az ml job stream -name <job_name>
```

Option 2 : Voir les journaux dans le studio 

Pour obtenir le lien vers l’exécution dans le studio, exécutez : 

```azurecli
az ml job show --name <job_name> --query interaction_endpoints.Studio.endpoint -o tsv
```

1. Ouvrez le travail dans le studio à l’aide de la valeur retournée par la commande ci-dessus. 
1. Choisissez **batchscoring**
1. Ouvrez l’onglet **Sorties + journaux** 
1. Choisissez un ou plusieurs journaux à revoir

### <a name="understand-log-structure"></a>Comprendre la structure des journaux

Il existe deux dossiers de journaux de niveau supérieur : `azureml-logs` et `logs`. 

Le fichier `~/azureml-logs/70_driver_log.txt` contient des informations provenant du contrôleur qui lance le script de scoring.  

En raison de la nature distribuée des travaux de scoring par lot, il existe des journaux provenant de plusieurs sources différentes. Toutefois, deux fichiers combinés sont créés et fournissent des informations générales : 

- `~/logs/job_progress_overview.txt` : Ce fichier fournit des informations générales sur le nombre de mini-lots (également appelés tâches) créés jusqu’à présent et le nombre de mini-lots traités jusqu’à présent. À l’achèvement des mini-lots, le journal enregistre les résultats du travail. Si le travail a échoué, le message d’erreur s’affiche et indique où démarrer la résolution des problèmes.

- `~/logs/sys/master_role.txt`: Ce fichier fournit la vue du nœud principal (également connu sous le nom d’orchestrateur) du travail en cours d’exécution. Ce journal fournit des informations sur la création de tâches, la supervision de la progression et le résultat de l’exécution.

Pour une compréhension concise des erreurs contenues dans votre script :

- `~/logs/user/error.txt`: Ce fichier tentera de résumer les erreurs contenues dans votre script.

Pour plus d’informations sur les erreurs dans votre script :

- `~/logs/user/error/` : Ce fichier contient des traces de la pile complète d’exceptions levées pendant le chargement et l’exécution du script d’entrée.

Lorsque vous avez besoin de comprendre en détail la façon dont chaque nœud a exécuté le script de score, examinez les journaux de processus individuels pour chaque nœud. Les journaux de processus se trouvent dans le dossier `sys/node`, regroupés par nœuds Worker :

- `~/logs/sys/node/<ip_address>/<process_name>.txt`: Ce fichier fournit des informations détaillées sur chaque mini-lot au fur et à mesure qu’il est sélectionné ou traité par un Worker. Pour chaque mini-lot, ce fichier comprend les éléments suivants :

    - L’adresse IP et le PID du processus Worker. 
    - Le nombre total d’éléments, le nombre d’éléments traités avec succès et le nombre d’élément ayant échoué.
    - L’heure de début, la durée, le temps de traitement et la durée de la méthode d’exécution.

Vous pouvez également voir les résultats de vérifications périodiques de l’utilisation de ressources pour chaque nœud. Les fichiers journaux et les fichiers d’installation se trouvent dans ce dossier :

- `~/logs/perf` : Définissez `--resource_monitor_interval` pour modifier l’intervalle de vérification en secondes. La valeur définir pour l’intervalle par défaut est `600`, soit environ 10 minutes. Pour arrêter la surveillance, définissez la valeur sur `0`. Chaque dossier `<ip_address>` comprend les éléments suivants :

    - `os/` : informations sur tous les processus en cours d’exécution dans le nœud. Une vérification exécute une commande du système d’exploitation et enregistre le résultat dans un fichier. Sous Linux, la commande est `ps`.
        - `%Y%m%d%H` : le nom du sous-dossier correspond à l’heure.
            - `processes_%M` : le nom du fichier se termine par la minute à laquelle la vérification a été effectuée.
    - `node_disk_usage.csv` : utilisation détaillée du disque du nœud.
    - `node_resource_usage.csv` : vue d’ensemble de l’utilisation des ressources du nœud.
    - `processes_resource_usage.csv` : vue d’ensemble de l’utilisation des ressources de chaque processus.

### <a name="how-to-log-in-scoring-script"></a>Comment journaliser dans le script de scoring

Vous pouvez utiliser la journalisation Python dans votre script de scoring. Les journaux sont stockés dans `logs/user/stdout/<node_id>/processNNN.stdout.txt`. 

```python
import argparse
import logging

# Get logging_level
arg_parser = argparse.ArgumentParser(description="Argument parser.")
arg_parser.add_argument("--logging_level", type=str, help="logging level")
args, unknown_args = arg_parser.parse_known_args()
print(args.logging_level)

# Initialize python logger
logger = logging.getLogger(__name__)
logger.setLevel(args.logging_level.upper())
logger.info("Info log statement")
logger.debug("Debug log statement")
```