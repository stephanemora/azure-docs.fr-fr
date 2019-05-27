---
title: Référence du développeur PowerShell pour Azure Functions
description: Découvrez comment développer des fonctions à l’aide de PowerShell.
services: functions
documentationcenter: na
author: tylerleonhardt
manager: jeconnoc
ms.service: azure-functions
ms.devlang: powershell
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: tyleonha, glenga
ms.openlocfilehash: 46b1e5c99dd86fed6f87ac3b8f0ff6555187899b
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65833518"
---
# <a name="azure-functions-powershell-developer-guide"></a>Guide du développeur Azure fonctions PowerShell

Cet article fournit des détails sur la façon dont vous écrivez des fonctions Azure à l’aide de PowerShell.

[!INCLUDE [functions-powershell-preview-note](../../includes/functions-powershell-preview-note.md)]

Une fonction PowerShell Azure (fonction) est représentée comme un script PowerShell qui s’exécute lorsque déclenchée. Chaque script de la fonction associée à une `function.json` fichier qui définit comment la fonction se comporte, telles que la façon dont elle est déclenchée et ses paramètres d’entrée et de sortie. Pour plus d’informations, consultez le [déclencheurs et article de la liaison](functions-triggers-bindings.md). 

Comme d’autres types de fonctions, les fonctions de script PowerShell prennent de paramètres correspondant aux noms de toutes les liaisons d’entrée définis dans le `function.json` fichier. Un `TriggerMetadata` paramètre est également passé qui contient des informations supplémentaires sur le déclencheur qui a démarré la fonction.

Cet article suppose que vous ayez déjà lu l’article [Informations de référence pour les développeurs sur Azure Functions](functions-reference.md). Vous devez également avoir terminé la [fonctions Guide de démarrage rapide pour PowerShell](functions-create-first-function-powershell.md) à créer votre première fonction PowerShell.

## <a name="folder-structure"></a>Structure de dossiers

