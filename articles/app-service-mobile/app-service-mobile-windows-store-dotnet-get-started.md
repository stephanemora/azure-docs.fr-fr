---
title: Créer une application UWP
description: Suivez ce didacticiel pour commencer à utiliser des services principaux d’applications mobiles Azure pour le développement d’applications UWP en C#, Visual Basic ou JavaScript.
ms.assetid: 47124296-2908-4d92-85e0-05c4aa6db916
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: a0f78239406567513c1eb94b48bf1090165d9185
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668700"
---
# <a name="create-a-windows-app-with-an-azure-backend"></a>Créer une application Windows avec un back-end Azure

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> Visual Studio App Center prend en charge les services intégrés essentiels au développement d’applications mobiles. Les développeurs peuvent utiliser les services **Build**, **Test** et **Distribute** pour configurer le pipeline de livraison et d’intégration continues. Une fois l’application déployée, les développeurs peuvent superviser l’état et l’utilisation de leur application à l’aide des services **Analytics** et **Diagnostics**, puis interagir avec les utilisateurs à l’aide du service **Push**. Les développeurs peuvent aussi utiliser **Auth** pour authentifier leurs utilisateurs ainsi que le service **Data** pour conserver et synchroniser les données d’application dans le cloud.
>
> Si vous souhaitez intégrer des services cloud à votre application mobile, inscrivez-vous à [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) dès aujourd’hui.

## <a name="overview"></a>Vue d'ensemble

Ce didacticiel présente l’ajout d’un service principal cloud à une application de plateforme Windows universelle (UWP). Pour plus d’informations, consultez [Que sont les applications Mobile Apps ?](app-service-mobile-value-prop.md). Voici les captures d’écran générées à partir de l’application terminée :

![Application de bureau terminée](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed-desktop.png)

Vous devez suivre ce didacticiel avant de pouvoir suivre les autres didacticiels Mobile App pour les applications UWP.

## <a name="prerequisites"></a>Prérequis

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Un compte Azure actif. Si vous n'avez pas de compte, vous pouvez vous inscrire pour une évaluation d'Azure et obtenir jusqu'à 10 applications mobiles gratuites que vous pourrez conserver après l'expiration de votre période d'évaluation. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/).
* Windows 10.
* Visual Studio Community 2017.
* Connaissance du développement d’applications UWP. Consultez la [documentation UWP](https://docs.microsoft.com/windows/uwp/) pour savoir comment vous [préparer](https://docs.microsoft.com/windows/uwp/get-started/get-set-up) à créer des applications UWP.

## <a name="create-a-new-azure-mobile-app-backend"></a>Créer un serveur principal d'applications mobiles Azure

Suivez ces étapes pour créer un serveur principal d’application mobile.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Créer une connexion de base de données et configurer le projet client et de serveur
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-client-project"></a>Exécuter le projet client

1. Ouvrez le projet UWP.

2. Ouvrez le [Portail Microsoft Azure](https://portal.azure.com/), puis accédez à l’application mobile que vous avez créée. Sur le panneau `Overview`, recherchez l’URL qui correspond au point de terminaison public de votre application mobile. Exemple : la valeur sitename relative au nom de l’application « test123 » est https://test123.azurewebsites.net.

3. Ouvrez le fichier `App.xaml.cs` dans le dossier windows-uwp-cs/ZUMOAPPNAME/. Le nom de l’application est `ZUMOAPPNAME`.

4. Dans la classe `App`, remplacez le paramètre `ZUMOAPPURL` par le point de terminaison public ci-dessus.

    `public static MobileServiceClient MobileService = new MobileServiceClient("ZUMOAPPURL");`

    devient
    
    `public static MobileServiceClient MobileService = new MobileServiceClient("https://test123.azurewebsites.net");`

5. Appuyez sur la touche F5 pour déployer et exécuter l'application.

6. Dans l’application, tapez un texte explicite, comme *Suivre le didacticiel*, dans la zone de texte **Insérer un TodoItem**, puis cliquez sur **Enregistrer**.

    ![Démarrage rapide Windows - Bureau terminé](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-startup.png)

    Ceci envoie une demande POST vers le nouveau backend d'application mobile qui est hébergé dans Azure.
