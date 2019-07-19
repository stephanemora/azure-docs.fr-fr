---
title: Inscrire des extensions de liaison Azure Functions
description: Découvrez comment inscrire une extension de liaison Azure Functions en fonction de votre environnement.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 02/25/2019
ms.author: cshoe
ms.openlocfilehash: 88ffd6ec24ed19dd3b1e57277884c8759cdac1f9
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67480334"
---
# <a name="register-azure-functions-binding-extensions"></a>Inscrire des extensions de liaison Azure Functions

Dans Azure Functions version 2.x, des [liaisons](./functions-triggers-bindings.md) sont disponibles sous forme de packages distincts du runtime de fonctions. Alors que les fonctions .NET accèdent aux liaisons via des packages NuGet, les offres groupées d’extension permettent à d’autres fonctions d’accéder à toutes les liaisons via un paramètre de configuration.

Prenez en compte les éléments suivants liés aux extensions de liaison :

- Les extensions de liaison ne sont pas explicitement inscrites dans Functions 1.x, sauf lors de la [création d’une bibliothèque de classes C# à l’aide de Visual Studio](#local-csharp).

- Les déclencheurs HTTP et compteur sont pris en charge par défaut et ne nécessitent pas une extension.

Le tableau suivant indique quand et comment vous inscrivez des liaisons.

| Environnement de développement |Inscription<br/> dans Functions 1.x  |Inscription<br/> dans Functions 2.x  |
|-------------------------|------------------------------------|------------------------------------|
|Portail Azure|Automatique|Automatique|
|Langages non .NET ou développement Azure Core Tools local|Automatique|[Utiliser Azure Functions Core Tools et des offres groupées d’extension](#extension-bundles)|
|Bibliothèque de classes C# avec Visual Studio 2019|[Utiliser les outils NuGet](#c-class-library-with-visual-studio-2019)|[Utiliser les outils NuGet](#c-class-library-with-visual-studio-2019)|
|Bibliothèque de classes C# avec Visual Studio Code|N/A|[Utiliser CLI .NET Core](#c-class-library-with-visual-studio-code)|

## <a name="extension-bundles">Offres groupées d’extension pour développement local</a>

Les offres groupées d’extension constituent une technologie de développement local pour le runtime version 2.x qui vous permet d’ajouter un jeu d’extensions de liaison Functions compatible à votre projet d’application de fonction. Ces packages d’extension sont ensuite inclus dans le package de déploiement lorsque vous déployez sur Azure. Les offres groupées permettent de mettre à disposition toutes les liaisons publiées par Microsoft via un paramètre dans le fichier *host.json*. Les packages d’extension définis dans une offre groupée sont compatibles entre eux, vous permettant ainsi d’éviter les conflits entre les packages. Lors d’un développement local, vérifiez que vous utilisez la dernière version d’[Azure Functions Core Tools](functions-run-local.md#v2).

Utilisez des offres groupées d’extension pour l’ensemble du développement local à l’aide d’Azure Functions Core Tools ou de Visual Studio Code.

Si vous n’utilisez pas d’offres groupées d’extension, vous devez installer le Kit de développement logiciel (SDK) .NET Core 2.x sur votre ordinateur local avant d’installer des extensions de liaison. Les offres groupées suppriment cette exigence pour le développement local. 

Pour utiliser des offres groupées d’extension, mettez à jour le fichier *host.json* pour inclure l’entrée suivante pour `extensionBundle` :

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```

Les propriétés suivantes sont disponibles dans `extensionBundle` :

| Propriété | Description |
| -------- | ----------- |
| **`id`** | Espace de noms pour les offres groupées d’extension Microsoft Azure Functions. |
| **`version`** | Version de l’offre groupée à installer. Le runtime Functions récupère toujours la version autorisée maximale définie par la plage ou l’intervalle de version. La valeur de version ci-dessus autorise toutes les versions d’offre groupée jusqu’à 1.0.0 mais sans inclure la version 2.0.0. Pour plus d’informations, consultez la [notation d’intervalle de spécification de plages de versions](https://docs.microsoft.com/nuget/reference/package-versioning#version-ranges-and-wildcards). |

Les versions d’offre groupées sont incrémentées à mesure que les packages contenus dans l’offre groupée changent. Un changement de version majeure se produit lorsque les packages contenus dans l’offre groupée sont incrémentés par une version majeure, ce qui coïncide généralement avec un changement de version majeure du runtime Functions.  

L’ensemble actuel d’extensions installées par l’offre groupée par défaut est énuméré dans ce [fichier extensions.json](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json).

<a name="local-csharp"></a>

## <a name="c-class-library-with-visual-studio-2019"></a>Bibliothèque de classes C\# avec Visual Studio 2019

Dans **Visual Studio 2019**, vous pouvez installer des packages à partir de la console du Gestionnaire de package avec la commande [Install-Package](https://docs.microsoft.com/nuget/tools/ps-ref-install-package), comme indiqué dans l’exemple suivant :

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

Le nom du package utilisé pour une liaison donnée est fourni dans l’article de référence pour cette liaison. Pour obtenir un exemple, consultez la [section Packages de l’article Informations de référence sur les liaisons Service Bus](functions-bindings-service-bus.md#packages---functions-1x).

Remplacez `<TARGET_VERSION>` dans l’exemple par une version spécifique du package, comme `3.0.0-beta5`. Les versions valides sont répertoriées sur les pages de chaque package sur [NuGet.org](https://nuget.org). Les versions majeures qui correspondent aux runtime Functions 1.x ou 2.x sont spécifiées dans l’article de référence pour la liaison.

## <a name="c-class-library-with-visual-studio-code"></a>Bibliothèque de classes C# avec Visual Studio Code

> [!NOTE]
> Nous vous recommandons d’utiliser des [offres groupées d’extension](#extension-bundles) pour que Functions installe automatiquement un ensemble de packages d’extension de liaison compatible.

Dans **Visual Studio Code**, installez des packages pour un projet de bibliothèque de classes C# à partir de l’invite de commandes avec la commande [dotnet add package](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) dans CLI .NET Core, comme indiqué dans l’exemple suivant :

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.ServiceBus --version <TARGET_VERSION>
```

CLI .NET Core peut être utilisé seulement pour le développement Azure Functions 2.x.

Le nom du package à utiliser pour une liaison donnée est fourni dans l’article de référence pour cette liaison. Pour obtenir un exemple, consultez la [section Packages de l’article Informations de référence sur les liaisons Service Bus](functions-bindings-service-bus.md#packages---functions-1x).

Remplacez `<TARGET_VERSION>` dans l’exemple par une version spécifique du package, comme `3.0.0-beta5`. Les versions valides sont répertoriées sur les pages de chaque package sur [NuGet.org](https://nuget.org). Les versions majeures qui correspondent aux runtime Functions 1.x ou 2.x sont spécifiées dans l’article de référence pour la liaison.

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Exemples de liaisons et de déclencheurs Azure Functions](./functions-bindings-example.md)

