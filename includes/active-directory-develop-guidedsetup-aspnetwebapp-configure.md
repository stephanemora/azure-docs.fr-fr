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
ms.date: 05/04/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 7c052f30e564700b0a99b76342dc6c34ec5983b7
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2018
ms.locfileid: "33901276"
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
3. Assurez-vous que la case de l’option Guided Setup (Installation guidée) est désactivée.
4. Cliquez sur `Add Platform`, puis sélectionnez `Web`.
5. Retournez dans Visual Studio, puis, dans l’Explorateur de solutions, sélectionnez le projet et examinez la fenêtre Propriétés (si vous ne la voyez pa, appuyez sur F4).
6. Définissez l’option SSL activé sur `True`
7. Cliquez avec le bouton droit sur le projet dans Visual Studio, puis choisissez **Propriétés** et cliquez sur l’onglet **Web**. Dans la section *Serveurs*, modifiez l’*URL du projet* de manière à indiquer l’URL SSL.
8. Copiez l’URL SSL et ajoutez cette URL à la liste d’URL de redirection dans la liste du portail d’inscription d’URL de redirection :<br/><br/>![Propriétés du projet](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
9. Ajoutez le code suivant dans le fichier `web.config` situé dans le dossier racine, sous la section `configuration\appSettings` :

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="redirectUri" value="Enter_the_Redirect_URL_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" />
    ```

10. Remplacez `ClientId` par l’ID d’application que vous venez d’enregistrer.
11. Remplacez `redirectUri` par l’URL SSL de votre projet

