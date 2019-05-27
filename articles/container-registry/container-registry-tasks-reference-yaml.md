---
title: Référence des tâches Azure Container Registry - YAML
description: Référence pour la définition de tâches dans YAML pour ACR Tasks, y compris les propriétés de tâche, les types d’étapes, les propriétés d’étape et les variables intégrées.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 03/28/2019
ms.author: danlep
ms.openlocfilehash: bdf88657c11bdb5ab5bcde97c155780328065c7e
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2019
ms.locfileid: "65954477"
---
# <a name="acr-tasks-reference-yaml"></a>Référence ACR Tasks : YAML

La définition de tâche en plusieurs étapes dans ACR Tasks fournit une primitive de calcul orientée conteneur axée sur la génération, le test et la mise à jour corrective des conteneurs. Cet article traite les commandes, paramètres, propriétés et la syntaxe pour les fichiers YAML qui définissent vos tâches de plusieurs étapes.

Cet article contient les informations de référence pour la création des fichiers YAML de tâche en plusieurs étapes pour ACR Tasks. Pour une présentation d’ACR Tasks, consultez la [vue d’ensemble d’ACR Tasks](container-registry-tasks-overview.md).

## <a name="acr-taskyaml-file-format"></a>format de fichier acr-task.yaml

ACR Tasks prend en charge la déclaration de tâche en plusieurs étapes dans la syntaxe YAML standard. Vous définissez les étapes d’une tâche dans un fichier YAML. Vous pouvez ensuite exécuter la tâche manuellement en transmettant le fichier à la [acr az exécuter] [ az-acr-run] commande. Ou, utilisez le fichier pour créer une tâche avec [az acr tâche créer] [ az-acr-task-create] qui est déclenché automatiquement sur une Git commit ou base image mise à jour. Bien que cet article fasse référence à `acr-task.yaml` comme fichier qui contient les étapes, ACR Tasks accepte n’importe quel nom de fichier valide avec une [extension prise en charge](#supported-task-filename-extensions).

Les primitives `acr-task.yaml` de niveau supérieur sont des **propriétés de tâche**, des **types d’étape** et des **propriétés d’étape** :

* Les [propriétés de tâche](#task-properties) s’appliquent à toutes les étapes d’exécution de la tâche. Il existe plusieurs propriétés de tâche globale, y compris :
  * `version`
  * `stepTimeout`
  * `workingDirectory`
* Les [types d’étape de tâche](#task-step-types) représentent les types d’actions qui peuvent être effectuées dans une tâche. Il existe trois types d’étapes :
  * `build`
  * `push`
  * `cmd`
* Les [propriétés d’étape de tâche](#task-step-properties) sont des paramètres qui s’appliquent à une étape individuelle. Il existe plusieurs propriétés d’étape, notamment :
  * `startDelay`
  * `timeout`
  * `when`
  * et plus encore.

Le format de base d’un fichier `acr-task.yaml`, y compris certaines propriétés communes d’étape, suit. Bien qu’il ne s’agisse pas d’une représentation exhaustive de toutes les propriétés d’étape disponibles ou de l’utilisation des types d’étape, il fournit une rapide vue d’ensemble pour le format de fichier de base.

```yml
version: # acr-task.yaml format version.
stepTimeout: # Seconds each step may take.
steps: # A collection of image or container actions.
  - build: # Equivalent to "docker build," but in a multi-tenant environment
  - push: # Push a newly built or retagged image to a registry.
    when: # Step property that defines either parallel or dependent step execution.
  - cmd: # Executes a container, supports specifying an [ENTRYPOINT] and parameters.
    startDelay: # Step property that specifies the number of seconds to wait before starting execution.
```

### <a name="supported-task-filename-extensions"></a>Extensions de nom de fichier de tâche prises en charge

ACR Tasks a plusieurs extensions de nom de fichier réservées, y compris `.yaml`, qui sera traitée comme un fichier de tâche. N’importe quelle extension *ne figurant pas* dans la liste suivante est considérée par ACR Tasks comme un fichier Dockerfile : .yaml, .yml, .toml, .json, .sh, .bash, .zsh, .ps1, .ps, .cmd, .bat, .ts, .js, .php, .py, .rb, .lua

YAML est le seul format de fichier actuellement pris en charge par ACR Tasks. Les autres extensions de nom de fichier sont réservées pour une éventuelle prise en charge future.

## <a name="run-the-sample-tasks"></a>Exécuter les exemples de tâche

Plusieurs exemples de fichiers de tâche sont référencés dans les sections suivantes de cet article. Les exemples de tâche se trouvent dans un référentiel GitHub public, [Azure-Samples/acr-tasks][acr-tasks]. Vous pouvez les exécuter via l’interface de ligne de commande Azure avec la commande [az acr run][az-acr-run]. Les exemples de commandes ressembles à ceci :

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

La mise en forme des exemples de commandes part du principe que vous avez configuré un registre par défaut dans l’interface de ligne de commande Azure. Le paramètre `--registry` est donc omis. Pour configurer un registre par défaut, utilisez la commande [az configure][az-configure] avec le paramètre `--defaults`, qui accepte une valeur `acr=REGISTRY_NAME`.

Par exemple, pour configurer l’interface de ligne de commande Azure avec un registre par défaut nommé « myregistry », procédez comme suit :

```azurecli
az configure --defaults acr=myregistry
```

## <a name="task-properties"></a>Propriétés de tâche

Propriétés de la tâche apparaissent généralement dans la partie supérieure d’un `acr-task.yaml` de fichiers, et sont des propriétés globales qui s’appliquent lors de l’exécution complète des étapes de tâches. Certaines de ces propriétés globales peuvent être remplacées au sein d’une étape individuelle.

| Propriété | Type | Facultatif | Description | Remplacement pris en charge | Valeur par défaut |
| -------- | ---- | -------- | ----------- | ------------------ | ------------- |
| `version` | string | Oui | Version du fichier `acr-task.yaml` analysé par le service ACR Tasks. Si ACR Tasks s’efforce de maintenir la compatibilité descendante, cette valeur permet à ACR Tasks d’assurer la compatibilité au sein d’une version définie. Si non spécifié, par défaut est la dernière version. | Non  | Aucun |
| `stepTimeout` | int (secondes) | Oui | Nombre maximal de secondes pendant lesquelles une étape peut être exécutée. Si la propriété est spécifiée sur une tâche, il définit la valeur par défaut `timeout` propriété de toutes les étapes. Si le `timeout` propriété est spécifiée sur une étape, il substitue à la propriété fournie par la tâche. | Oui | 600 (10 minutes) |
| `workingDirectory` | string | Oui | Le répertoire de travail du conteneur pendant l’exécution. Si la propriété est spécifiée sur une tâche, il définit la valeur par défaut `workingDirectory` propriété de toutes les étapes. S’il est spécifié sur une étape, elle remplace la propriété fournie par la tâche. | Oui | `$HOME` |
| `env` | [chaîne, chaîne,...] | Oui |  Tableau de chaînes dans `key=value` format qui définissent les variables d’environnement pour la tâche. Si la propriété est spécifiée sur une tâche, il définit la valeur par défaut `env` propriété de toutes les étapes. S’il est spécifié sur une étape, il remplace les variables d’environnement hérités de la tâche. | Aucun |
| `secrets` | [secret, secret,...] | Oui | Tableau de [secret](#secret) objets. | Aucun |
| `networks` | [network, réseau,...] | Oui | Tableau de [réseau](#network) objets. | Aucun |

### <a name="secret"></a>secret

L’objet secret a les propriétés suivantes.

| Propriété | Type | Facultatif | Description | Valeur par défaut |
| -------- | ---- | -------- | ----------- | ------- |
| `id` | string | Non  | L’identificateur de la clé secrète. | Aucun |
| `keyvault` | string | Oui | L’URL de Secret de coffre de clés Azure. | Aucun |
| `clientID` | string | Oui | L’ID client de l’utilisateur affecté identité managée pour ressources Azure. | Aucun |

### <a name="network"></a>réseau

L’objet de réseau a les propriétés suivantes.

| Propriété | Type | Facultatif | Description | Valeur par défaut |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | string | Non  | Nom du réseau. | Aucun |
| `driver` | string | Oui | Le pilote pour gérer le réseau. | Aucun |
| `ipv6` | bool | Oui | Si la mise en réseau IPv6 est activé. | `false` |
| `skipCreation` | bool | Oui | Indique s’il faut ou non ignorer la création du réseau. | `false` |
| `isDefault` | bool | Oui | Si le réseau est un réseau par défaut fourni avec Azure Container Registry | `false` |

## <a name="task-step-types"></a>Types d’étape de tâche

ACR Tasks prend en charge trois types d’étape. Chaque type d’étape prend en charge plusieurs propriétés, détaillées dans la section correspondante.

| Type d’étape | Description |
| --------- | ----------- |
| [`build`](#build) | Génère une image conteneur à l’aide de la syntaxe `docker build` standard. |
| [`push`](#push) | Exécute un `docker push` d’images tout juste générées ou ré-étiquetées dans un registre de conteneurs. Azure Container Registry, d’autres registres privés et le hub Docker public sont pris en charge. |
| [`cmd`](#cmd) | Exécute un conteneur en tant que commande, avec les paramètres transmis à l’élément `[ENTRYPOINT]` du conteneur. Le `cmd` type d’étape prend en charge les paramètres comme `env`, `detach`et autres familier `docker run` options de commande, l’activation des tests d’unité et fonctionnelle avec l’exécution simultanée de conteneur. |

## <a name="build"></a>build

Générez une image conteneur. Le type d’étape `build` représente un moyen sécurisé et mutualisé d’exécuter `docker build` dans le cloud en tant que primitive de première classe.

### <a name="syntax-build"></a>Syntaxe : build

```yml
version: v1.0.0
steps:
  - [build]: -t [imageName]:[tag] -f [Dockerfile] [context]
    [property]: [value]
```

Le type d’étape `build` prend en charge les paramètres du tableau suivant. Le type d'étape `build` prend également en charge toutes les options de build de la commande [docker build](https://docs.docker.com/engine/reference/commandline/build/), comme `--build-arg` pour définir des variables de build.

| Paramètre | Description | Facultatif |
| --------- | ----------- | :-------: |
| `-t` &#124; `--image` | Définit l’élément `image:tag` complet de l’image générée.<br /><br />Comme les images peuvent être utilisées pour les validations de tâche internes, telles que les tests fonctionnels, certaines images n’ont pas besoin de `push` vers un registre. Toutefois, l’instanciation d’une image dans une exécution de tâche, l’image a besoin d’un nom comme référence.<br /><br />Contrairement à `az acr build`, ACR tâches en cours d’exécution ne fournit pas de comportement de push par défaut. Avec ACR Tasks, le scénario par défaut suppose la possibilité de générer, valider, puis envoyer une image. Consultez [push](#push) pour savoir comment envoyer (push) les images générées. | Oui |
| `-f` &#124; `--file` | Spécifie le fichier Docker transmis à `docker build`. S’il n’est pas indiqué, le fichier Docker par défaut à la racine du contexte est supposé. Pour spécifier un fichier Dockerfile, transmettez le nom de fichier relatif à la racine du contexte. | Oui |
| `context` | Répertoire racine transmis à `docker build`. Le répertoire racine de chaque tâche est défini pour un [répertoire de travail](#task-step-properties) partagé et inclut la racine du répertoire cloné Git associé. | Non  |

### <a name="properties-build"></a>Propriétés : build

Le type d’étape `build` prend en charge les propriétés suivantes : Rechercher les détails de ces propriétés dans le [propriétés de l’étape tâche](#task-step-properties) section de cet article.

| | | |
| -------- | ---- | -------- |
| `detach` | bool | Facultatif |
| `disableWorkingDirectoryOverride` | bool | Facultatif |
| `entryPoint` | string | Facultatif |
| `env` | [chaîne, chaîne,...] | Facultatif |
| `expose` | [chaîne, chaîne,...] | Facultatif |
| `id` | string | Facultatif |
| `ignoreErrors` | bool | Facultatif |
| `isolation` | string | Facultatif |
| `keep` | bool | Facultatif |
| `network` | objet | Facultatif |
| `ports` | [chaîne, chaîne,...] | Facultatif |
| `pull` | bool | Facultatif |
| `repeat` | int | Facultatif |
| `retries` | int | Facultatif |
| `retryDelay` | int (secondes) | Facultatif |
| `secret` | objet | Facultatif |
| `startDelay` | int (secondes) | Facultatif |
| `timeout` | int (secondes) | Facultatif |
| `when` | [chaîne, chaîne,...] | Facultatif |
| `workingDirectory` | string | Facultatif |

### <a name="examples-build"></a>Exemples : build

#### <a name="build-image---context-in-root"></a>Générer une image : contexte dans la racine

```azurecli
az acr run -f build-hello-world.yaml https://github.com/AzureCR/acr-tasks-sample.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-hello-world.yaml)]

#### <a name="build-image---context-in-subdirectory"></a>Générer une image : contexte dans un sous-répertoire

```yml
version: v1.0.0
steps:
  - build: -t {{.Run.Registry}}/hello-world -f hello-world.dockerfile ./subDirectory
```

## <a name="push"></a>push

Envoie (push) des images générées ou ré-étiquetées vers un registre de conteneurs. Prend en charge l’envoi (push) vers des registres privés comme Azure Container Registry, ou vers le hub Docker public.

### <a name="syntax-push"></a>Syntaxe : push

Le type d’étape `push` prend en charge une collection d’images. La syntaxe de collection YAML prend en charge les formats imbriqués et inline. L’envoi (push) d’une image unique est généralement représenté à l’aide de la syntaxe inline :

```yml
version: v1.0.0
steps:
  # Inline YAML collection syntax
  - push: ["{{.Run.Registry}}/hello-world:{{.Run.ID}}"]
```

Pour une meilleure lisibilité, utilisez la syntaxe imbriquée lors de l’envoi de plusieurs images :

```yml
version: v1.0.0
steps:
  # Nested YAML collection syntax
  - push:
    - {{.Run.Registry}}/hello-world:{{.Run.ID}}
    - {{.Run.Registry}}/hello-world:latest
```

### <a name="properties-push"></a>Propriétés : push

Le type d’étape `push` prend en charge les propriétés suivantes : Rechercher les détails de ces propriétés dans le [propriétés de l’étape tâche](#task-step-properties) section de cet article.

| | | |
| -------- | ---- | -------- |
| `env` | [chaîne, chaîne,...] | Facultatif |
| `id` | string | Facultatif |
| `ignoreErrors` | bool | Facultatif |
| `startDelay` | int (secondes) | Facultatif |
| `timeout` | int (secondes) | Facultatif |
| `when` | [chaîne, chaîne,...] | Facultatif |

### <a name="examples-push"></a>Exemples : push

#### <a name="push-multiple-images"></a>Envoyer (push) plusieurs images

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-push-hello-world.yaml)]

#### <a name="build-push-and-run"></a>Générer, envoyer (push) et exécuter

```azurecli
az acr run -f build-run-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-run-hello-world.yaml)]

## <a name="cmd"></a>cmd

Le type d’étape `cmd` exécute un conteneur.

### <a name="syntax-cmd"></a>Syntaxe : cmd

```yml
version: v1.0.0
steps:
  - [cmd]: [containerImage]:[tag (optional)] [cmdParameters to the image]
```

### <a name="properties-cmd"></a>Propriétés : cmd

Le type d’étape `cmd` prend en charge les propriétés suivantes :

| | | |
| -------- | ---- | -------- |
| `detach` | bool | Facultatif |
| `disableWorkingDirectoryOverride` | bool | Facultatif |
| `entryPoint` | string | Facultatif |
| `env` | [chaîne, chaîne,...] | Facultatif |
| `expose` | [chaîne, chaîne,...] | Facultatif |
| `id` | string | Facultatif |
| `ignoreErrors` | bool | Facultatif |
| `isolation` | string | Facultatif |
| `keep` | bool | Facultatif |
| `network` | objet | Facultatif |
| `ports` | [chaîne, chaîne,...] | Facultatif |
| `pull` | bool | Facultatif |
| `repeat` | int | Facultatif |
| `retries` | int | Facultatif |
| `retryDelay` | int (secondes) | Facultatif |
| `secret` | objet | Facultatif |
| `startDelay` | int (secondes) | Facultatif |
| `timeout` | int (secondes) | Facultatif |
| `when` | [chaîne, chaîne,...] | Facultatif |
| `workingDirectory` | string | Facultatif |

Vous trouverez les détails de ces propriétés dans la section [Propriétés d’étape de tâche](#task-step-properties) de cet article.

### <a name="examples-cmd"></a>Exemples : cmd

#### <a name="run-hello-world-image"></a>Exécuter l’image hello-world

Cette commande exécute le fichier de tâche `hello-world.yaml`, qui fait référence à l’image [hello-world](https://hub.docker.com/_/hello-world/) sur le hub Docker.

```azurecli
az acr run -f hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.yaml -->
[!code-yml[task](~/acr-tasks/hello-world.yaml)]

#### <a name="run-bash-image-and-echo-hello-world"></a>Exécuter l’image bash et renvoyer « hello world »

Cette commande exécute le fichier de tâche `bash-echo.yaml`, qui fait référence à l’image [bash](https://hub.docker.com/_/bash/) sur le hub Docker.

```azurecli
az acr run -f bash-echo.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo.yaml)]

#### <a name="run-specific-bash-image-tag"></a>Exécuter une balise d’image bash spécifique

Pour exécuter une version d’image spécifique, spécifiez la balise dans `cmd`.

Cette commande exécute le fichier de tâche `bash-echo-3.yaml`, qui fait référence à l’image [bash:3.0](https://hub.docker.com/_/bash/) sur le hub Docker.

```azurecli
az acr run -f bash-echo-3.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo-3.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo-3.yaml)]

#### <a name="run-custom-images"></a>Exécuter des images personnalisées

Le type d’étape `cmd` fait référence à des images à l’aide du format `docker run` standard. Les images non précédées d’un registre sont supposées provenir de docker.io. L’exemple précédent peut également être représenté comme suit :

```yml
version: v1.0.0
steps:
  - cmd: docker.io/bash:3.0 echo hello world
```

À l’aide de la norme `docker run` convention de référence, l’image `cmd` peuvent exécuter des images à partir de n’importe quel Registre privé ou le Hub Docker public. Si vous faites référence à des images du registre dans lequel ACR Tasks s’exécute, vous n’avez pas besoin de spécifier des informations d’identification de registre.

* Exécuter une image à partir d’un Registre de conteneurs Azure

    Remplacez `[myregistry]` par le nom de votre registre :

    ```yml
    version: v1.0.0
    steps:
        - cmd: [myregistry].azurecr.io/bash:3.0 echo hello world
    ```

* Généraliser la référence de registre avec une variable d’exécution

    Au lieu de coder de manière irréversible le nom de votre registre dans un fichier `acr-task.yaml`, vous pouvez rendre plus portable en utilisant une [variable d’exécution](#run-variables). La variable `Run.Registry` est développée lors de l’exécution pour donner le nom du registre dans lequel la tâche s’exécute.

    Pour généraliser la tâche précédente de sorte qu’elle fonctionne dans n’importe quel registre de conteneurs Azure, vous devez référencer la variable [Run.Registry](#runregistry) dans le nom de l’image :

    ```yml
    version: v1.0.0
    steps:
      - cmd: {{.Run.Registry}}/bash:3.0 echo hello world
    ```

## <a name="task-step-properties"></a>Propriétés d’étape de tâche

Chaque type d’étape prend en charge plusieurs propriétés appropriées pour son type. Le tableau suivant définit toutes les propriétés d’étape disponibles. Certains types d’étape ne prennent pas en charge toutes les propriétés. Pour connaître les propriétés disponibles pour chaque type d’étape, consultez les sections de référence sur les types d’étape [cmd](#cmd), [build](#build) et [push](#push).

| Propriété | Type | Facultatif | Description | Valeur par défaut |
| -------- | ---- | -------- | ----------- | ------- |
| `detach` | bool | Oui | Indique si le conteneur doit être détaché lors de l’exécution. | `false` |
| `disableWorkingDirectoryOverride` | bool | Oui | S’il faut désactiver `workingDirectory` substituer des fonctionnalités. Utilisez-le conjointement avec `workingDirectory` contrôle total sur le répertoire de travail du conteneur. | `false` |
| `entryPoint` | string | Oui | Remplace le `[ENTRYPOINT]` du conteneur d’une étape. | Aucun |
| `env` | [chaîne, chaîne,...] | Oui | Tableau de chaînes au format `key=value` qui définissent les variables d’environnement pour l’étape. | Aucun |
| `expose` | [chaîne, chaîne,...] | Oui | Tableau des ports qui sont exposées à partir du conteneur. |  Aucun |
| [`id`](#example-id) | string | Oui | Identifie l’étape de la tâche de manière unique. Les autres étapes de la tâche peuvent faire référence à l’élément `id` d’une étape (par exemple, pour la vérification des dépendances avec `when`).<br /><br />L’élément `id` est également le nom du conteneur en cours d’exécution. Les processus qui s’exécutent dans d’autres conteneurs de la tâche peuvent faire référence à l’élément `id` avec son nom d’hôte DNS ou pour y accéder avec les journaux d’activité Docker [id], par exemple. | `acb_step_%d`, où `%d` est l’index de base 0 de l’étape de haut en bas dans le fichier YAML |
| `ignoreErrors` | bool | Oui | Si vous souhaitez marquer l’étape comme réussie, quel que soit si une erreur s’est produite pendant l’exécution du conteneur. | `false` |
| `isolation` | string | Oui | Le niveau d’isolation du conteneur. | `default` |
| `keep` | bool | Oui | Indique si le conteneur de l’étape doit être conservé après l’exécution. | `false` |
| `network` | objet | Oui | Identifie un réseau dans lequel le conteneur s’exécute. | Aucun |
| `ports` | [chaîne, chaîne,...] | Oui | Tableau des ports qui sont publiés à partir du conteneur à l’hôte. |  Aucun |
| `pull` | bool | Oui | Indique s’il faut ou non forcer une opération d’extraction du conteneur avant son exécution pour éviter tout comportement de mise en cache. | `false` |
| `privileged` | bool | Oui | Indique s’il faut ou non exécuter le conteneur en mode privilégié. | `false` |
| `repeat` | int | Oui | Le nombre de tentatives de répéter l’exécution d’un conteneur. | 0 |
| `retries` | int | Oui | Le nombre de tentatives à effectuer si un conteneur elle-même échoue son exécution. Une nouvelle tentative est uniquement effectuée si le code de sortie d’un conteneur est différente de zéro. | 0 |
| `retryDelay` | int (secondes) | Oui | Le délai en secondes entre les tentatives de l’exécution d’un conteneur. | 0 |
| `secret` | objet | Oui | Identifie un secret Azure Key Vault ou une identité gérée pour les ressources Azure. | Aucun |
| `startDelay` | int (secondes) | Oui | Nombre de secondes de délai d’exécution d’un conteneur. | 0 |
| `timeout` | int (secondes) | Oui | Nombre maximal de secondes pendant lesquelles une étape peut s’exécuter avant d’être terminée. | 600 |
| [`when`](#example-when) | [chaîne, chaîne,...] | Oui | Configure les dépendances d’une étape sur une ou plusieurs autres étapes au sein de la tâche. | Aucun |
| `user` | string | Oui | Le nom d’utilisateur ou un UID d’un conteneur | Aucun |
| `workingDirectory` | string | Oui | Définit le répertoire de travail pour une étape. Par défaut, ACR Tasks crée un répertoire racine comme répertoire de travail. Toutefois, si votre build dispose de plusieurs étapes, les étapes précédentes peuvent partager des artefacts avec étapes suivantes en spécifiant le même répertoire de travail. | `$HOME` |

### <a name="examples-task-step-properties"></a>Exemples : Propriétés d’étape de tâche

#### <a name="example-id"></a>Exemple : id

Générez deux images, en instanciant une image de test fonctionnel. Chaque étape est identifiée par un élément `id` unique mentionné dans d’autres étapes de la tâche dans leur propriété `when`.

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

#### <a name="example-when"></a>Exemple : when

La propriété `when` spécifie les dépendances d’une étape sur d’autres étapes au sein de la tâche. Elle prend en charge deux valeurs de paramètre :

* `when: ["-"]` : indique l’absence de dépendance sur d’autres étapes. Une étape indiquant `when: ["-"]` est exécutée immédiatement et permet l’exécution simultanée des étapes.
* `when: ["id1", "id2"]` : indique que l’étape dépend des étapes avec `id` « id1 » et `id` « id2 ». Cette étape ne sera pas être exécutée tant que les étapes « id1 » et « id2 » ne seront pas terminées.

Si `when` n’est pas spécifié dans une étape, cette étape dépend de l’achèvement de l’étape précédente dans le fichier `acr-task.yaml`.

Exécution séquentielle des étapes sans `when` :

```azurecli
az acr run -f when-sequential-default.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-default.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-default.yaml)]

Exécution séquentielle des étapes avec `when` :

```azurecli
az acr run -f when-sequential-id.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-id.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-id.yaml)]

Génération d’images parallèles :

```azurecli
az acr run -f when-parallel.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel.yaml)]

Génération d’images en parallèle et test des dépendances :

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

## <a name="run-variables"></a>Exécuter des variables

ACR Tasks inclut un ensemble de variables par défaut qui sont disponibles pour les étapes de tâche lors de l’exécution. Ces variables sont accessibles à l’aide du format `{{.Run.VariableName}}`, où `VariableName` est une des valeurs suivantes :

* `Run.ID`
* `Run.Registry`
* `Run.Date`
* `Run.Commit`
* `Run.Branch`

### <a name="runid"></a>Run.ID

Chaque exécution, via `az acr run` ou lancée par un déclencheur, des tâches créées avec `az acr task create` a un ID unique. L’ID représente l’exécution en cours.

Généralement utilisé pour un balisage qui identifie de façon unique une image :

```yml
version: v1.0.0
steps:
    - build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} .
```

### <a name="runregistry"></a>Run.Registry

Nom complet du serveur du registre. Généralement utilisé pour référencer de façon générique le registre dans lequel la tâche est en cours d’exécution.

```yml
version: v1.0.0
steps:
  - build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} .
```

### <a name="rundate"></a>Run.Date

Heure UTC de début de l’exécution.

### <a name="runcommit"></a>Run.Commit

Pour une tâche déclenchée par une validation à un référentiel GitHub, l’identificateur de la validation.

### <a name="runbranch"></a>Run.Branch

Pour une tâche déclenchée par une validation à un référentiel GitHub, le nom de branche.

## <a name="next-steps"></a>Étapes suivantes

Pour une vue d’ensemble des tâches en plusieurs étapes, consultez la rubrique [Exécuter des tâches de génération, de test et de correction en plusieurs étapes dans les ACR Tasks](container-registry-tasks-multi-step.md).

Pour les builds à une seule étape, consultez la [vue d’ensemble d’ACR Tasks](container-registry-tasks-overview.md).

<!-- IMAGES -->

<!-- LINKS - External -->
[acr-tasks]: https://github.com/Azure-Samples/acr-tasks

<!-- LINKS - Internal -->
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-configure]: /cli/azure/reference-index#az-configure