La structure de dossiers requis pour un projet de PowerShell est similaire à celui-ci. Cette valeur par défaut peut être modifiée. Pour plus d’informations, consultez la section [scriptFile](#configure-function-scriptfile)ci-dessous.

```
PSFunctionApp
 | - MyFirstFunction
 | | - run.ps1
 | | - function.json
 | - MySecondFunction
 | | - run.ps1
 | | - function.json
 | - Modules
 | | - myFirstHelperModule
 | | | - myFirstHelperModule.psd1
 | | | - myFirstHelperModule.psm1
 | | - mySecondHelperModule
 | | | - mySecondHelperModule.psd1
 | | | - mySecondHelperModule.psm1
 | - local.settings.json
 | - host.json
 | - requirements.psd1
 | - profile.ps1
 | - extensions.csproj
 | - bin
```

À la racine du projet, il est une connexion partagée [ `host.json` ](functions-host-json.md) fichier qui peut être utilisé pour configurer l’application de fonction. Chaque fonction a un dossier avec son propre fichier de code (.ps1) et le fichier de configuration de liaison (`function.json`). Le nom du répertoire de parent du fichier function.json est toujours le nom de votre fonction.

Certaines liaisons nécessitent la présence d’un `extensions.csproj` fichier. Requis dans les extensions de liaison [version 2.x](functions-versions.md) du runtime Functions, sont définies dans le `extensions.csproj` fichier, avec les fichiers de bibliothèque réelles dans le `bin` dossier. Quand vous développez localement, vous devez [inscrire les extensions de liaison](functions-bindings-register.md#local-development-with-azure-functions-core-tools-and-extension-bundles). Quand vous développez des fonctions dans le portail Azure, cet enregistrement est effectué pour vous.

Dans les applications de fonction PowerShell, vous devrez éventuellement un `profile.ps1` qui s’exécute lorsqu’une application de fonction commence à s’exécuter (sinon connu sous le nom un  *[démarrage à froid](#cold-start)*. Pour plus d’informations, consultez [profil PowerShell](#powershell-profile).

## <a name="defining-a-powershell-script-as-a-function"></a>Définition d’un script PowerShell en tant que fonction

Par défaut, le runtime Functions recherche votre fonction dans `run.ps1`, où `run.ps1` partage le même répertoire parent en tant que `function.json` correspondant.

Votre script est passé un nombre d’arguments lors de l’exécution. Pour gérer ces paramètres, ajoutez un `param` bloc vers le haut de votre script, comme dans l’exemple suivant :

```powershell
# $TriggerMetadata is optional here. If you don't need it, you can safely remove it from the param block
param($MyFirstInputBinding, $MySecondInputBinding, $TriggerMetadata)
```

### <a name="triggermetadata-parameter"></a>Paramètre de TriggerMetadata

Le `TriggerMetadata` paramètre est utilisé pour fournir des informations supplémentaires sur le déclencheur. Les métadonnées supplémentaires varient à partir d’une liaison à une liaison, mais ils contiennent tous un `sys` propriété qui contient les données suivantes :

```powershell
$TriggerMetadata.sys
```

| Propriété   | Description                                     | Type     |
|------------|-------------------------------------------------|----------|
| UtcNow     | Quand, au format UTC, la fonction a été déclenchée        | DateTime |
| MethodName | Le nom de la fonction qui a été déclenchée     | string   |
| RandGuid   | un guid unique pour cette exécution de la fonction | string   |

Chaque type de déclencheur a un autre ensemble de métadonnées. Par exemple, le `$TriggerMetadata` pour `QueueTrigger` contient le `InsertionTime`, `Id`, `DequeueCount`, entre autres choses. Pour plus d’informations sur les métadonnées du déclencheur de file d’attente, accédez à la [documentation officielle pour les déclencheurs de file d’attente](functions-bindings-storage-queue.md#trigger---message-metadata). Consultez la documentation sur le [déclencheurs](functions-triggers-bindings.md) vous travaillez pour voir ce qui est fourni dans les métadonnées de déclencheur.

## <a name="bindings"></a>Liaisons

Dans PowerShell, [liaisons](functions-triggers-bindings.md) sont configurées et définies dans le fichier function.json d’une fonction. Les fonctions interagissent avec des liaisons de plusieurs façons.

### <a name="reading-trigger-and-input-data"></a>Lecture du déclencheur et données d’entrée

Déclencheur et des liaisons d’entrée sont lues en tant que paramètres transmis à votre fonction. Les liaisons d’entrée ont un `direction` la valeur `in` dans function.json. Le `name` propriété définie dans `function.json` correspond au nom du paramètre, dans le `param` bloc. Étant donné que PowerShell utilise des paramètres nommés pour la liaison, peu importe l’ordre des paramètres. Toutefois, il est recommandé de suivre l’ordre des liaisons définies dans le `function.json`.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)
```

### <a name="writing-output-data"></a>Écriture des données de sortie

Dans les fonctions, une liaison de sortie a un `direction` la valeur `out` dans le fichier function.json. Vous pouvez écrire à une liaison de sortie en utilisant le `Push-OutputBinding` applet de commande, qui est disponible pour le runtime Functions. Dans tous les cas, le `name` propriété de la liaison, tel que défini dans `function.json` correspond à la `Name` paramètre de la `Push-OutputBinding` applet de commande.

L’exemple suivant montre comment appeler `Push-OutputBinding` dans votre script de fonction :

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Push-OutputBinding -Name myQueue -Value $myValue
```

Vous pouvez également passer une valeur pour une liaison spécifique à travers le pipeline.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Produce-MyOutputValue | Push-OutputBinding -Name myQueue
```

`Push-OutputBinding` se comporte différemment selon la valeur spécifiée pour `-Name`:

* Lorsque le nom spécifié ne peut pas être résolu à une liaison de sortie valide, une erreur est levée.

* Lorsque la liaison de sortie accepte une collection de valeurs, vous pouvez appeler `Push-OutputBinding` à plusieurs reprises pour envoyer plusieurs valeurs.

* Quand la liaison de sortie accepte uniquement une valeur singleton, l’appel `Push-OutputBinding` une deuxième fois génère une erreur.

#### <a name="push-outputbinding-syntax"></a>`Push-OutputBinding` Syntaxe

Les éléments suivants sont des paramètres valides pour appeler `Push-OutputBinding`:

| Nom | Type | Position | Description  |
| ---- | ---- |  -------- | ----------- |
| **`-Name`** | String | 1 | Le nom de la liaison de sortie que vous souhaitez définir. |
| **`-Value`** | Object | 2 | La valeur de la liaison de sortie vous souhaitez définir, qui est accepté depuis le pipeline ByValue. |
| **`-Clobber`** | SwitchParameter | named | (Facultatif) Si spécifié, force la valeur à définir pour une liaison de sortie spécifié. | 

Les paramètres courants suivants sont également pris en charge : 
* `Verbose`
* `Debug`
* `ErrorAction`
* `ErrorVariable`
* `WarningAction`
* `WarningVariable`
* `OutBuffer`
* `PipelineVariable`
* `OutVariable` 

Pour plus d’informations, consultez [sur Paramètres_courants](https://go.microsoft.com/fwlink/?LinkID=113216).

#### <a name="push-outputbinding-example-http-responses"></a>Exemple de push-OutputBinding : Réponses HTTP

Un déclencheur HTTP retourne une réponse à l’aide d’une liaison de sortie nommée `response`. Dans l’exemple suivant, la liaison de sortie de `response` a la valeur de « sortie #1 » :

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #1"
})
```

Étant donné que la sortie est au protocole HTTP, qui accepte uniquement une valeur de singleton, une erreur est levée lorsque `Push-OutputBinding` est appelée une deuxième fois.

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #2"
})
```

Pour les sorties qui acceptent uniquement les valeurs singleton, vous pouvez utiliser le `-Clobber` paramètre pour remplacer l’ancienne valeur au lieu de tenter de l’ajouter à une collection. L’exemple suivant suppose que vous avez déjà ajouté une valeur. À l’aide de `-Clobber`, la réponse à partir de l’exemple suivant remplace la valeur existante pour retourner une valeur de « sortie #3 » :

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #3"
}) -Clobber
```

#### <a name="push-outputbinding-example-queue-output-binding"></a>Exemple de push-OutputBinding : Liaison de sortie de file d’attente

`Push-OutputBinding` est utilisé pour envoyer des données aux liaisons de sortie, comme un [liaison de sortie Azure Queue storage](functions-bindings-storage-queue.md#output). Dans l’exemple suivant, le message écrit dans la file d’attente a une valeur de « sortie #1 » :

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #1"
```

La liaison de sortie pour une file d’attente de stockage accepte plusieurs valeurs de sortie. L’appel dans ce cas, l’exemple suivant après la première écrit dans la file d’attente, une liste avec deux éléments : « output #1 » et « output #2 ».

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #2"
```

L’exemple suivant, lorsqu’elle est appelée après les deux précédentes, ajoute deux valeurs supplémentaires à la collection de sortie :

```powershell
PS >Push-OutputBinding -Name outQueue -Value @("output #3", "output #4")
```

Lors de l’écriture à la file d’attente, le message contient ces quatre valeurs : « output #1 », « output #2 », « output #3 » et « output #4 ».

#### <a name="get-outputbinding-cmdlet"></a>`Get-OutputBinding` Applet de commande

Vous pouvez utiliser le `Get-OutputBinding` applet de commande pour récupérer les valeurs actuellement définies pour les liaisons de sortie. Cette applet de commande extrait une table de hachage qui contient les noms des liaisons de sortie avec leurs valeurs respectives. 

Voici un exemple d’utilisation `Get-OutputBinding` pour retourner des valeurs de liaison actuel :

```powershell
Get-OutputBinding
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
MyOtherQueue                   myData
```

`Get-OutputBinding` contient également un paramètre appelé `-Name`, qui peut être utilisé pour filtrer la liaison retournée, comme dans l’exemple suivant :

```powershell
Get-OutputBinding -Name MyQ*
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
```

Les caractères génériques (*) sont pris en charge dans `Get-OutputBinding`.

## <a name="logging"></a>Journalisation

Journalisation dans des fonctions PowerShell fonctionne comme journalisation PowerShell standard. Vous pouvez utiliser les applets de commande de journalisation à écrire dans chaque flux de sortie. Chaque applet de commande est mappé à un niveau de journal utilisé par les fonctions.

| Fonctions de niveau de journalisation | Applet de commande de journalisation |
| ------------- | -------------- |
| Error | **`Write-Error`** |
| Avertissement | **`Write-Warning`**  | 
| Information | **`Write-Information`** <br/> **`Write-Host`** <br /> **`Write-Output`**      | Information | Écrit dans _informations_ journalisation au niveau. |
| Déboguer | **`Write-Debug`** |
| Trace | **`Write-Progress`** <br /> **`Write-Verbose`** |

En plus de ces applets de commande, quoi que ce soit écrit dans le pipeline est redirigé vers le `Information` journal niveau et affiché avec la mise en forme de PowerShell par défaut.

> [!IMPORTANT]
> À l’aide de la `Write-Verbose` ou `Write-Debug` applets de commande n’est pas suffisant pour voir verbose et la journalisation au niveau du débogage. Vous devez également configurer le seuil du niveau de journal, qui déclare quel est le niveau de journaux importantes. Pour plus d’informations, consultez [configurer le niveau de journal de l’application de fonction](#configure-the-function-app-log-level).

### <a name="configure-the-function-app-log-level"></a>Configurer le niveau de journal de l’application de fonction

Functions vous permet de définir le niveau de seuil pour le rendre facile à contrôler les fonctions de façon écrit dans les journaux. Pour définir le seuil de toutes les traces écrites dans la console, utilisez le `logging.logLevel.default` propriété dans le [ `host.json` fichier][référence de host.json]. Ce paramètre s’applique à toutes les fonctions dans votre Function App.

L’exemple suivant définit le seuil pour activer la journalisation détaillée pour toutes les fonctions, mais définit le seuil pour activer la journalisation de débogage pour une fonction nommée `MyFunction`:

```json
{
    "logging": {
        "logLevel": {
            "Function.MyFunction": "Debug",
            "default": "Trace"
        }
    }
}  
```

Pour plus d’informations, consultez l’article de référence sur [référence de Host.JSON].

### <a name="viewing-the-logs"></a>Affichage des journaux

Si votre application de fonction s’exécute dans Azure, vous pouvez utiliser Application Insights à surveiller. Consultez [Supervision des fonctions Azure](functions-monitoring.md) pour en savoir plus sur l’affichage et l’interrogation des journaux d’activité de fonction.

Si vous exécutez votre application de fonction local pour le développement, consigne par défaut dans le système de fichiers. Pour afficher les journaux dans la console, définissez le `AZURE_FUNCTIONS_ENVIRONMENT` variable d’environnement `Development` avant de démarrer l’application de fonction.

## <a name="triggers-and-bindings-types"></a>Déclencheurs et des types de liaisons

Il existe un nombre de déclencheurs et liaisons disponibles à utiliser avec votre application de fonction. La liste complète des déclencheurs et liaisons [trouverez ici](functions-triggers-bindings.md#supported-bindings).

Tous les déclencheurs et liaisons sont représentées en tant que quelques types de données réelles dans le code :

* Hashtable
* string
* byte[]
* int
* double
* HttpRequestContext
* HttpResponseContext

Les cinq premiers types dans cette liste sont des types .NET standard. Les deux derniers sont utilisés uniquement par le [HttpTrigger déclencheur](#http-triggers-and-bindings).

Chaque paramètre de liaison dans vos fonctions doit être un de ces types.

### <a name="http-triggers-and-bindings"></a>Déclencheurs et liaisons HTTP

Les déclencheurs HTTP et webhook ainsi que les liaisons de sortie HTTP utilisent les objets de requête et de réponse pour représenter la messagerie HTTP.

#### <a name="request-object"></a>Objet Requête

L’objet de demande qui est passé dans le script est de type `HttpRequestContext`, ce qui a les propriétés suivantes :

| Propriété  | Description                                                    | Type                      |
|-----------|----------------------------------------------------------------|---------------------------|
| **`Body`**    | Objet qui contient le corps de la demande. `Body` est sérialisé dans le meilleur type en fonction des données. Par exemple, si les données JSON, il est passé dans comme une table de hachage. Si les données sont une chaîne, il est transmis comme une chaîne. | objet |
| **`Headers`** | Dictionnaire qui contient les en-têtes de demande.                | Dictionnaire < chaîne, chaîne ><sup>*</sup> |
| **`Method`** | Méthode HTTP de la demande.                                | string                    |
| **`Params`**  | Objet qui contient les paramètres de routage de la demande. | Dictionnaire < chaîne, chaîne ><sup>*</sup> |
| **`Query`** | Objet qui contient les paramètres de la requête.                  | Dictionnaire < chaîne, chaîne ><sup>*</sup> |
| **`Url`** | URL de la demande.                                        | string                    |

<sup>*</sup> Tous les `Dictionary<string,string>` clés respectent la casse.

#### <a name="response-object"></a>Objet Réponse

L’objet de réponse doit renvoyer est du type `HttpResponseContext`, ce qui a les propriétés suivantes :

| Propriété      | Description                                                 | Type                      |
|---------------|-------------------------------------------------------------|---------------------------|
| **`Body`**  | Objet qui contient le corps de la réponse.           | objet                    |
| **`ContentType`** | Une main courte pour définir le type de contenu pour la réponse. | string                    |
| **`Headers`** | Objet qui contient les en-têtes de la réponse.               | Dictionnaire ou une table de hachage   |
| **`StatusCode`**  | Code d’état HTTP de la réponse.                       | chaîne ou entier             |

#### <a name="accessing-the-request-and-response"></a>Accès à la demande et à la réponse

Lorsque vous travaillez avec des déclencheurs HTTP, vous pouvez accéder à la demande HTTP la même façon que vous le feriez avec toute autre liaison d’entrée. Il se trouve dans le `param` bloc.

Utilisez un `HttpResponseContext` objet à retourner une réponse, comme indiqué dans l’exemple suivant :

`function.json`

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "anonymous"
    },
    {
      "type": "http",
      "direction": "out"
    }
  ]
}
```

`run.ps1`

```powershell
param($req, $TriggerMetadata)

$name = $req.Query.Name

Push-OutputBinding -Name res -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "Hello $name!"
})
```

Le résultat de l’appel de cette fonction est alors :

```
PS > irm http://localhost:5001?Name=Functions
Hello Functions!
```

### <a name="type-casting-for-triggers-and-bindings"></a>Conversion de type pour les déclencheurs et liaisons

Pour certaines liaisons comme la liaison d’objets blob, vous êtes en mesure de spécifier le type du paramètre.

Par exemple, pour les données à partir du stockage d’objets Blob fourni sous forme de chaîne, ajouter le type suivant casté en mon `param` bloc :

```powershell
param([string] $myBlob)
```

## <a name="powershell-profile"></a>Profil PowerShell

Dans PowerShell, il existe le concept d’un profil de PowerShell. Si vous n’êtes pas familiarisé avec les profils de PowerShell, consultez [sur les profils](/powershell/module/microsoft.powershell.core/about/about_profiles).

Dans les fonctions de PowerShell, le script de profil s’exécute au démarrage de l’application de fonction. Applications de fonction démarrer lors du premier déploiement et après être inactif ([démarrage à froid](#cold-start)).

Lorsque vous créez une application de fonction à l’aide des outils, tels que Visual Studio Code et Azure Functions Core Tools, une valeur par défaut `profile.ps1` est créé pour vous. Le profil par défaut est conservé [sur le référentiel GitHub d’outils Core](https://github.com/Azure/azure-functions-core-tools/blob/dev/src/Azure.Functions.Cli/StaticResources/profile.ps1) et contient :

* Authentification MSI automatique vers Azure.
* La capacité à mettre sur Azure PowerShell `AzureRM` alias PowerShell si vous le souhaitez.

## <a name="powershell-version"></a>Version de PowerShell

Le tableau suivant présente la version de PowerShell utilisée par chaque version majeure du runtime Functions :

| Version de Functions | Version de PowerShell                             |
|-------------------|------------------------------------------------|
| 1.x               | Windows PowerShell 5.1 (verrouillée par le runtime) |
| 2.x               | PowerShell Core 6                              |

Vous pouvez voir la version actuelle en imprimant `$PSVersionTable` à partir de n’importe quelle fonction.

## <a name="dependency-management"></a>Gestion des dépendances

Fonctions PowerShell prend en charge la gestion des modules Azure par le service. En modifiant le host.json et en définissant la propriété managedDependency activé sur true, le fichier requirements.psd1 est traité. Les derniers modules Azure seront automatiquement téléchargés et mis à disposition à la fonction.

host.json
```json
{
    "managedDependency": {
        "enabled": true
    }
}
```

requirements.psd1

```powershell
@{
    Az = '1.*'
}
```

En tirant parti de vos propres modules ou des modules à partir de la [PowerShell Gallery](https://powershellgallery.com) est un peu différente de celle de la façon dont vous le feriez normalement.

Lorsque vous installez le module sur votre ordinateur local, il est placé dans un des dossiers globalement disponibles dans votre `$env:PSModulePath`. Étant donné que votre fonction s’exécute dans Azure, vous n’avez pas accès aux modules installés sur votre ordinateur. Ceci nécessite que le `$env:PSModulePath` pour une fonction PowerShell application diffère de `$env:PSModulePath` dans un script PowerShell régulière.

Dans les fonctions, `PSModulePath` contient deux chemins d’accès :

* Un `Modules` dossier existe à la racine de votre application de fonction.
* Un chemin d’accès à un `Modules` dossier réside dans le processus de travail du langage PowerShell.

### <a name="function-app-level-modules-folder"></a>Niveau application de la fonction `Modules` dossier

Pour utiliser des modules personnalisés ou des modules PowerShell à partir de PowerShell Gallery, vous pouvez placer des modules dont dépendent vos fonctions dans un `Modules` dossier. À partir de ce dossier, les modules sont automatiquement disponibles pour le runtime functions. Toute fonction dans l’application de fonction peut utiliser ces modules.

Pour tirer parti de cette fonctionnalité, vous devez créer un `Modules` dossier à la racine de votre application de fonction. Enregistrer les modules que vous souhaitez utiliser dans vos fonctions à cet emplacement.

```powershell
mkdir ./Modules
Save-Module MyGalleryModule -Path ./Modules
```

Utilisez `Save-Module` pour enregistrer tous les modules à utilisent vos fonctions, ou copier vos propres modules personnalisés pour le `Modules` dossier. Dans un dossier de Modules, votre application de fonction doit avoir la structure de dossiers suivante :

```
PSFunctionApp
 | - MyFunction
 | | - run.ps1
 | | - function.json
 | - Modules
 | | - MyGalleryModule
 | | - MyOtherGalleryModule
 | | - MyCustomModule.psm1
 | - local.settings.json
 | - host.json
```

Lorsque vous démarrez votre application de fonction, le processus de travail du langage PowerShell ajoute ce `Modules` dossier pour le `$env:PSModulePath` afin que vous pouvez vous appuyer sur le module de chargement automatique comme vous le feriez dans un script PowerShell régulière.

### <a name="language-worker-level-modules-folder"></a>Niveau de worker de langage `Modules` dossier

Plusieurs modules sont couramment utilisés par le processus de travail du langage PowerShell. Ces modules sont définis dans la dernière position de `PSModulePath`. 

La liste actuelle des modules est comme suit :

* [Microsoft.PowerShell.Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive): module utilisé pour travailler avec des archives, par exemple `.zip`, `.nupkg`et d’autres.
* **ThreadJob**: Une implémentation basée sur le thread de l’API de tâche PowerShell.

La version la plus récente de ces modules est utilisée par les fonctions. Pour utiliser une version spécifique de ces modules, vous pouvez placer la version spécifique dans le `Modules` dossier de votre application de fonction.

## <a name="environment-variables"></a>Variables d’environnement

Dans Functions, les [paramètres de l’application](functions-app-settings.md), par exemple, les chaînes de connexion de service, sont exposées en tant que variables d’environnement pendant l’exécution. Vous pouvez accéder à ces paramètres à l’aide de `$env:NAME_OF_ENV_VAR`, comme illustré dans l’exemple suivant :

```powershell
param($myTimer)

Write-Host "PowerShell timer trigger function ran! $(Get-Date)"
Write-Host $env:AzureWebJobsStorage
Write-Host $env:WEBSITE_SITE_NAME
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Lors de l’exécution en local, les paramètres de l’application sont lus à partir du fichier projet [local.settings.json](functions-run-local.md#local-settings-file).

## <a name="concurrency"></a>Concurrence

Par défaut, le runtime de fonctions PowerShell ne peut traiter un appel d’une fonction à la fois. Toutefois, ce niveau d’accès concurrentiel peut ne pas suffire dans les situations suivantes :

* Lorsque vous essayez de gérer un grand nombre d’appels en même temps.
* Lorsque vous disposez de fonctions qui appellent d’autres fonctions à l’intérieur de la même application de fonction.

Vous pouvez modifier ce comportement en définissant la variable d’environnement suivantes sur une valeur entière :

```
PSWorkerInProcConcurrencyUpperBound
```

Vous définissez cette variable d’environnement le [paramètres de l’application](functions-app-settings.md) de votre Function App.

### <a name="considerations-for-using-concurrency"></a>Considérations sur l’utilisation d’accès concurrentiel

PowerShell est un _mono-thread_ langage de script par défaut. Toutefois, l’accès concurrentiel peut être ajouté à l’aide de plusieurs instances d’exécution de PowerShell dans le même processus. Cette fonctionnalité est la façon dont le runtime Azure fonctions PowerShell fonctionne.

Il existe certains inconvénients avec cette approche.

#### <a name="concurrency-is-only-as-good-as-the-machine-its-running-on"></a>Accès concurrentiel n’est plus efficace que l’ordinateur, sur qu'il est en cours d’exécution

Si votre application de fonction s’exécute sur un [plan App Service](functions-scale.md#app-service-plan) qui prend uniquement en charge un seul cœur, puis l’accès concurrentiel ne sont pas très utile. Voilà, étant donné qu’aucun cœurs supplémentaires pour aider à équilibrer la charge. Dans ce cas, les performances peuvent varier lorsque le cœur unique doit de changement de contexte entre les instances d’exécution.

Le [plan de consommation](functions-scale.md#consumption-plan) s’exécute à l’aide d’un seul cœur, donc vous ne pouvez pas tirer parti d’accès concurrentiel. Si vous souhaitez tirer pleinement parti de l’accès concurrentiel, déployez à la place vos fonctions à une application de fonction en cours d’exécution sur un plan App Service dédié avec suffisamment de cœurs.

#### <a name="azure-powershell-state"></a>État d’Azure PowerShell

Azure PowerShell utilise certaines _au niveau du processus_ contextes et état pour aider à gagner de la saisie excessive. Toutefois, si vous activez d’accès concurrentiel dans votre function app et invoquer des actions qui modifient l’état, vous pouvez vous retrouver avec des conditions de concurrence. Ces conditions de concurrence sont difficiles à déboguer, car un seul appel s’appuie sur un certain état et l’autre appel a modifié l’état.

Il est considérable de l’accès concurrentiel avec Azure PowerShell, étant donné que certaines opérations peuvent prendre un temps considérable. Toutefois, vous devez vous agissez avec précaution. Si vous pensez que vous rencontrez une condition de concurrence, la concurrence du jeu à `1` et réessayez la demande.

## <a name="configure-function-scriptfile"></a>Configurer (fonction) `scriptFile`

Par défaut, une fonction PowerShell est exécutée à partir `run.ps1`, un fichier qui partage le même répertoire parent en tant que son correspondant `function.json`.

Le `scriptFile` propriété dans le `function.json` peut être utilisé pour obtenir une structure de dossiers qui ressemble à l’exemple suivant :

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.ps1
```

Dans ce cas, le `function.json` pour `myFunction` inclut un `scriptFile` propriété faisant référence au fichier avec la fonction exportée à exécuter.

```json
{
  "scriptFile": "../lib/PSFunction.ps1",
  "bindings": [
    // ...
  ]
}
```

## <a name="use-powershell-modules-by-configuring-an-entrypoint"></a>Utiliser des modules PowerShell en configurant un point d’entrée

Cet article vous a montré les fonctions PowerShell dans la valeur par défaut `run.ps1` générée par les modèles de fichier de script.
Toutefois, vous pouvez également inclure vos fonctions dans les modules PowerShell. Vous pouvez référencer votre code de fonction spécifique dans le module en utilisant le `scriptFile` et `entryPoint` champs dans le fichier function.json « fichier de configuration.

Dans ce cas, `entryPoint` est le nom d’une fonction ou l’applet de commande dans le module PowerShell référencé dans `scriptFile`.

Considérez la structure de dossier suivante :

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.psm1
```

Où `PSFunction.psm1` contient :

```powershell
function Invoke-PSTestFunc {
    param($InputBinding, $TriggerMetadata)

    Push-OutputBinding -Name OutputBinding -Value "output"
}

Export-ModuleMember -Function "Invoke-PSTestFunc"
```

Dans cet exemple, la configuration de `myFunction` inclut un `scriptFile` propriété qui fait référence à `PSFunction.psm1`, qui est un module PowerShell dans un autre dossier.  Le `entryPoint` références de propriété le `Invoke-PSTestFunc` (fonction), qui est le point d’entrée dans le module.

```json
{
  "scriptFile": "../lib/PSFunction.psm1",
  "entryPoint": "Invoke-PSTestFunc",
  "bindings": [
    // ...
  ]
}
```

Avec cette configuration, le `Invoke-PSTestFunc` obtient exécutées exactement comme un `run.ps1` serait.

## <a name="considerations-for-powershell-functions"></a>Considérations relatives aux fonctions de PowerShell

Lorsque vous travaillez avec des fonctions de PowerShell, tenez compte des considérations dans les sections suivantes.

### <a name="cold-start"></a>Démarrage à froid

Lors du développement d’Azure Functions dans le [modèle d’hébergement sans serveur](functions-scale.md#consumption-plan), à froid démarre est une réalité. *Démarrage à froid* fait référence à la période de temps il prend pour votre application de fonction commencer à exécuter pour traiter une requête. Démarrage à froid se produit plus fréquemment dans la consommation de plan, car votre function app s’arrête pendant les périodes d’inactivité.

### <a name="bundle-modules-instead-of-using-install-module"></a>Modules de bundle au lieu d’utiliser `Install-Module`

Votre script est exécuté sur chaque appel. Évitez d’utiliser `Install-Module` dans votre script. À la place utiliser `Save-Module` avant de le publier afin que votre fonction ne doit pas perdre de temps de téléchargement du module. Si les démarrages à froid ont une incidence sur vos fonctions, envisagez de déployer votre application de fonction pour un [plan App Service](functions-scale.md#app-service-plan) définie sur *always on* ou à un [plan Premium](functions-scale.md#premium-plan-public-preview).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les ressources suivantes :

* [Meilleures pratiques pour Azure Functions](functions-best-practices.md)
* [Informations de référence pour les développeurs sur Azure Functions](functions-reference.md)
* [Azure Functions triggers and bindings (Déclencheurs et liaisons Azure Functions)](functions-triggers-bindings.md)

[référence de Host.JSON]: functions-host-json.md
