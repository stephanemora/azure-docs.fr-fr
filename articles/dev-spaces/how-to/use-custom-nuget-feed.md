---
title: Guide pratique pour utiliser un flux NuGet personnalisé dans Azure Dev Spaces
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/17/2019
ms.topic: conceptual
description: Utilisez un flux NuGet personnalisé pour accéder à des packages NuGet et les utiliser dans un espace Azure Dev Spaces.
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, conteneurs
manager: gwallace
ms.openlocfilehash: 44a87491d276e09e1fa8fed3f5e6803648c3e4a2
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68305397"
---
#  <a name="use-a-custom-nuget-feed-in-an-azure-dev-space"></a>Utiliser un flux NuGet personnalisé dans un espace Azure Dev Spaces

Un flux NuGet offre un moyen pratique d’inclure des sources de package dans un projet. Azure Dev Spaces doit accéder à ce flux pour pouvoir installer les dépendances dans le conteneur Docker.

## <a name="set-up-a-nuget-feed"></a>Configurer un flux NuGet

Ajoutez une [référence de package](https://docs.microsoft.com/nuget/consume-packages/package-references-in-project-files) dans le fichier `*.csproj` sous le nœud `PackageReference`. Par exemple :

```xml
<ItemGroup>
    <!-- ... -->
    <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
    <!-- ... -->
</ItemGroup>
```

Créez un fichier [NuGet.Config](https://docs.microsoft.com/nuget/reference/nuget-config-file) dans le dossier du projet et définissez les sections `packageSources` et `packageSourceCredentials` pour votre flux NuGet. La section `packageSources` contient votre URL de flux, qui doit être accessible publiquement. Les `packageSourceCredentials` sont les informations d’identification permettant d’accéder au flux. Par exemple :

```xml
<packageSources>
    <add key="Contoso" value="https://contoso.com/packages/" />
</packageSources>

<packageSourceCredentials>
    <Contoso>
        <add key="Username" value="user@contoso.com" />
        <add key="ClearTextPassword" value="33f!!lloppa" />
    </Contoso>
</packageSourceCredentials>
```

Mettez à jour vos fichiers Dockerfile pour copier le fichier `NuGet.Config` vers l’image. Par exemple :

```console
COPY ["<project folder>/NuGet.Config", "./NuGet.Config"]
```

> [!TIP]
> Sur Windows, `NuGet.Config`, `Nuget.Config` et `nuget.config` fonctionnent tous comme des noms de fichiers valides. Sur Linux, seul `NuGet.Config` est un nom de fichier valide pour ce fichier. Étant donné qu’Azure Dev Spaces utilise Docker et Linux, ce fichier doit être appelé `NuGet.Config`. Vous pouvez corriger manuellement le nom ou vous pouvez le faire en exécutant `dotnet restore --configfile nuget.config`.


Si vous utilisez Git, vous ne devez pas avoir les informations d’identification de votre flux NuGet dans le contrôle de version. Ajoutez `NuGet.Config` à `.gitignore` pour votre projet afin que le fichier `NuGet.Config` ne soit pas ajouté au contrôle de version. Azure Dev Spaces a besoin de ce fichier pendant le processus de génération de l’image de conteneur, mais par défaut, il respecte les règles définies dans `.gitignore` et `.dockerignore` pendant la synchronisation. Pour modifier la valeur par défaut et autoriser Azure Dev Spaces à synchroniser le fichier `NuGet.Config`, mettez à jour le fichier `azds.yaml` :

```yaml
build:
useGitIgnore: true
ignore:
- “!NuGet.Config”
```

Si vous n’utilisez pas Git, vous pouvez ignorer cette étape.

La prochaine fois que vous exécuterez`azds up` ou `F5` dans Visual Studio Code ou Visual Studio, Azure Dev Spaces synchronisera le fichier `NuGet.Config` pour installer les dépendances du package.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur [NuGet et son fonctionnement](https://docs.microsoft.com/nuget/what-is-nuget).