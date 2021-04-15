---
title: Profilage de la mémoire sur les applications Python dans Azure Functions
description: Apprenez à profiler l’utilisation de la mémoire des applications Python et à identifier le goulot d’étranglement de mémoire.
ms.topic: how-to
author: hazhzeng
ms.author: hazeng
ms.date: 3/22/2021
ms.custom: devx-track-python
ms.openlocfilehash: f47451809a2fa2b7d22805e6415056f19c4c1d88
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107258807"
---
# <a name="profile-python-apps-memory-usage-in-azure-functions"></a>Profiler l’utilisation de la mémoire des applications Python dans Azure Functions

Pendant le développement ou après le déploiement de votre projet d’application de fonction Python local dans Azure, il est recommandé d’analyser les goulots d’étranglement de mémoire potentiels dans vos fonctions. De tels goulots d’étranglement peuvent réduire les performances de vos fonctions et entraîner des erreurs. L’instruction suivante vous montre comment utiliser le package Python du [profileur de mémoire](https://pypi.org/project/memory-profiler), qui permet d’analyser la consommation de mémoire ligne par ligne de vos fonctions au fur et à mesure de leur exécution.

> [!NOTE]
> Le profilage de la mémoire est destiné uniquement à l’analyse de l’empreinte mémoire sur l’environnement de développement. N’appliquez pas le profileur de mémoire sur les applications de fonction de production.

## <a name="prerequisites"></a>Prérequis

Avant de commencer à développer une application de fonction Python, vous devez respecter les conditions suivantes :

* [Python 3.6.x ou version ultérieure](https://www.python.org/downloads/release/python-374/). Pour consulter la liste complète des versions de Python prises en charge dans Azure Functions, consultez le [Guide du développeur Python](functions-reference-python.md#python-version).

* [Azure Functions Core Tools](functions-run-local.md#v2) version 3.x.

* [Visual Studio Code](https://code.visualstudio.com/) doit être installé sur l’une des [plateformes prises en charge](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

* Un abonnement Azure actif.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="memory-profiling-process"></a>Processus de profilage de la mémoire

1. Dans votre requirements.txt, ajoutez `memory-profiler` pour vous assurer que le package est groupé à votre déploiement. Si vous développez sur votre ordinateur local, vous souhaiterez peut-être [activer un environnement virtuel Python](create-first-function-cli-python.md#create-venv) et effectuer une résolution de package par `pip install -r requirements.txt`.

2. Dans votre script de fonction (généralement \_\_init\_\_.py), ajoutez les lignes suivantes au-dessus de la fonction `main()`. Cela permet de s’assurer que l’enregistreur d'événements racine signale les noms d’enregistreur d’événements enfants, afin que les journaux de profilage de la mémoire se distinguent par le préfixe `memory_profiler_logs`.

    ```python
    import logging
    import memory_profiler
    root_logger = logging.getLogger()
    root_logger.handlers[0].setFormatter(logging.Formatter("%(name)s: %(message)s"))
    profiler_logstream = memory_profiler.LogFile('memory_profiler_logs', True)

3. Apply the following decorator above any functions that need memory profiling. This does not work directly on the trigger entrypoint `main()` method. You need to create subfunctions and decorate them. Also, due to a memory-profiler known issue, when applying to an async coroutine, the coroutine return value will always be None.

    ```python
    @memory_profiler.profile(stream=profiler_logstream)

4. Test the memory profiler on your local machine by using azure Functions Core Tools command `func host start`. This should generate a memory usage report with file name, line of code, memory usage, memory increment, and the line content in it.

5. To check the memory profiling logs on an existing function app instance in Azure, you can query the memory profiling logs in recent invocations by pasting the following Kusto queries in Application Insights, Logs.

:::image type="content" source="media/python-memory-profiler-reference/application-insights-query.png" alt-text="Query memory usage of a Python app in Application Insights":::

```text
traces
| where timestamp > ago(1d)
| where message startswith_cs "memory_profiler_logs:"
| parse message with "memory_profiler_logs: " LineNumber "  " TotalMem_MiB "  " IncreMem_MiB "  " Occurences "  " Contents
| union (
    traces
    | where timestamp > ago(1d)
    | where message startswith_cs "memory_profiler_logs: Filename: "
    | parse message with "memory_profiler_logs: Filename: " FileName
    | project timestamp, FileName, itemId
)
| project timestamp, LineNumber=iff(FileName != "", FileName, LineNumber), TotalMem_MiB, IncreMem_MiB, Occurences, Contents, RequestId=itemId
| order by timestamp asc
```

## <a name="example"></a>Exemple

Voici un exemple d’exécution du profilage de la mémoire sur des déclencheurs HTTP asynchrone et synchrone, nommés respectivement « HttpTriggerAsync » et « HttpTriggerSync ». Nous allons créer une application de fonction Python qui envoie simplement des requêtes GET à la page d’accueil de Microsoft.

### <a name="create-a-python-function-app"></a>Créer une application de fonction Python

Une application de fonction Python doit suivre la [structure de dossiers](functions-reference-python.md#folder-structure) Azure Functions spécifiée. Pour générer un modèle de structure pour le projet, nous vous recommandons d’utiliser Azure Functions Core Tools en exécutant les commandes suivantes :

```bash
func init PythonMemoryProfilingDemo --python
cd PythonMemoryProfilingDemo
func new -l python -t HttpTrigger -n HttpTriggerAsync -a anonymous
func new -l python -t HttpTrigger -n HttpTriggerSync -a anonymous
```

### <a name="update-file-contents"></a>Mettre à jour les contenus de fichiers

Le requirements.txt définit les packages qui seront utilisés dans notre projet. En plus du kit SDK Azure Functions et profileur de mémoire, nous avons introduit `aiohttp` pour les requêtes HTTP asynchrones et `requests` pour les appels HTTP synchrones.

```text
# requirements.txt

azure-functions
memory-profiler
aiohttp
requests
```

Nous devons également réécrire le déclencheur HTTP asynchrone `HttpTriggerAsync/__init__.py` et configurer le profileur de mémoire, le format d’enregistreur d'événements racine et la liaison de diffusion d’enregistreur d’événements.

```python
# HttpTriggerAsync/__init__.py

import azure.functions as func
import aiohttp
import logging
import memory_profiler

# Update root logger's format to include the logger name. Ensure logs generated
# from memory profiler can be filtered by "memory_profiler_logs" prefix.
root_logger = logging.getLogger()
root_logger.handlers[0].setFormatter(logging.Formatter("%(name)s: %(message)s"))
profiler_logstream = memory_profiler.LogFile('memory_profiler_logs', True)

async def main(req: func.HttpRequest) -> func.HttpResponse:
    await get_microsoft_page_async('https://microsoft.com')
    return func.HttpResponse(
        f"Microsoft Page Is Loaded",
        status_code=200
    )

@memory_profiler.profile(stream=profiler_logstream)
async def get_microsoft_page_async(url: str):
    async with aiohttp.ClientSession() as client:
        async with client.get(url) as response:
            await response.text()
    # @memory_profiler.profile does not support return for coroutines.
    # All returns become None in the parent functions.
    # GitHub Issue: https://github.com/pythonprofilers/memory_profiler/issues/289
```

Pour un déclencheur HTTP synchrone, reportez-vous à la section de code `HttpTriggerSync/__init__.py` suivante :

```python
# HttpTriggerSync/__init__.py

import azure.functions as func
import requests
import logging
import memory_profiler

# Update root logger's format to include the logger name. Ensure logs generated
# from memory profiler can be filtered by "memory_profiler_logs" prefix.
root_logger = logging.getLogger()
root_logger.handlers[0].setFormatter(logging.Formatter("%(name)s: %(message)s"))
profiler_logstream = memory_profiler.LogFile('memory_profiler_logs', True)

def main(req: func.HttpRequest) -> func.HttpResponse:
    content = profile_get_request('https://microsoft.com')
    return func.HttpResponse(
        f"Microsoft Page Response Size: {len(content)}",
        status_code=200
    )

@memory_profiler.profile(stream=profiler_logstream)
def profile_get_request(url: str):
    response = requests.get(url)
    return response.content
```

### <a name="profile-python-function-app-in-local-development-environment"></a>Profiler une application de fonction Python dans un environnement de développement local

Après avoir apporté toutes les modifications ci-dessus, il existe quelques étapes supplémentaires pour initialiser un environnement virtuel Python pour le runtime Azure Functions.

1. Ouvrez Windows PowerShell ou n’importe quel interpréteur de commandes Linux comme vous le souhaitez.
2. Créez un environnement virtuel Python en `py -m venv .venv` dans Windows ou `python3 -m venv .venv` dans Linux.
3. Activez l’environnement virtuel Python avec `.venv\Scripts\Activate.ps1` dans Windows PowerShell ou `source .venv/bin/activate` dans l’interpréteur de commandes Linux.
4. Restaurer les dépendances Python avec `pip install requirements.txt`
5. Démarrez le runtime Azure Functions localement avec Azure Functions Core Tools `func host start`
6. Envoyez une requête GET à `https://localhost:7071/api/HttpTriggerAsync` ou `https://localhost:7071/api/HttpTriggerSync`.
7. Elle doit afficher un rapport de profilage de la mémoire similaire à la section ci-dessous dans Azure Functions Core Tools.

    ```text
    Filename: <ProjectRoot>\HttpTriggerAsync\__init__.py
    Line #    Mem usage    Increment  Occurences   Line Contents
    ============================================================
        19     45.1 MiB     45.1 MiB           1   @memory_profiler.profile
        20                                         async def get_microsoft_page_async(url: str):
        21     45.1 MiB      0.0 MiB           1       async with aiohttp.ClientSession() as client:
        22     46.6 MiB      1.5 MiB          10           async with client.get(url) as response:
        23     47.6 MiB      1.0 MiB           4               await response.text()
    ```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le développement Python Azure Functions, consultez les ressources suivantes :

* [Guide du développeur Python sur Azure Functions](functions-reference-python.md)
* [Meilleures pratiques pour Azure Functions](functions-best-practices.md)
* [Informations de référence pour les développeurs sur Azure Functions](functions-reference.md)
