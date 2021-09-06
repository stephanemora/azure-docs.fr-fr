---
title: Référence Azure Functions Core Tools
description: Documentation de référence qui prend en charge les Azure Functions Core Tools (func.exe).
ms.topic: reference
ms.date: 07/13/2021
ms.openlocfilehash: 90d4a9575c2fe6bb2dc4fbd18132e3bc21e4a07c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122532897"
---
# <a name="azure-functions-core-tools-reference"></a>Référence Azure Functions Core Tools

Cet article fournit une documentation de référence pour le Azure Functions Core Tools, qui vous permet de développer, de gérer et de déployer des projets Azure Functions à partir de votre ordinateur local. Pour plus d’informations sur l’utilisation de Core Tools, consultez [Utiliser Azure Functions Core Tools](functions-run-local.md). 

Les commandes des outils principaux sont organisées dans les contextes suivants, chacun fournissant un ensemble unique d’actions.

| Contexte de commande | Description |
| ----- | ----- |
| [`func`](#func-init) | Commandes utilisées pour créer et exécuter des fonctions sur votre ordinateur local. |
| [`func azure`](#func-azure-functionapp-fetch-app-settings) | Commandes permettant d’utiliser des ressources Azure, notamment la publication. |
| [`func durable`](#func-durable-delete-task-hub)    | Commandes permettant d’utiliser [Durable Functions](./durable/durable-functions-overview.md). |
| [`func extensions`](#func-extensions-install) | Commandes pour l’installation et la gestion des extensions. |
| [`func kubernetes`](#func-kubernetes-deploy) | Commandes pour travailler avec Kubernetes et Azure Functions. |
| [`func settings`](#func-settings-decrypt)   | Commandes pour la gestion des paramètres d’environnement pour l’hôte Functions local. |
| [`func templates`](#func-templates-list)  | Commandes pour répertorier les modèles de fonction disponibles. |

Avant d’utiliser les commandes de cet article, vous devez [installer Core Tools](functions-run-local.md#install-the-azure-functions-core-tools). 

## <a name="func-init"></a>func init 

Crée un projet de fonctions dans un langage spécifique.

```command
func init <PROJECT_FOLDER>
```

Lorsque vous fournissez `<PROJECT_FOLDER>`, le projet est créé dans un nouveau dossier portant ce nom. Sinon, le dossier actif est initialisé.

`func init` prend en charge les options suivantes, qui sont, sauf indication contraire, uniquement des versions 3.x/2.x :

| Option     | Description                            |
| ------------ | -------------------------------------- |
| **`--csx`** | Crée des fonctions .NET en tant que script C#, ce qui correspond au comportement de la version 1.x. Valide uniquement avec `--worker-runtime dotnet`. |
| **`--docker`** | Crée un fichier Dockerfile pour un conteneur à l’aide d’une image de base définie par le `--worker-runtime` choisi. Utiliser cette option lorsque vous projetez de publier sur un conteneur Linux personnalisé. |
| **`--docker-only`** |  Ajoute un fichier Dockerfile à un projet existant. Demande le runtime Worker s’il n’est pas spécifié ou défini dans local.settings.json. Utiliser cette option lorsque vous projetez de publier un projet existant sur un conteneur Linux personnalisé. |
| **`--force`** | Initialiser le projet même lorsque celui-ci contient des fichiers existants. Ce paramètre remplace les fichiers existants portant le même nom. D’autres fichiers dans le dossier du projet ne sont pas affectés. |
| **`--language`** | Initialise un projet spécifique au langage. Actuellement pris en charge lorsque `--worker-runtime` est défini sur `node`. Les options sont `typescript` et `javascript`. Vous pouvez également utiliser `--worker-runtime javascript` ou `--worker-runtime typescript`.  |
| **`--managed-dependencies`**  | Installe des dépendances gérées. Actuellement, seul un runtime worker PowerShell prend en charge cette fonctionnalité. |
| **`--source-control`** | Contrôle la création d’un référentiel git. Par défaut, un référentiel n’est pas créé. Un référentiel est créé lorsque `true`. |
| **`--worker-runtime`** | Définit le runtime de langage pour le projet. Les valeurs prises en charge sont `csharp`, `dotnet`, `dotnet-isolated`, `javascript`,`node` (JavaScript), `powershell`, `python` et `typescript`. Pour Java, utilisez [Maven](functions-reference-java.md#create-java-functions). Pour générer un projet indépendant du langage avec uniquement les fichiers projet, utilisez `custom`. Lorsqu’il n’est pas défini, vous êtes invité à choisir votre runtime pendant l’initialisation. |
|

> [!NOTE]
> Lorsque vous utilisez l'option`--docker` ou `--dockerfile`, Core Tools crée automatiquement le fichier dockerfile pour les fonctions C#, JavaScript, Python et PowerShell. Pour les fonctions Java, vous devez créer manuellement le fichier Dockerfile. Utilisez la [liste d’images](https://github.com/Azure/azure-functions-docker) Azure Functions pour trouver la bonne image de base du conteneur qui exécute la fonction Azure Functions.

## <a name="func-logs"></a>journaux func

Obtient les journaux pour les fonctions qui s’exécutent dans un cluster Kubernetes.

```
func logs --platform kubernetes --name <APP_NAME>
``` 

L’action `func logs` prend en charge les options suivantes :

| Option     | Description                            |
| ------------------------------------------ | -------------------------------------- |
| **`--platform`** | Plateforme d’hébergement pour l’application de fonction. Options prises en charge : `kubernetes`. |
| **`--name`** | Nom de l’application de fonction dans Azure. |

Pour en savoir plus, voir [Azure Functions sur Kubernetes avec KEDA](functions-kubernetes-keda.md).

## <a name="func-new"></a>func new

Crée une fonction dans le projet actuel à partir d’un modèle.

```
func new
``` 

L’action `func new` prend en charge les options suivantes :

| Option     | Description                            |
| ------------------------------------------ | -------------------------------------- |
| **`--authLevel`** | Vous permet de définir le niveau d’autorisation pour un déclencheur HTTP. Les valeurs prises en charge sont les suivantes : `function`, `anonymous`, `admin`. L’autorisation n’est pas appliquée lors de l’exécution locale. Pour plus d’informations, consultez l’article sur la [liaison HTTP](functions-bindings-http-webhook-trigger.md#authorization-keys). |
| **`--csx`** | (Version 2.x et versions ultérieures.) Génère les mêmes modèles de script C# (.csx) que ceux utilisés dans la version 1.x et dans le portail. |
| **`--language`**, **`-l`**| Langage de programmation du modèle, tel que C#, F# ou JavaScript. Cette option est requise dans la version 1.x. Dans la version 2. x et les versions ultérieures, vous n’utilisez pas cette option, car la langue est définie par le runtime du Worker. |
| **`--name`**, **`-n`** | Nom de la fonction. |
| **`--template`**, **`-t`** | Utilisez la commande `func templates list` pour afficher la liste complète des modèles disponibles pour chaque langage pris en charge.   |

Pour plus d’informations, consultez [Créer une fonction](functions-run-local.md#create-func).

## <a name="func-run"></a>exécution func

*Version 1.x uniquement.*

Permet d'invoquer directement une fonction, ce qui est similaire à l’exécution d’une fonction à l’aide de l’onglet **Test** dans le portail Azure. Cette action est uniquement prise en charge dans la version 1. x. Pour les versions ultérieures, utilisez `func start` et [appelez directement le point de terminaison de la fonction](functions-run-local.md#passing-test-data-to-a-function) .

```command
func run
```

L’action `func run` prend en charge les options suivantes :

| Option     | Description                            |
| ------------ | -------------------------------------- |
| **`--content`** | Contenu inclus passé à la fonction. |
| **`--debug`** | Joindre un débogueur au processus hôte avant d’exécuter la fonction.|
| **`--file`** | Nom du fichier à utiliser en tant que contenu.|
| **`--no-interactive`** | Ne demande pas d’entrée, ce qui est utile pour les scénarios d’automatisation.|
| **`--timeout`** | Délai d’attente (en secondes) jusqu’à ce que l’hôte Functions local soit prêt.|

Par exemple, pour appeler une fonction déclenchée par HTTP et passer un corps de contenu, exécutez la commande suivante :

```
func run MyHttpTrigger --content '{\"name\": \"Azure\"}'
```

## <a name="func-start"></a>début de la func

Démarre l’hôte du runtime local et charge le projet de fonction dans le dossier actif. 

La commande spécifique dépend de la [version du runtime](functions-versions.md).   

# <a name="v2x"></a>[v2.x+](#tab/v2)

```command
func start
```

`func start` prend en charge les options suivantes :

| Option     | Description                            |
| ------------ | -------------------------------------- |
| **`--cert`** | Le chemin d’accès vers un fichier .pfx qui contient une clé privée. Pris en charge uniquement sur `--useHttps`. |
| **`--cors`** | Liste séparée par des virgules d’origines CORS, sans espaces. |
| **`--cors-credentials`** | Autoriser les demandes authentifiées cross-origin (autrement dit, les cookies et l’en-tête d’authentification). |
| **`--dotnet-isolated-debug`** | Quand la valeur `true` est affectée, interrompt le processus de travail .NET jusqu’à ce qu’un débogueur soit attaché à partir du projet isolé .NET en cours de débogage. |
| **`--enable-json-output`** | Émet les journaux de la console au format JSON, lorsque cela est possible. |
| **`--enableAuth`** | Activez le pipeline de gestion complète de l’authentification. |
| **`--functions`** | Liste séparée par des espaces des fonctions à charger. |
| **`--language-worker`** | Arguments pour configurer le travailleur de langage. Par exemple, vous pouvez activer le débogage pour le rôle de travail du langage en fournissant un [port de débogage et d’autres arguments requis](https://github.com/Azure/azure-functions-core-tools/wiki/Enable-Debugging-for-language-workers). |
| **`--no-build`** | Ne générez pas le projet actif avant l’exécution. Pour les projets de classe .NET uniquement. Par défaut, il s’agit de `false`.  |
| **`--password`** | Le mot de passe ou un fichier qui contient le mot de passe pour un fichier .pfx. Utilisé uniquement avec `--cert`. |
| **`--port`** | Port local à écouter. Valeur par défaut : 7071. |
| **`--timeout`** | Délai d’expiration pour le démarrage de l’hôte Functions, en secondes. Valeur par défaut : 20 secondes.|
| **`--useHttps`** | Liaison avec `https://localhost:{port}` plutôt que `http://localhost:{port}`. Par défaut, cette option crée un certificat de confiance sur votre ordinateur.|

Une fois le projet en cours d’exécution, vous pouvez [vérifier des points de terminaison de fonction individuels](functions-run-local.md#passing-test-data-to-a-function).

# <a name="v1x"></a>[v1.x](#tab/v1)

```command
func host start
```

`func start` prend en charge les options suivantes :

| Option     | Description                            |
| ------------ | -------------------------------------- |
| **`--cors`** | Liste séparée par des virgules d’origines CORS, sans espaces. |
| **`--port`** | Port local à écouter. Valeur par défaut : 7071. |
| **`--pause-on-error`** | Marquage d’une pause pour des entrées supplémentaires avant de quitter le processus. Uniquement utilisé lors du lancement des outils de base à partir d’un environnement de développement intégré (IDE).|
| **`--script-root`** | Utilisé pour spécifier le chemin d’accès à la racine de l’application de fonction qui doit être exécutée ou déployée. Il est utilisé pour les projets compilés qui génèrent des fichiers projet dans un sous-dossier. Par exemple, lorsque vous générez un projet de bibliothèque de classes C#, les host.json, local.settings.json et function.json sont générés dans un sous-dossier *racine* avec un chemin d’accès comme `MyProject/bin/Debug/netstandard2.0`. Dans ce cas, définissez le préfixe comme `--script-root MyProject/bin/Debug/netstandard2.0`. Il s’agit de la racine de l’application de fonction lors de l’exécution sur Azure. |
| **`--timeout`** | Délai d’expiration pour le démarrage de l’hôte Functions, en secondes. Valeur par défaut : 20 secondes.|
| **`--useHttps`** | Liaison avec `https://localhost:{port}` plutôt que `http://localhost:{port}`. Par défaut, cette option crée un certificat de confiance sur votre ordinateur.|

Dans la version 1.x, vous pouvez également utiliser la [`func run`commande](#func-run) pour exécuter une fonction spécifique et lui transmettre des données de test. 

---

## <a name="func-azure-functionapp-fetch-app-settings"></a>func azure functionapp fetch-app-settings

Obtient les paramètres d’une application de fonction spécifique.

```command
func azure functionapp fetch-app-settings <APP_NAME>
```

Pour en voir un exemple consultez [Obtenir vos chaînes de connexion de stockage](functions-run-local.md#get-your-storage-connection-strings).

Les paramètres sont téléchargés dans le fichier local.settings.jsdu projet. Les valeurs affichées à l’écran sont masquées pour la sécurité. Vous pouvez protéger les paramètres dans le fichier local.settings.json en[activant le chiffrement local](#func-settings-encrypt). 

## <a name="func-azure-functionapp-list-functions"></a>func azure functionapp list-functions

Retourne une liste des fonctions dans l’application de fonction spécifiée.

```command
func azure functionapp list-functions <APP_NAME>
```
## <a name="func-azure-functionapp-logstream"></a>func azure functionapp logstream

Connecte l’invite de commandes locale aux journaux de diffusion en continu pour l’application de fonction dans Azure.

```command
func azure functionapp logstream <APP_NAME>
```

Le délai d’expiration par défaut de la connexion est de 2 heures. Vous pouvez modifier le délai d'attente en ajoutant un paramètre d'application appelé [SCM_LOGSTREAM_TIMEOUT](functions-app-settings.md#scm_logstream_timeout), avec une valeur de délai d'attente en secondes. Pas encore pris en charge pour les applications Linux dans le plan de consommation. Pour ces applications, utilisez l'option `--browser` permettant d'afficher les journaux dans le portail.

L’action `deploy` prend en charge les options suivantes :

| Option     | Description                            |
| ------------ | -------------------------------------- |
| **`--browser`** | Ouvrez Azure Application Insights Live Stream pour l’application de fonction dans le navigateur par défaut. |

Pour en savoir plus, consultez la section [Activer les journaux en continu](functions-run-local.md#enable-streaming-logs).

## <a name="func-azure-functionapp-publish"></a>func azure functionapp publish 

Déploie un projet de fonctions sur une ressource function app existante dans Azure. 

```command
func azure functionapp publish <FunctionAppName>
```

Pour plus d'informations, consultez [Déployer des fichiers projet](functions-run-local.md#project-file-deployment).

Les options de publication suivantes s’appliquent, selon les versions :

# <a name="v2x"></a>[v2.x+](#tab/v2)

| Option     | Description                            |
| ------------ | -------------------------------------- |
| **`--additional-packages`** | Liste des packages à installer lors de la création des dépendances natives. Par exemple : `python3-dev libevent-dev`. |
| **`--build`**, **`-b`** | Exécute l’action de génération lors du déploiement dans une application de fonction Linux. Accepte : `remote` et `local`. |
| **`--build-native-deps`** | Ignore la génération du dossier `.wheels` lors de la publication des applications de fonction Python. |
| **`--csx`** | Publiez un projet de Script C# (.csx). |
| **`--force`** | Ignorez la vérification de prépublication dans certains scénarios. |
| **`--dotnet-cli-params`** | Lors de la publication de fonctions C# (. csproj) compilées, les outils principaux appellent `dotnet build --output bin/publish`. Tous les paramètres transmis seront ajoutés à la ligne de commande. |
|**`--list-ignored-files`** | Affiche une liste de fichiers ignorés lors de la publication basée sur le fichier `.funcignore`. |
| **`--list-included-files`** | Affiche une liste de fichiers publiés basée sur le fichier `.funcignore`. |
| **`--no-build`** | Le projet n’est pas généré lors de la publication. Pour Python, `pip install` n’intervient pas. |
| **`--nozip`** | Désactive le mode par défaut `Run-From-Package`. |
| **`--overwrite-settings -y`** | Supprimer l’invite de remplacement des paramètres de l’application lorsque `--publish-local-settings -i` est utilisé.|
| **`--publish-local-settings -i`** |  Publier dans Azure les paramètres figurant dans local.settings.json, avec demande de confirmation du remplacement si le paramètre existe déjà. Si vous utilisez l’Émulateur de stockage Microsoft Azure, commencez par changer le paramètre d’application en choisissant une [connexion de stockage réelle](functions-run-local.md#get-your-storage-connection-strings). |
| **`--publish-settings-only`**, **`-o`** |  Publiez les paramètres uniquement et ignorez le contenu. Par défaut, l’accord de l’utilisateur est sollicité. |
| **`--slot`** | Nom facultatif d’un emplacement spécifique dans lequel effectuer la publication. |

# <a name="v1x"></a>[v1.x](#tab/v1)

| Option     | Description                            |
| ------------ | -------------------------------------- |
| **`--overwrite-settings -y`** | Supprimer l’invite de remplacement des paramètres de l’application lorsque `--publish-local-settings -i` est utilisé.|
| **`--publish-local-settings -i`** |  Publier dans Azure les paramètres figurant dans local.settings.json, avec demande de confirmation du remplacement si le paramètre existe déjà. Si vous utilisez l’Émulateur de stockage Microsoft Azure, commencez par changer le paramètre d’application en choisissant une [connexion de stockage réelle](functions-run-local.md#get-your-storage-connection-strings). |

---

## <a name="func-azure-storage-fetch-connection-string"></a>func azure storage fetch-connection-string    

Définit la chaîne de connexion pour le compte de stockage Azure spécifié.

```command
func azure storage fetch-connection-string <STORAGE_ACCOUNT_NAME>
```

## <a name="func-deploy"></a>func deploy

Déploie une application de fonction dans un conteneur Linux personnalisé sur un cluster Kubernetes sans KEDA.

```command
func deploy --name <FUNCTION_APP> --platform kubernetes --registry <DOCKER_USER>
```

Cette commande génère votre projet en tant que conteneur personnalisé et le publie sur un cluster Kubernetes à l’aide de l’échelle par défaut ou de KNative. Pour publier sur un cluster à l’aide de KEDA pour une mise à l’échelle dynamique, utilisez plutôt la [`func kubernetes deploy`commande](#func-kubernetes-deploy). Les conteneurs personnalisés doivent contenir un fichier Dockerfile. Pour créer une application avec un fichier Dockerfile, utilisez l’option `--dockerfile` avec la [`func init` commande](#func-init).     

L’action `deploy` prend en charge les options suivantes :

| Option     | Description                            |
| ------------ | -------------------------------------- |
| **`--config`** | Définit un fichier de configuration de déploiement optionnel. |
| **`--max`**  | Le cas échéant, définit le nombre maximal d’instances d’application de fonction à déployer. |
| **`--min`**  | Le cas échéant, définit le nombre minimal d’instances d’application de fonction à déployer. |
| **`--name`** | Nom de l’application de fonction (obligatoire). |
| **`--platform`** | Plateforme d’hébergement pour l’application de fonction (obligatoire). Les options valides sont : `kubernetes` et `knative`.|
| **`--registry`** | Le nom d’un registre Docker auquel l’utilisateur actuel est connecté (obligatoire). |

Core Tools tirera parti de l’interface de ligne de commande de Docker pour créer et publier l’image. 

Assurez-vous que votre Docker est déjà installé localement. Utilisez la commande `docker login` pour vous connecter à votre cluster.

## <a name="func-durable-delete-task-hub"></a>func durable delete-task-hub

Supprime tous les artefacts de stockage dans le hub de tâches Durable Functions.

```command
func durable delete-task-hub
```

L’action `delete-task-hub` prend en charge les options suivantes :

| Option     | Description                            |
| ------------ | -------------------------------------- |
| **`--connection-string-setting`** | Nom facultatif du paramètre d’application contenant la chaîne de connexion de stockage à utiliser. |
| **`--task-hub-name`** |             Nom facultatif du hub de tâches durable à utiliser. |

Pour plus d’informations, consultez la [documentation relative à Durable Functions](./durable/durable-functions-instance-management.md#delete-a-task-hub).

## <a name="func-durable-get-history"></a>func durable get-history

Retourne l’historique de l’instance d’orchestration spécifiée.

```command
func durable get-history --id <INSTANCE_ID>
```

L’action `get-history` prend en charge les options suivantes :

| Option     | Description                            |
| ------------ | -------------------------------------- |
| **`--id`** | Spécifie l’ID d’une instance d’orchestration (obligatoire). |
| **`--connection-string-setting`** | Nom facultatif du paramètre d’application contenant la chaîne de connexion de stockage à utiliser. |
| **`--task-hub-name`** |             Nom facultatif du hub de tâches durable à utiliser. |

Pour plus d’informations, consultez la [documentation relative à Durable Functions](./durable/durable-functions-instance-management.md#azure-functions-core-tools-1).

## <a name="func-durable-get-instances"></a>func durable get-instances

Retourne l’état de toutes les instances d’orchestration. Supporte la pagination en utilisant le paramètre `top`.

```command
func durable get-instances
```

L’action `get-instances` prend en charge les options suivantes :

| Option     | Description                            |
| ------------ | -------------------------------------- |
| **`--continuation-token`** | Jeton facultatif qui indique une page/section spécifique des demandes à retourner. |
| **`--connection-string-setting`** | Nom facultatif d’un paramètre d’application comportant la chaîne de connexion de stockage à utiliser. |
| **`--created-after`** | Facultatif, récupère les instances créées après cette date/heure (UTC). Toutes les dates et heures au format ISO 8601 sont acceptées. |
| **`--created-before`** | Si vous le souhaitez, vous pouvez obtenir les instances créées avant une date/heure spécifique (UTC). Toutes les dates et heures au format ISO 8601 sont acceptées. |
| **`--runtime-status`** | Si vous le souhaitez, vous pouvez obtenir les instances dont l’état correspond à un état spécifique, notamment `running`, `completed` et `failed`. Vous pouvez fournir un ou plusieurs statuts séparés par des espaces. |
| **`--top`** | Limitez éventuellement le nombre d’enregistrements retournés dans une demande donnée. |
| **`--task-hub-name`** | Nom facultatif du hub de tâches Durable Functions à utiliser. |

Pour plus d’informations, consultez la [documentation relative à Durable Functions](./durable/durable-functions-instance-management.md#azure-functions-core-tools-2).

## <a name="func-durable-get-runtime-status"></a>func durable get-runtime-status  

Retourne le statut de l’instance d’orchestration spécifiée.

```command
func durable get-runtime-status --id <INSTANCE_ID>
```

L’action `get-runtime-status` prend en charge les options suivantes :

| Option     | Description                            |
| ------------ | -------------------------------------- |
| **`--connection-string-setting`** | Nom facultatif du paramètre d’application contenant la chaîne de connexion de stockage à utiliser. |
| **`--id`** | Spécifie l’ID d’une instance d’orchestration (obligatoire). |
| **`--show-input`** | Une fois définie, la réponse contient l’entrée de la fonction. |
| **`--show-output`** | Une fois définie, la réponse contient l’historique d’exécution. |
| **`--task-hub-name`** | Nom facultatif du hub de tâches Durable Functions à utiliser. |

Pour plus d’informations, consultez la [documentation relative à Durable Functions](./durable/durable-functions-instance-management.md#azure-functions-core-tools-1).

## <a name="func-durable-purge-history"></a>func durable purge-history

Purge l’état de l’instance d’orchestration, l’historique et le stockage d’objets BLOB pour les orchestrations antérieures au seuil spécifié.

```command
func durable purge-history
```

L’action `purge-history` prend en charge les options suivantes :

| Option     | Description                            |
| ------------ | -------------------------------------- |
| **`--connection-string-setting`** | Nom facultatif du paramètre d’application contenant la chaîne de connexion de stockage à utiliser. |
| **`--created-after`** | Facultatif, purge l’historique des instances créées après cette date/heure (UTC). Toutes les dates et heures au format ISO 8601 sont acceptées. |
| **`--created-before`** | Facultatif, purge l’historique des instances créées avant cette date/heure (UTC). Toutes les dates et heures au format ISO 8601 sont acceptées.|
| **`--runtime-status`** | Si vous le souhaitez, vous pouvez obtenir les instances dont l’état correspond à un état spécifique, notamment `completd`, `terminated`, `canceled` et `failed`. Vous pouvez fournir un ou plusieurs statuts séparés par des espaces. Si vous n’incluez pas `--runtime-status`, l’historique des instances est supprimé, quel que soit l’état.|
| **`--task-hub-name`** | Nom facultatif du hub de tâches Durable Functions à utiliser. |

Pour plus d’informations, consultez la [documentation relative à Durable Functions](./durable/durable-functions-instance-management.md#azure-functions-core-tools-7).

## <a name="func-durable-raise-event"></a>func durable raise-event         

Déclenche un événement à l’instance d’orchestration spécifiée.

```command
func durable raise-event --event-name <EVENT_NAME> --event-data <DATA>
```

L’action `raise-event` prend en charge les options suivantes :

| Option     | Description                            |
| ------------ | -------------------------------------- |
| **`--connection-string-setting`** | Nom facultatif du paramètre d’application contenant la chaîne de connexion de stockage à utiliser. |
| **`--event-data`** | Données à passer à l’événement, incluses ou à partir d’un fichier JSON (obligatoire). Pour les fichiers, préfixez le chemin du fichier avec une esperluette(`@`), par exemple, `@path/to/file.json`. |
| **`--event-name`** | Nom de l’événement à déclencher (obligatoire).
| **`--id`** | Spécifie l’ID d’une instance d’orchestration (obligatoire). |
| **`--task-hub-name`** | Nom facultatif du hub de tâches Durable Functions à utiliser. |

Pour plus d’informations, consultez la [documentation relative à Durable Functions](./durable/durable-functions-instance-management.md#azure-functions-core-tools-5).

## <a name="func-durable-rewind"></a>func durable rewind              

Rembobine l’instance d’orchestration spécifiée.

```command
func durable rewind --id <INSTANCE_ID> --reason <REASON>
```

L’action `rewind` prend en charge les options suivantes :

| Option     | Description                            |
| ------------ | -------------------------------------- |
| **`--connection-string-setting`** | Nom facultatif du paramètre d’application contenant la chaîne de connexion de stockage à utiliser. |
| **`--id`** | Spécifie l’ID d’une instance d’orchestration (obligatoire). |
| **`--reason`** | Motif de rembobinage de l’instance d’orchestration (obligatoire).|
| **`--task-hub-name`** | Nom facultatif du hub de tâches Durable Functions à utiliser. |

Pour plus d’informations, consultez la [documentation relative à Durable Functions](./durable/durable-functions-instance-management.md#azure-functions-core-tools-6).

## <a name="func-durable-start-new"></a>func durable start-new

Démarre l’exécution d’une nouvelle instance de la fonction d’orchestrateur spécifiée.

```command
func durable start-new --id <INSTANCE_ID> --function-name <FUNCTION_NAME> --input <INPUT>
```

L’action `start-new` prend en charge les options suivantes :

| Option     | Description                            |
| ------------ | -------------------------------------- |
| **`--connection-string-setting`** | Nom facultatif du paramètre d’application contenant la chaîne de connexion de stockage à utiliser. |
| **`--function-name`** | Nom de la fonction d’orchestrateur à démarrer (obligatoire).|
| **`--id`** | Spécifie l’ID d’une instance d’orchestration (obligatoire). |
| **`--input`** | Entrée de la fonction d'orchestration, soit en ligne, soit à partir d'un fichier JSON (obligatoire). Pour les fichiers, préfixez le chemin du fichier avec une esperluette(`@`), par exemple, `@path/to/file.json`. |
| **`--task-hub-name`** | Nom facultatif du hub de tâches Durable Functions à utiliser. |

Pour plus d’informations, consultez la [documentation relative à Durable Functions](./durable/durable-functions-instance-management.md#azure-functions-core-tools).

## <a name="func-durable-terminate"></a>func durable terminate

Arrête l’instance d’orchestration spécifiée.

```command
func durable terminate --id <INSTANCE_ID> --reason <REASON>
```

L’action `terminate` prend en charge les options suivantes :

| Option     | Description                            |
| ------------ | -------------------------------------- |
| **`--connection-string-setting`** | Nom facultatif du paramètre d’application contenant la chaîne de connexion de stockage à utiliser. |
| **`--id`** | Spécifie l’ID d’une instance d’orchestration (obligatoire). |
| **`--reason`** | Motif d’arrêt de l’instance d’orchestration (obligatoire). |
| **`--task-hub-name`** | Nom facultatif du hub de tâches Durable Functions à utiliser. |

Pour plus d’informations, consultez la [documentation relative à Durable Functions](./durable/durable-functions-instance-management.md#azure-functions-core-tools-4).

## <a name="func-extensions-install"></a>func extensions install

Installe les extensions de fonctions dans un projet de bibliothèque de classes non-C#. 

Dans la mesure du possible, vous devez utiliser à la place des packages d’extension. Pour plus d’informations, voir [Offres groupées d’extension](functions-bindings-register.md#extension-bundles).

Pour les projets de bibliothèque de classes C# et d’isolation .net, utilisez plutôt des méthodes d’installation de package NuGet standard, telles que `dotnet add package`.

L’action `install` prend en charge les options suivantes :

| Option     | Description                            |
| ------------ | -------------------------------------- |
| **`--configPath`** | Chemin d’accès du répertoire contenant le fichier extensions. csproj.|
| **`--csx`** |   Prend en charge les projets de script C# (.csx). |
| **`--force`** |  Mettez à jour les versions des extensions existantes. |
| **`--output`** |  Chemin de sortie pour les extensions. |
| **`--package`** |   Identificateur d’un package d’extension spécifique. Lorsqu’elles ne sont pas spécifiées, toutes les extensions référencées sont installées, comme avec `func extensions sync` .|
| **`--source`** |  Source de flux NuGet si vous n’utilisez pas NuGet.org.|
| **`--version`** |  Fichier de package d’extension. |

Aucune action n’est effectuée lorsqu’un bundle d’extension est défini dans votre host.json sur le fichier.

## <a name="func-extensions-sync"></a>func extensions sync

Installe toutes les extensions ajoutées à l’application de fonction.

L’action `sync` prend en charge les options suivantes :

| Option     | Description                            |
| ------------ | -------------------------------------- |
| **`--configPath`** | Chemin d’accès du répertoire contenant le fichier extensions. csproj.|
| **`--csx`** |   Prend en charge les projets de script C# (.csx). |
| **`--output`** |  Chemin de sortie pour les extensions. |

Régénère un fichier extensions. csproj manquant. Aucune action n’est effectuée lorsqu’un bundle d’extension est défini dans votre host.json sur le fichier.

## <a name="func-kubernetes-deploy"></a>func kubernetes deploy

Déploie un projet de fonctions en tant que conteneur d’ancrage personnalisé sur un cluster Kubernetes à l’aide de KEDA.

```command
func kubernetes deploy 
```

Cette commande construit votre projet en tant que conteneur personnalisé et le publie sur un cluster Kubernetes en utilisant KEDA pour une mise à l'échelle dynamique. Pour publier sur un cluster à l’aide d’un adapteur par défaut ou à l’aide de KNative, utilisez la [`func deploy` commande](#func-deploy). Les conteneurs personnalisés doivent contenir un fichier Dockerfile. Pour créer une application avec un fichier Dockerfile, utilisez l’option `--dockerfile` avec la [`func init` commande](#func-init). 

Les options de déploiement de Kubernetes suivantes sont disponibles :

| Option     | Description                            |
| ------------ | -------------------------------------- |
| **`--dry-run`** | Affiche éventuellement le modèle de déploiement, sans exécution. |
| **`--config-map-name`** | Nom facultatif d'un ConfigMap existant contenant les [paramètres des applications fonctionnelles](functions-how-to-use-azure-function-app-settings.md#settings) à utiliser dans le déploiement. Exige `--use-config-map`. Le comportement par défaut est de créer des paramètres basés sur l'objet`Values` dans le [fichier local.settings.json].|
| **`--cooldown-period`** | La période de refroidissement (en secondes) après la fin de tous les déclencheurs n’est plus active avant que le déploiement ne soit mis à l’échelle à zéro, avec une valeur par défaut de 300 s. |
| **`--ignore-errors`** | Poursuit le déploiement après qu’une ressource a retourné une erreur. Le comportement par défaut est d’arrêter en cas d’erreur. |
| **`--image-name`** | Nom de l’image à utiliser pour le déploiement du pod et à partir duquel lire les fonctions. |
| **`--keda-version`** | Définit la version de KEDA à installer. Les options valides sont : `v1` et `v2` (par défaut). |
| **`--keys-secret-name`** | Nom d’une collection de secrets Kubernetes à utiliser pour le stockage des [clés d’accès aux fonctions](functions-bindings-http-webhook-trigger.md#authorization-keys). |
| **`--max-replicas`** | Définit le nombre maximal de réplicas pour les mises à l’échelle du Pod horizontal (HPA). |
| **`--min-replicas`** | Définit le nombre minimal de réplicas sous lequel HPA n’est pas mis à l’échelle. |
| **`--mount-funckeys-as-containervolume`** | Monte les [touches d'accès aux fonctions](functions-bindings-http-webhook-trigger.md#authorization-keys) comme un volume de conteneur. |
| **`--name`** | Nom utilisé pour le déploiement et d’autres artefacts dans Kubernetes. |
| **`--namespace`** | Définit l’espace de noms Kubernetes sur lequel effectuer le déploiement, qui est par défaut l’espace de noms par défaut. |
| **`--no-docker`** | Les fonctions sont lues à partir du répertoire actif et non à partir d’une image. Nécessite le montage du système de fichiers image. |
| **`--registry`** | Quand elle est définie, une build d’ancrage est exécutée et l’image est envoyée à un registre de ce nom. Vous ne pouvez pas utiliser `--registry` avec `--image-name`. Pour l’ancrage, utilisez votre nom d’utilisateur. |
| **`--polling-interval`** | Intervalle d’interrogation (en secondes) pour vérifier les déclencheurs non-HTTP, avec la valeur par défaut 30 s. |
| **`--pull-secret`** | Le secret utilisé pour accéder aux informations d’identification du registre privé. |
| **`--secret-name`** | Le nom d'une collection de secrets Kubernetes existante qui contient les [paramètres des applications fonctionnelles](functions-how-to-use-azure-function-app-settings.md#settings) à utiliser dans le déploiement. Le comportement par défaut est de créer des paramètres basés sur l'objet`Values` dans le [fichier local.settings.json]. |
| **`--show-service-fqdn`** | Affiche les URL des déclencheurs HTTP avec le nom de domaine complet Kubernetes au lieu du comportement par défaut de l’utilisation d’une adresse IP. |
| **`--service-type`** | Définit le type de service Kubernetes. Les valeurs prises en charge sont : `ClusterIP`, `NodePort` et `LoadBalancer` (par défaut). |
| **`--use-config-map`** | Utilisez un objet `ConfigMap` (v1) au lieu d'un objet `Secret` (v1) pour configurer les [paramètres de l'application fonctionnelle](functions-how-to-use-azure-function-app-settings.md#settings). Le nom de mappage est défini à l’aide de `--config-map-name`.|

Core Tools tirera parti de l’interface de ligne de commande de Docker pour créer et publier l’image. 

Assurez-vous que votre Docker est déjà installé localement. Utilisez la commande `docker login` pour vous connecter à votre cluster.

Pour en savoir plus, consultez [Déploiement d’une application de fonction sur Kubernetes](functions-kubernetes-keda.md#deploying-a-function-app-to-kubernetes).

## <a name="func-kubernetes-install"></a>func kubernetes install

Installe KEDA dans un cluster Kubernetes.

```command
func kubernetes install 
```

Installe KEDA sur le cluster défini dans le fichier de configuration kubectl.

L’action `install` prend en charge les options suivantes :

| Option     | Description                            |
| ------------ | -------------------------------------- |
| **`--dry-run`** | Affiche éventuellement le modèle de déploiement, sans exécution. |
| **`--keda-version`** | Définit la version de KEDA à installer. Les options valides sont : `v1` et `v2` (par défaut). |
| **`--namespace`** | Prend en charge l’installation sur un espace de noms Kubernetes spécifique. S'il n'est pas défini, l'espace de noms par défaut est utilisé. |

Pour en savoir plus, consultez [Gestion de KEDA et des fonctions dans Kubernetes](functions-kubernetes-keda.md#managing-keda-and-functions-in-kubernetes).

## <a name="func-kubernetes-remove"></a>func kubernetes remove

Supprime KEDA du cluster Kubernetes défini dans le fichier de configuration kubectl.

```command
func kubernetes remove 
```

Supprime KEDA du cluster défini dans le fichier de configuration kubectl.

L’action `remove` prend en charge les options suivantes :

| Option     | Description                            |
| ------------ | -------------------------------------- |
| **`--namespace`** | Prend en charge la désinstallation d’un espace de noms Kubernetes spécifique. S'il n'est pas défini, l'espace de noms par défaut est utilisé. | 

Pour en savoir plus, consultez [Désinstallation de KEDA de Kubernetes](functions-kubernetes-keda.md#uninstalling-keda-from-kubernetes).

## <a name="func-settings-add"></a>func settings add

Ajoute un nouveau paramètre à la collection`Values` du [fichier local.settings.json].

```command
func settings add <SETTING_NAME> <VALUE>
```

Remplacez `<SETTING_NAME>` par le nom du paramètre d’application, et `<VALUE>` par la valeur à assigner à celui-ci. 

L’action `add` prend en charge les options suivantes :

| Option     | Description                            |
| ------------ | -------------------------------------- |
| **`--connectionString`** | Ajoute la paire nom-valeur à la collection `ConnectionStrings` au lieu de la collection `Values`. N'utiliser la collection `ConnectionStrings` que lorsque certaines infrastructures l'exigent. Pour en savoir plus, consultez la section [fichier local.settings.json]. |

## <a name="func-settings-decrypt"></a>func settings decrypt

Décrypte les valeurs précédemment cryptées dans la collection `Values` du [fichier local.settings.json].

```command
func settings decrypt
```

Les valeurs de chaîne de connexion dans la collection `ConnectionStrings` sont également déchiffrées. Dans local.settings.json, `IsEncrypted` est également défini à `false` . Chiffrez les paramètres locaux pour réduire le risque de fuite d’informations précieuses de local.settings.json. Dans Azure, les paramètres d’application sont toujours stockés chiffrés. 

## <a name="func-settings-delete"></a>func settings delete

Supprime un paramètre existant à la collection`Values` du [fichier local.settings.json].

```command
func settings delete <SETTING_NAME>
```

Remplacez `<SETTING_NAME>` par le nom du paramètre d’application, et `<VALUE>` par la valeur à assigner à celui-ci. 

L’action `delete` prend en charge les options suivantes :

| Option     | Description                            |
| ------------ | -------------------------------------- |
| **`--connectionString`** | Supprime la paire nom-valeur à la collection `ConnectionStrings` au lieu de la collection `Values`. |

## <a name="func-settings-encrypt"></a>func settings encrypt

Crypte les valeurs des éléments individuels dans la collection `Values` du [fichier local.settings.json].

```command
func settings encrypt
```

Les valeurs de chaîne de connexion dans la collection `ConnectionStrings` sont également chiffrées. Dans local.settings.json, `IsEncrypted` est également défini sur `true`, ce qui indique que le runtime local déchiffre les paramètres avant de les utiliser. Chiffrez les paramètres locaux pour réduire le risque de fuite d’informations précieuses de local.settings.json. Dans Azure, les paramètres d’application sont toujours stockés chiffrés. 

## <a name="func-settings-list"></a>func settings list

Produit une liste des paramètres de la collection `Values` dans le [fichier local.settings.json]. 

```command
func settings list
```

Les chaînes de connexion de la collection `ConnectionStrings` sont également éditées. Par défaut, les valeurs affichées sont masquées pour la sécurité. Vous pouvez utiliser l'option `--showValue` pour afficher la valeur réelle.

L’action `list` prend en charge les options suivantes :

| Option     | Description                            |
| ------------ | -------------------------------------- |
| **`--showValue`** | Affiche les valeurs non masquées réelles dans la sortie. |

## <a name="func-templates-list"></a>func templates list

Répertorie les modèles de fonction (déclencheur) disponibles.

L’action `list` prend en charge les options suivantes :

| Option     | Description                            |
| ------------ | -------------------------------------- |
| **`--language`** | Langue pour laquelle filtrer les modèles retournés. Par défaut, tous les langages sont retournés. |

[fichier local.settings.json]: functions-develop-local.md#local-settings-file
