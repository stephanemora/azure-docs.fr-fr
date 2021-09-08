---
title: Transformer des données en exécutant un notebook Synapse
description: Dans cet article, vous allez apprendre à créer et à développer une activité de bloc-notes Synapse et un pipeline Synapse.
services: synapse analytics
author: ruixinxu
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: conceptual
ms.date: 05/19/2021
ms.author: ruxu
ms.reviewer: ''
ms.custom: devx-track-python
ms.openlocfilehash: 9c021b8b0aec025458179ffb4859c351572762ee
ms.sourcegitcommit: ef448159e4a9a95231b75a8203ca6734746cd861
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123186980"
---
# <a name="transform-data-by-running-a-synapse-notebook"></a>Transformer des données en exécutant un notebook Synapse

[!INCLUDE[appliesto-adf-xxx-md](../data-factory/includes/appliesto-xxx-asa-md.md)]

L’activité du notebook Azure Synapse dans un [pipeline Synapse](../data-factory/concepts-pipelines-activities.md) exécute un notebook Synapse dans votre espace de travail Azure Synapse. Cet article s'appuie sur l'article [Activités de transformation des données](../data-factory/transform-data.md) qui présente une vue d'ensemble de la transformation des données et les activités de transformation prises en charge. 

## <a name="create-a-synapse-notebook-activity"></a>Créer une activité de notebook Synapse

Vous pouvez créer une activité de bloc-notes Synapse directement à partir du canevas de pipeline Synapse ou de l’éditeur de bloc-notes. L’activité de bloc-notes Synapse s’exécute sur le pool Spark qui est choisi dans le bloc-notes Synapse. 

### <a name="add-a-synapse-notebook-activity-from-pipeline-canvas"></a>Ajouter une activité de bloc-notes Synapse à partir du canevas de pipeline

Glisser-déplacer un **bloc-notes Synapse** sous **Activités** dans le canevas de pipeline Synapse. Sélectionnez dans la zone activité du bloc-notes Synapse et configurez le contenu du bloc-notes pour l’activité en cours dans les **paramètres**. Vous pouvez sélectionner un bloc-notes existant à partir de l’espace de travail actuel ou en ajouter un nouveau. 

![capture d’écran-présentation-créer-bloc-notes-activité](./media/synapse-notebook-activity/create-synapse-notebook-activity.png)

### <a name="add-a-notebook-to-synapse-pipeline"></a>Ajouter un bloc-notes à un pipeline Synapse

Sélectionnez le bouton **Ajouter au pipeline** dans le coin supérieur droit pour ajouter un notebook à un pipeline existant ou créer un pipeline.

![capture d’écran-présentation-ajouter-bloc-notes-à-pipeline](./media/synapse-notebook-activity/add-to-pipeline.png)

## <a name="passing-parameters"></a>Passage de paramètres

### <a name="designate-a-parameters-cell"></a>Désigner une cellule de paramètres

# <a name="classic-notebook"></a>[Bloc-notes classique](#tab/classical)

Pour paramétrer votre notebook, sélectionnez les points de sélection (…) pour accéder au menu des autres actions sur cellule à l’extrême droite. Sélectionnez ensuite **Activer/désactiver la cellule Paramètres** pour désigner la cellule comme cellule de paramètre.

