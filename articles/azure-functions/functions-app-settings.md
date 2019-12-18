---
title: Informations de référence sur les paramètres d’application d’Azure Functions
description: Documentation de référence pour les paramètres d’application ou les variables d’environnement d’Azure Functions.
ms.topic: conceptual
ms.date: 09/22/2018
ms.openlocfilehash: 1c7f5f9f8f6f198c5fe74baa613306732fa9b55b
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977265"
---
# <a name="app-settings-reference-for-azure-functions"></a>Informations de référence sur les paramètres d’application d’Azure Functions

Les paramètres d’une application de fonction contiennent les options de configuration globale qui affectent l’ensemble des fonctions de cette application de fonction. Lors d’une exécution locale, ces paramètres sont accessibles en tant que [variables d’environnement](functions-run-local.md#local-settings-file) locales. Cet article répertorie les paramètres d’application qui sont disponibles dans les applications de fonction.

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Les fichiers [host.json](functions-host-json.md) et [local.settings.json](functions-run-local.md#local-settings-file) contiennent d’autres options de configuration globale.

## <a name="appinsights_instrumentationkey"></a>APPINSIGHTS_INSTRUMENTATIONKEY

Clé d’instrumentation d’Application Insights si vous utilisez Application Insights. Reportez-vous à l’article [Surveiller l’exécution des fonctions Azure](functions-monitoring.md).

|Clé|Exemple de valeur|
|---|------------|
|APPINSIGHTS_INSTRUMENTATIONKEY|5dbdd5e9-af77-484b-9032-64f83bb83bb|

## <a name="azure_functions_environment"></a>AZURE_FUNCTIONS_ENVIRONMENT

Dans la version 2.x et les versions ultérieures du runtime Functions, configure le comportement de l’application en fonction de l’environnement d’exécution. Cette valeur est [lue lors de l’initialisation](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/Program.cs#L43). Vous pouvez définir `AZURE_FUNCTIONS_ENVIRONMENT` sur n’importe quelle valeur, mais [trois valeurs](/dotnet/api/microsoft.aspnetcore.hosting.environmentname) sont prises en charge : [Développement](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.development), [Préproduction](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.staging) et [Production](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.production). Lorsque `AZURE_FUNCTIONS_ENVIRONMENT` n’est pas défini, la valeur `Development` par défaut est sur un environnement local et `Production` sur Azure. Ce paramètre doit être utilisé à la place de `ASPNETCORE_ENVIRONMENT` pour définir l’environnement d’exécution. 

## <a name="azurewebjobsdashboard"></a>AzureWebJobsDashboard

Chaîne de connexion du compte de stockage facultatif pour stocker des journaux d’activité et les afficher dans l’onglet **Surveiller** du portail. Le compte de stockage doit être à usage général. Il prend en charge les objets blob, les files d’attente et les tables. Consultez les sections [Compte de stockage](functions-infrastructure-as-code.md#storage-account) et [Conditions requises pour le compte de stockage](functions-create-function-app-portal.md#storage-account-requirements).

|Clé|Exemple de valeur|
|---|------------|
|AzureWebJobsDashboard|DefaultEndpointsProtocol=https;AccountName=[name];AccountKey=[key]|

> [!TIP]
> Pour améliorer les performances et l’expérience, il est recommandé d’utiliser APPINSIGHTS_INSTRUMENTATIONKEY et App Insights pour la supervision, plutôt qu’AzureWebJobsDashboard.

## <a name="azurewebjobsdisablehomepage"></a>AzureWebJobsDisableHomepage

La valeur `true` désigne la désactivation de la page d’arrivée par défaut qui s’affiche pour l’URL racine d’une application de fonction. La valeur par défaut est `false`.

|Clé|Exemple de valeur|
|---|------------|
|AzureWebJobsDisableHomepage|true|

Lorsque ce paramètre d’application est omis ou défini sur `false`, une page semblable à celle indiquée dans l’exemple suivant s’affiche en réponse à l’URL `<functionappname>.azurewebsites.net`.

![Page d’arrivée d’une application de fonction](media/functions-app-settings/function-app-landing-page.png)

## <a name="azurewebjobsdotnetreleasecompilation"></a>AzureWebJobsDotNetReleaseCompilation

La valeur `true` désigne l’utilisation du mode Mise en production lors de la compilation de code .NET ; la valeur `false` désigne l’utilisation du mode Débogage. La valeur par défaut est `true`.

|Clé|Exemple de valeur|
|---|------------|
|AzureWebJobsDotNetReleaseCompilation|true|

## <a name="azurewebjobsfeatureflags"></a>AzureWebJobsFeatureFlags

Liste délimitée par des virgules des fonctionnalités bêta à activer. Les fonctionnalités bêta activées par ces indicateurs ne sont pas prêtes pour la production, mais peuvent être activées pour une utilisation expérimentale avant leur mise en service.

|Clé|Exemple de valeur|
|---|------------|
|AzureWebJobsFeatureFlags|feature1,feature2|

## <a name="azurewebjobssecretstoragetype"></a>AzureWebJobsSecretStorageType

Spécifie le référentiel ou le fournisseur à utiliser pour le stockage de clés. Actuellement, les référentiels pris en charge sont le stockage d’objets blob (« Blob ») et le système de fichiers local (« Fichiers »). La valeur par défaut est blob dans la version 2 et le système de fichiers dans la version 1.

|Clé|Exemple de valeur|
|---|------------|
|AzureWebJobsSecretStorageType|Fichiers|

## <a name="azurewebjobsstorage"></a>AzureWebJobsStorage

Le runtime d’Azure Functions utilise cette chaîne de connexion de compte de stockage pour toutes les fonctions à l’exception de celles qui sont déclenchées par HTTP. Le compte de stockage doit être à usage général. Il prend en charge les objets blob, les files d’attente et les tables. Consultez les sections [Compte de stockage](functions-infrastructure-as-code.md#storage-account) et [Conditions requises pour le compte de stockage](functions-create-function-app-portal.md#storage-account-requirements).

|Clé|Exemple de valeur|
|---|------------|
|AzureWebJobsStorage|DefaultEndpointsProtocol=https;AccountName=[name];AccountKey=[key]|

## <a name="azurewebjobs_typescriptpath"></a>AzureWebJobs_TypeScriptPath

Chemin d’accès au compilateur utilisé pour TypeScript. Vous permet d’écraser la valeur par défaut au besoin.

|Clé|Exemple de valeur|
|---|------------|
|AzureWebJobs_TypeScriptPath|%HOME%\typescript|

## <a name="function_app_edit_mode"></a>FUNCTION\_APP\_EDIT\_MODE

Détermine si la modification est activée dans le portail Azure. Les valeurs valides sont « readwrite » et « readonly ».

|Clé|Exemple de valeur|
|---|------------|
|FUNCTION\_APP\_EDIT\_MODE|readonly|

## <a name="functions_extension_version"></a>FUNCTIONS\_EXTENSION\_VERSION

Version du runtime Azure Functions à utiliser dans cette application de fonction. Un tilde accompagné d’une version principale désigne l’utilisation de la version la plus récente de cette version principale (par exemple, « ~2 »). Lorsque de nouvelles versions sont disponibles pour une même version principale, elles sont automatiquement installées dans l’application de fonction. Pour épingler l’application à une version spécifique, utilisez le numéro de version complet (par exemple, « 2.0.12345 »). La valeur par défaut est « ~2 ». La valeur `~1` épingle votre application à la version 1.x du runtime.

|Clé|Exemple de valeur|
|---|------------|
|FUNCTIONS\_EXTENSION\_VERSION|~2|

## <a name="functions_worker_process_count"></a>FUNCTIONS\_WORKER\_PROCESS\_COUNT

Spécifie le nombre maximal de processus de traitement de langue, avec la valeur `1` par défaut. La valeur maximale autorisée est `10`. Les appels de fonction sont répartis uniformément entre les processus de travail de langage. Les processus de travail de langage sont générés toutes les 10 secondes jusqu’à ce que le nombre défini par \_WORKER\_PROCESS\_COUNT soit atteint. L’utilisation de plusieurs processus de traitement de langue n’a pas le même effet qu’une [mise à l’échelle](functions-scale.md). Envisagez d’utiliser ce paramètre lorsque votre charge de travail a une combinaison d’appels liés à l’UC et aux E/S. Ce paramètre s’applique à toutes les langues non-.NET.

|Clé|Exemple de valeur|
|---|------------|
|FUNCTIONS\_WORKER\_PROCESS\_COUNT|2|


## <a name="functions_worker_runtime"></a>FUNCTIONS\_WORKER\_RUNTIME

Runtime du rôle de travail de langage à charger dans l’application de fonction.  Correspond au langage utilisé dans votre application (par exemple, « dotnet »). Pour les fonctions dans plusieurs langages, vous devrez les publier dans plusieurs applications, chacune avec une valeur de runtime de travail correspondante.  Les valeurs valides sont `dotnet` (C#/F#), `node` (JavaScript/TypeScript), `java` (Java), `powershell` (PowerShell) et `python` (Python).

|Clé|Exemple de valeur|
|---|------------|
|FUNCTIONS\_WORKER\_RUNTIME|dotnet|

## <a name="website_contentazurefileconnectionstring"></a>WEBSITE_CONTENTAZUREFILECONNECTIONSTRING

Pour les plans de consommation et Premium uniquement. Chaîne de connexion du compte de stockage dans lequel la configuration et le code de l’application de fonction sont stockés. Consultez la section [Créer une application de fonction](functions-infrastructure-as-code.md#create-a-function-app).

|Clé|Exemple de valeur|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|DefaultEndpointsProtocol=https;AccountName=[name];AccountKey=[key]|

## <a name="website_contentshare"></a>WEBSITE\_CONTENTSHARE

Pour les plans de consommation et Premium uniquement. Chemin d’accès au fichier contenant la configuration et le code de l’application de fonction. Utilisé avec WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. La valeur par défaut est une chaîne unique qui commence par le nom de l’application de fonction. Consultez la section [Créer une application de fonction](functions-infrastructure-as-code.md#create-a-function-app).

|Clé|Exemple de valeur|
|---|------------|
|WEBSITE_CONTENTSHARE|functionapp091999e2|

## <a name="website_max_dynamic_application_scale_out"></a>WEBSITE\_MAX\_DYNAMIC\_APPLICATION\_SCALE\_OUT

Nombre maximal d’instances possibles de l’application de fonction. Par défaut, il n’y pas de limite.

> [!NOTE]
> Ce paramètre est une fonctionnalité d’évaluation et fiable uniquement si la valeur définie est <= 5

|Clé|Exemple de valeur|
|---|------------|
|WEBSITE\_MAX\_DYNAMIC\_APPLICATION\_SCALE\_OUT|5\.|

## <a name="website_node_default_version"></a>WEBSITE\_NODE\_DEFAULT_VERSION

_Windows uniquement._  
Définit la version de Node.js à utiliser lors de l’exécution de votre application de fonction sur Windows. Vous devez utiliser un tilde (~) pour que le runtime utilise la dernière version disponible de la version principale ciblée. Par exemple, lorsqu’elle est définie sur `~10`, la dernière version de Node.js 10 est utilisée. Quand une version majeure est ciblée avec un tilde, vous n’avez pas besoin de mettre à jour manuellement la version mineure. 

|Clé|Exemple de valeur|
|---|------------|
|WEBSITE\_NODE\_DEFAULT_VERSION|~10|

## <a name="website_run_from_package"></a>WEBSITE\_RUN\_FROM\_PACKAGE

Permet à votre application de fonction de s’exécuter à partir d’un fichier de package monté.

|Clé|Exemple de valeur|
|---|------------|
|WEBSITE\_RUN\_FROM\_PACKAGE|1|

Les valeurs valides sont soit une URL qui correspond à l’emplacement d’un fichier de package de déploiement, soit `1`. Lorsque la valeur `1` est définie, le package doit se trouver dans le dossier `d:\home\data\SitePackages`. Lorsque vous utilisez le déploiement zip avec ce paramètre, le package est automatiquement chargé vers cet emplacement. Dans la préversion, ce paramètre s’appelait `WEBSITE_RUN_FROM_ZIP`. Pour plus d’informations, consultez [Exécuter des fonctions Azure à partir d’un fichier de package](run-functions-from-deployment-package.md).

## <a name="azure_function_proxy_disable_local_call"></a>AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL

Par défaut, les proxies Functions utilisent un raccourci pour envoyer des appels d’API à partir de proxies directement vers les fonctions de Function App, au lieu de créer une requête HTTP. Ce paramètre vous permet de désactiver ce comportement.

|Clé|Valeur|Description|
|-|-|-|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|true|Les appels dont l’URL back-end pointe vers une fonction dans l’application de fonction locale ne sont plus envoyés directement vers la fonction. Au lieu de cela, ils sont redirigés vers le front-end HTTP pour l’application de fonction|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|false|Il s’agit de la valeur par défaut. Les appels dont l’URL principale pointe vers une fonction de Function App en local sont transférés directement vers Function|


## <a name="azure_function_proxy_backend_url_decode_slashes"></a>AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES

Ce paramètre vérifie si %2F est décodé en tant que barres obliques dans les paramètres d’itinéraire lorsqu’ils sont réinsérés dans l’URL principale. 

|Clé|Valeur|Description|
|-|-|-|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|true|Les paramètres d’itinéraire avec des barres obliques encodées les décodent. `example.com/api%2ftest` devient `example.com/api/test`|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|false|Il s’agit du comportement par défaut. Tous les paramètres d’itinéraire restent inchangés lors de leur transmission|

### <a name="example"></a>Exemples

Voici un fichier exemple proxies.json dans une application de fonction sur l’URL myfunction.com

```JSON
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "root": {
            "matchCondition": {
                "route": "/{*all}"
            },
            "backendUri": "example.com/{all}"
        }
    }
}
```
|Décodage d’URL|Entrée|Output|
|-|-|-|
|true|myfunction.com/test%2fapi|example.com/test/api
|false|myfunction.com/test%2fapi|example.com/test%2fapi|


## <a name="next-steps"></a>Étapes suivantes

[Découvrez comment mettre à jour les paramètres d’application](functions-how-to-use-azure-function-app-settings.md#settings)

[Consultez les paramètres globaux dans le fichier host.json](functions-host-json.md)

[Consultez les autres paramètres des applications App Service](https://github.com/projectkudu/kudu/wiki/Configurable-settings)
