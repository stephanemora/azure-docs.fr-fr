---
title: Modifications apportées aux projets ASP.NET lorsque vous vous connectez à Azure Key Vault | Microsoft Docs
description: Décrit ce qui se passe pour votre projet ASP.NET lorsque vous vous connectez à Key Vault à l’aide des services connectés Visual Studio.
services: key-vault
author: ghogen
manager: douge
tags: azure-resource-manager
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 04/15/2018
ms.author: ghogen
ms.openlocfilehash: a74e4e10681f6b91e028067d8985408b0745dcd2
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/18/2018
ms.locfileid: "42140277"
---
# <a name="what-happened-to-my-aspnet-project-visual-studio-key-vault-connected-service"></a>Qu’est-il arrivé à mon projet ASP.NET (service connecté Key Vault Visual Studio) ?

> [!div class="op_single_selector"]
> - [Prise en main](vs-key-vault-aspnet-get-started.md)
> - [Que s'est-il passé ?](vs-key-vault-aspnet-what-happened.md)

Cet article identifie les changements exacts apportés à un projet ASP.NET lors de l’ajout du [Service connecté Key Vault avec Visual Studio](vs-key-vault-add-connected-service.md).

Pour plus d’informations sur l’utilisation du service connecté, consultez la page [Bien démarrer](vs-key-vault-aspnet-get-started.md).

## <a name="added-references"></a>Références ajoutées

Affecte les références *.NET du fichier projet et `packages.config` (références NuGet).

| type | Informations de référence |
| --- | --- |
| .NET ; NuGet | Microsoft.Azure.KeyVault |
| .NET ; NuGet | Microsoft.Azure.KeyVault.WebKey |
| .NET ; NuGet | Microsoft.Rest.ClientRuntime |
| .NET ; NuGet | Microsoft.Rest.ClientRuntime.Azure |

## <a name="added-files"></a>Fichiers ajoutés

- Ajout de ConnectedService.json, qui enregistre des informations sur le fournisseur et la version du service connecté, ainsi qu’un lien vers la documentation.

## <a name="project-file-changes"></a>Modifications apportées au fichier projet

- Ajout de l’ItemGroup des services connectés et du fichier ConnectedServices.json.
- Références aux assemblys .NET décrits dans la section [Références ajoutées](#added-references).

## <a name="webconfig-or-appconfig-changes"></a>Modifications apportées à web.config ou à app.config

- Ajout des entrées de configuration suivantes :

    ```xml
    <appSettings>
       <add key="vaultName" value="<your Key Vault name>" />
       <add key="vaultUri" value="<the URI to your Key Vault in Azure>" />
    </appSettings>
    ```

## <a name="changes-on-azure"></a>Modifications apportées à Azure

- Création d’un groupe de ressources (ou utilisation d’un groupe existant).
- Création d’un coffre de clés dans le groupe de ressources spécifié.

