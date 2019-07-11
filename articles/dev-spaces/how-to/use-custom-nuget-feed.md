---
title: Guide pratique pour utiliser un flux NuGet personnalisé dans Azure Dev Spaces
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: johnsta
ms.author: johnsta
ms.date: 05/11/2018
ms.topic: conceptual
description: Utilisez un flux NuGet personnalisé pour accéder à des packages NuGet et les utiliser dans un espace Azure Dev Spaces.
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, conteneurs
manager: gwallace
ms.openlocfilehash: ca1fee76dfe280322a39fad56b9f85ebe1a92d3b
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704038"
---
#  <a name="use-a-custom-nuget-feed-in-an-azure-dev-space"></a>Utiliser un flux NuGet personnalisé dans un espace Azure Dev Spaces

Un flux NuGet offre un moyen pratique d’inclure des sources de package dans un projet. Azure Dev Spaces doit pouvoir accéder à ce flux pour permettre l’installation des dépendances dans le conteneur Docker.

## <a name="set-up-a-nuget-feed"></a>Configurer un flux NuGet

Pour configurer un flux NuGet :
1. Ajoutez une [référence de package](https://docs.microsoft.com/nuget/consume-packages/package-references-in-project-files) dans le fichier `*.csproj` sous le nœud `PackageReference`.

   ```xml
   <ItemGroup>
       <!-- ... -->
       <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
       <!-- ... -->
   </ItemGroup>
   ```

2. Créez un fichier [NuGet.Config](https://docs.microsoft.com/nuget/reference/nuget-config-file) dans le dossier du projet.
     * Dans la section `packageSources`, référencez l’emplacement du flux NuGet. Important : Le flux NuGet doit être accessible publiquement.
     * Dans la section `packageSourceCredentials`, configurez les informations d’identification (nom d’utilisateur et mot de passe). 

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

3. Si vous utilisez le contrôle de code source :
    - Référencez `NuGet.Config` dans votre fichier `.gitignore` pour éviter de valider accidentellement les informations d’identification dans votre dépôt source.
    - Ouvrez le fichier `azds.yaml` dans votre projet puis, dans la section `build`, insérez l’extrait de code ci-dessous de façon à synchroniser le fichier `NuGet.Config` avec Azure et ainsi garantir son utilisation au cours du processus de génération de l’image du conteneur. (Par défaut, Azure Dev Spaces ne synchronise pas les fichiers qui présentent une correspondance avec les règles `.gitignore` et `.dockerignore`.)

        ```yaml
        build:
        useGitIgnore: true
        ignore:
        - “!NuGet.Config”
        ```


## <a name="next-steps"></a>Étapes suivantes

Une fois que vous aurez effectué les étapes précédentes, la prochaine fois que vous exécuterez `azds up` (ou que vous appuierez sur `F5` dans VSCode ou Visual Studio), Azure Dev Spaces synchronisera le fichier `NuGet.Config` avec Azure, qui sera dès lors utilisé par `dotnet restore` pour installer les dépendances de package dans le conteneur.