[![capture d’écran-présentation-basculer-paramètre](./media/synapse-notebook-activity/toggle-parameter-cell.png)](./media/synapse-notebook-activity/toggle-parameter-cell.png#lightbox)

# <a name="preview-notebook"></a>[Notebook en préversion](#tab/preview)

Pour paramétrer votre notebook, sélectionnez le bouton de sélection (…) pour accéder au menu **Plus de commandes** au niveau de la barre d’outils de la cellule. Sélectionnez ensuite **Activer/désactiver la cellule Paramètres** pour désigner la cellule comme cellule de paramètre.

[![capture d’écran-présentation-azure-bloc-notes-basculer-paramètre](./media/synapse-notebook-activity/azure-notebook-toggle-parameter-cell.png)](./media/synapse-notebook-activity/azure-notebook-toggle-parameter-cell.png#lightbox)

---

Azure Data Factory recherche la cellule de paramètre et la traite comme cellule par défaut pour les paramètres transmis au moment de l’exécution. Le moteur d’exécution ajoutera une nouvelle cellule sous la cellule des paramètres avec des paramètres d’entrée en vue de remplacer les valeurs par défaut. 


### <a name="assign-parameters-values-from-a-pipeline"></a>Attribuer des valeurs de paramètres à partir d’un pipeline

Une fois que vous avez créé un bloc-notes avec les paramètres, vous pouvez l’exécuter depuis un pipeline à l’aide de l’activité de bloc-notes Synapse. Après avoir ajouté l’activité à votre canevas de pipeline, vous serez en mesure de définir les valeurs des paramètres dans la section **Paramètres de base** de l’onglet **Paramètres**. 

[![capture d’écran-présentation-affecter-un-paramètre](./media/synapse-notebook-activity/assign-parameter.png)](./media/synapse-notebook-activity/assign-parameter.png#lightbox)

Lors de l’attribution des valeurs de paramètre, vous pouvez utiliser le [langage d’expression du pipeline](../data-factory/control-flow-expression-language-functions.md) ou des [variables système](../data-factory/control-flow-system-variables.md).


## <a name="read-synapse-notebook-cell-output-value"></a>Lire la valeur de sortie de la cellule du bloc-notes Synapse

Vous pouvez lire la valeur de sortie de la cellule du bloc-notes dans les activités suivantes en suivant les étapes ci-dessous :
1. Appelez l’API [mssparkutils.notebook.exit](./spark/microsoft-spark-utilities.md#exit-a-notebook) dans votre activité de bloc-notes Synapse pour retourner la valeur que vous souhaitez afficher dans la sortie de l’activité, par exemple :  

    ```python
    mssparkutils.notebook.exit("hello world") 
    ```
    
    En enregistrant le contenu du bloc-notes et en redéclenchant le pipeline, la sortie de l’activité du bloc-notes contient les exitValue qui peuvent être utilisés pour les activités suivantes à l’étape 2. 

2.  Lit la propriété exitValue de la sortie de l’activité du bloc-notes. Voici un exemple d’expression qui est utilisé pour vérifier si le exitValue extrait de la sortie d’activité du bloc-notes est égal à « Hello World » : 

    [![capture d’écran-présentation-lire-sortie-valeur](./media/synapse-notebook-activity/synapse-read-exit-value.png)](./media/synapse-notebook-activity/synapse-read-exit-value.png#lightbox)


## <a name="run-another-synapse-notebook"></a>Exécuter un autre bloc-notes Synapse 

Vous pouvez référencer d’autres bloc-notes dans une activité de bloc-notes Synapse en appelant [%run magic](./spark/apache-spark-development-using-notebooks.md#notebook-reference) ou les [utilitaires de bloc-notes mssparkutils](./spark/microsoft-spark-utilities.md#notebook-utilities). Les deux prennent en charge l’imbrication des appels de fonction. Les principales différences entre ces deux méthodes que vous devez prendre en compte en fonction de votre scénario sont les suivantes :

- [%run magic](./spark/apache-spark-development-using-notebooks.md#notebook-reference) copie toutes les cellules du bloc-notes référencé dans la cellule %run et partage le contexte de la variable. Lorsque notebook1 fait référence notebook2 via `%run notebook2` et que notebook2 appelle une fonction [mssparkutils.notebook.exit](./spark/microsoft-spark-utilities.md#exit-a-notebook), l’exécution de la cellule dans notebook1 cesse. Nous vous recommandons d’utiliser %run magic quand vous souhaitez « inclure » un fichier de bloc-notes.
- Les [utilitaires de bloc-notes mssparkutils](./spark/microsoft-spark-utilities.md#notebook-utilities) appellent le bloc-notes référencé comme une méthode ou une fonction. Le contexte de la variable n’est pas partagé. Lorsque notebook1 fait référence notebook2 via `mssparkutils.notebook.run("notebook2")` et que notebook2 appelle une fonction [mssparkutils.notebook.exit](./spark/microsoft-spark-utilities.md#exit-a-notebook), l’exécution de la cellule dans notebook1 se poursuit. Nous vous recommandons d’utiliser les utilitaires de bloc-notes mssparkutils lorsque vous souhaitez « importer » un bloc-notes.

>[!Note]
> Exécuter un autre Bloc-notes Synapse à partir d’un pipeline Synapse fonctionne uniquement pour un Bloc-notes avec la Préversion activée.


## <a name="see-notebook-activity-run-history"></a>Consulter l’historique des exécutions d’activité du bloc-notes

Consultez **Exécutions de pipeline** sous l’onglet **Surveillance**, vous visualiserez le pipeline que vous avez déclenché. Ouvrez le pipeline qui contient l’activité du bloc-notes pour afficher l’historique des exécutions. 

Vous pouvez visualiser la dernière capture instantanée d’exécution de bloc-notes, y compris les deux cellules d’entrée et de sortie en sélectionnant le bouton **ouvrir le bloc-notes**. 
![visualiser-bloc-notes-activité-historique](./media/synapse-notebook-activity/input-output-open-notebook.png)


Vous pouvez visualiser les entrées ou les sorties de l’activité du bloc-notes en sélectionnant le bouton **entrées** ou **sorties**. Si votre pipeline échoue avec une erreur utilisateur, sélectionnez les **sorties** pour vérifier le champs **résultats** et visualiser le traceback détaillé des erreurs de l’utilisateur.

![capture d’écran-affichage-visualiser-sortie-utilisateur-erreur](./media/synapse-notebook-activity/notebook-output-user-error.png)


## <a name="synapse-notebook-activity-definition"></a>Définition de l’activité de bloc-notes Synapse

Voici l’exemple de définition JSON d’une activité de bloc-notes Synapse :

```json
{
    "name": "parameter_test",
    "type": "SynapseNotebook",
    "dependsOn": [],
    "policy": {
        "timeout": "7.00:00:00",
        "retry": 0,
        "retryIntervalInSeconds": 30,
        "secureOutput": false,
        "secureInput": false
    },
    "userProperties": [],
    "typeProperties": {
        "notebook": {
            "referenceName": "parameter_test",
            "type": "NotebookReference"
        },
        "parameters": {
            "input": {
                "value": {
                    "value": "@pipeline().parameters.input",
                    "type": "Expression"
                }
            }
        }
    }
}

```


## <a name="synapse-notebook-activity-output"></a>Sortie d’activité de bloc-notes Synapse

Voici l’exemple de code JSON d’une sortie d’activité de bloc-notes Synapse :

```json

{
    "status": {
        "Status": 1,
        "Output": {
            "status": <livySessionInfo>
            },
            "result": {
                "runId": "<GUID>",
                "runStatus": "Succeed",
                "message": "Notebook execution is in Succeeded state",
                "lastCheckedOn": "2021-03-23T00:40:10.6033333Z",
                "errors": {
                    "ename": "",
                    "evalue": ""
                },
                "sessionId": 4,
                "sparkpool": "sparkpool",
                "snapshotUrl": "https://myworkspace.dev.azuresynapse.net/notebooksnapshot/{guid}",
                "exitCode": "abc" // return value from user notebook via mssparkutils.notebook.exit("abc")
            }
        },
        "Error": null,
        "ExecutionDetails": {}
    },

    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (West US 2)",
    "executionDuration": 234,
    "durationInQueue": {
        "integrationRuntimeQueue": 0
    },
    "billingReference": {
        "activityType": "ExternalActivity",
        "billableDuration": [
            {
                "meterType": "AzureIR",
                "duration": 0.06666666666666667,
                "unit": "Hours"
            }
        ]
    }
}

```
