---
title: Utilisation d’Azure Functions Core Tools | Microsoft Docs
description: Apprenez à coder et à tester des fonctions Azure à partir de l’invite de commandes ou du terminal sur votre ordinateur local avant de les exécuter dans Azure Functions.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: article
ms.date: 06/26/2018
ms.author: glenga
ms.openlocfilehash: 57011e1f7633688e00a4639ba36fd4442073161d
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/08/2018
ms.locfileid: "39618612"
---
# <a name="work-with-azure-functions-core-tools"></a>Utiliser Azure Functions Core Tools

Azure Functions Core Tools vous permet de développer et de tester vos fonctions sur votre ordinateur local à partir de l’invite de commandes ou du terminal. Vos fonctions locales peuvent être connectées aux services Azure actifs, et vous pouvez déboguer vos fonctions sur votre ordinateur local à l’aide du runtime Functions complet. Vous pouvez même déployer une application de fonction sur votre abonnement Azure.

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

## <a name="core-tools-versions"></a>Versions de Core Tools

Il existe deux versions d’Azure Functions Core Tools. La version que vous utilisez dépend de votre environnement de développement local, du choix du langage et du niveau de prise en charge requis :

+ [Version 1.x](#v1) : prend en charge la version 1.x du runtime, qui est généralement disponible (Generally Available, GA). Cette version des outils est uniquement prise en charge sur les ordinateurs Windows et est installée à partir d’un [package npm](https://docs.npmjs.com/getting-started/what-is-npm). Avec cette version, vous pouvez créer des fonctions dans les langages expérimentaux, non officiellement pris en charge. Pour en savoir plus, consultez la page [Langages pris en charge dans Azure Functions](supported-languages.md).

+ [Version 2.x](#v2) : prend en charge la [version 2.x du runtime](functions-versions.md). Cette version prend en charge [Windows](#windows-npm), [macOS](#brew) et [Linux](#linux). Utilise des gestionnaires de packages ou npm spécifiques à la plateforme pour l’installation. Comme le runtime 2.x, cette version des outils principaux est une préversion. 

Sauf indication contraire, les exemples de cet article concernent la version 2.x. Pour recevoir les mises à jour importantes sur la version 2.x, y compris les annonces relatives aux changements cassants, reportez-vous au référentiel des [annonces Azure App Service](https://github.com/Azure/app-service-announcements/issues).

## <a name="install-the-azure-functions-core-tools"></a>Installer Azure Functions Core Tools

[Azure Functions Core Tools] inclut une version du même runtime qu’Azure Functions, que vous pouvez exécuter sur votre ordinateur de développement local. Il fournit également des commandes pour créer des fonctions, se connecter à Azure et déployer des projets de fonction.

### <a name="v1"></a>Version 1.x

La version d’origine des outils utilise le runtime de Functions 1.x. Cette version utilise .NET Framework (4.7.1) et n’est prise en charge que sur les ordinateurs Windows. Avant d’installer les outils de la version 1.x, vous devez [installer NodeJS](https://docs.npmjs.com/getting-started/installing-node), qui inclut npm.

Pour installer les outils de la version 1.x, utilisez la commande suivante :

```bash
npm install -g azure-functions-core-tools
```

### <a name="v2"></a>Version 2.x

>[!NOTE]
> Le runtime d’Azure Functions 2.0 est en préversion ; pour le moment, les fonctionnalités d’Azure Functions ne sont pas toutes prises en charge. Pour plus d’informations, consultez [Versions Azure Functions](functions-versions.md) 

La version 2.x des outils utilise le runtime d’Azure Functions 2.x qui repose sur .NET Core. Cette version est prise en charge sur tous les supports des plateformes .NET Core 2.x, y compris [Windows](#windows-npm), [macOS](#brew) et [Linux](#linux).

#### <a name="windows-npm"></a>Windows

Les étapes suivantes utilisent npm pour installer les outils Core sur Windows. Vous pouvez également utiliser [Chocolatey](https://chocolatey.org/). Pour plus d’informations, consultez le [fichier Lisez-moi Outils Core](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#windows).

1. Installez [.NET Core 2.1 pour Windows](https://www.microsoft.com/net/download/windows).

2. Installez [Node.js], qui inclut npm. Pour la version 2.x des outils, seuls Node.js 8.5 et les versions ultérieures sont pris en charge.

3. Installez le package Outils Core :

    ```bash
    npm install -g azure-functions-core-tools@core
    ```

#### <a name="brew"></a>MacOS avec Homebrew

Les étapes suivantes utilisent Homebrew pour installer les Outils Core sur macOS.

1. Installez [.NET Core 2.1 pour macOS](https://www.microsoft.com/net/download/macos).

2. Installez [Homebrew](https://brew.sh/), s’il n’est pas déjà installé.

3. Installez le package Outils Core :

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools 
    ```

#### <a name="linux"></a> Linux (Ubuntu/Debian) avec APT

Les étapes suivantes utilisent [APT](https://wiki.debian.org/Apt) pour installer les Outils Core sur votre distribution Ubuntu/Debian Linux. Pour les autres distributions Linux, consultez le [fichier Lisez-moi Outils Core](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#linux).

1. Installez [.NET Core 2.1 pour Linux](https://www.microsoft.com/net/download/linux).

2. Enregistrez la clé de produit de Microsoft comme approuvé :

    ```bash
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
    ```

3. Vérifiez que votre serveur Ubuntu exécute une des versions appropriées figurant dans le tableau ci-dessous. Pour ajouter la source apt, exécutez :

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-$(lsb_release -cs)-prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    sudo apt-get update
    ```

    | Distribution Linux | Version |
    | --------------- | ----------- |
    | Ubuntu 17.10    | `artful`    |
    | Ubuntu 17.04    | `zesty`     |
    | Ubuntu 16.04/Linux Mint 18    | `xenial`  |

4. Installez le package Outils Core :

    ```bash
    sudo apt-get install azure-functions-core-tools
    ```

## <a name="create-a-local-functions-project"></a>Créer un projet Functions local

Un répertoire de projet de fonctions contient les fichiers [host.json](functions-host-json.md) et [local.settings.json](#local-settings-file), ainsi que des sous-dossiers qui contiennent le code des fonctions individuelles. Ce répertoire est l’équivalent d’une application de fonction dans Azure. Pour en savoir plus sur la structure de dossiers Functions, consultez le [Guide de développement Azure Functions](functions-reference.md#folder-structure).

Pour la version 2.x, vous devez sélectionner un langage par défaut pour votre projet lors de son initialisation, et toutes les fonctions ajoutées utilisent les modèles de langage par défaut. Dans la version 1.x, vous spécifiez le langage à chaque fois que vous créez une fonction.

Dans la fenêtre du terminal ou à partir d’une invite de commandes, exécutez la commande suivante pour créer le projet et le référentiel Git local :

```bash
func init MyFunctionProj
```

Dans la version 2.x, lorsque vous exécutez la commande, vous devez choisir un runtime pour votre projet. Si vous envisagez de développer des fonctions JavaScript, choisissez **nœud** :

```output
Select a worker runtime:
dotnet
node
java
```

Utilisez les touches de direction haut/bas pour choisir un langage, puis appuyez sur Entrée. Le résultat ressemble à l’exemple suivant pour un projet JavaScript :

```output
Select a worker runtime: node
Writing .gitignore
Writing host.json
Writing local.settings.json
Writing C:\myfunctions\myMyFunctionProj\.vscode\extensions.json
Initialized empty Git repository in C:/myfunctions/myMyFunctionProj/.git/
```

Pour créer le projet sans référentiel Git local, utilisez l’option `--no-source-control [-n]`.

> [!IMPORTANT]
> Par défaut, la version 2.x des outils Core crée les projets d’application de fonctions pour le runtime .NET en tant que [projets de classes C#](functions-dotnet-class-library.md) (.csproj). Ces projets C#, qui peuvent être utilisés avec Visual Studio 2017 ou Visual Studio Code, sont compilés pendant les tests et lors de la publication sur Azure. Si vous voulez plutôt créer et utiliser les mêmes fichiers de script C# (.csx) que ceux créés dans la version 1.x et dans le portail, vous devez inclure le paramètre `--csx` quand vous créez et que vous déployez des fonctions.

## <a name="register-extensions"></a>Inscrire des extensions

Dans la version 2.x du runtime Azure Functions, vous devez inscrire explicitement les extensions de liaison (types de liaison) que vous utilisez dans votre application de fonction.

[!INCLUDE [Register extensions](../../includes/functions-core-tools-install-extension.md)]

Pour plus d’informations, consultez [Concepts des déclencheurs et liaisons Azure Functions](functions-triggers-bindings.md#register-binding-extensions).

## <a name="local-settings-file"></a>Fichier de paramètres locaux

Le fichier local.settings.json stocke les paramètres de l’application, les chaînes de connexion et les paramètres d’Azure Functions Core Tools. Sa structure est la suivante :

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "<connection-string>",
    "AzureWebJobsDashboard": "<connection-string>",
    "MyBindingConnection": "<binding-connection-string>"
  },
  "Host": {
    "LocalHttpPort": 7071,
    "CORS": "*"
  },
  "ConnectionStrings": {
    "SQLConnectionString": "<sqlclient-connection-string>"
  }
}
```

| Paramètre      | Description                            |
| ------------ | -------------------------------------- |
| **IsEncrypted** | Lorsque la valeur est définie sur **true**, toutes les valeurs sont chiffrées à l’aide d’une clé d’ordinateur local. Utilisé avec les commandes `func settings`. La valeur par défaut est **false**. |
| **Valeurs** | Collection des paramètres d’application et des chaînes de connexion utilisés lors de l’exécution locale. Ces valeurs correspondent aux paramètres d’application de votre application de fonction dans Azure, par exemple **AzureWebJobsStorage** et **AzureWebJobsDashboard**. Plusieurs déclencheurs et liaisons ont une propriété qui fait référence à un paramètre d’application de chaîne de connexion, par exemple **Connexion** pour le [déclencheur de stockage blob](functions-bindings-storage-blob.md#trigger---configuration). Pour ces propriétés, vous avez besoin d’un paramètre d’application défini dans le tableau **Valeurs**. <br/>**AzureWebJobsStorage** est un paramètre d’application requis pour les déclencheurs autres que HTTP. Lorsque vous avez installé l’[émulateur de stockage Azure](../storage/common/storage-use-emulator.md) localement, vous pouvez définir **AzureWebJobsStorage** sur `UseDevelopmentStorage=true`, et Core Tools utilise alors l’émulateur. Cette configuration est utile lors du développement, mais vous devez la tester avec une connexion de stockage réelle avant le déploiement. |
| **Hôte** | Les paramètres de cette section personnalisent le processus hôte Functions lors de l’exécution locale. |
| **LocalHttpPort** | Définit le port par défaut utilisé lors de l’exécution de l’hôte Functions local (`func host start` et `func run`). L’option de ligne de commande `--port` est prioritaire sur cette valeur. |
| **CORS** | Définit les origines autorisées pour [cross-origin resource sharing (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). Les origines sont fournies sous la forme d’une liste séparée par des virgules, sans espaces. La valeur de caractère générique (\*) est prise en charge, ce qui autorise les demandes à partir de toute origine. |
| **ConnectionStrings** | N’utilisez pas cette collection pour les chaînes de connexion utilisées par vos liaisons de fonction. Cette collection est utilisée seulement par les frameworks qui obtiennent généralement les chaînes de connexion à partir de la section **ConnectionStrings** d’un fichier de configuration, comme [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). Les chaînes de connexion dans cet objet sont ajoutées à l’environnement avec le type de fournisseur [System.Data.SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx). Les éléments de cette collection ne sont pas publiés sur Azure avec d’autres paramètres d’application. Vous devez explicitement ajouter ces valeurs à la collection **Chaînes de connexion** des paramètres d’application de fonction. Si vous créez une [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) dans votre code de fonction, vous devez stocker la valeur de la chaîne de connexion dans les **Paramètres d’application** avec vos autres connexions. |

Ces valeurs de paramètres d’application de fonction peuvent aussi être lues dans votre code en tant que variables d’environnement. Pour plus d’informations, consultez la section Variables d’environnement de ces rubriques de référence spécifiques à une langue :

+ [C# précompilé](functions-dotnet-class-library.md#environment-variables)
+ [Script C# (.csx)](functions-reference-csharp.md#environment-variables)
+ [F#](functions-reference-fsharp.md#environment-variables)
+ [Java](functions-reference-java.md#environment-variables) 
+ [JavaScript](functions-reference-node.md#environment-variables)

Les paramètres dans le fichier local.settings.json sont uniquement utilisés par les outils Functions lors de l’exécution locale. Par défaut, ces paramètres ne sont pas migrés automatiquement lorsque le projet est publié dans Azure. Utilisez le commutateur `--publish-local-settings` [lors de la publication](#publish) pour vous assurer que ces paramètres sont ajoutés à l’application de fonction dans Azure. Les valeurs dans **ConnectionStrings** ne sont jamais publiées.

Si aucune chaîne de connexion de stockage valide n’est définie pour **AzureWebJobsStorage** et que l’émulateur n’est pas utilisé, le message d’erreur suivant s’affiche :  

>Valeur manquante pour AzureWebJobsStorage dans local.settings.json. Cette valeur est nécessaire pour tous les déclencheurs autres que HTTP. Vous pouvez exécuter 'func azure functionapp fetch-app-settings <functionAppName>' ou spécifier une chaîne de connexion dans local.settings.json.

### <a name="get-your-storage-connection-strings"></a>Obtenir vos chaînes de connexion de stockage

Même si vous utilisez l’émulateur de stockage pour le développement, vous pouvez tester votre configuration avec une connexion de stockage réelle. Si vous avez déjà [créé un compte de stockage](../storage/common/storage-create-storage-account.md), vous pouvez obtenir une chaîne de connexion de stockage valide de l’une des manières suivantes :

+ À partir du [portail Azure]. Accédez à votre compte de stockage, sélectionnez **Clés d’accès** dans **Paramètres**, puis copiez une des valeurs **Chaîne de connexion**.

  ![Copier une chaîne de connexion à partir du portail Azure](./media/functions-run-local/copy-storage-connection-portal.png)

+ Utilisez l’[Explorateur Stockage Azure](http://storageexplorer.com/) pour vous connecter à votre compte Azure. Dans l’**Explorateur**, développez votre abonnement, sélectionnez votre compte de stockage et copiez la chaîne de connexion principale ou secondaire. 

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
| **`--language -l`**| Langage de programmation du modèle, tel que C#, F# ou JavaScript. Cette option est requise dans la version 1.x. Dans la version 2.x, n’utilisez pas cette option ou choisissez le langage par défaut de votre projet. |
| **`--template -t`** | Utilisez la commande `func templates list` pour afficher la liste complète des modèles disponibles pour chaque langage pris en charge.   |
| **`--name -n`** | Nom de la fonction. |
| **`--csx`** | (Version 2.x) Génère les mêmes modèles de script C# (.csx) que ceux utilisés dans la version 1.x et dans le portail. |

Par exemple, pour créer un déclencheur HTTP JavaScript dans une seule commande, exécutez :

```bash
func new --template "Http Trigger" --name MyHttpTrigger
```

Pour créer une fonction de déclencheur par file d’attente, exécutez :

```bash
func new --template "Queue Trigger" --name QueueTriggerJS
```

## <a name="start"></a>Exécuter des fonctions localement

Pour exécuter un projet Functions, exécutez l’hôte Functions. L’hôte active les déclencheurs pour toutes les fonctions du projet :

```bash
func host start
```

`func host start` prend en charge les options suivantes :

| Option     | Description                            |
| ------------ | -------------------------------------- |
|**`--port -p`** | Port local à écouter. Valeur par défaut : 7071. |
| **`--debug <type>`** | Démarrage de l’hôte avec le port de débogage ouvert afin que vous puissiez associer le processus **func.exe** à partir de [Visual Studio Code](https://code.visualstudio.com/tutorials/functions-extension/getting-started) ou [Visual Studio 2017](functions-dotnet-class-library.md). Les options *\<type\>* sont `VSCode` et `VS`.  |
| **`--cors`** | Liste séparée par des virgules d’origines CORS, sans espaces. |
| **`--nodeDebugPort -n`** | Port du débogueur de nœud à utiliser. Valeur par défaut : une valeur issue de launch.json ou 5858. |
| **`--debugLevel -d`** | Niveau de trace de la console (off, verbose, info, warning ou error). Valeur par défaut : info.|
| **`--timeout -t`** | Délai d’expiration pour le démarrage de l’hôte Functions, en secondes. Valeur par défaut : 20 secondes.|
| **`--useHttps`** | Liaison avec `https://localhost:{port}` plutôt que `http://localhost:{port}`. Par défaut, cette option crée un certificat de confiance sur votre ordinateur.|
| **`--pause-on-error`** | Marquage d’une pause pour des entrées supplémentaires avant de quitter le processus. Utilisation lors du lancement de Core Tools dans Visual Studio ou VS Code.|

Quand l’hôte Functions démarre, il génère l’URL des fonctions déclenchées par HTTP :

```bash
Found the following functions:
Host.Functions.MyHttpTrigger

Job host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
```

### <a name="passing-test-data-to-a-function"></a>Transmission de données de test à une fonction

Pour tester vos fonctions localement, vous [démarrez l’hôte Functions](#start) et vous appelez des points de terminaison sur le serveur local avec des requêtes HTTP. Le point de terminaison que vous appelez varie selon le type de fonction.

>[!NOTE]  
> Les exemples de cette rubrique utilisent l’outil cURL pour envoyer des requêtes HTTP à partir du terminal ou d’une invite de commandes. Vous pouvez utiliser un outil de votre choix pour envoyer les requêtes HTTP au serveur local. L’outil cURL est disponible par défaut sur les systèmes Linux. Sous Windows, vous devez d’abord télécharger et installer [l’outil cURL](https://curl.haxx.se/).

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
````

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

### <a name="viewing-log-files-locally"></a>Consultation locale des fichiers journaux

[!INCLUDE [functions-local-logs-location](../../includes/functions-local-logs-location.md)]

## <a name="publish"></a>Publication dans Azure

Pour publier un projet Functions dans une application de fonction au sein d’Azure, utilisez la commande `publish` :

```bash
func azure functionapp publish <FunctionAppName>
```

Vous pouvez utiliser les options suivantes :

| Option     | Description                            |
| ------------ | -------------------------------------- |
| **`--publish-local-settings -i`** |  Publier dans Azure les paramètres figurant dans local.settings.json, avec demande de confirmation du remplacement si le paramètre existe déjà. Si vous utilisez l’émulateur de stockage, vous modifiez le paramètre d’application en choisissant une [connexion de stockage réelle](#get-your-storage-connection-strings). |
| **`--overwrite-settings -y`** | Doit être utilisé avec `-i`. Remplace les paramètres d’application dans Azure par la valeur locale s’ils sont différents. Par défaut, l’accord de l’utilisateur est sollicité.|

Cette commande publie du contenu vers une application de fonction existante dans Azure. Une erreur se produit si le `<FunctionAppName>` n’existe pas dans votre abonnement. Pour découvrir comment créer une application de fonction à partir de l’invite de commandes ou d’une fenêtre de terminal à l’aide d’Azure CLI, consultez [Créer une application de fonction pour une exécution sans serveur](./scripts/functions-cli-create-serverless.md).

La commande `publish` charge le contenu du répertoire du projet Functions. Si vous supprimez les fichiers localement, la commande `publish` ne les supprime pas d’Azure. Vous pouvez supprimer des fichiers dans Azure à l’aide de [l’outil Kudu](functions-how-to-use-azure-function-app-settings.md#kudu) dans le [portail Azure].  

>[!IMPORTANT]  
> Lorsque vous créez une application de fonction dans Azure, elle utilise par défaut la version 1.x du runtime de Function. Pour que l’application de fonction utilise la version 2.x du runtime, ajoutez le paramètre d’application `FUNCTIONS_EXTENSION_VERSION=beta`.  
Le code d’Azure CLI suivant permet d’ajouter ce paramètre à votre application de fonction :

```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group myResourceGroup \
--settings FUNCTIONS_EXTENSION_VERSION=beta   
```

## <a name="next-steps"></a>Étapes suivantes

Azure Functions Core Tools est [disponible en open source et hébergé sur GitHub](https://github.com/azure/azure-functions-cli).  
Pour enregistrer un bogue ou une demande de fonctionnalité, [créez un problème GitHub](https://github.com/azure/azure-functions-cli/issues).

<!-- LINKS -->

[Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools
[portail Azure]: https://portal.azure.com 
[Node.JS]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
