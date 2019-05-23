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
ms.date: 04/10/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: c4dc3d422e18d9ee41bf16ac3e6f22c3d7e466d7
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66121610"
---
## <a name="register-your-application"></a>Inscrivez votre application

Vous pouvez inscrire votre application de deux manières.

### <a name="option-1-express-mode"></a>Option 1 : Mode Express

Vous pouvez inscrire rapidement votre application en procédant comme suit :
1. Accédez au [portail Azure - Inscription d’applications](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/WinDesktopQuickstartPage/sourceType/docs).
1. Entrez un nom pour votre application, puis sélectionnez **Inscrire**.
1. Suivez les instructions pour télécharger et configurer automatiquement votre nouvelle application en un seul clic.

### <a name="option-2-advanced-mode"></a>Option 2 : Mode Avancé

Pour inscrire votre application et ajouter les informations d’inscription de l’application à votre solution, procédez comme suit :
1. Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte professionnel ou scolaire ou avec un compte personnel Microsoft.
1. Si votre compte vous propose un accès à plusieurs locataires, sélectionnez votre compte en haut à droite et définissez votre session de portail sur le locataire Azure AD souhaité.
1. Accédez à la page [Inscriptions des applications](https://go.microsoft.com/fwlink/?linkid=2083908) de la plateforme d’identité Microsoft pour les développeurs.
1. Sélectionnez **Nouvelle inscription**.
   - Dans la section **Nom**, saisissez un nom d’application cohérent qui s’affichera pour les utilisateurs de l’application, par exemple `Win-App-calling-MsGraph`.
   - Dans la section **Types de comptes pris en charge**, sélectionnez **Comptes dans un annuaire organisationnel et comptes personnels Microsoft (par exemple, Skype, Xbox, Outlook.com)**.
   - Sélectionnez **Inscrire** pour créer l’application.
1. Dans la liste des pages de l’application, sélectionnez **Authentification**.
   1. Dans la section **URI de redirection**, dans la liste des URI de redirection :
   1. Dans la colonne **TYPE**, sélectionnez **Client public (mobile et bureau)**.
   1. Entrez `urn:ietf:wg:oauth:2.0:oob` dans la colonne **URI de redirection**.
1. Sélectionnez **Enregistrer**.
1. Accédez à Visual Studio, ouvrez le fichier *App.xaml.cs*, puis remplacez `Enter_the_Application_Id_here` par l’ID de l’application que vous venez d’inscrire et de copier.

    ```csharp
    private static string ClientId = "Enter_the_Application_Id_here";
    ```
