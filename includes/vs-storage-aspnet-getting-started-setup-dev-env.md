---
title: Fichier Include
description: Fichier Include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/15/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: a7c696870e22e1692ca5ed778e47f8e4cc00615a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "67176964"
---
## <a name="set-up-the-development-environment"></a>Configuration de l’environnement de développement

Cette section explique les étapes à effectuer pour configurer l’environnement de développement. Création d’une application MVC ASP.NET, ajout d’une connexion Services connectés, ajout d’un contrôleur et spécification des directives sur les espaces de noms obligatoires.

### <a name="create-an-aspnet-mvc-app-project"></a>Création d’un projet d’application MVC ASP.NET

1. Ouvrez Visual Studio.

1. Dans le menu principal, sélectionnez **Fichier** > **Nouveau** > **Projet**.

1. Dans la boîte de dialogue **Nouveau projet**, sélectionnez **Web** > **Application web ASP.NET (.NET Framework)** . Dans le champ **Nom**, spécifiez **StorageAspNet**. Sélectionnez **OK**.

    ![Capture d’écran de la boîte de dialogue Nouveau projet](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-1.png)

1. Dans la boîte de dialogue **Nouvelle application web ASP.NET**, sélectionnez **MVC**, puis sélectionnez **OK**.

    ![Capture d’écran de la boîte de dialogue Nouvelle application web ASP.NET](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-2.png)

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>Utiliser les services connectés pour se connecter à un compte de stockage Azure

1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet.

1. Dans le menu contextuel, sélectionnez **Ajouter** > **Service connecté**.

1. Dans la boîte de dialogue **Services connectés**, sélectionnez **Stockage cloud avec Stockage Azure**.

    ![Capture d’écran de la boîte de dialogue Services connectés](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-3.png)

1. Dans la boîte de dialogue **Stockage Azure**, sélectionnez le compte de stockage Azure à utiliser dans le cadre de ce didacticiel. Pour créer un compte de stockage Azure, sélectionnez **Créer un compte de stockage** et remplissez le formulaire. Après avoir sélectionné un compte de stockage existant ou en avoir créé un, sélectionnez **Ajouter**. Visual Studio installe le package NuGet pour le Stockage Azure et une chaîne de connexion de stockage pour **Web.config**.

1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur **Dépendances**, choisissez **Gérer les packages NuGet**et ajoutez une référence de package NuGet à la dernière version de Microsoft.Azure.ConfigurationManager.

> [!TIP]
> Pour apprendre à créer un compte de stockage avec le [portail Azure](https://portal.azure.com), consultez [Créez un compte de stockage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account).
>
> Il est également possible de créer un compte de stockage Azure avec [Azure PowerShell](../articles/storage/common/storage-powershell-guide-full.md), [Azure CLI](../articles/storage/common/storage-azure-cli.md) ou [Azure Cloud Shell](../articles/cloud-shell/overview.md).
