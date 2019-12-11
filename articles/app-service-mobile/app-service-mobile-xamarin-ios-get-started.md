---
title: Bien démarrer avec les applications Xamarin.iOS
description: Suivez ce didacticiel pour commencer à utiliser Mobile Apps pour le développement Xamarin.iOS.
ms.assetid: 14428794-52ad-4b51-956c-deb296cafa34
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: 2bb13393a9f348d85ab9fc964b784ba4d4d6a783
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668629"
---
# <a name="create-a-xamarinios-app"></a>Création d’une application Xamarin.iOS
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> Visual Studio App Center prend en charge les services intégrés essentiels au développement d’applications mobiles. Les développeurs peuvent utiliser les services **Build**, **Test** et **Distribute** pour configurer le pipeline de livraison et d’intégration continues. Une fois l’application déployée, les développeurs peuvent superviser l’état et l’utilisation de leur application à l’aide des services **Analytics** et **Diagnostics**, puis interagir avec les utilisateurs à l’aide du service **Push**. Les développeurs peuvent aussi utiliser **Auth** pour authentifier leurs utilisateurs ainsi que le service **Data** pour conserver et synchroniser les données d’application dans le cloud.
>
> Si vous souhaitez intégrer des services cloud à votre application mobile, inscrivez-vous à [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) dès aujourd’hui.

## <a name="overview"></a>Vue d'ensemble
Ce didacticiel vous montre comment ajouter un service de serveur principal basé sur le cloud à une application mobile Xamarin.iOS à l’aide d’un serveur principal d’application mobile Azure.  Vous allez créer un serveur principal d’application mobile et une simple application Xamarin.iOS *Todo list* qui stocke les données d’application dans Azure.

Le suivi de ce didacticiel est un prérequis pour tous les autres didacticiels Xamarin.iOS sur l’utilisation de la fonctionnalité Mobile Apps dans Azure App Service.

## <a name="prerequisites"></a>Prérequis
Pour effectuer ce didacticiel, vous avez besoin de ce qui suit :

* Un compte Azure actif. Si vous n'avez pas de compte, inscrivez-vous pour une évaluation d'Azure et obtenir jusqu'à 10 applications mobiles gratuites que vous pourrez conserver après l'expiration de votre période d'évaluation. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/).
* Visual Studio pour Mac. Consultez [Configuration et installation pour Visual Studio pour Mac](https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019)
* Un Mac avec Xcode 9.0 ou version ultérieure.
  
## <a name="create-an-azure-mobile-app-backend"></a>Créer un serveur principal d'applications mobiles Azure
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Créer une connexion de base de données et configurer le projet client et de serveur
[!INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-xamarinios-app"></a>Exécuter l’application Xamarin.iOS
1. Ouvrez le projet Xamarin.iOS.

2. Ouvrez le [Portail Microsoft Azure](https://portal.azure.com/), puis accédez à l’application mobile que vous avez créée. Sur le panneau `Overview`, recherchez l’URL qui correspond au point de terminaison public de votre application mobile. Exemple : la valeur sitename relative au nom de l’application « test123 » est https://test123.azurewebsites.net.

3. Ouvrez le fichier `QSTodoService.cs` dans le dossier xamarin.iOS/ZUMOAPPNAME. Le nom de l’application est `ZUMOAPPNAME`.

4. Dans la classe `QSTodoService`, remplacez la variable `ZUMOAPPURL` par le point de terminaison public ci-dessus.

    `const string applicationURL = @"ZUMOAPPURL";`

    devient
    
    `const string applicationURL = @"https://test123.azurewebsites.net";`
    
5. Appuyez sur la touche F5 pour déployer et exécuter l’application sur un émulateur iPhone.

6. Dans l’application, tapez un texte explicite, comme *Suivre le didacticiel*, puis cliquez sur le bouton +.

    ![][10]

    Les données de la requête sont insérées dans la table TodoItem. Les éléments stockés dans cette table sont renvoyés par le serveur principal d'applications mobiles et les données apparaissent dans la liste.

   > [!NOTE]
   > Vous pouvez vérifier le code qui se trouve dans le fichier C# ToDoActivity.cs et permet d'accéder à votre backend d'application mobile pour exécuter une requête et insérer des données.
   
<!-- Images. -->
[10]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-quickstart-startup-ios.png