---
title: Définir une page d’accueil personnalisée pour les applications publiées à l’aide du proxy d’application Azure AD | Microsoft Docs
description: Couvre les bases sur les connecteurs de proxy d’application Azure AD.
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/08/2017
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 0b6b85ef3117343cc8c7dc654bd5debe0c4dfd81
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55149683"
---
# <a name="set-a-custom-home-page-for-published-apps-by-using-azure-ad-application-proxy"></a>Définir une page d’accueil personnalisée pour les applications publiées à l’aide du proxy d’application Azure AD

Cet article explique comment configurer des applications pour diriger les utilisateurs vers une page d’accueil personnalisée. Lorsque vous publiez une application avec le proxy d’application, vous définissez une URL interne, mais parfois, ce n’est pas la page que vos utilisateurs doivent d’abord voir. Définissez une page d’accueil personnalisée afin que les utilisateurs tombent sur la page adéquate quand ils accèdent aux applications. Les utilisateurs voient la page d’accueil personnalisée que vous avez définie, qu’ils accèdent à l’application à partir du volet d’accès Azure Active Directory ou du lanceur d’applications Office 365.

Lorsque les utilisateurs lancent l’application, ils sont dirigés par défaut vers l’URL du domaine racine de l’application publiée. La page d’arrivée est généralement définie comme URL de page d’accueil. Le module PowerShell Azure AD permet de définir l’URL de la page d’accueil personnalisée lorsque vous souhaitez que les utilisateurs de l’application arrivent sur une page spécifique au sein de l’application. 

Voici un exemple de définition d’une page d’accueil personnalisée au sein d’une entreprise :
- Sur votre réseau d’entreprise, les utilisateurs accèdent à *https://ExpenseApp/login/login.aspx* pour se connecter et accéder à votre application.
- Comme vous disposez d’autres ressources comme les images dont le proxy d’application a besoin pour accéder au niveau supérieur de la structure de dossiers, vous publiez l’application avec *https://ExpenseApp* en tant qu’URL interne.
- L’URL externe par défaut est *https://ExpenseApp-contoso.msappproxy.net*, qui n’envoie pas vos utilisateurs vers la page de connexion.  
- Réglez l’URL de la page d'accueil sur *https://ExpenseApp-contoso.msappproxy.net/login/login.aspx*. 

