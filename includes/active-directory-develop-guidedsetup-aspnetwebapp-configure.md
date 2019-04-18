---
title: Fichier Include
description: Fichier Include
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 2cdc6ea01e6c3555740102f319d0f4e8e4fc1c22
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/12/2019
ms.locfileid: "59528528"
---
## <a name="register-your-application"></a>Inscrivez votre application

Pour inscrire votre application et ajouter les informations d’inscription de l’application à votre solution, deux options s’offrent à vous :

### <a name="option-1-express-mode"></a>Option 1 : Mode Express

Vous pouvez inscrire rapidement votre application en procédant comme suit :

1. Accédez au nouveau volet [Portail Azure - Inscriptions d’applications](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs).
1. Saisissez un nom pour votre application et cliquez sur **Inscrire**.
1. Suivez les instructions pour télécharger et configurer automatiquement votre nouvelle application pour vous en un seul clic.

### <a name="option-2-advanced-mode"></a>Option 2 : Mode Avancé

Pour inscrire votre application et ajouter manuellement les informations d’inscription de l’application à votre solution, procédez comme suit :

1. Accédez à Visual Studio et :
   1. Dans l’Explorateur de solutions, sélectionnez le projet et examinez la fenêtre Propriétés (si vous ne voyez pas une fenêtre Propriétés, appuyez sur F4).
   1. Définissez l’option SSL activé sur `True`.
   1. Cliquez avec le bouton droit sur le projet dans Visual Studio, puis choisissez **Propriétés** et cliquez sur l’onglet **Web**. Dans la section *Serveurs*, modifiez *l’URL du projet* de manière à indiquer l’URL SSL.
   1. Copiez l’URL SSL. Vous allez ajouter cette URL à la liste d’URL de redirection dans la liste du portail d’inscription d’URL de redirection à l’étape suivante :<br/><br/>![Propriétés du projet](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Si votre compte vous propose un accès à plusieurs locataires, sélectionnez votre compte en haut à droite et définissez votre session de portail sur le locataire Azure AD souhaité.
1. Accédez à la page [Inscriptions des applications](https://go.microsoft.com/fwlink/?linkid=2083908) de la plateforme d’identité Microsoft pour les développeurs.
1. Sélectionnez **Nouvelle inscription**.
1. Lorsque la page **Inscrire une application** s’affiche, saisissez les informations d’inscription de votre application :
   1. Dans la section **Nom**, saisissez un nom d’application cohérent qui s’affichera pour les utilisateurs de l’application, par exemple `ASPNET-Tutorial`.
   1. Ajoutez l’URL SSL que vous avez copiée à partir de Visual Studio à l’étape 1 (par exemple `https://localhost:44368/`) dans **URL de réponse**, puis cliquez sur **Inscrire**.
1. Sélectionnez le menu **Authentification**, définissez les **Jetons d’ID** sous **Octroi implicite**, puis sélectionnez **Enregistrer**.
1. Ajoutez le code suivant dans le fichier `web.config` situé dans le dossier racine, sous la section `configuration\appSettings` :

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="redirectUri" value="Enter_the_Redirect_URL_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" />
    ```

1. Remplacez `ClientId` par l’ID de l’application que vous venez d’inscrire.
1. Remplacez `redirectUri` par l’URL SSL de votre projet.
