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
origin.date: 02/18/2019
ms.date: 04/26/2019
ms.author: v-junlch
ms.openlocfilehash: 5534086d5754691f650370e465fa2c63210e0dc7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61437852"
---
# <a name="register-azure-functions-binding-extensions"></a>Inscrire des extensions de liaison d’Azure Functions

Azure Functions prend en charge HTTP et le minuteur prêts à l’emploi. Pour travailler avec d’autres services, vous devez installer ou *inscrire* un [liaison](./functions-triggers-bindings.md) extension. Extensions de liaison sont fournies via les packages Azure Core Tools ou NuGet. 

Le tableau suivant indique quand et comment vous inscrire des liaisons.

| Environnement de développement |Inscription<br/> dans Functions 1.x  |Inscription<br/> dans Functions 2.x  |
|-------------------------|------------------------------------|------------------------------------|
|Portail Azure|Automatique|[Automatique - avec invite](#azure-portal-development)|
|Langages non .NET ou développement Azure Core Tools local|Automatique|[Utiliser les commandes CLI des outils de base](#local-development-azure-functions-core-tools)|
|Bibliothèque de classes C# avec Visual Studio 2017|[Utiliser les outils NuGet](#c-class-library-with-visual-studio-2017)|[Utiliser les outils NuGet](#c-class-library-with-visual-studio-2017)|
|Bibliothèque de classes C# avec Visual Studio Code|N/A|[Utiliser CLI .NET Core](#c-class-library-with-visual-studio-code)|

Les types de liaison suivants sont des exceptions qui ne nécessitent pas d’inscription explicite, car ils sont inscrits automatiquement dans toutes les versions et tous les environnements : HTTP et minuteur.

> [!IMPORTANT]
> Ce contenu pour le reste de cet article s’applique uniquement à Functions 2.x. Les extensions de liaison ne sont pas inscrits explicitement dans les fonctions 1.x sauf quand [création d’un C# bibliothèque de classes à l’aide de Visual Studio 2017](#local-csharp).

## <a name="azure-portal-development"></a>Développement sur le portail Azure

Quand vous créez une fonction ou que vous ajoutez une liaison, vous êtes averti quand l’extension pour le déclencheur ou la liaison nécessite une inscription. Répondez à l’invite en cliquant sur **Installer** pour inscrire l’extension. L’installation peut prendre jusqu’à 10 minutes sur un plan de consommation. 

Vous n’avez besoin d’installer chaque extension qu’une seule fois pour une application de fonction donnée. Pour les liaisons prises en charge qui ne sont pas disponibles dans le portail ou pour mettre à jour une extension installée, vous pouvez également [installer ou mettre à jour manuellement des extensions de liaison d’Azure Functions à partir du portail](install-update-binding-extensions-manual.md).  

## <a name="local-development-azure-functions-core-tools"></a>Azure Functions Core Tools pour le développement local

[!INCLUDE [functions-core-tools-install-extension](../../includes/functions-core-tools-install-extension.md)]

<a name="local-csharp"></a>
## <a name="c-class-library-with-visual-studio-2017"></a>Bibliothèque de classes C# avec Visual Studio 2017

Dans **Visual Studio 2017**, vous pouvez installer des packages à partir de la console du Gestionnaire de package avec la commande [Install-Package](https://docs.microsoft.com/nuget/tools/ps-ref-install-package), comme indiqué dans l’exemple suivant :

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions.ServiceBus -Version <target_version>
```

Le nom du package à utiliser pour une liaison donnée est fourni dans l’article de référence pour cette liaison. Pour obtenir un exemple, consultez la [section Packages de l’article Informations de référence sur les liaisons Service Bus](functions-bindings-service-bus.md#packages---functions-1x).

Remplacez `<target_version>` dans l’exemple par une version spécifique du package, comme `3.0.0-beta5`. Les versions valides sont répertoriées sur les pages de chaque package sur [NuGet.org](https://nuget.org). Les versions majeures qui correspondent aux runtime Functions 1.x ou 2.x sont spécifiées dans l’article de référence pour la liaison.

## <a name="c-class-library-with-visual-studio-code"></a>Bibliothèque de classes C# avec Visual Studio Code

Dans **Visual Studio Code**, vous pouvez installer des packages à partir de l’invite de commandes avec la commande [dotnet add package](https://docs.microsoft.com/dotnet/core/tools/dotnet-add-package) dans CLI .NET Core, comme indiqué dans l’exemple suivant :

```terminal
dotnet add package Microsoft.Azure.WebJobs.Extensions.ServiceBus --version <target_version>
```

CLI .NET Core peut être utilisé seulement pour le développement Azure Functions 2.x.

Le nom du package à utiliser pour une liaison donnée est fourni dans l’article de référence pour cette liaison. Pour obtenir un exemple, consultez la [section Packages de l’article Informations de référence sur les liaisons Service Bus](functions-bindings-service-bus.md#packages---functions-1x).

Remplacez `<target_version>` dans l’exemple par une version spécifique du package, comme `3.0.0-beta5`. Les versions valides sont répertoriées sur les pages de chaque package sur [NuGet.org](https://nuget.org). Les versions majeures qui correspondent aux runtime Functions 1.x ou 2.x sont spécifiées dans l’article de référence pour la liaison.

## <a name="next-steps"></a>Étapes suivantes
> [!div class="nextstepaction"]
> [Exemple de déclencheur et la liaison de fonction Azure](./functions-bindings-example.md)


