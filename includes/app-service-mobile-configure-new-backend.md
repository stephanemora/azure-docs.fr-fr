---
title: Fichier Include
description: Fichier Include
services: app-service\mobile
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/06/2019
ms.author: crdun
ms.custom: include file
ms.openlocfilehash: 99ca7e82a11687d25355589e7ea539a14cdb493b
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66420703"
---
1. Télécharger le client SDK guides de démarrage rapide pour les plateformes suivantes :
    
    [iOS (Objective-C)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/iOS)  
    [iOS (Swift)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/iOS-Swift)  
    [Android (Java)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/android)  
    [Xamarin.iOS](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.iOS)  
    [Xamarin.Android](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.android)  
    [Xamarin.Forms](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.forms)  
    [Cordova](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/cordova)  
    [Windows (C#)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/windows-uwp-cs)  

    > [!NOTE]
    > Si vous utilisez le projet iOS, vous devez la télécharger « azuresdk-iOS -\*.zip » à partir de [dernière version de GitHub](https://github.com/Azure/azure-mobile-apps-ios-client/releases/latest). Décompressez et ajouter la `MicrosoftAzureMobile.framework` fichier à la racine du projet.
    >

2. Vous devrez ajouter une connexion de base de données ou de se connecter à une connexion existante. Tout d’abord, déterminez si vous allez créer un magasin de données ou utilisez-en un existant.

    - **Créer un nouveau magasin de données**: Si vous allez créer un magasin de données, utilisez le démarrage rapide suivant :

        [Démarrage rapide : Prise en main des bases de données uniques dans la base de données SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-quickstart-guide)

    - **Source de données existante**: Suivez les instructions ci-dessous si vous souhaitez utiliser une connexion de base de données existante

        1. Format de chaîne de connexion de base de données SQL : `Data Source=tcp:{your_SQLServer},{port};Initial Catalog={your_catalogue};User ID={your_username};Password={your_password}`

           **{your_SQLServer}**  Nom du serveur, cela peut être trouvé dans la page de vue d’ensemble de votre base de données et est généralement sous la forme de « server_name.database.windows.net ».
            **{port}**  généralement 1433.
            **{your_catalogue}**  Nom de la base de données.
            **{your_username}**  Nom d’utilisateur à accéder à votre base de données.
            **{your_password}**  Mot de passe pour accéder à votre base de données.

            [En savoir plus sur le format de chaîne de connexion SQL](https://docs.microsoft.com/dotnet/framework/data/adonet/connection-string-syntax#sqlclient-connection-strings)

        2. Ajoutez la chaîne de connexion dans votre **application mobile** dans App Service, vous pouvez gérer les chaînes de connexion pour votre application à l’aide de la **Configuration** option dans le menu.

            Pour ajouter une chaîne de connexion :

            1. Cliquez sur le **paramètres d’Application** onglet.

            2. Cliquez sur **[+] nouvelle chaîne de connexion**.

            3. Vous devrez fournir **nom**, **valeur** et **Type** pour votre chaîne de connexion.

            4. Type **nom** en tant que `MS_TableConnectionString`

            5. Valeur doit être la chaîne de connexion que vous formé à l’étape avant.

            6. Si vous ajoutez une chaîne de connexion à une base de données SQL Azure choisissez **SQLAzure** sous **type**.

3. Azure Mobile Apps a des kits de développement logiciel pour les principaux de .NET et Node.js.

   - **Backend Node.js**
    
     Si vous prévoyez d’utiliser l’application de démarrage rapide Node.js, suivez les instructions ci-dessous.

     1. Dans le portail Azure, accédez à **Tables faciles**, vous verrez cet écran.
      
        ![Nœud des Tables faciles](./media/app-service-mobile-configure-new-backend/node-easy-tables.png)

     2. Assurez-vous que la chaîne de connexion SQL a déjà été ajoutée dans le **Configuration** onglet. Cochez la case de **je reconnais que cette opération va remplacer tout contenu de site** et cliquez sur le **créer une table TodoItem** bouton.
     
        ![Configuration du nœud Tables faciles](./media/app-service-mobile-configure-new-backend/node-easy-tables-configuration.png)

     3. Dans **Tables faciles**, cliquez sur le **+ ajouter** bouton.
    
        ![Bouton Ajouter un nœud des Tables faciles](./media/app-service-mobile-configure-new-backend/node-easy-tables-add.png)

     4. Créer un `TodoItem` table avec l’accès anonyme.
      
        ![Ajouter des Tables faciles de nœud Table](./media/app-service-mobile-configure-new-backend/node-easy-tables-table-add.png)

   - **Backend .NET**
    
        Si vous prévoyez d’utiliser l’application de démarrage rapide de .NET, suivez les instructions ci-dessous.

        1. Télécharger le projet de serveur .NET Azure Mobile Apps à partir de la [azure-mobile-apps-guides de démarrage rapide référentiel](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/backend/dotnet/Quickstart).

        2. Générez le projet de serveur .NET localement dans Visual Studio.

        3. Dans Visual Studio, ouvrez l’Explorateur de solutions, avec le bouton droit sur `ZUMOAPPNAMEService` du projet, cliquez sur **publier**, vous verrez un `Publish to App Service` fenêtre. Si vous travaillez sur Mac, découvrez d’autres façons de déployer l’application [ici](https://docs.microsoft.com/en-us/azure/app-service/deploy-local-git).
        
           ![Publication de Visual studio](./media/app-service-mobile-configure-new-backend/visual-studio-publish.png)

        4. Sélectionnez **App Service** comme cible de publication, puis cliquez sur **sélectionner**, puis cliquez sur le **publier** bouton en bas de la fenêtre.

        5. Vous devrez d’abord de vous connecter à Visual Studio avec votre abonnement Azure. Sélectionnez le `Subscription`, `Resource Group`, puis sélectionnez le nom de votre application. Lorsque vous êtes prêt, cliquez sur **OK**, cette action déploie le projet de serveur .NET que vous avez localement dans le backend App Service. Lorsque le déploiement terminé, vous êtes redirigé vers `http://{zumoappname}.azurewebsites.net/` dans le navigateur.
        
           ![Serveur principal est opérationnel](./media/app-service-mobile-configure-new-backend/backend-is-up.png)
