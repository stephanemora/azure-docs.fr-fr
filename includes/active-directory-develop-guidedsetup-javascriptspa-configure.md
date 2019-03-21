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
ms.openlocfilehash: ec9eba4766da1afbbee568374de1ce06dc92ab2b
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58203322"
---
## <a name="register-your-application"></a>Inscrivez votre application

1. Connectez-vous au [portail Azure](https://portal.azure.com/) pour inscrire une application.
1. Si votre compte vous propose un accès à plusieurs locataires, sélectionnez votre compte en haut à droite et définissez votre session de portail sur le locataire Azure AD souhaité.
1. Dans le volet de navigation gauche, sélectionnez le service **Azure Active Directory**, puis sélectionnez **Inscriptions d’applications (préversion) > Nouvelle inscription**.
1. Lorsque la page **Inscrire une application** s’affiche, entrez le nom de votre application.
1. Sous **Types de comptes pris en charge**, sélectionnez **Comptes dans un annuaire organisationnel et comptes personnels Microsoft**.
1. Sous la section **URI de redirection**, sélectionnez la plateforme **Web** et définissez la valeur sur l’URL de l’application basée sur votre serveur web. Consultez les sections ci-dessous pour obtenir des instructions permettant de définir et d’obtenir l’URL de redirection dans Visual Studio et Node.
1. Lorsque vous avez terminé, sélectionnez **Inscrire**.
1. Sur la page **Vue d’ensemble**, notez la valeur **ID d’application (client)**.
1. Ce démarrage rapide requiert l’activation du [flux d’octroi implicite](../articles/active-directory/develop/v2-oauth2-implicit-grant-flow.md). Dans le volet de navigation de gauche de l’application inscrite, sélectionnez **Authentification**.
1. Dans **Paramètres avancés**, sous **Octroi implicite**, cochez les cases **Jetons d’ID** et **Jetons d’accès**. Les jetons d’ID et jetons d’accès sont nécessaires dans la mesure où cette application doit connecter des utilisateurs et appeler une API.
1. Sélectionnez **Enregistrer**.

> #### <a name="setting-the-redirect-url-for-node"></a>Définition de l’URL de redirection pour Node
> Pour Node.js, vous pouvez définir le port du serveur web dans le fichier *server.js*. Ce didacticiel utilise le port 30662 pour référence, mais vous pouvez utiliser tout autre port disponible. Suivez les instructions ci-après pour configurer une URL de redirection dans les informations d’inscription d’application :<br/>
> - Revenez à *Inscription des applications* et entrez `http://localhost:30662/` dans `Redirect URL`, ou optez pour `http://localhost:[port]/` si vous utilisez un port TCP personnalisé (*[port]* étant le numéro de port TCP).

<p/>

> #### <a name="visual-studio-instructions-for-obtaining-the-redirect-url"></a>Instructions Visual Studio pour obtenir l’URL de redirection
> Pour obtenir l’URL de redirection, procédez comme suit :
> 1. Dans **l’Explorateur de solutions**, sélectionnez le projet et examinez la fenêtre **Propriétés**. Si vous ne voyez pas de fenêtre **Propriétés**, appuyez sur **F4**.
> 2. Copiez la valeur du champ **URL** dans le Presse-papiers :<br/> ![Propriétés du projet](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3. Revenez à *Inscription des applications* et définissez la valeur en tant qu’**URL de redirection**.

#### <a name="configure-your-javascript-spa"></a>Configurer une application SPA JavaScript

1. Dans le fichier `index.html` créé au cours de la configuration du projet, ajoutez les informations d’inscription d’application. Ajoutez le code ci-après dans la partie supérieure entre les balises `<script></script>` dans le corps de votre fichier `index.html` :

    ```javascript
    var applicationConfig = {
        clientID: "Enter_the_Application_Id_here",
        authority: "https://login.microsoftonline.com/common",
        graphScopes: ["user.read"],
        graphEndpoint: "https://graph.microsoft.com/v1.0/me"
    };
    ```

<ol start="2">
<li>
Remplacez <code>Enter the application Id here</code> par l’ID de l’application que vous venez d’inscrire.
</li>
</ol>
