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
ms.date: 05/04/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: cce0bb9d1a9317396d197d182a424a45c8448f1b
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58203610"
---
## <a name="register-your-application"></a>Inscrivez votre application

Pour inscrire votre application et ajouter les informations d’inscription de l’application à votre solution, deux options s’offrent à vous :

### <a name="option-1-express-mode"></a>Option 1 : Mode Express

Vous pouvez inscrire rapidement votre application en procédant comme suit :

1. Inscrivez votre application via le [portail d’inscription des applications de Microsoft](https://apps.dev.microsoft.com/portal/register-app?appType=serverSideWebApp&appTech=aspNetWebAppOwin&step=configure).
2. Entrez un nom pour votre application, ainsi que votre adresse e-mail.
3. Assurez-vous que la case de l’option Assistant Installation est cochée.
4. Suivez les instructions pour ajouter une URL de redirection à votre application.

### <a name="option-2-advanced-mode"></a>Option 2 : Mode Avancé

Pour inscrire votre application et ajouter les informations d’inscription de l’application à votre solution, procédez comme suit :

1. Accédez au [portail d’inscription des applications de Microsoft](https://apps.dev.microsoft.com/portal/register-app) pour inscrire une application.
2. Entrez un nom pour votre application, ainsi que votre adresse e-mail.
3. Assurez-vous que la case de l’option Guided Setup (Installation guidée) est désactivée.
4. Sélectionnez `Add Platform`, puis `Web`.
5. Retournez dans Visual Studio, puis, dans l’Explorateur de solutions, sélectionnez le projet et examinez la fenêtre Propriétés (si vous ne la voyez pa, appuyez sur F4).
6. Définissez l’option SSL activé sur `True`.
7. Cliquez avec le bouton droit sur le projet dans Visual Studio, puis choisissez **Propriétés** et cliquez sur l’onglet **Web**. Dans la section *Serveurs*, modifiez *l’URL du projet* de manière à indiquer l’URL SSL.
8. Copiez l’URL SSL et ajoutez cette URL à la liste d’URL de redirection dans la liste du portail d’inscription d’URL de redirection :<br/><br/>![Propriétés du projet](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
9. Ajoutez le code suivant dans le fichier `web.config` situé dans le dossier racine, sous la section `configuration\appSettings` :

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="redirectUri" value="Enter_the_Redirect_URL_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" />
    ```

10. Remplacez `ClientId` par l’ID de l’application que vous venez d’inscrire.
11. Remplacez `redirectUri` par l’URL SSL de votre projet.
