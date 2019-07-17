---
title: Configurer des fonctions Azure pour des fournisseurs personnalisés Azure
description: Ce tutoriel vous explique comment créer une fonction Azure et comment la configurer pour qu’elle fonctionne avec des fournisseurs personnalisés Azure.
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: d7e4de43659db88bfd9aad40cc3b9f1753189bba
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67799118"
---
# <a name="setup-azure-functions-for-azure-custom-providers"></a>Configurer des fonctions Azure pour des fournisseurs personnalisés Azure

Les fournisseurs personnalisés vous permettent de personnaliser des workflows dans Azure. Un fournisseur personnalisé implique qu’un contrat soit passé entre Azure et un point de terminaison (`endpoint`). Ce tutoriel vous guide tout au long du processus de configuration d’une fonction Azure comme `endpoint` d’un fournisseur personnalisé .

Dans ce tutoriel, vous allez effectuer les étapes suivantes :

- Créer la fonction Azure
- Installer les liaisons Table Azure
- Mettre à jour les méthodes HTTP RESTful
- Ajouter des packages NuGet Azure Resource Manager

Ce tutoriel s’appuie sur les tutoriels suivants :

- [Création de votre première fonction Azure à l’aide du portail Azure](../azure-functions/functions-create-first-azure-function.md)

## <a name="creating-the-azure-function"></a>Créer la fonction Azure

> [!NOTE]
> Dans ce tutoriel, nous allons créer un point de terminaison de service simple à l’aide d’une fonction Azure. Toutefois, un fournisseur personnalisé peut utiliser n’importe quel `endpoint` accessible au public. Azure Logic Apps, Azure API Management et Azure Web Apps sont d’excellentes alternatives.

Avant de commencer, suivez le tutoriel pour [créer votre première fonction Azure dans le portail Azure](../azure-functions/functions-create-first-azure-function.md). Ce tutoriel crée une fonction webhook .NET Core qui peut être modifiée dans le portail Azure.

## <a name="install-azure-table-bindings"></a>Installer les liaisons Table Azure

Cette section vous montre comment installer rapidement les liaisons de stockage Table Azure.

1. Accédez à l’onglet `Integrate` pour HttpTrigger.
2. Cliquez sur `+ New Input`.
3. Sélectionnez `Azure Table Storage`.
4. Installez `Microsoft.Azure.WebJobs.Extensions.Storage` si ce n’est pas déjà fait.
5. Définissez « tableStorage » comme `Table parameter name` et « myCustomResources » comme `Table name`.
6. Enregistrez le paramètre d’entrée mis à jour.

![Vue d’ensemble du fournisseur personnalisé](./media/create-custom-providers/azure-functions-table-bindings.png)

## <a name="update-restful-http-methods"></a>Mettre à jour les méthodes HTTP RESTful

Cette section vous montre comment configurer rapidement la fonction Azure de manière à inclure les méthodes de demande RESTful du fournisseur personnalisé.

1. Accédez à l’onglet `Integrate` pour HttpTrigger.
2. Sous `Selected HTTP methods`, cochez GET, POST, DELETE et PUT.

![Vue d’ensemble du fournisseur personnalisé](./media/create-custom-providers/azure-functions-http-methods.png)

## <a name="modifying-the-csproj"></a>Modifier le fichier csproj

> [!NOTE]
> Si le fichier csproj ne figure pas dans le répertoire, vous pouvez l’ajouter manuellement ou il apparaîtra une fois l’extension `Microsoft.Azure.WebJobs.Extensions.Storage` installée sur la fonction.

Ensuite, mettez à jour le fichier csproj en y ajoutant des bibliothèques NuGet utiles qui facilitent l’analyse de demandes entrantes en provenance de fournisseurs personnalisés. Suivez les étapes pour [ajouter des extensions à partir du portail](../azure-functions/install-update-binding-extensions-manual.md) et mettez à jour le fichier csproj pour y ajouter les références de package suivantes :

```xml
<PackageReference Include="Microsoft.Azure.WebJobs.Extensions.Storage" Version="3.0.4" />
<PackageReference Include="Microsoft.Azure.Management.ResourceManager.Fluent" Version="1.22.2" />
<PackageReference Include="Microsoft.Azure.WebJobs.Script.ExtensionsMetadataGenerator" Version="1.1.*" />
```

Exemple de fichier csproj :

```xml
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <TargetFramework>netstandard2.0</TargetFramework>
    <WarningsAsErrors />
  </PropertyGroup>
  <ItemGroup>
    <PackageReference Include="Microsoft.Azure.WebJobs.Extensions.Storage" Version="3.0.4" />
    <PackageReference Include="Microsoft.Azure.Management.ResourceManager.Fluent" Version="1.22.2" />
    <PackageReference Include="Microsoft.Azure.WebJobs.Script.ExtensionsMetadataGenerator" Version="1.1.*" />
  </ItemGroup>
</Project>
```

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, nous avons préparé une fonction Azure pour qu’elle fonctionne comme `endpoint` d’un fournisseur personnalisé Azure. Accédez à l’article suivant pour apprendre à créer un `endpoint` de fournisseur personnalisé RESTful.

- [Tutoriel : Création d’un point de terminaison de fournisseur personnalisé RESTful](./tutorial-custom-providers-function-authoring.md)
