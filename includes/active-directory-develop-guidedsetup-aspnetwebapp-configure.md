---
title: Fichier Include
description: Fichier Include
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: c1971e1eb3abc653ad8bdc6af772c699f8549019
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2018
---
## <a name="register-your-application"></a>Inscrivez votre application
Pour inscrire votre application et ajouter les informations d’inscription de l’application à votre solution, deux options s’offrent à vous :

### <a name="option-1-express-mode"></a>Option 1 : mode Express

Vous pouvez inscrire rapidement votre application en procédant comme suit :
1. Inscrivez votre application via le [portail d’inscription des applications de Microsoft](https://apps.dev.microsoft.com/portal/register-app?appType=serverSideWebApp&appTech=aspNetWebAppOwin&step=configure).
2.  Entrez un nom pour votre application, ainsi que votre adresse e-mail.
3.  Assurez-vous que la case de l’option Guided Setup (Installation guidée) est activée.
4.  Suivez les instructions pour ajouter une URL de redirection à votre application.

### <a name="option-2-advanced-mode"></a>Option 2 : mode Avancé

Pour inscrire votre application et ajouter les informations d’inscription de l’application à votre solution, procédez comme suit :

1. Accédez au [portail d’inscription des applications de Microsoft](https://apps.dev.microsoft.com/portal/register-app) pour inscrire une application.
2. Entrez un nom pour votre application, ainsi que votre adresse e-mail. 
3.  Assurez-vous que la case de l’option Guided Setup (Installation guidée) est désactivée.
4.  Cliquez sur `Add Platform`, puis sélectionnez `Web`.
5.  Retournez dans Visual Studio, puis, dans l’Explorateur de solutions, sélectionnez le projet et examinez la fenêtre Propriétés (si vous ne la voyez pa, appuyez sur F4).
6.  Définissez l’option SSL activé sur `True`
7.  Copiez l’URL SSL et ajoutez cette URL à la liste d’URL de redirection dans la liste du portail d’inscription d’URL de redirection :<br/><br/>![Propriétés du projet](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
8.  Ajoutez le code suivant dans le fichier `web.config` situé dans le dossier racine, sous la section `configuration\appSettings` :

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="redirectUri" value="Enter_the_Redirect_URL_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" /> 
    ```

9. Remplacez `ClientId` par l’ID d’application que vous venez d’enregistrer.
10. Remplacez `redirectUri` par l’URL SSL de votre projet 

