---
title: Bien démarrer avec les applications Xamarin.Android
description: Suivez ce tutoriel pour bien démarrer avec l’utilisation d’Azure Mobile Apps pour le développement d’applications Xamarin Android.
ms.assetid: 81649dd3-544f-40ff-b9b7-60c66d683e60
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: b42205436c88f9075423bfcaf9e5a9fd931ee4f4
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461366"
---
# <a name="create-a-xamarinandroid-app"></a>Création d'une application Xamarin.Android
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Vue d’ensemble
Ce didacticiel montre comment ajouter un service backend cloud à une application Xamarin Android. Pour plus d’informations, consultez [Que sont les applications Mobile Apps ?](app-service-mobile-value-prop.md).

Voici une capture d’écran de l’application terminée :

![][0]

Vous devez suivre ce didacticiel avant de pouvoir suivre tous les autres didacticiels Mobile Apps pour les applications Xamarin.Android.

## <a name="prerequisites"></a>Prérequis
Pour effectuer ce didacticiel, vous avez besoin de ce qui suit :

* Un compte Azure actif. Si vous n’avez pas de compte, inscrivez-vous pour obtenir une version d’évaluation Azure et jusqu’à 10 applications mobiles gratuites. Pour plus d’informations, consultez [Essai gratuit Azure](https://azure.microsoft.com/pricing/free-trial/).
* Visual Studio avec Xamarin. Pour obtenir des instructions, consultez la page [Configuration et installation pour Visual Studio et Xamarin](/visualstudio/cross-platform/setup-and-install) .

## <a name="create-an-azure-mobile-app-backend"></a>Créer un serveur principal d'applications mobiles Azure
Suivez ces étapes pour créer un serveur principal d’application mobile.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Vous avez maintenant configuré un serveur principal d’application mobile Azure qui peut être utilisé par vos applications clientes mobiles. Téléchargez ensuite un projet de serveur pour un serveur principal « todo list » simple et le publier dans Azure.

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Créer une connexion de base de données et configurer le projet client et de serveur
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-xamarinandroid-app"></a>Exécuter l’application Xamarin.Android
1. Ouvrez le projet Xamarin.Android.

2. Ouvrez le [Portail Microsoft Azure](https://portal.azure.com/), puis accédez à l’application mobile que vous avez créée. Sur le panneau `Overview`, recherchez l’URL qui correspond au point de terminaison public de votre application mobile. Exemple : la valeur sitename relative au nom de l’application « test123 » est https://test123.azurewebsites.net.

3. Ouvrez le fichier `ToDoActivity.cs` dans ce dossier - xamarin.android/ZUMOAPPNAME/ToDoActivity.cs. Le nom de l’application est `ZUMOAPPNAME`.

4. Dans la classe `ToDoActivity`, remplacez la variable `ZUMOAPPURL` par le point de terminaison public ci-dessus.

    `const string applicationURL = @"ZUMOAPPURL";`

    devient
    
    `const string applicationURL = @"https://test123.azurewebsites.net";`
    
5. Appuyez sur la touche F5 pour déployer et exécuter l’application.

6. Dans l’application, tapez un texte explicite, comme *Suivre le didacticiel* , puis cliquez sur l’icône **Ajouter**.

    ![][10]

    Les données de la requête sont insérées dans la table TodoItem. Les éléments stockés dans cette table sont renvoyés par le serveur principal d'applications mobiles et les données apparaissent dans la liste.

   > [!NOTE]
   > Vous pouvez vérifier le code qui se trouve dans le fichier C# ToDoActivity.cs et permet d'accéder à votre backend d'application mobile pour exécuter une requête et insérer des données.
   
## <a name="troubleshooting"></a>Dépannage
Si vous avez des difficultés à générer la solution, exécutez le gestionnaire de package NuGet et mettez à jour les packages de support `Xamarin.Android`. Les projets de démarrage rapide n’incluent pas toujours les dernières versions.

Veuillez noter que tous les packages de support référencés dans le projet doivent avoir la même version. Le [package NuGet Azure Mobile Apps](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) comporte une dépendance `Xamarin.Android.Support.CustomTabs` à la plateforme Android. Par conséquent, si votre projet utilise des packages de support plus récents, vous devez installer directement ce package avec la version requise pour éviter les conflits.

<!-- Images. -->
[0]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-completed-android.png
[10]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-startup-android.png
<!-- URLs. -->
[Visual Studio]: https://go.microsoft.com/fwLink/p/?LinkID=534203
