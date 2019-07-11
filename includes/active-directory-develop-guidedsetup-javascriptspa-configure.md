---
title: Fichier Include
description: Fichier Include
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 050bae64a62e90bdb74c93948109e255b69d7518
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133546"
---
## <a name="register-your-application"></a>Inscrivez votre application

1. Connectez-vous au [Portail Azure](https://portal.azure.com/).

1. Si votre compte vous propose un accès à plusieurs locataires, sélectionnez le compte en haut à droite et définissez votre session de portail sur le locataire Azure AD souhaité.
1. Accédez à la page [Inscriptions d’applications](https://go.microsoft.com/fwlink/?linkid=2083908) de la plateforme d’identité Microsoft pour les développeurs.
1. Lorsque la page **Inscrire une application** s’affiche, entrez le nom de votre application.
1. Sous **Types de comptes pris en charge**, sélectionnez **Comptes dans un annuaire organisationnel et comptes personnels Microsoft**.
1. Sous la section **URI de redirection**, dans la liste déroulante, sélectionnez la plateforme **Web**, puis définissez la valeur sur l’URL de l’application basée sur votre serveur web. 

   Pour plus d’informations sur la définition et l’obtention de l’URL de redirection dans Visual Studio et Node.js, consultez les deux sections suivantes.

1. Sélectionnez **Inscription**.
1. Dans la page **Vue d’ensemble**, notez la valeur de **ID d’application (client)** pour une utilisation ultérieure.
1. Ce démarrage rapide requiert l’activation du [flux d’octroi implicite](../articles/active-directory/develop/v2-oauth2-implicit-grant-flow.md). Dans le volet gauche de l’application inscrite, sélectionnez **Authentification**.
1. Dans **Paramètres avancés**, sous **Octroi implicite**, cochez les cases **Jetons d’ID** et **Jetons d’accès**. Les jetons d’ID et les jetons d’accès sont nécessaires, car cette application doit connecter des utilisateurs et appeler une API.
1. Sélectionnez **Enregistrer**.

> #### <a name="set-a-redirect-url-for-nodejs"></a>Définir d’une URL de redirection pour Node.js
> Pour Node.js, vous pouvez définir le port du serveur web dans le fichier *server.js*. Ce didacticiel utilise le port 30662 pour référence, mais vous pouvez utiliser tout autre port disponible. 
>
> Pour configurer une URL de redirection dans les informations d’inscription de l’application, retournez dans le volet **Inscription d’application** et effectuez l’une des opérations suivantes :
>
> - Définissez *`http://localhost:30662/`* en tant qu’**URL de redirection**.
> - Si vous utilisez un port TCP personnalisé, utilisez *`http://localhost:<port>/`* (où *\<port>* correspond au numéro du port TCP personnalisé).
>
> #### <a name="set-a-redirect-url-for-visual-studio"></a>Définir une URL de redirection pour Visual Studio
> Pour obtenir l’URL de redirection pour Visual Studio, procédez comme suit :
> 1. Dans l’**Explorateur de solutions**, sélectionnez le projet.
>
>    La fenêtre **Propriétés** apparaît. Si ce n’est pas le cas, appuyez sur **F4**.
>
>    ![Fenêtre Propriétés du projet JavaScriptSPA](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)
>
> 1. Copiez la valeur d’**URL**.
 
> 1. Basculez vers le volet **Inscriptions des applications**, collez la valeur en tant **qu’URL de redirection**.

#### <a name="configure-your-javascript-spa"></a>Configurer une application SPA JavaScript

1. Dans le fichier *index.html* que vous avez créé au cours de la configuration du projet, ajoutez les informations d’inscription d’application. Dans le haut du fichier, entre les balises `<script></script>`, ajoutez le code suivant :

    ```javascript
    var msalConfig = {
        auth: {
            clientId: "<Enter_the_Application_Id_here>",
            authority: "https://login.microsoftonline.com/<Enter_the_Tenant_info_here>"
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };
    ```

    Où :
    - *\<Enter_the_Application_Id_here>* est l’**ID d’application (client)** de l’application que vous avez inscrite.
    - *\<Enter_the_Tenant_info_here >* est défini sur une des options suivantes :
       - Si votre application prend en charge *Comptes dans cet annuaire organisationnel*, remplacez cette valeur par l’**ID de locataire** ou le **nom du locataire** (par exemple, *contoso.microsoft.com*).
       - Si votre application prend en charge *Comptes dans un annuaire organisationnel*, remplacez cette valeur par **organizations**.
       - Si votre application prend en charge *Comptes dans un annuaire organisationnel et comptes personnels Microsoft*, remplacez cette valeur par **common**. Pour limiter la prise en charge aux *Comptes Microsoft personnels uniquement*, remplacez cette valeur par **consumers**.
