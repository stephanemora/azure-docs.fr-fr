---
title: Bien démarrer avec les applications Xamarin.iOS
description: Suivez ce didacticiel pour commencer à utiliser Mobile Apps pour le développement Xamarin.iOS.
ms.assetid: 14428794-52ad-4b51-956c-deb296cafa34
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: 6c35189e7c841fa2724f1cfe84afc689d5510676
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77459002"
---
# <a name="create-a-xamarinios-app"></a>Création d’une application Xamarin.iOS
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Vue d’ensemble
Ce didacticiel vous montre comment ajouter un service de serveur principal basé sur le cloud à une application mobile Xamarin.iOS à l’aide d’un serveur principal d’application mobile Azure.  Vous allez créer un serveur principal d’application mobile et une simple application Xamarin.iOS *Todo list* qui stocke les données d’application dans Azure.

Le suivi de ce didacticiel est un prérequis pour tous les autres didacticiels Xamarin.iOS sur l’utilisation de la fonctionnalité Mobile Apps dans Azure App Service.

## <a name="prerequisites"></a>Prérequis
Pour effectuer ce didacticiel, vous avez besoin de ce qui suit :

* Un compte Azure actif. Si vous n'avez pas de compte, inscrivez-vous pour une évaluation d'Azure et obtenir jusqu'à 10 applications mobiles gratuites que vous pourrez conserver après l'expiration de votre période d'évaluation. Pour plus d’informations, consultez [Essai gratuit Azure](https://azure.microsoft.com/pricing/free-trial/).
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
