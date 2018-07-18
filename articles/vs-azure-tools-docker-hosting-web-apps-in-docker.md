---
title: Déployer un conteneur Docker ASP.NET sur Azure Container Registry (ACR) | Microsoft Docs
description: Découvrez comment utiliser Visual Studio Tools pour Docker afin de déployer une application web ASP.NET Core dans un registre de conteneurs.
services: azure-container-service
documentationcenter: .net
author: mlearned
manager: douge
editor: ''
ms.assetid: e5e81c5e-dd18-4d5a-a24d-a932036e78b9
ms.service: azure-container-service
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/21/2018
ms.author: mlearned
ms.openlocfilehash: 58df17b17de1d93683875b68dd7c6c087bc6d16d
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38972306"
---
# <a name="deploy-an-aspnet-container-to-a-container-registry-using-visual-studio"></a>Déployer un conteneur ASP.NET dans un registre de conteneurs à l’aide de Visual Studio
## <a name="overview"></a>Vue d'ensemble
Docker est un moteur de conteneur léger, semblable à certains égards à une machine virtuelle, que vous pouvez utiliser pour héberger des applications et des services.
Ce tutoriel vous guide dans l’utilisation de Visual Studio pour publier votre application en conteneur sur un [registre de conteneurs Azure](https://azure.microsoft.com/services/container-registry).

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/dotnet/?utm_source=acr-publish-doc&utm_medium=docs&utm_campaign=docs) avant de commencer.

## <a name="prerequisites"></a>Prérequis
Pour suivre ce tutoriel :

* Installer la dernière version de [Visual Studio 2017](https://azure.microsoft.com/downloads/) avec la charge de travail « Développement web et ASP.NET »
* Installer [Docker pour Windows](https://docs.docker.com/docker-for-windows/install/)

## <a name="1-create-an-aspnet-core-web-app"></a>1. Créez une application web ASP.NET Core
La procédure suivante vous accompagne dans la création d’une application ASP.NET Core qui sera utilisée dans ce didacticiel.

[!INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## <a name="2-publish-your-container-to-azure-container-registry"></a>2. Publier votre conteneur Docker sur Azure Container Registry
1. Cliquez avec le bouton droit sur votre projet dans l’**Explorateur de solutions** et choisissez **Publier**.
2. Dans la boîte de dialogue de la cible de publication, sélectionnez l’onglet **Registre de conteneurs**.
3. Choisissez **Nouveau registre de conteneurs Azure** et cliquez sur **Publier**.
4. Renseignez les valeurs souhaitées dans **Créer un registre de conteneurs Azure**.

    | Paramètre      | Valeur suggérée  | Description                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Préfixe DNS** | Nom globalement unique | Nom qui identifie uniquement votre registre de conteneurs. |
    | **Abonnement** | Choisissez votre abonnement | Sélectionnez l’abonnement Azure à utiliser. |
    | **[Groupe de ressources](../articles/azure-resource-manager/resource-group-overview.md)** | myResourceGroup |  Nom du groupe de ressources où créer votre registre de conteneurs. Choisissez **Nouveau** pour créer un groupe de ressources.|
    | **[SKU](https://docs.microsoft.com/azure/container-registry/container-registry-skus)** | Standard | Niveau de service du registre de conteneurs  |
    | **Emplacement du registre** | Un emplacement proche de vous | Choisissez un emplacement dans une [région](https://azure.microsoft.com/regions/) près de chez vous ou près d’autres services que votre registre de conteneurs va utiliser. |
    ![Boîte de dialogue de création d’un registre de conteneurs Azure dans Visual Studio][0]
5. Cliquez sur **Créer**

Vous pouvez désormais extraire le conteneur à partir du registre sur tout hôte en mesure d’exécuter des images Docker, par exemple [Azure Container Instances](./container-instances/container-instances-tutorial-deploy-app.md).

[0]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/vs-acr-provisioning-dialog.png
