---
title: Améliorer les performances de débit des applications Python dans Azure Functions
description: Découvrez comment développer des applications Azure Functions en Python qui sont très performantes et dont la mise à l’échelle s’effectue correctement sous une charge.
ms.topic: article
ms.date: 10/13/2020
ms.custom: devx-track-python
ms.openlocfilehash: e3bbdb8819062d45d071633e0208fb58a003da54
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98786104"
---
# <a name="improve-throughput-performance-of-python-apps-in-azure-functions"></a>Améliorer les performances de débit des applications Python dans Azure Functions

Lors du développement d’applications de fonction à l’aide de Python, vous devez comprendre le fonctionnement des fonctions et la façon dont les performances ont un impact sur la mise à l’échelle de l’application de fonction. Cela est d’autant plus important lors de la conception d’applications hautement performantes. Les principaux facteurs à prendre en compte lors de la conception, de l’écriture et de la configuration de vos applications de fonction sont les configurations de la mise à l’échelle horizontale et des performances de débit.

## <a name="horizontal-scaling"></a>Mise à l’échelle horizontale
Par défaut, Azure Functions surveille automatiquement la charge sur votre application et crée des instances d’hôte supplémentaires pour Python, si nécessaire. Azure Functions utilise des seuils intégrés pour différents types de déclencheurs afin de décider quand ajouter des instances, comme l’ancienneté des messages et la taille de la file d’attente pour QueueTrigger. Ces seuils ne sont pas configurables par l'utilisateur. Pour plus d’informations, consultez [Mise à l’échelle basée sur les événements dans Azure Functions](event-driven-scaling.md).

## <a name="improving-throughput-performance"></a>Améliorer les performances de débit

Les configurations par défaut conviennent à la plupart des applications Azure Functions. Cependant, vous pouvez améliorer les performances de débit de vos applications en utilisant des configurations basées sur votre profil de charge de travail. La première étape consiste à identifier le type de charge de travail que vous exécutez.

| Type de charge de travail | Caractéristiques de l'application de fonction       | Exemples                                          |
| ------------- | ---------------------------------- | ------------------------------------------------- |
| **Limite d’E/S**     | • L’application doit gérer un grand nombre d’appels simultanés.<br>• L’application traite un grand nombre d’événements d’E/S, tels que des appels réseau et des lectures/écritures sur le disque. | • API web.                                          |
| **Limite de l’UC**     | • L’application effectue des calculs de longue durée, tels que le redimensionnement d’images.<br>• L’application procède à une transformation des données.                                                | • Traitement des données.<br>• Inférence par Machine Learning.<br> |

 
Étant donné que les charges de travail des fonctions réelles sont généralement une combinaison d’E/S et d’UC, vous devez profiler l’application sous des charges de production réalistes.


### <a name="performance-specific-configurations"></a>Configurations spécifiques aux performances

Après avoir identifié le profil de charge de travail de votre application de fonction, voici les configurations que vous pouvez utiliser pour améliorer les performances de débit de vos fonctions.

