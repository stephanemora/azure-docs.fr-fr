---
title: Inscrire des extensions de liaison d’Azure Functions
description: Découvrez comment inscrire une extension de liaison d’Azure Functions en fonction de votre environnement.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 02/25/2019
ms.author: cshoe
ms.openlocfilehash: 802e177b6f3844abe4d24c26b7ea2d0d4fb1688c
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64697011"
---
# <a name="register-azure-functions-binding-extensions"></a>Inscrire des extensions de liaison d’Azure Functions

Depuis la version d’Azure Functions 2.x, [liaisons](./functions-triggers-bindings.md) sont disponibles sous forme de packages distincts à partir de l’exécution de fonctions. Tandis que les fonctions .NET accéder aux liaisons via les packages NuGet, offres groupées d’extension autoriser l’accès des autres fonctions à toutes les liaisons via un paramètre de configuration.

Tenez compte des éléments suivants se rapportant aux extensions de liaison :

- Les extensions de liaison ne sont pas inscrits explicitement dans les fonctions 1.x sauf quand [création d’un C# bibliothèque de classes à l’aide de Visual Studio 2017](#local-csharp).

- Les déclencheurs HTTP et de la minuterie sont prises en charge par défaut et ne nécessitent pas une extension.

Le tableau suivant indique quand et comment vous inscrire des liaisons.

| Environnement de développement |Inscription<br/> dans Functions 1.x  |Inscription<br/> dans Functions 2.x  |
|-------------------------|------------------------------------|------------------------------------|
|Portail Azure|Automatique|Automatique|
|Langages non .NET ou développement Azure Core Tools local|Automatique|[Utiliser Azure Functions Core Tools et des offres groupées d’extension](#local-development-with-azure-functions-core-tools-and-extension-bundles)|
|Bibliothèque de classes C# avec Visual Studio 2017|[Utiliser les outils NuGet](#c-class-library-with-visual-studio-2017)|[Utiliser les outils NuGet](#c-class-library-with-visual-studio-2017)|
|Bibliothèque de classes C# avec Visual Studio Code|N/A|[Utiliser CLI .NET Core](#c-class-library-with-visual-studio-code)|

## <a name="local-development-with-azure-functions-core-tools-and-extension-bundles"></a>Développement local avec Azure Functions Core Tools et des offres groupées d’extension

[!INCLUDE [functions-core-tools-install-extension](../../includes/functions-core-tools-install-extension.md)]

<a name="local-csharp"></a>
## <a name="c-class-library-with-visual-studio-2017"></a>Bibliothèque de classes C# avec Visual Studio 2017

Dans **Visual Studio 2017**, vous pouvez installer des packages à partir de la console du Gestionnaire de package avec la commande [Install-Package](https://docs.microsoft.com/nuget/tools/ps-ref-install-package), comme indiqué dans l’exemple suivant :

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <TARGET_VERSION>
```

Le nom du package utilisé pour une liaison donnée est fourni dans l’article de référence pour cette liaison. Pour obtenir un exemple, consultez la [section Packages de l’article Informations de référence sur les liaisons Service Bus](functions-bindings-service-bus.md#packages---functions-1x).

Remplacez `<TARGET_VERSION>` dans l’exemple par une version spécifique du package, comme `3.0.0-beta5`. Les versions valides sont répertoriées sur les pages de chaque package sur [NuGet.org](https://nuget.org). Les versions majeures qui correspondent aux runtime Functions 1.x ou 2.x sont spécifiées dans l’article de référence pour la liaison.

## <a name="c-class-library-with-visual-studio-code"></a>Bibliothèque de classes C# avec Visual Studio Code

Dans **Visual Studio Code**, vous pouvez installer des packages à partir de l’invite de commandes avec la commande [dotnet add package](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) dans CLI .NET Core, comme indiqué dans l’exemple suivant :

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.ServiceBus --version <TARGET_VERSION>
```

CLI .NET Core peut être utilisé seulement pour le développement Azure Functions 2.x.

Le nom du package à utiliser pour une liaison donnée est fourni dans l’article de référence pour cette liaison. Pour obtenir un exemple, consultez la [section Packages de l’article Informations de référence sur les liaisons Service Bus](functions-bindings-service-bus.md#packages---functions-1x).

Remplacez `<TARGET_VERSION>` dans l’exemple par une version spécifique du package, comme `3.0.0-beta5`. Les versions valides sont répertoriées sur les pages de chaque package sur [NuGet.org](https://nuget.org). Les versions majeures qui correspondent aux runtime Functions 1.x ou 2.x sont spécifiées dans l’article de référence pour la liaison.

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Exemple de déclencheur et la liaison de fonction Azure](./functions-bindings-example.md)

