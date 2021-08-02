---
title: Développer des extensions de Worker Python pour Azure Functions
description: Découvrez comment créer et publier des extensions de Worker qui vous permettent d’injecter un comportement d’intergiciel dans des fonctions Python s’exécutant sur Azure.
ms.topic: how-to
author: hazhzeng
ms.author: hazeng
ms.date: 6/1/2021
ms.custom: devx-track-python
ms.openlocfilehash: 592562caa57980500d9bbc19b015a98e5604bb7a
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111987832"
---
# <a name="develop-python-worker-extensions-for-azure-functions"></a>Développer des extensions de Worker Python pour Azure Functions

Avec Azure Functions vous intégrez des comportements personnalisés dans le cadre de l’exécution d’une fonction Python. Cette fonctionnalité vous permet de créer une logique métier que les clients peuvent facilement utiliser dans leurs propres applications de fonction. Pour en savoir plus, consultez les [Informations de référence pour les développeurs Python](functions-reference-python.md#python-worker-extensions).

Ce didacticiel vous montre comment effectuer les opérations suivantes : 
> [!div class="checklist"]
> * Créer une extension de Worker Python au niveau de l’application pour Azure Functions. 
> * Consommer votre extension dans une application de la même façon que vos clients. 
> * Empaqueter et publier une extension pour la consommation. 

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vous devez satisfaire aux exigences suivantes :

* [Python 3.6.x ou version ultérieure](https://www.python.org/downloads/release/python-374/). Pour connaître la liste complète des versions de Python prises en charge dans Azure Functions, consultez le [Guide du développeur Python](functions-reference-python.md#python-version).

* [Azure Functions Core Tools](functions-run-local.md#v2), version 3.0.3568 ou ultérieure.

* [Visual Studio Code](https://code.visualstudio.com/) doit être installé sur l’une des [plateformes prises en charge](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

## <a name="create-the-python-worker-extension"></a>Créer l’extension de Worker Python

L’extension que vous créez signale le temps écoulé d’un appel de déclencheur HTTP dans les journaux de la console et dans le corps de la réponse HTTP.

### <a name="folder-structure"></a>Structure de dossiers

Le dossier de votre extension de projet doit suivre la structure suivante :

```
<python_worker_extension_root>/
 | - .venv/
 | - python_worker_extension_timer/
 | | - __init__.py
 | - setup.py
 | - readme.md
```

| Dossier\fichier | Description |
| --- | --- |
| **.venv/** | (Facultatif) Contient un environnement virtuel Python utilisé pour le développement local. |
| **python_worker_extension/** | Contient le code source de l’extension de Worker Python. Ce dossier contient le module Python principal à publier dans PyPI. |
| **setup.py** | Contient les métadonnées du package d’extension de Worker Python. |
| **readme.md** | (Facultatif) Contient l’instruction et l’utilisation de votre extension. Ce contenu est affiché comme descriptif dans la page d’accueil de votre projet PyPI. |

### <a name="configure-project-metadata"></a>Configurer les métadonnées du projet

Tout d’abord, vous devez créer `setup.py`, qui fournit des informations essentielles sur votre package. Pour vérifier que votre extension est distribuée et intégrée correctement dans les applications de fonction de votre client, vérifiez que `'azure-functions >= 1.7.0, < 2.0.0'` figure dans la section `install_requires`.

Dans le modèle suivant, vous devez modifier les champs `author`, `author_email`, `install_requires`, `license`, `packages` et `url` en fonction des besoins.

:::code language="python" source="~/azure-functions-python-worker-extension/setup.py":::

Ensuite, vous implémentez votre code d’extension dans l’étendue du niveau application.

### <a name="implement-the-timer-extension"></a>Implémenter l’extension de minuterie

Ajoutez le code suivant dans `python_worker_extension_timer/__init__.py` pour implémenter l’extension au niveau de l’application :

:::code language="python" source="~/azure-functions-python-worker-extension/python_worker_extension_timer/__init__.py":::

Ce code hérite de [AppExtensionBase](https://github.com/Azure/azure-functions-python-library/blob/dev/azure/functions/extension/app_extension_base.py), afin que l’extension s’applique à chaque fonction de l’application. Vous pouvez également implémenter l’extension sur une étendue au niveau fonction en héritant de [FuncExtensionBase](https://github.com/Azure/azure-functions-python-library/blob/dev/azure/functions/extension/func_extension_base.py).

La méthode `init` est une méthode de classe appelée par le Worker lors de l’importation de la classe d’extension. Vous pouvez effectuer des actions d’initialisation ici pour l’extension. Dans ce cas, un mappage de hachage est initialisé pour l’enregistrement de l’heure de début d’appel de chaque fonction.

La méthode `configure` est destinée aux clients. Dans votre fichier readme, vous pouvez indiquer à vos clients le moment où ils doivent appeler `Extension.configure()`. Ce fichier doit également documenter les fonctionnalités de l’extension, la configuration possible et l’utilisation de votre extension. Dans cet exemple, les clients peuvent choisir si le temps écoulé est signalé dans `HttpResponse`.

La méthode `pre_invocation_app_level` est appelée par le Worker Python avant l’exécution de la fonction. Elle fournit des informations sur la fonction, telles que les arguments et le contexte de la fonction. Dans cet exemple, l’extension journalise un message et enregistre l’heure de début d’un appel en fonction de son invocation_id.

De même, `post_invocation_app_level` est appelé après l’exécution de la fonction. Cet exemple calcule le temps écoulé en fonction de l’heure de début et de l’heure actuelle. La valeur de retour de la réponse HTTP est également remplacée.

## <a name="consume-your-extension-locally"></a>Utiliser votre extension localement

À présent que vous avez créé une extension, vous pouvez l’utiliser dans un projet d’application pour vérifier qu’elle fonctionne comme prévu. 

### <a name="create-an-http-trigger-function"></a>Créer une fonction de déclencheur HTTP

1. Créez un dossier pour votre projet d’application et accédez-y. 

1. À partir de l’interpréteur de commandes approprié, tel que Bash, exécutez la commande suivante pour initialiser le projet :

    ```bash
    func init --python
    ```

1. Utilisez la commande suivante pour créer une nouvelle fonction de déclencheur HTTP qui autorise l’accès anonyme :

    ```bash
    func new -t HttpTrigger -n HttpTrigger -a anonymous
    ```

### <a name="activate-a-virtual-environment"></a>Activer un environnement virtuel

1. Créez un environnement virtuel Python basé sur le système d’exploitation comme suit :

    # <a name="linux"></a>[Linux](#tab/linux)
    ```bash
    python3 -m venv .venv
    ```
    # <a name="windows"></a>[Windows](#tab/windows)
    ```console
    py -m venv .venv
    ``` 
    ---

1. Activez l’environnement virtuel Python selon le système d’exploitation comme suit :
    # <a name="linux"></a>[Linux](#tab/linux)
    ```bash
    source .venv/bin/activate
    ```
    # <a name="windows"></a>[Windows](#tab/windows)
    ```console
    .venv\Scripts\Activate.ps1
    ``` 
    ---

### <a name="configure-the-extension"></a>Configurer l’extension

1. Installez les packages distants de votre projet d’application de fonction à l’aide de la commande suivante : 
    
    ```bash
    pip install -r requirements.txt
    ```

1. Installez l’extension à partir de votre chemin de fichier local, en mode modifiable comme suit :

    ```bash
    pip install -e <PYTHON_WORKER_EXTENSION_ROOT>
    ```

    Dans cet exemple, remplacez `<PYTHON_WORKER_EXTENSION_ROOT>` par l’emplacement du fichier de votre projet d’extension.   
    Lorsqu’un client utilise votre extension, il ajoute à la place l’emplacement de votre package d’extension au fichier requirements.txt, comme dans les exemples suivants :

    # <a name="pypi"></a>[PyPI](#tab/pypi)
    ```python
    # requirements.txt
    python_worker_extension_timer==1.0.0
    ``` 
    # <a name="github"></a>[GitHub](#tab/github)
    
    ```python
    # requirements.txt
    git+https://github.com/Azure-Samples/python-worker-extension-timer@main
    ```
    ---

1. Ouvrez le fichier de projet local.settings.json et ajoutez le champ suivant à `Values` :

    ```json
    "PYTHON_ENABLE_WORKER_EXTENSIONS": "1" 
    ```

    Lors de l’exécution dans Azure, vous ajoutez à la place `PYTHON_ENABLE_WORKER_EXTENSIONS=1` aux [paramètres de l’application dans l’application de fonction](functions-how-to-use-azure-function-app-settings.md#settings).

1. Ajoutez les deux lignes suivantes devant la fonction `main` dans \_\_init.py\_\_ :

    ```python
    from python_worker_extension_timer import TimerExtension
    TimerExtension.configure(append_to_http_response=True)
    ```

    Ce code importe le module `TimerExtension` et définit la valeur de configuration `append_to_http_response`.

### <a name="verify-the-extension"></a>Vérifier l’extension

1. À partir du dossier racine de votre projet d’application, démarrez l’hôte de la fonction à l’aide de `func host start --verbose`. Vous devez voir le point de terminaison local de votre fonction dans la sortie, sous la forme `https://localhost:7071/api/HttpTrigger`.

1. Dans le navigateur, envoyez une requête GET à `https://localhost:7071/api/HttpTrigger`. Vous devez voir une réponse semblable à la suivante, avec les données **TimeElapsed** de la requête ajoutée. 

    <pre>
    This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response. (TimeElapsed: 0.0009996891021728516 sec)
    </pre>

## <a name="publish-your-extension"></a>Publier votre extension

Une fois que vous avez créé et vérifié votre extension, vous devez encore effectuer ces tâches de publication restantes :

> [!div class="checklist"]
> + Choisir une licence.
> + Créer un fichier readme.md et d’autres documentations. 
> + Publier la bibliothèque d’extensions dans un registre de packages Python ou un système de contrôle de version (VCS).

# <a name="pypi"></a>[PyPI](#tab/pypi)

Pour publier votre extension sur PyPI :

1. Exécutez la commande suivante pour installer `twine` et `wheel` dans votre environnement Python par défaut ou dans un environnement virtuel :

    ```bash
    pip install twine wheel
    ```

1. Supprimez l’ancien dossier `dist/` de votre référentiel d’extensions.

1. Exécutez la commande suivante pour générer un nouveau package à l’intérieur de `dist/` :

    ```bash
    python setup.py sdist bdist_wheel
    ```

1. Exécutez la commande suivante pour charger le package sur PyPI :

    ```bash
    twine upload dist/*
    ```

    Vous devrez peut-être fournir les informations d’identification de votre compte PyPI lors du chargement.

Après ces étapes, les clients peuvent utiliser votre extension en incluant le nom de votre package dans leur fichier requirements.txt.

Pour plus d’informations, consultez le [tutoriel officiel Python sur l’empaquetage](https://packaging.python.org/tutorials/packaging-projects/).

# <a name="github"></a>[GitHub](#tab/github)

Vous pouvez également publier le code source de l’extension avec le fichier setup.py dans un dépôt GitHub, comme illustré dans [cet exemple de dépôt](https://github.com/Azure-Samples/python-worker-extension-timer). 

Pour plus d’informations sur la prise en charge du système de contrôle de version dans pip, consultez la [documentation officielle pip sur la prise en charge de VCS](https://pip.pypa.io/en/stable/cli/pip_install/#vcs-support).

---

## <a name="examples"></a>Exemples

+ Vous pouvez afficher l’exemple du projet d’extension terminé à partir de cet article dans l’exemple de dépôt [python_worker_extension_timer](https://github.com/Azure-Samples/python-worker-extension-timer). 

+ L’intégration de OpenCensus est un projet open source qui utilise l’interface d’extension pour intégrer le suivi de télémétrie dans les applications Python sur Azure Functions. Consultez le dépôt [opencensus-python-extensions-azure](https://github.com/census-ecosystem/opencensus-python-extensions-azure/tree/main/extensions/functions) pour vérifier l’implémentation de cette extension de Worker Python.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le développement Python Azure Functions, consultez les ressources suivantes :

* [Guide du développeur Python sur Azure Functions](functions-reference-python.md)
* [Meilleures pratiques pour Azure Functions](functions-best-practices.md)
* [Informations de référence pour les développeurs sur Azure Functions](functions-reference.md)