* [Async](#async)
* [Worker multilingue](#use-multiple-language-worker-processes)
* [Nombre maximal de Workers au sein d’un processus Worker de langage](#set-up-max-workers-within-a-language-worker-process)
* [Boucle d’événements](#managing-event-loop)
* [Mise à l’échelle verticale](#vertical-scaling)



#### <a name="async"></a>Async

Étant donné que [Python est un runtime à thread unique](https://wiki.python.org/moin/GlobalInterpreterLock), une instance de l’hôte pour Python ne peut traiter qu’un seul appel de fonction à la fois par défaut. Pour les applications qui traitent un grand nombre d'événements d'E/S et/ou qui sont liées aux E/S, vous pouvez considérablement améliorer les performances en exécutant des fonctions de manière asynchrone.

Pour exécuter une fonction de manière asynchrone, utilisez l’instruction `async def`, qui exécute la fonction avec [asyncio](https://docs.python.org/3/library/asyncio.html) directement :

```python
async def main():
    await some_nonblocking_socket_io_op()
```
Voici un exemple de fonction avec déclencheur HTTP qui utilise le client http [aiohttp](https://pypi.org/project/aiohttp/) :

```python
import aiohttp

import azure.functions as func

async def main(req: func.HttpRequest) -> func.HttpResponse:
    async with aiohttp.ClientSession() as client:
        async with client.get("PUT_YOUR_URL_HERE") as response:
            return func.HttpResponse(await response.text())

    return func.HttpResponse(body='NotFound', status_code=404)
```


Une fonction sans le mot clé `async` est exécutée automatiquement dans un pool de threads ThreadPoolExecutor :

```python
# Runs in an ThreadPoolExecutor threadpool. Number of threads is defined by PYTHON_THREADPOOL_THREAD_COUNT. 
# The example is intended to show how default synchronous function are handled.

def main():
    some_blocking_socket_io()
```

Afin de tirer pleinement parti de l'exécution asynchrone des fonctions, l'opération/la bibliothèque d'E/S utilisée dans votre code doit également être implémentée de manière asynchrone. L'utilisation d'opérations d'E/S synchrones dans des fonctions définies comme asynchrones **peut nuire** aux performances globales. Si la version asynchrone n’est pas implémentée sur les bibliothèques que vous utilisez, vous pouvez toujours tirer parti de l’exécution asynchrone de votre code en [gérant la boucle d’événements](#managing-event-loop) dans votre application. 

Voici quelques exemples de bibliothèques clientes qui ont implémenté un modèle asynchrone :
- [aiohttp](https://pypi.org/project/aiohttp/) : client/serveur HTTP pour asyncio 
- [Streams API](https://docs.python.org/3/library/asyncio-stream.html) : primitives de haut niveau, asynchrones et prêtes à l'emploi destinées à fonctionner avec une connexion réseau
- [Janus Queue](https://pypi.org/project/janus/) : file d'attente thread-safe et compatible asyncio pour Python
- [pyzmq](https://pypi.org/project/pyzmq/) : liaisons Python pour ZeroMQ
 
##### <a name="understanding-async-in-python-worker"></a>Comprendre la fonction asynchrone dans un Worker Python

Lorsque vous définissez `async` devant une signature de fonction, Python marque la fonction comme coroutine. Lors de l’appel de la coroutine, elle peut être planifiée en tant que tâche dans une boucle d’événements. Lorsque vous appelez `await` dans une fonction asynchrone, il enregistre une continuation de la boucle d’événements et autorise la boucle d’événements à traiter la tâche suivante pendant le temps d’attente.

Dans notre Worker Python, le Worker partage la boucle d’événements avec la fonction `async` du client et il est capable de traiter plusieurs demandes simultanément. Nous encourageons vivement nos clients à utiliser des bibliothèques compatibles avec asyncio (par exemple, [aiohttp](https://pypi.org/project/aiohttp/), [pyzmq](https://pypi.org/project/pyzmq/)). L’application de ces recommandations augmentera considérablement le débit de votre fonction par rapport à ces bibliothèques implémentées de façon synchrone.

> [!NOTE]
>  Si votre fonction est déclarée comme `async` sans `await` dans son implémentation, les performances de votre fonction seront gravement pénalisées puisque la boucle d’événement sera bloquée, ce qui empêchera le Worker Python de traiter des requêtes concurrentes.

#### <a name="use-multiple-language-worker-processes"></a>Utiliser plusieurs processus Worker de langage

Par défaut, chaque instance d’hôte Functions a un seul processus Worker de langage. Vous pouvez augmenter le nombre de processus Worker par hôte (jusqu’à 10) à l’aide du paramètre d’application [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count). Azure Functions essaie ensuite de distribuer uniformément les appels de fonction simultanés à ces différents Workers.

Pour les applications liées au processeur, vous devez définir le nombre de Workers de langage de manière à ce qu'il soit supérieur ou égal au nombre de cœurs disponibles par application de fonction. Pour en savoir plus, consultez [Références SKU des instances disponibles](functions-premium-plan.md#available-instance-skus). 

Les applications liées aux E/S peuvent également bénéficier de l'augmentation du nombre de processus Worker au-delà du nombre de cœurs disponibles. Gardez à l'esprit que la définition d'un nombre de Workers trop élevé peut avoir un impact sur les performances globales en raison du nombre accru de changements de contexte requis. 

FUNCTIONS_WORKER_PROCESS_COUNT s’applique à chaque hôte créé par Functions lors du scale-out de votre application pour répondre à la demande.

#### <a name="set-up-max-workers-within-a-language-worker-process"></a>Configurer le nombre maximal de Workers au sein d’un processus Worker de langage

Comme mentionné dans la section [Async](#understanding-async-in-python-worker), le Worker du langage Python traite les fonctions et les [coroutines](https://docs.python.org/3/library/asyncio-task.html#coroutines) différemment. Une coroutine est exécutée dans la même boucle d’événements que le Worker de langage. En revanche, un appel de fonction est exécuté dans un [ThreadPoolExecutor](https://docs.python.org/3/library/concurrent.futures.html#threadpoolexecutor), qui est géré par le Worker de langage, en tant que thread.

Vous pouvez définir la valeur du nombre maximum de Workers autorisés pour l’exécution des fonctions de synchronisation en utilisant le paramètre d’application [PYTHON_THREADPOOL_THREAD_COUNT](functions-app-settings.md#python_threadpool_thread_count). Cette valeur définit l’argument `max_worker` de l’objet ThreadPoolExecutor, ce qui permet à Python d’utiliser un pool d’au plus `max_worker` threads pour exécuter des appels de manière asynchrone. `PYTHON_THREADPOOL_THREAD_COUNT` s’applique à chaque Worker créé par l’hôte Functions, et Python décide quand créer un nouveau thread ou réutiliser le thread inactif existant. Pour les anciennes versions de Python (c’est-à-dire, `3.8`, `3.7` et `3.6`), la valeur de `max_worker` est définie sur 1. Pour la version `3.9` de Python, `max_worker` est défini sur `None`.

Pour les applications liées à l’UC, vous devez conserver le paramètre sur un nombre faible, en commençant à 1 et en l’augmentant à mesure que vous expérimentez avec votre charge de travail. Cette suggestion vise à réduire le temps passé sur les changements de contexte et à permettre aux tâches liées à l’UC de se terminer.

Pour les applications liées aux E/S, vous devriez constater des gains substantiels en augmentant le nombre de threads travaillant sur chaque appel. La recommandation consiste à commencer avec la valeur par défaut de Python (le nombre de cœurs + 4), puis d’ajuster en fonction des valeurs de débit que vous voyez.

Pour les applications à charges de travail mixtes, vous devez équilibrer les configurations `FUNCTIONS_WORKER_PROCESS_COUNT` et `PYTHON_THREADPOOL_THREAD_COUNT` pour maximiser le débit. Pour comprendre à quoi vos applications de fonction consacrent le plus de temps, nous vous recommandons de les profiler et de définir les valeurs en fonction du comportement qu’elles présentent. Reportez-vous également à cette [section](#use-multiple-language-worker-processes) pour en savoir plus sur les paramètres d’application FUNCTIONS_WORKER_PROCESS_COUNT.

> [!NOTE]
>  Bien que ces recommandations s’appliquent aux fonctions déclenchées via HTTP et à celles non déclenchées via HTTP, vous devrez peut-être ajuster d’autres configurations spécifiques au déclencheur pour les fonctions non déclenchées via HTTP afin d’obtenir les performances attendues sur vos applications de fonction. Pour plus d’informations à ce sujet, reportez-vous à cet [article](functions-best-practices.md).


#### <a name="managing-event-loop"></a>Gestion de la boucle d’événements

Vous devez utiliser des bibliothèques tierces compatibles avec asyncio. Si aucune bibliothèque tierce ne répond à vos besoins, vous pouvez également gérer les boucles d’événements dans Azure Functions. La gestion des boucles d’événements vous offre plus de souplesse dans la gestion des ressources de calcul et permet également d’inclure des bibliothèques d’E/S synchrones dans des coroutines.

Il existe de nombreux documents officiels de Python qui traitent [des coroutines et des tâches](https://docs.python.org/3/library/asyncio-task.html) et de la [boucle d’événements](https://docs.python.org/3.8/library/asyncio-eventloop.html) en utilisant la bibliothèque **asyncio** intégrée.

Prenez la bibliothèque de [requêtes](https://github.com/psf/requests) suivante comme exemple : cet extrait de code utilise la bibliothèque **asyncio** pour inclure la méthode `requests.get()` dans une coroutine, en exécutant simultanément plusieurs requêtes web sur SAMPLE_URL.


```python
import asyncio
import json
import logging

import azure.functions as func
from time import time
from requests import get, Response


async def invoke_get_request(eventloop: asyncio.AbstractEventLoop) -> Response:
    # Wrap requests.get function into a coroutine
    single_result = await eventloop.run_in_executor(
        None,  # using the default executor
        get,  # each task call invoke_get_request
        'SAMPLE_URL'  # the url to be passed into the requests.get function
    )
    return single_result

async def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    eventloop = asyncio.get_event_loop()

    # Create 10 tasks for requests.get synchronous call
    tasks = [
        asyncio.create_task(
            invoke_get_request(eventloop)
        ) for _ in range(10)
    ]

    done_tasks, _ = await asyncio.wait(tasks)
    status_codes = [d.result().status_code for d in done_tasks]

    return func.HttpResponse(body=json.dumps(status_codes),
                             mimetype='application/json')
```
#### <a name="vertical-scaling"></a>Mise à l’échelle verticale
Pour un plus grand nombre d’unités de traitement, en particulier en cas de fonctionnement lié à l’UC, vous pourriez obtenir ce résultat en effectuant une mise à niveau vers le plan Premium avec des spécifications plus élevées. Avec des unités de traitement plus importantes, vous pouvez ajuster le nombre de processus Worker en fonction du nombre de cœurs disponibles et atteindre un degré de parallélisme plus élevé. 

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le développement Python Azure Functions, consultez les ressources suivantes :

* [Guide du développeur Python sur Azure Functions](functions-reference-python.md)
* [Meilleures pratiques pour Azure Functions](functions-best-practices.md)
* [Informations de référence pour les développeurs sur Azure Functions](functions-reference.md)

