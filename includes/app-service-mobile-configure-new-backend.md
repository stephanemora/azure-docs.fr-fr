---
title: Fichier include
description: Fichier include
services: app-service\mobile
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/06/2019
ms.author: crdun
ms.custom: include file
ms.openlocfilehash: a7e543dcad9ad1b016d1244451cd87cda5ad7492
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "67440222"
---
1. Télécharger les démarrages rapides du kit de développement logiciel (SDK) client pour les plateformes suivantes :
    
    [iOS (Objective-C)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/iOS)  
    [iOS (Swift)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/iOS-Swift)  
    [Android (Java)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/android)  
    [Xamarin.iOS](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.iOS)  
    [Xamarin.Android](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.android)  
    [Xamarin.Forms](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.forms)  
    [Cordova](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/cordova)  
    [Windows (C#)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/windows-uwp-cs)  

    > [!NOTE]
    > Si vous utilisez le projet iOS, vous devez télécharger « azuresdk-iOS-\*.zip » à partir de la [toute dernière version de GitHub](https://github.com/Azure/azure-mobile-apps-ios-client/releases/latest). Décompressez et ajoutez le fichier `MicrosoftAzureMobile.framework` à la racine du projet.
    >

2. Vous devrez ajouter une connexion de base de données ou vous connecter à une connexion existante. Tout d’abord, déterminez si vous allez créer un magasin de données ou en utiliser un existant.

    - **Créer une nouveau magasin de données** : Si vous créez un magasin de données, utilisez le démarrage rapide suivant :

        [Démarrage rapide : Prise en main de bases de données uniques dans Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-quickstart-guide)

    - **Source de données existante** : Suivez les instructions ci-dessous si vous souhaitez utiliser une connexion de base de données existante

        1. Format de chaîne de connexion à la base de données SQL - `Data Source=tcp:{your_SQLServer},{port};Initial Catalog={your_catalogue};User ID={your_username};Password={your_password}`

           **{your_SQLServer}** Nom du serveur, vous le trouverez dans la page de vue d’ensemble de votre base de données. Il se présente généralement comme suit : « nom_serveur.database.windows.net ».
            **{port}** généralement 1433.
            **{your_catalogue}** Nom de la base de données.
            **{your_username}** Nom d’utilisateur pour accéder à votre base de données.
            **{your_password}**  Mot de passe pour accéder à votre base de données.

            [En savoir plus sur le format de chaîne de connexion SQL](https://docs.microsoft.com/dotnet/framework/data/adonet/connection-string-syntax#sqlclient-connection-strings)

        2. Ajoutez la chaîne de connexion à votre **application mobile** dans App Service, vous pouvez gérer les chaînes de connexion pour votre application à l’aide de l’option **Configuration** dans le menu.

            Pour ajouter une chaîne de connexion :

            1. Cliquez sur l’onglet **Paramètres de l’application**.

            2. Cliquez sur **[+] Nouveau chaîne de connexion**.

            3. Vous devrez fournir le **Nom**, la **Valeur** et le **Type** pour votre chaîne de connexion.

            4. Pour le **Nom**, saisissez `MS_TableConnectionString`

            5. La valeur doit correspondre à la chaîne de connexion que vous avez formée à l’étape précédente.

            6. Si vous ajoutez une chaîne de connexion à une base de données SQL Azure, choisissez **SQLAzure** sous **Type**.

3. Azure Mobile Apps a des kits de développement logiciel (SDK) pour les serveurs principaux .NET et Node.js.

   - **Backend Node.js**
    
     Si vous prévoyez d’utiliser l’application de démarrage rapide Node.js, suivez les instructions ci-dessous.

     1. Dans le portail Azure, accédez à **Tables faciles**, vous verrez cet écran.
      
        ![Tables faciles Node](./media/app-service-mobile-configure-new-backend/node-easy-tables.png)

     2. Assurez-vous que la chaîne de connexion SQL a déjà été ajoutée dans l’onglet **Configuration**. Cochez ensuite la case **Je reconnais que cette opération va remplacer tout le contenu du site**, puis cliquez sur le bouton **Créer une table TodoItem**.
     
        ![Tables faciles Node - Configuration](./media/app-service-mobile-configure-new-backend/node-easy-tables-configuration.png)

     3. Dans **Tables faciles**, cliquez sur le bouton **+ Ajouter**.
    
        ![Tables faciles Node - Bouton Ajouter](./media/app-service-mobile-configure-new-backend/node-easy-tables-add.png)

     4. Créez une table `TodoItem` avec l’accès anonyme.
      
        ![Tables faciles Node - Ajouter une table](./media/app-service-mobile-configure-new-backend/node-easy-tables-table-add.png)

   - **Backend .NET**
    
        Si vous prévoyez d’utiliser l’application de démarrage rapide .NET, suivez les instructions ci-dessous.

        1. Téléchargez le projet de serveur .NET Azure Mobile Apps à partir du [référentiel azure-mobile-apps-quickstarts](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/backend/dotnet/Quickstart).

        2. Générez le projet de serveur .NET localement dans Visual Studio.

        3. Dans Visual Studio, ouvrez l’Explorateur de solutions, cliquez avec le bouton droit sur le projet `ZUMOAPPNAMEService`, cliquez sur **Publier**, vous verrez une fenêtre `Publish to App Service`. Si vous travaillez sur Mac, découvrez d’autres façons de déployer l’application [ici](https://docs.microsoft.com/azure/app-service/deploy-local-git).
        
           ![Publication de Visual studio](./media/app-service-mobile-configure-new-backend/visual-studio-publish.png)

        4. Sélectionnez **App Service** comme cible de publication, puis cliquez sur **Sélectionner existant**, puis sur le bouton **Publier** bouton au bas de la fenêtre.

        5. Vous devrez d’abord vous connecter à Visual Studio avec votre abonnement Azure. Sélectionnez `Subscription`, `Resource Group`, puis le nom de votre application. Lorsque vous êtes prêt, cliquez sur **OK**, cette action déploie le projet serveur .NET que vous avez localement dans le serveur principal App Service. Lorsque le déploiement se termine, vous êtes redirigé vers `http://{zumoappname}.azurewebsites.net/` dans le navigateur.                   