>[!NOTE]
>Si vous octroyez aux utilisateurs un accès aux applications publiées, celles-ci sont affichées dans le [volet d’accès Azure AD](../user-help/active-directory-saas-access-panel-introduction.md) et le [lanceur d’applications Office 365](https://blogs.office.com/2016/09/27/introducing-the-new-office-365-app-launcher).

## <a name="before-you-start"></a>Avant de commencer

Avant de définir l’URL de page d’accueil, n’oubliez pas les exigences suivantes :

* Vérifiez que le chemin d’accès que vous spécifiez est un chemin d’accès de sous-domaine de l’URL du domaine racine.

  Si l’URL de domaine à la racine est, par exemple, https://apps.contoso.com/app1/, l’URL de la page d’accueil que vous configurez doit commencer par https://apps.contoso.com/app1/.

* Si vous apportez une modification à l’application publiée, ce changement peut réinitialiser la valeur de l’URL de page d’accueil. Ultérieurement, lorsque vous mettez à jour l’application, vous devez revérifier et, si nécessaire, mettre à jour l’URL de page d’accueil.

## <a name="change-the-home-page-in-the-azure-portal"></a>Changer la page d’accueil du portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com) en tant qu’administrateur.
2. Accédez à **Azure Active Directory** > **Inscriptions des applications** et choisissez votre application dans la liste. 
3. Dans les paramètres, sélectionnez **Propriétés**.
4. Mettez à jour le champ **URL de la page d’accueil** avec votre nouveau chemin d’accès. 

   ![Fournir la nouvelle URL de la page d’accueil](./media/application-proxy-configure-custom-home-page/homepage.png)

5. Sélectionnez **Enregistrer**.

## <a name="change-the-home-page-with-powershell"></a>Changer la page d’accueil à l’aide de PowerShell

### <a name="install-the-azure-ad-powershell-module"></a>Installer le module Azure AD PowerShell

Avant de définir une URL de page d’accueil personnalisée à l’aide de PowerShell, installez le module Azure AD PowerShell. Vous pouvez télécharger le package à partir de [PowerShell Gallery](https://www.powershellgallery.com/packages/AzureAD/2.0.0.131), qui utilise le point de terminaison de l’API Graph. 

Pour installer le package, procédez comme suit :

1. Ouvrez une fenêtre PowerShell standard, puis exécutez la commande suivante :

    ```
     Install-Module -Name AzureAD
    ```
    Si vous l’exécutez en tant que non-administrateur, utilisez l’option `-scope currentuser`.
2. Pendant l’installation, sélectionnez **Y** pour installer deux packages depuis Nuget.org. Les deux packages sont requis. 

### <a name="find-the-objectid-of-the-app"></a>Rechercher l’ObjectID de l’application

Obtenez l’ObjectID de l’application, puis recherchez l’application en fonction de sa page d’accueil.

1. Dans la même fenêtre PowerShell, importez le module Azure AD.

    ```
    Import-Module AzureAD
    ```

2. Connectez-vous au module Azure AD en tant qu’administrateur client.

    ```
    Connect-AzureAD
    ```
3. Recherchez l’application en fonction de son URL de page d’accueil. Vous pouvez trouver l’URL dans le portail en accédant à **Azure Active Directory** > **Applications d’entreprise** > **Toutes les applications**. Cet exemple utilise *sharepoint-iddemo*.

    ```
    Get-AzureADApplication | where { $_.Homepage -like "sharepoint-iddemo" } | fl DisplayName, Homepage, ObjectID
    ```
4. Vous devez obtenir un résultat semblable à celui qui est illustré ici. Copiez le GUID ObjectID à utiliser dans la section suivante.

    ```
    DisplayName : SharePoint
    Homepage    : https://sharepoint-iddemo.msappproxy.net/
    ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
    ```

### <a name="update-the-home-page-url"></a>Mettre à jour l’URL de la page d’accueil

Créez l’URL de la page d’accueil et mettez à jour votre application avec cette valeur. Ensuite, exécutez les commandes ci-après depuis la même fenêtre PowerShell. Ou, si vous utilisez une nouvelle fenêtre PowerShell, connectez-vous au module Azure AD à l’aide de `Connect-AzureAD`. 

1. Vérifiez que vous avez la bonne application et remplacez *8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4* par l’ObjectID que vous avez copié à la section précédente.

    ```
    Get-AzureADApplication -ObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4.
    ```

 Maintenant que vous avez vérifié l’application, vous êtes prêt à mettre à jour la page d’accueil comme suit.

2. Créez un objet d’application vide pour conserver les modifications que vous souhaitez apporter. Cette variable contient les valeurs que vous souhaitez mettre à jour. Rien n’est créé lors de cette étape.

    ```
    $appnew = New-Object "Microsoft.Open.AzureAD.Model.Application"
    ```

3. Définissez l’URL de page d’accueil sur la valeur souhaitée. La valeur doit correspondre à un chemin d’accès de sous-domaine de l’application publiée. Par exemple, si vous changez l’URL de la page d’accueil de `https://sharepoint-iddemo.msappproxy.net/` en `https://sharepoint-iddemo.msappproxy.net/hybrid/`, les utilisateurs de l’application accèdent directement à la page d’accueil personnalisée.

    ```
    $homepage = "https://sharepoint-iddemo.msappproxy.net/hybrid/"
    ```
4. Procédez à la mise à jour à l’aide du GUID (ObjectID) que vous avez copié lors de l’étape 1 : Rechercher l’ObjectID de l’application.

    ```
    Set-AzureADApplication -ObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4 -Homepage $homepage
    ```
5. Pour vérifier la réussite de la modification, redémarrez l’application.

    ```
    Get-AzureADApplication -ObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
    ```

>[!NOTE]
>Les modifications que vous apportez à l’application peuvent réinitialiser l’URL de page d’accueil. Si l’URL de votre page d’accueil est réinitialisée, répétez les étapes décrites dans cette section pour la rétablir.

## <a name="next-steps"></a>Étapes suivantes

- [Activer l’accès distant pour SharePoint avec le proxy d’application Azure AD](application-proxy-integrate-with-sharepoint-server.md)
- [Activer le proxy d’application dans le portail Azure](application-proxy-add-on-premises-application.md)
