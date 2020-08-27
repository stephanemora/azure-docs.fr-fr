---
title: Inscrire des extensions de liaison Azure Functions
description: Découvrez comment inscrire une extension de liaison Azure Functions en fonction de votre environnement.
author: craigshoemaker
ms.topic: reference
ms.date: 07/08/2019
ms.author: cshoe
ms.openlocfilehash: 43bc278ea3cbd14690f1a9ac9263872536b5b174
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88224779"
---
# <a name="register-azure-functions-binding-extensions"></a>Inscrire des extensions de liaison Azure Functions

À partir d’Azure Functions version 2.x, des [liaisons](./functions-triggers-bindings.md) sont disponibles sous forme de packages distincts du runtime de fonctions. Alors que les fonctions .NET accèdent aux liaisons via des packages NuGet, les offres groupées d’extension permettent à d’autres fonctions d’accéder à toutes les liaisons via un paramètre de configuration.

Prenez en compte les éléments suivants liés aux extensions de liaison :

- Les extensions de liaison ne sont pas explicitement inscrites dans Functions 1.x, sauf lors de la [création d’une bibliothèque de classes C# à l’aide de Visual Studio](#local-csharp).

- Les déclencheurs HTTP et compteur sont pris en charge par défaut et ne nécessitent pas une extension.

Le tableau suivant indique quand et comment vous inscrivez des liaisons.

| Environnement de développement |Inscription<br/> dans Functions 1.x  |Inscription<br/> dans Functions 3.x/2.x  |
|-------------------------|------------------------------------|------------------------------------|
|Portail Azure|Automatique|Automatique<sup>*</sup>|
|Langages non .NET ou développement Azure Core Tools local|Automatique|[Utiliser Azure Functions Core Tools et des offres groupées d’extension](#extension-bundles)|
|Bibliothèque de classes C# avec Visual Studio|[Utiliser les outils NuGet](#vs)|[Utiliser les outils NuGet](#vs)|
|Bibliothèque de classes C# avec Visual Studio Code|N/A|[Utiliser CLI .NET Core](#vs-code)|

<sup>*</sup> Le portail utilise des offres groupées d’extensions.

## <a name="extension-bundles"></a><a name="extension-bundles"></a>Offres groupées d’extensions

Les offres groupées d’extensions permettent d’ajouter un jeu d’extensions de liaison Functions compatible avec votre application de fonction. Lors de l’utilisation d’offres groupées, un ensemble prédéfini d’extensions est ajouté lorsque vous générez votre application. La compatibilité entre les packages d’extension définis dans une offre groupée est vérifiée, vous permettant ainsi d’éviter les conflits entre les packages. Les packages d’extension vous permettent d’éviter d’avoir à publier du code de projet .NET avec un projet de fonctions non .NET. Vous activez les offres groupées d’extensions dans le fichier host.json de l’application.  

Vous pouvez utiliser des offres groupées d’extensions avec la version 2.x et les versions ultérieures du runtime Functions. 

Utilisez des offres groupées d’extensions pour le développement local à l’aide d’Azure Functions Core Tools ou de Visual Studio Code, ou lorsque vous créez à distance. Lors d’un développement local, vérifiez que vous utilisez la dernière version d’[Azure Functions Core Tools](functions-run-local.md#v2). Les offres groupées d’extension sont également utilisées lors du développement de fonctions dans le Portail Azure. 

Si vous n’utilisez pas d’offres groupées d’extension, vous devez installer le Kit de développement logiciel (SDK) .NET Core 2.x sur votre ordinateur local avant d’[installer explicitement des extensions de liaison](#explicitly-install-extensions). Un fichier extensions.csproj, qui définit explicitement les extensions requises, est ajouté à votre projet. Les offres groupées d’extensions suppriment ces exigences pour le développement local. 

Pour utiliser des offres groupées d’extension, mettez à jour le fichier *host.json* pour inclure l’entrée suivante pour `extensionBundle` :
 
[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

## <a name="explicitly-install-extensions"></a>Installer des extensions de manière explicite

[!INCLUDE [functions-extension-register-core-tools](../../includes/functions-extension-register-core-tools.md)]

## <a name="nuget-packages"></a><a name="local-csharp"></a>Packages NuGet

Pour un projet de fonctions basé sur une bibliothèque de classe C#, vous devez installer les extensions directement. Les offres groupées d’extension sont conçues spécifiquement pour les projets qui ne sont pas basés sur une bibliothèque de classes C#.

### <a name="c-class-library-with-visual-studio"></a><a name="vs"></a> Bibliothèque de classes C\# avec Visual Studio

Dans **Visual Studio**, vous pouvez installer des packages à partir de la console du Gestionnaire de package avec la commande [Install-Package](/nuget/tools/ps-ref-install-package), comme indiqué dans l’exemple suivant :

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

Le nom du package utilisé pour une liaison donnée est fourni dans l’article de référence pour cette liaison. Pour obtenir un exemple, consultez la [section Packages de l’article Informations de référence sur les liaisons Service Bus](functions-bindings-service-bus.md#functions-1x).

Remplacez `<TARGET_VERSION>` dans l’exemple par une version spécifique du package, comme `3.0.0-beta5`. Les versions valides sont répertoriées sur les pages de chaque package sur [NuGet.org](https://nuget.org). Les versions majeures qui correspondent aux runtime Functions 1.x ou 2.x sont spécifiées dans l’article de référence pour la liaison.

Si vous utilisez `Install-Package` pour faire référence à une liaison, vous n’avez pas besoin d’utiliser des [offres groupées d’extensions](#extension-bundles). Cette approche est spécifique aux bibliothèques de classes générées dans Visual Studio.

## <a name="c-class-library-with-visual-studio-code"></a><a name="vs-code"></a> Bibliothèque de classes C# avec Visual Studio Code

Dans **Visual Studio Code**, installez des packages pour un projet de bibliothèque de classes C# à partir de l’invite de commandes avec la commande [dotnet add package](/dotnet/core/tools/dotnet-add-package) dans CLI .NET Core. L’exemple suivant montre comment ajouter une liaison :

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.<BINDING_TYPE_NAME> --version <TARGET_VERSION>
```

CLI .NET Core peut être utilisé seulement pour le développement Azure Functions 2.x.

Remplacez `<BINDING_TYPE_NAME>` par le nom du package qui contient la liaison dont vous avez besoin. Vous trouverez l’article de référence de la liaison souhaitée dans la [liste des liaisons prises en charge](./functions-triggers-bindings.md#supported-bindings).

Remplacez `<TARGET_VERSION>` dans l’exemple par une version spécifique du package, comme `3.0.0-beta5`. Les versions valides sont répertoriées sur les pages de chaque package sur [NuGet.org](https://nuget.org). Les versions majeures qui correspondent aux runtime Functions 1.x ou 2.x sont spécifiées dans l’article de référence pour la liaison.

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Exemples de liaisons et de déclencheurs Azure Functions](./functions-bindings-example.md)
