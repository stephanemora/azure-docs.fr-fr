---
title: Modifications apportées aux projets ASP.NET Core lorsque vous vous connectez à Azure Key Vault | Microsoft Docs
description: Décrit ce qui se passe pour votre projet ASP.NET Core lorsque vous vous connectez à Key Vault à l’aide des services connectés Visual Studio.
services: key-vault
author: ghogen
manager: douge
tags: azure-resource-manager
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 04/15/2018
ms.author: ghogen
ms.openlocfilehash: b7cbe55fa3a524965e0ebc16c5ff350a60d6e440
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/18/2018
ms.locfileid: "42140688"
---
# <a name="what-happened-to-my-aspnet-core-project-visual-studio-key-vault-connected-service"></a>Qu’est-il arrivé à mon projet ASP.NET Core (service connecté Key Vault Visual Studio) ?

> [!div class="op_single_selector"]
> - [Prise en main](vs-key-vault-aspnet-core-get-started.md)
> - [Que s'est-il passé ?](vs-key-vault-aspnet-core-what-happened.md)

Cet article identifie les changements exacts apportés à un projet ASP.NET lors de l’ajout du [Service connecté Key Vault avec Visual Studio](vs-key-vault-add-connected-service.md).

Pour plus d’informations sur l’utilisation du service connecté, consultez la page [Bien démarrer](vs-key-vault-aspnet-core-get-started.md).

## <a name="added-references"></a>Références ajoutées

Affecte les références *.NET du fichier projet et les références de package NuGet.

| type | Informations de référence |
| --- | --- |
| NuGet | Microsoft.AspNetCore.AzureKeyVault.HostingStartup |

## <a name="added-files"></a>Fichiers ajoutés

- Ajout de ConnectedService.json, qui enregistre des informations sur le fournisseur et la version du service connecté, ainsi qu’un lien vers la documentation.

## <a name="project-file-changes"></a>Modifications apportées au fichier projet

- Ajout de l’ItemGroup des services connectés et du fichier ConnectedServices.json.

## <a name="launchsettingsjson-changes"></a>Modifications de launchsettings.json

- Ajout des entrées de variables d’environnement suivantes au profil IIS Express et au profil qui correspond au nom de votre projet web :

    ```json
      "environmentVariables": {
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONENABLED": "true",
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONVAULT": "<your keyvault URL>"
      }
    ```

## <a name="changes-on-azure"></a>Modifications apportées à Azure

- Création d’un groupe de ressources (ou utilisation d’un groupe existant).
- Création d’un coffre de clés dans le groupe de ressources spécifié.

