---
title: Utiliser Azure Functions Core Tools
description: Apprenez à coder et à tester des fonctions Azure à partir de l’invite de commandes ou du terminal sur votre ordinateur local avant de les exécuter dans Azure Functions.
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.topic: conceptual
ms.date: 03/13/2019
ms.custom: 80e4ff38-5174-43
ms.openlocfilehash: 65fa8502be43076e06cea18b2499ceed9d7d770e
ms.sourcegitcommit: 541e6139c535d38b9b4d4c5e3bfa7eef02446fdc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/06/2020
ms.locfileid: "75667528"
---
# <a name="work-with-azure-functions-core-tools"></a>Utiliser Azure Functions Core Tools

Azure Functions Core Tools vous permet de développer et de tester vos fonctions sur votre ordinateur local à partir de l’invite de commandes ou du terminal. Vos fonctions locales peuvent être connectées aux services Azure actifs, et vous pouvez déboguer vos fonctions sur votre ordinateur local à l’aide du runtime Functions complet. Vous pouvez même déployer une application de fonction sur votre abonnement Azure.

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

Développez des fonctions sur votre ordinateur local et publiez-les sur Azure à l’aide de Core Tools en suivant ces étapes de base :

> [!div class="checklist"]
> * [Installez Core Tools et les dépendances.](#v2)
> * [Créez un projet d’application de fonction à partir d’un modèle spécifique au langage.](#create-a-local-functions-project)
> * [Inscrivez les extensions de liaison et de déclencheur.](#register-extensions)
> * [Définissez le stockage et les autres connexions.](#local-settings-file)
> * [Créez une fonction à partir d’un déclencheur et d’un modèle spécifique au langage.](#create-func)
> * [Exécutez la fonction localement.](#start)
> * [Publiez le projet sur Azure.](#publish)

## <a name="core-tools-versions"></a>Versions de Core Tools

Il existe trois versions d’Azure Functions Core Tools. La version que vous utilisez dépend de votre environnement de développement local, du [choix du langage](supported-languages.md) et du niveau de prise en charge requis :

+ **Version 1.x** : Prend en charge la version 1.x du runtime Azure Functions. Cette version des outils est uniquement prise en charge sur les ordinateurs Windows et est installée à partir d’un [package npm](https://www.npmjs.com/package/azure-functions-core-tools).

+ [**Version 2.x/3.x**](#v2) : Prend en charge la [version 2.x ou 3.x du runtime Azure Functions](functions-versions.md). Ces versions prennent en charge [Windows](#windows-npm), [macOS](#brew) et [Linux](#linux) et utilisent des gestionnaires de package spécifiques à la plateforme ou npm pour l’installation.

Sauf indication contraire, les exemples de cet article concernent la version 3.x.

## <a name="install-the-azure-functions-core-tools"></a>Installer Azure Functions Core Tools

[Azure Functions Core Tools] inclut une version du même runtime qu’Azure Functions, que vous pouvez exécuter sur votre ordinateur de développement local. Il fournit également des commandes pour créer des fonctions, se connecter à Azure et déployer des projets de fonction.

### <a name="v2"></a>Versions 2.x et 3.x

La version 2.x ou 3.x des outils utilise le runtime Azure Functions qui repose sur .NET Core. Cette version est prise en charge sur tous les supports des plateformes .NET Core, notamment [Windows](#windows-npm), [macOS](#brew) et [Linux](#linux). 

> [!IMPORTANT]
> Vous pouvez contourner l’obligation d’installer le kit SDK .NET Core en utilisant des [bundles d’extension].

#### <a name="windows-npm"></a>Windows

Les étapes suivantes utilisent npm pour installer les outils Core sur Windows. Vous pouvez également utiliser [Chocolatey](https://chocolatey.org/). Pour plus d’informations, consultez le [fichier Lisez-moi Outils Core](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#windows).

1. Installez [Node.js], qui inclut npm.
    - Pour la version 2.x des outils, seuls Node.js 8.5 et les versions ultérieures sont pris en charge.
    - Pour la version 3.x des outils, seuls Node 10 et les versions ultérieures sont pris en charge.

1. Installez le package Outils Core :

    ##### <a name="v2x"></a>v2.x

    ```bash
    npm install -g azure-functions-core-tools
    ```

    ##### <a name="v3x"></a>v3.x

    ```bash
    npm install -g azure-functions-core-tools@3
    ```

   Le téléchargement et l’installation du package Core Tools par npm peut prendre quelques minutes.

1. Si vous ne prévoyez pas d’utiliser des [bundles d’extension], installez le [Kit de développement logiciel (SDK) .NET Core 2.x pour Windows](https://www.microsoft.com/net/download/windows).

#### <a name="brew"></a>MacOS avec Homebrew

Les étapes suivantes utilisent Homebrew pour installer les Outils Core sur macOS.

1. Installez [Homebrew](https://brew.sh/), s’il n’est pas déjà installé.

1. Installez le package Outils Core :

    ##### <a name="v2x"></a>v2.x

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools
    ```

    ##### <a name="v3x"></a>v3.x

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools@3
    # if upgrading on a machine that has 2.x installed
    brew link --overwrite azure-functions-core-tools@3
    ```

#### <a name="linux"></a> Linux (Ubuntu/Debian) avec APT

Les étapes suivantes utilisent [APT](https://wiki.debian.org/Apt) pour installer les Outils Core sur votre distribution Ubuntu/Debian Linux. Pour les autres distributions Linux, consultez le [fichier Lisez-moi Outils Core](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#linux).

1. Installez la clé GPG du référentiel de packages Microsoft pour valider l’intégrité du package :

    ```bash
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
    ```

1. Configurez la liste de sources de développement .NET avant d’effectuer une mise à jour d’APT.

   Pour configurer la liste de sources APT pour Ubuntu, exécutez la commande suivante :

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-$(lsb_release -cs)-prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

   Pour configurer la liste de sources APT pour Debian, exécutez la commande suivante :

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/debian/$(lsb_release -rs)/prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

1. Vérifiez la présence de l’une des chaînes de version Linux appropriées ci-dessous dans le fichier `/etc/apt/sources.list.d/dotnetdev.list` :

    | Distribution Linux | Version |
    | --------------- | ----------- |
    | Debian 9 | `stretch` |
    | Debian 8 | `jessie` |
    | Ubuntu 18.10    | `cosmic`    |
    | Ubuntu 18.04    | `bionic`    |
    | Ubuntu 17.04    | `zesty`     |
    | Ubuntu 16.04/Linux Mint 18    | `xenial`  |

1. Démarrez la mise à jour des sources APT :

    ```bash
    sudo apt-get update
    ```

1. Installez le package Outils Core :

    ```bash
    sudo apt-get install azure-functions-core-tools
    ```

1. Si vous ne prévoyez pas d’utiliser des [bundles d’extension], installez le [Kit de développement logiciel (SDK) .NET Core 2.x pour Linux](https://www.microsoft.com/net/download/linux).

## <a name="create-a-local-functions-project"></a>Créer un projet Functions local

Un répertoire de projet de fonctions contient les fichiers [host.json](functions-host-json.md) et [local.settings.json](#local-settings-file), ainsi que des sous-dossiers qui contiennent le code des fonctions individuelles. Ce répertoire est l’équivalent d’une application de fonction dans Azure. Pour en savoir plus sur la structure de dossiers Functions, consultez le [Guide de développement Azure Functions](functions-reference.md#folder-structure).

Pour la version 2.x, vous devez sélectionner un langage par défaut pour votre projet lors de son initialisation. Dans la version 2. x, toutes les fonctions ajoutées utilisent des modèles de langage par défaut. Dans la version 1.x, vous spécifiez le langage à chaque fois que vous créez une fonction.

Dans la fenêtre du terminal ou à partir d’une invite de commandes, exécutez la commande suivante pour créer le projet et le référentiel Git local :

```bash
func init MyFunctionProj
```

Lorsque vous fournissez un nom de projet, un nouveau dossier portant ce nom est créé et initialisé. Sinon, le dossier actif est initialisé.  
Dans la version 2.x, lorsque vous exécutez la commande, vous devez choisir un runtime pour votre projet. 

```output
Select a worker runtime:
dotnet
node
python 
powershell
```

Utilisez les touches de direction haut/bas pour choisir un langage, puis appuyez sur Entrée. Si vous envisagez de développer des fonctions JavaScript ou TypeScript, choisissez **nœud**, puis sélectionnez la langue. TypeScript comprend [des exigences supplémentaires](functions-reference-node.md#typescript). 

Le résultat ressemble à l’exemple suivant pour un projet JavaScript :

```output
Select a worker runtime: node
Writing .gitignore
Writing host.json
Writing local.settings.json
Writing C:\myfunctions\myMyFunctionProj\.vscode\extensions.json
Initialized empty Git repository in C:/myfunctions/myMyFunctionProj/.git/
```

`func init` prend en charge les options suivantes, qui sont, sauf indication contraire, uniquement des versions 2.x :

| Option     | Description                            |
| ------------ | -------------------------------------- |
| **`--csharp`**<br/> **`--dotnet`** | Initialise un [projet de bibliothèque de classes C# (.cs)](functions-dotnet-class-library.md). |
| **`--csx`** | Initialise un [projet de Script C# (.csx)](functions-reference-csharp.md). Vous devez spécifier `--csx` dans les commandes suivantes. |
| **`--docker`** | Créez un fichier Dockerfile pour un conteneur à l’aide d’une image de base définie par le `--worker-runtime` choisi. Utiliser cette option lorsque vous projetez de publier sur un conteneur Linux personnalisé. |
| **`--docker-only`** |  Ajoute un fichier Dockerfile à un projet existant. Demande le runtime Worker s’il n’est pas spécifié ou défini dans local.settings.json. Utiliser cette option lorsque vous projetez de publier un projet existant sur un conteneur Linux personnalisé. |
| **`--force`** | Initialiser le projet même lorsque celui-ci contient des fichiers existants. Ce paramètre remplace les fichiers existants portant le même nom. D’autres fichiers dans le dossier du projet ne sont pas affectés. |
| **`--java`**  | Initialise un [projet Java](functions-reference-java.md). |
| **`--javascript`**<br/>**`--node`**  | Initialise un [projet JavaScript](functions-reference-node.md). |
| **`--no-source-control`**<br/>**`-n`** | Empêche la création par défaut d’un référentiel Git dans la version 1.x. Dans la version 2.x, le référentiel git n’est pas créé par défaut. |
| **`--powershell`**  | Initialise un [projet PowerShell](functions-reference-powershell.md). |
| **`--python`**  | Initialise un [projet Python](functions-reference-python.md). |
| **`--source-control`** | Contrôle la création d’un référentiel git. Par défaut, un référentiel n’est pas créé. Un référentiel est créé lorsque `true`. |
| **`--typescript`**  | Initialise un [projet TypeScript](functions-reference-node.md#typescript). |
| **`--worker-runtime`** | Définit le runtime de langage pour le projet. Les valeurs prises en charge sont `csharp`, `dotnet`, `java`, `javascript`,`node` (JavaScript), `powershell`, `python` et `typescript`. Lorsqu’il n’est pas défini, vous êtes invité à choisir votre runtime pendant l’initialisation. |

> [!IMPORTANT]
> Par défaut, la version 2.x des outils Core crée les projets d’application de fonctions pour le runtime .NET en tant que [projets de classes C#](functions-dotnet-class-library.md) (.csproj). Ces projets C#, qui peuvent être utilisés avec Visual Studio ou Visual Studio Code, sont compilés pendant les tests et lors de la publication sur Azure. Si vous voulez plutôt créer et utiliser les mêmes fichiers de script C# (.csx) que ceux créés dans la version 1.x et dans le portail, vous devez inclure le paramètre `--csx` quand vous créez et que vous déployez des fonctions.

[!INCLUDE [functions-core-tools-install-extension](../../includes/functions-core-tools-install-extension.md)]

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Par défaut, ces paramètres ne sont pas migrés automatiquement lorsque le projet est publié dans Azure. Utilisez le commutateur `--publish-local-settings`[lors de la publication](#publish) pour vous assurer que ces paramètres sont ajoutés à l’application de fonction dans Azure. Notez que les valeurs dans **ConnectionStrings** ne sont jamais publiées.

Ces valeurs de paramètres d’application de fonction peuvent aussi être lues dans votre code en tant que variables d’environnement. Pour plus d’informations, consultez la section Variables d’environnement de ces rubriques de référence spécifiques à une langue :

* [C# précompilé](functions-dotnet-class-library.md#environment-variables)
* [Script C# (.csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [JavaScript](functions-reference-node.md#environment-variables)

Si aucune chaîne de connexion de stockage valide n’est définie pour [`AzureWebJobsStorage`] et que l’émulateur n’est pas utilisé, le message d’erreur suivant s’affiche :

> Valeur manquante pour AzureWebJobsStorage dans local.settings.json. Cette valeur est nécessaire pour tous les déclencheurs autres que HTTP. Vous pouvez exécuter 'func azure functionapp fetch-app-settings \<functionAppName\>' ou spécifier une chaîne de connexion dans local.settings.json.

### <a name="get-your-storage-connection-strings"></a>Obtenir vos chaînes de connexion de stockage

Même si vous utilisez l’Émulateur de stockage Microsoft Azure pour le développement, vous pouvez tester votre configuration avec une connexion de stockage réelle. Si vous avez déjà [créé un compte de stockage](../storage/common/storage-create-storage-account.md), vous pouvez obtenir une chaîne de connexion de stockage valide de l’une des manières suivantes :

- Dans le [Azure portal], recherchez et sélectionnez **Comptes de stockage**. 
  ![Sélectionner des comptes de stockage à partir du Portail Azure](./media/functions-run-local/select-storage-accounts.png)
  
  Sélectionnez votre compte de stockage, sélectionnez **Clés d’accès** dans **Paramètres**, puis copiez une des valeurs **Chaîne de connexion**.
  ![Copier une chaîne de connexion à partir du portail Azure](./media/functions-run-local/copy-storage-connection-portal.png)

- Utilisez l’[Explorateur Stockage Azure](https://storageexplorer.com/) pour vous connecter à votre compte Azure. Dans l’**Explorateur**, développez votre abonnement, sélectionnez votre compte de stockage et copiez la chaîne de connexion principale ou secondaire.

  ![Copier une chaîne de connexion à partir de l’Explorateur Stockage Azure](./media/functions-run-local/storage-explorer.png)

+ Utilisez Core Tools pour télécharger la chaîne de connexion à partir d’Azure à l’aide d’une des commandes suivantes :

  + Téléchargez tous les paramètres à partir d’une application de fonction existante :

    ```bash
    func azure functionapp fetch-app-settings <FunctionAppName>
    ```
  + Obtenez la chaîne de connexion pour un compte de stockage spécifique :

    ```bash
    func azure storage fetch-connection-string <StorageAccountName>
    ```

    Si vous n’êtes pas encore connecté à Azure, vous êtes invité à le faire.

## <a name="create-func"></a>Créer une fonction

Exécutez la commande suivante pour créer une fonction :

```bash
func new
```

Dans la version 2.x, lorsque vous exécutez `func new`, vous êtes invité à choisir un modèle dans le langage par défaut de votre application de fonction, puis vous êtes également invité à choisir un nom pour votre fonction. Dans la version 1.x, vous êtes également invité à choisir le langage.

```output
Select a language: Select a template:
Blob trigger
Cosmos DB trigger
Event Grid trigger
HTTP trigger
Queue trigger
SendGrid
Service Bus Queue trigger
Service Bus Topic trigger
Timer trigger
```

Le code de la fonction est généré dans un sous-dossier portant le nom que vous avez indiqué pour la fonction, comme vous pouvez le voir dans la sortie de déclencheur de file d’attente suivante :

```output
Select a language: Select a template: Queue trigger
Function name: [QueueTriggerJS] MyQueueTrigger
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\index.js
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\readme.md
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\sample.dat
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\function.json
```

Vous pouvez également spécifier ces options dans la commande en utilisant les arguments suivants :

| Argument     | Description                            |
| ------------------------------------------ | -------------------------------------- |
| **`--csx`** | (Version 2.x) Génère les mêmes modèles de script C# (.csx) que ceux utilisés dans la version 1.x et dans le portail. |
| **`--language -l`**| Langage de programmation du modèle, tel que C#, F# ou JavaScript. Cette option est requise dans la version 1.x. Dans la version 2.x, n’utilise pas cette option ou choisissez un langage qui correspond au runtime worker. |
| **`--name -n`** | Nom de la fonction. |
| **`--template -t`** | Utilisez la commande `func templates list` pour afficher la liste complète des modèles disponibles pour chaque langage pris en charge.   |

Par exemple, pour créer un déclencheur HTTP JavaScript dans une seule commande, exécutez :

```bash
func new --template "Http Trigger" --name MyHttpTrigger
```

Pour créer une fonction de déclencheur par file d’attente, exécutez :

```bash
func new --template "Queue Trigger" --name QueueTriggerJS
```

## <a name="start"></a>Exécuter des fonctions localement

Pour exécuter un projet Functions, exécutez l’hôte Functions. L’hôte active les déclencheurs pour toutes les fonctions du projet. 

### <a name="version-2x"></a>Version 2.x

Dans la version 2.x du runtime, la commande de démarrage varie selon le langage de votre projet.

#### <a name="c"></a>C\#

```command
func start --build
```

#### <a name="javascript"></a>JavaScript

```command
func start
```

#### <a name="typescript"></a>TypeScript

```command
npm install
npm start     
```

### <a name="version-1x"></a>Version 1.x

La version 1. x du runtime Functions requiert la commande `host`, comme dans l’exemple suivant :

```command
func host start
```

`func start` prend en charge les options suivantes :

| Option     | Description                            |
| ------------ | -------------------------------------- |
| **`--no-build`** | Ne générez pas le projet actif avant l’exécution. Pour les projets dotnet uniquement. La valeur par défaut est false. Version 2.x uniquement. |
| **`--cert`** | Le chemin d’accès vers un fichier .pfx qui contient une clé privée. Utilisé uniquement avec `--useHttps`. Version 2.x uniquement. |
| **`--cors-credentials`** | Autoriser les demandes authentifiées cross-origin (autrement dit, les cookies et l’en-tête d’authentification). Version 2.x uniquement. |
| **`--cors`** | Liste séparée par des virgules d’origines CORS, sans espaces. |
| **`--language-worker`** | Arguments pour configurer le travailleur de langage. Par exemple, vous pouvez activer le débogage pour le rôle de travail du langage en fournissant un [port de débogage et d’autres arguments requis](https://github.com/Azure/azure-functions-core-tools/wiki/Enable-Debugging-for-language-workers). Version 2.x uniquement. |
| **`--nodeDebugPort -n`** | Port du débogueur de nœud à utiliser. Valeur par défaut : une valeur issue de launch.json ou 5858. Version 1.x uniquement. |
| **`--password`** | Le mot de passe ou un fichier qui contient le mot de passe pour un fichier .pfx. Utilisé uniquement avec `--cert`. Version 2.x uniquement. |
| **`--port -p`** | Port local à écouter. Valeur par défaut : 7071. |
| **`--pause-on-error`** | Marquage d’une pause pour des entrées supplémentaires avant de quitter le processus. Uniquement utilisé lors du lancement des outils de base à partir d’un environnement de développement intégré (IDE).|
| **`--script-root --prefix`** | Utilisé pour spécifier le chemin d’accès à la racine de l’application de fonction qui doit être exécutée ou déployée. Il est utilisé pour les projets compilés qui génèrent des fichiers projet dans un sous-dossier. Par exemple, lorsque vous générez un projet de bibliothèque de classes C#, les host.json, local.settings.json et function.json sont générés dans un sous-dossier *racine* avec un chemin d’accès comme `MyProject/bin/Debug/netstandard2.0`. Dans ce cas, définissez le préfixe comme `--script-root MyProject/bin/Debug/netstandard2.0`. Il s’agit de la racine de l’application de fonction lors de l’exécution sur Azure. |
| **`--timeout -t`** | Délai d’expiration pour le démarrage de l’hôte Functions, en secondes. Valeur par défaut : 20 secondes.|
| **`--useHttps`** | Liaison avec `https://localhost:{port}` plutôt que `http://localhost:{port}`. Par défaut, cette option crée un certificat de confiance sur votre ordinateur.|

Quand l’hôte Functions démarre, il génère l’URL des fonctions déclenchées par HTTP :

```output
Found the following functions:
Host.Functions.MyHttpTrigger

Job host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
```

>[!IMPORTANT]
>Lors d’une exécution locale, l’authentification n’est pas appliquée pour les points de terminaison HTTP. Cela signifie que toutes les demandes HTTP locales sont gérées de manière `authLevel = "anonymous"`. Pour plus d’informations, consultez l’article sur la [liaison HTTP](functions-bindings-http-webhook.md#authorization-keys).

### <a name="passing-test-data-to-a-function"></a>Transmission de données de test à une fonction

Pour tester vos fonctions localement, vous [démarrez l’hôte Functions](#start) et vous appelez des points de terminaison sur le serveur local avec des requêtes HTTP. Le point de terminaison que vous appelez varie selon le type de fonction.

>[!NOTE]
> Les exemples de cette rubrique utilisent l’outil cURL pour envoyer des requêtes HTTP à partir du terminal ou d’une invite de commandes. Vous pouvez utiliser un outil de votre choix pour envoyer les requêtes HTTP au serveur local. L’outil cURL est disponible par défaut sur les systèmes Linux et Windows 10 Build 17063 et versions ultérieures. Avec les anciennes versions de Windows, vous devez d’abord télécharger et installer [l’outil cURL](https://curl.haxx.se/).

Pour des informations plus générales sur le test de fonctions, consultez [Stratégies permettant de tester votre code dans Azure Functions](functions-test-a-function.md).

#### <a name="http-and-webhook-triggered-functions"></a>Fonctions déclenchées par HTTP et par Webhook

Vous appelez le point de terminaison suivant pour exécuter localement des fonctions déclenchées par HTTP et par Webhook :

    http://localhost:{port}/api/{function_name}

Vérifiez que vous utilisez le même nom de serveur et le même port que celui où l’hôte Functions écoute. Vous voyez cela dans la sortie générée lors du démarrage de l’hôte Functions. Vous pouvez appeler cette URL en utilisant n’importe quelle méthode HTTP prise en charge par le déclencheur.

La commande cURL suivante déclenche la fonction de démarrage rapide `MyHttpTrigger` à partir d’une demande GET avec le paramètre _name_passé dans la chaîne de requête.

```bash
curl --get http://localhost:7071/api/MyHttpTrigger?name=Azure%20Rocks
```

L’exemple suivant est la même fonction appelée à partir d’une demande POST en passant _name_ dans le corps de la demande :

```bash
curl --request POST http://localhost:7071/api/MyHttpTrigger --data '{"name":"Azure Rocks"}'
```

Notez que vous pouvez passer des requêtes GET depuis un navigateur en passant des données dans la chaîne de requêtes. Pour toutes les autres méthodes HTTP, vous devez utiliser cURL, Fiddler, Postman ou un outil de test HTTP similaire.

#### <a name="non-http-triggered-functions"></a>Fonctions non déclenchées via HTTP

Pour tous les types de fonctions autres que les déclencheurs et Webhooks HTTP, vous pouvez tester vos fonctions localement en appelant un point de terminaison d’administration. L’appel de ce point de terminaison au moyen d’une requête HTTP POST sur le serveur local déclenche la fonction. Vous pouvez éventuellement passer des données de test à l’exécution dans le corps de la requête POST. Cette fonctionnalité est similaire à l’onglet **Test** dans le portail Azure.

Vous appelez le point de terminaison d’administrateur suivant pour déclencher des fonctions non-HTTP :

    http://localhost:{port}/admin/functions/{function_name}

Pour passer des données de test au point de terminaison d’administrateur d’une fonction, vous devez fournir les données dans le corps d’un message de requête POST. Le corps du message doit avoir le format JSON suivant :

```JSON
{
    "input": "<trigger_input>"
}
```

La valeur de `<trigger_input>` contient des données dans un format attendu par la fonction. L’exemple cURL suivant est une demande POST adressée à une fonction `QueueTriggerJS`. Dans ce cas, l’entrée est une chaîne qui est équivalente au message attendu dans la file d’attente.

```bash
curl --request POST -H "Content-Type:application/json" --data '{"input":"sample queue data"}' http://localhost:7071/admin/functions/QueueTriggerJS
```

#### <a name="using-the-func-run-command-in-version-1x"></a>Utilisation de la commande `func run` dans la version 1.x

>[!IMPORTANT]
> La commande `func run` n’est pas prise en charge dans la version 2.x des outils. Pour plus d’informations, consultez la rubrique [Comment cibler des versions du runtime Azure Functions](set-runtime-version.md).

Vous pouvez également appeler une fonction directement à l’aide de `func run <FunctionName>` et fournir des données d’entrée pour la fonction. Cette commande est similaire à l’exécution d’une fonction à l’aide de l’onglet **Test** dans le portail Azure.

`func run` prend en charge les options suivantes :

| Option     | Description                            |
| ------------ | -------------------------------------- |
| **`--content -c`** | Contenu inclus. |
| **`--debug -d`** | Joindre un débogueur au processus hôte avant d’exécuter la fonction.|
| **`--timeout -t`** | Délai d’attente (en secondes) jusqu’à ce que l’hôte Functions local soit prêt.|
| **`--file -f`** | Nom du fichier à utiliser en tant que contenu.|
| **`--no-interactive`** | Ne pas demander d’entrée. Utile pour les scénarios d’automatisation.|

Par exemple, pour appeler une fonction déclenchée par HTTP et passer un corps de contenu, exécutez la commande suivante :

```bash
func run MyHttpTrigger -c '{\"name\": \"Azure\"}'
```

## <a name="publish"></a>Publication dans Azure

Azure Functions Core Tools prend en charge deux types de déploiement : le déploiement des fichiers projet de fonction directement dans votre application de fonction via [Zip Deploy](functions-deployment-technologies.md#zip-deploy), et le [déploiement d’un conteneur Docker personnalisé](functions-deployment-technologies.md#docker-container). Vous devez avoir déjà [créé une application de fonction dans votre abonnement Azure](functions-cli-samples.md#create) où vous prévoyez de déployer votre code. Les projets qui nécessitent une compilation doivent être générés pour favoriser le déploiements des fichiers binaires.

Un dossier de projet peut contenir des fichiers et des répertoires spécifiques à une langue qui ne doivent pas être publiés. Les éléments exclus sont listés dans un fichier .funcignore situé dans le dossier racine du projet.     

### <a name="project-file-deployment"></a>Déploiement (fichiers projet)

Pour publier votre code local dans une application de fonction sur Azure, utilisez la commande `publish` :

```bash
func azure functionapp publish <FunctionAppName>
```

Cette commande publie du contenu vers une application de fonction existante dans Azure. Vous obtiendrez une erreur si vous tentez de publier sur une application `<FunctionAppName>` qui n’existe pas dans votre abonnement. Pour découvrir comment créer une application de fonction à partir de l’invite de commandes ou d’une fenêtre de terminal à l’aide d’Azure CLI, consultez [Créer une application de fonction pour une exécution sans serveur](./scripts/functions-cli-create-serverless.md). Par défaut, cette commande utilise la [build distante](functions-deployment-technologies.md#remote-build) et déploie votre application pour [une exécution à partir du package de déploiement](run-functions-from-deployment-package.md). Pour désactiver ce mode de déploiement recommandé, utilisez l'option `--nozip`.

>[!IMPORTANT]
> Lorsque vous créez une application de fonction dans le portail Azure, elle utilise par défaut la version 2.x du runtime de Function. Pour que l’application de fonction utilise la version 1.x du runtime, suivez les instructions dans [Exécution sur la version 1.x](functions-versions.md#creating-1x-apps).
> Vous ne pouvez pas modifier la version du runtime pour une application de fonction qui possède des fonctions déjà existantes.

Les options de publication suivantes s’appliquent aux versions 1.x et 2.x :

| Option     | Description                            |
| ------------ | -------------------------------------- |
| **`--publish-local-settings -i`** |  Publier dans Azure les paramètres figurant dans local.settings.json, avec demande de confirmation du remplacement si le paramètre existe déjà. Si vous utilisez l’Émulateur de stockage Microsoft Azure, commencez par changer le paramètre d’application en choisissant une [connexion de stockage réelle](#get-your-storage-connection-strings). |
| **`--overwrite-settings -y`** | Supprimer l’invite de remplacement des paramètres de l’application lorsque `--publish-local-settings -i` est utilisé.|

Les options de publication suivantes sont uniquement prises en charge dans la version 2.x :

| Option     | Description                            |
| ------------ | -------------------------------------- |
| **`--publish-settings-only -o`** |  Publiez les paramètres uniquement et ignorez le contenu. Par défaut, l’accord de l’utilisateur est sollicité. |
|**`--list-ignored-files`** | Affiche une liste de fichiers ignorés lors de la publication basée sur le fichier .funcignore. |
| **`--list-included-files`** | Affiche une liste de fichiers publiés basée sur le fichier .funcignore. |
| **`--nozip`** | Désactive le mode par défaut `Run-From-Package`. |
| **`--build-native-deps`** | Ignore la création du dossier .wheels dossier lors de la publication des applications de fonction python. |
| **`--build`**<br/>**`-b`** | Exécute l’action de génération lors du déploiement dans une application de fonction Linux. Accepte : `remote` et `local`. |
| **`--additional-packages`** | Liste des packages à installer lors de la création des dépendances natives. Par exemple : `python3-dev libevent-dev`. |
| **`--force`** | Ignorez la vérification de prépublication dans certains scénarios. |
| **`--csx`** | Publiez un projet de Script C# (.csx). |
| **`--no-build`** | Ne générez pas de fonctions de bibliothèque de classes .NET. |
| **`--dotnet-cli-params`** | Si les fonctions C# (.csproj) sont compilées lors de la publication, Core Tools appelle « dotnet build --output bin/publish ». Tous les paramètres transmis seront ajoutés à la ligne de commande. |

### <a name="deployment-custom-container"></a>Déploiement (conteneur personnalisé)

Azure Functions vous permet de déployer un projet de fonction dans un [conteneur Docker personnalisé](functions-deployment-technologies.md#docker-container). Pour plus d’informations, consultez [Créer une fonction sur Linux en utilisant une image personnalisée](functions-create-function-linux-custom-image.md). Les conteneurs personnalisés doivent contenir un fichier Dockerfile. Pour créer une application avec un fichier Dockerfile, utilisez l’option --dockerfile sur `func init`.

```bash
func deploy
```

Les options de déploiement de conteneur personnalisées suivantes sont disponibles :

| Option     | Description                            |
| ------------ | -------------------------------------- |
| **`--registry`** | Le nom d’un registre Docker auquel l’utilisateur actuel est connecté. |
| **`--platform`** | Plateforme d’hébergement pour l’application de fonction. Les options valides sont `kubernetes` |
| **`--name`** | Nom de l’application de fonction. |
| **`--max`**  | Le cas échéant, définit le nombre maximal d’instances d’application de fonction à déployer. |
| **`--min`**  | Le cas échéant, définit le nombre minimal d’instances d’application de fonction à déployer. |
| **`--config`** | Définit un fichier de configuration de déploiement optionnel. |

## <a name="monitoring-functions"></a>Surveillance des fonctions

Il est recommandé de superviser l’exécution de vos fonctions par l’intégration à Azure Application Insights. Vous pouvez également diffuser des journaux d’exécution sur votre ordinateur local. Pour en savoir plus, consultez [Surveiller l’exécution des fonctions Azure](functions-monitoring.md).

### <a name="application-insights-integration"></a>Intégration d’Application Insights

L’intégration d’Application Insights doit être activée lorsque vous créez votre application de fonction dans Azure. Si, pour une raison quelconque, votre application de fonction n’est pas connectée à une instance Application Insights, il est facile d’effectuer cette intégration dans le portail Azure. 

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

### <a name="enable-streaming-logs"></a>Activer les journaux de diffusion en continu

Vous pouvez afficher un flux de fichiers journaux générés par vos fonctions dans une session de ligne de commande sur votre ordinateur local. 

#### <a name="native-streaming-logs"></a>Journaux de diffusion en continu natifs

[!INCLUDE [functions-streaming-logs-core-tools](../../includes/functions-streaming-logs-core-tools.md)]

Les journaux de diffusion en continu de ce type nécessitent que l’intégration d’Application Insights soit activée pour votre application de fonction.   


## <a name="next-steps"></a>Étapes suivantes

Découvrez comment développer, tester et publier des fonctions Azure en utilisant Azure Functions Core Tools [module Microsoft Learn](https://docs.microsoft.com/learn/modules/develop-test-deploy-azure-functions-with-core-tools/). Azure Functions Core Tools est [open source et hébergé sur GitHub](https://github.com/azure/azure-functions-cli).  
Pour enregistrer un bogue ou une demande de fonctionnalité, [créez un problème GitHub](https://github.com/azure/azure-functions-cli/issues).

<!-- LINKS -->

[Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure portal]: https://portal.azure.com 
[Node.JS]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
[`FUNCTIONS_WORKER_RUNTIME`]: functions-app-settings.md#functions_worker_runtime
[AzureWebJobsStorage]: functions-app-settings.md#azurewebjobsstorage
[Bundles d’extension]: functions-bindings-register.md#extension-bundles
