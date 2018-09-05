---
title: Informations de référence sur les paramètres d’application d’Azure Functions
description: Documentation de référence pour les paramètres d’application ou les variables d’environnement d’Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/22/2018
ms.author: glenga
ms.openlocfilehash: 46c1cb0a0cb3104e3705e4a7d4ef0dd894a7c2d7
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42819044"
---
# <a name="app-settings-reference-for-azure-functions"></a>Informations de référence sur les paramètres d’application d’Azure Functions

Les paramètres d’une application de fonction contiennent les options de configuration globale qui affectent l’ensemble des fonctions de cette application de fonction. Lors d’une exécution locale, ces paramètres sont contenus dans des variables d’environnement. Cet article répertorie les paramètres d’application qui sont disponibles dans les applications de fonction.

[!INCLUDE [Paramètres de l’application de fonctions](../../includes/functions-app-settings.md]

Les fichiers [host.json](functions-host-json.md) et [local.settings.json](functions-run-local.md#local-settings-file) contiennent d’autres options de configuration globale.

## <a name="appinsightsinstrumentationkey"></a>APPINSIGHTS_INSTRUMENTATIONKEY

Clé d’instrumentation d’Application Insights si vous utilisez Application Insights. Reportez-vous à l’article [Surveiller l’exécution des fonctions Azure](functions-monitoring.md).

|Clé|Exemple de valeur|
|---|------------|
|APPINSIGHTS_INSTRUMENTATIONKEY|5dbdd5e9-af77-484b-9032-64f83bb83bb|

## <a name="azurewebjobsdashboard"></a>AzureWebJobsDashboard

Chaîne de connexion du compte de stockage facultatif pour stocker des journaux et les afficher dans l’onglet **Surveiller** du portail. Le compte de stockage doit être à usage général. Il prend en charge les objets blob, les files d’attente et les tables. Consultez les sections [Compte de stockage](functions-infrastructure-as-code.md#storage-account) et [Conditions requises pour le compte de stockage](functions-create-function-app-portal.md#storage-account-requirements).

|Clé|Exemple de valeur|
|---|------------|
|AzureWebJobsDashboard|DefaultEndpointsProtocol=https;AccountName=[name];AccountKey=[key]|

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

## <a name="azurewebjobsscriptroot"></a>AzureWebJobsScriptRoot

Chemin d’accès au répertoire racine dans lequel se trouvent les dossiers de fonctions et du fichier *host.json*. Dans une application de fonction, la valeur par défaut est `%HOME%\site\wwwroot`.

|Clé|Exemple de valeur|
|---|------------|
|AzureWebJobsScriptRoot|%HOME%\site\wwwroot|

## <a name="azurewebjobssecretstoragetype"></a>AzureWebJobsSecretStorageType

Spécifie le référentiel ou le fournisseur à utiliser pour le stockage de clés. Actuellement, les référentiels pris en charge sont l’objet blob (« Blob ») et le système de fichiers (« disabled »). La valeur par défaut est le système de fichiers (« disabled »).

|Clé|Exemple de valeur|
|---|------------|
|AzureWebJobsSecretStorageType|disabled|

## <a name="azurewebjobsstorage"></a>AzureWebJobsStorage

Le runtime d’Azure Functions utilise cette chaîne de connexion de compte de stockage pour toutes les fonctions à l’exception de celles qui sont déclenchées par HTTP. Le compte de stockage doit être à usage général. Il prend en charge les objets blob, les files d’attente et les tables. Consultez les sections [Compte de stockage](functions-infrastructure-as-code.md#storage-account) et [Conditions requises pour le compte de stockage](functions-create-function-app-portal.md#storage-account-requirements).

|Clé|Exemple de valeur|
|---|------------|
|AzureWebJobsStorage|DefaultEndpointsProtocol=https;AccountName=[name];AccountKey=[key]|

## <a name="azurewebjobstypescriptpath"></a>AzureWebJobs_TypeScriptPath

Chemin d’accès au compilateur utilisé pour TypeScript. Vous permet d’écraser la valeur par défaut au besoin.

|Clé|Exemple de valeur|
|---|------------|
|AzureWebJobs_TypeScriptPath|%HOME%\typescript|

## <a name="functionappeditmode"></a>FUNCTION\_APP\_EDIT\_MODE

Les valeurs valides sont « readwrite » et « readonly ».

|Clé|Exemple de valeur|
|---|------------|
|FUNCTION\_APP\_EDIT\_MODE|readonly|

## <a name="functionsextensionversion"></a>FUNCTIONS\_EXTENSION\_VERSION

Version du runtime d’Azure Functions à utiliser dans cette application de fonction. Un tilde accompagné d’une version principale désigne l’utilisation de la version la plus récente de cette version principale (par exemple, « ~ 1 »). Lorsque de nouvelles versions sont disponibles pour une même version principale, elles sont automatiquement installées dans l’application de fonction. Pour épingler l’application à une version spécifique, utilisez le numéro de version complet (par exemple, « 1.0.12345 »). La valeur par défaut est « ~1 ».

|Clé|Exemple de valeur|
|---|------------|
|FUNCTIONS\_EXTENSION\_VERSION|~1|

## <a name="websitecontentazurefileconnectionstring"></a>WEBSITE_CONTENTAZUREFILECONNECTIONSTRING

Pour les plans de consommation uniquement. Chaîne de connexion du compte de stockage dans lequel la configuration et le code de l’application de fonction sont stockés. Consultez la section [Créer une application de fonction](functions-infrastructure-as-code.md#create-a-function-app).

|Clé|Exemple de valeur|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|DefaultEndpointsProtocol=https;AccountName=[name];AccountKey=[key]|

## <a name="websitecontentshare"></a>WEBSITE\_CONTENTSHARE

Pour les plans de consommation uniquement. Chemin d’accès au fichier contenant la configuration et le code de l’application de fonction. Utilisé avec WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. La valeur par défaut est une chaîne unique qui commence par le nom de l’application de fonction. Consultez la section [Créer une application de fonction](functions-infrastructure-as-code.md#create-a-function-app).

|Clé|Exemple de valeur|
|---|------------|
|WEBSITE_CONTENTSHARE|functionapp091999e2|

## <a name="websitemaxdynamicapplicationscaleout"></a>WEBSITE\_MAX\_DYNAMIC\_APPLICATION\_SCALE\_OUT

Nombre maximal d’instances possibles de l’application de fonction. Par défaut, il n’y pas de limite.

> [!NOTE]
> Ce paramètre concerne une fonctionnalité d’évaluation.

|Clé|Exemple de valeur|
|---|------------|
|WEBSITE\_MAX\_DYNAMIC\_APPLICATION\_SCALE\_OUT|10|

## <a name="websitenodedefaultversion"></a>WEBSITE\_NODE\_DEFAULT_VERSION

La valeur par défaut est « 6.5.0 ».

|Clé|Exemple de valeur|
|---|------------|
|WEBSITE\_NODE\_DEFAULT_VERSION|6.5.0|

## <a name="websiterunfromzip"></a>WEBSITE\_RUN\_FROM\_ZIP

Permet à votre application de fonction de s’exécuter à partir d’un fichier de package monté.

> [!NOTE]
> Ce paramètre concerne une fonctionnalité d’évaluation.

|Clé|Exemple de valeur|
|---|------------|
|WEBSITE\_RUN\_FROM\_ZIP|1|

Les valeurs valides sont soit une URL qui correspond à l’emplacement d’un fichier de package de déploiement, soit `1`. Lorsque la valeur `1` est définie, le package doit se trouver dans le dossier `d:\home\data\SitePackages`. Lorsque vous utilisez le déploiement zip avec ce paramètre, le package est automatiquement chargé vers cet emplacement.  Pour plus d’informations, consultez [Exécuter des fonctions Azure à partir d’un fichier de package](run-functions-from-deployment-package.md).

## <a name="next-steps"></a>Étapes suivantes

[Découvrez comment mettre à jour les paramètres d’application](functions-how-to-use-azure-function-app-settings.md#manage-app-service-settings)

[Consultez les paramètres globaux dans le fichier host.json](functions-host-json.md)

[Consultez les autres paramètres des applications App Service](https://github.com/projectkudu/kudu/wiki/Configurable-settings)
