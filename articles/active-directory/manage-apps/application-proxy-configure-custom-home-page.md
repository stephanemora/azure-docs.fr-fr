---
title: Définir une page d’accueil personnalisée pour les applications publiées à l’aide du proxy d’application Azure AD | Microsoft Docs
description: Couvre les bases sur les connecteurs de proxy d’application Azure AD.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: celested
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3143dfafdd402bd9eeb2f201f4e728d84c4b9f09
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64691163"
---
# <a name="set-a-custom-home-page-for-published-apps-by-using-azure-ad-application-proxy"></a>Définir une page d’accueil personnalisée pour les applications publiées à l’aide du proxy d’application Azure AD

Cet article explique comment configurer une application pour diriger un utilisateur vers une page d’accueil personnalisée, qui peut différer selon qu’elles soient internes ou externes. Lorsque vous publiez une application avec Proxy d’Application, vous définissez une URL interne, mais qui n’est parfois pas la page de qu'un utilisateur doit tout d’abord voir. Définir une page d’accueil personnalisée afin qu’un utilisateur obtient la bonne page lorsqu’ils accèdent à l’application. Un utilisateur verra la page d’accueil personnalisée que vous définissez, quel que soit leur accès l’application à partir du volet d’accès Azure Active Directory ou du Lanceur d’applications Office 365.

Lorsqu’un utilisateur lance l’application, ils sont dirigés par défaut pour l’URL du domaine racine de l’application publiée. La page d’arrivée est généralement définie comme URL de page d’accueil. Utiliser le module Azure AD PowerShell pour définir une URL de page d’accueil personnalisée lorsque vous souhaitez un utilisateur de l’application arrivent sur une page spécifique au sein de l’application.

Un scénario de Voici qui explique pourquoi votre entreprise définirait une page d’accueil personnalisée, et pourquoi il serait différente selon le type d’utilisateur :

- Étant donné que vous avez d’autres composants (tels que des images) que le Proxy d’Application doit accéder au niveau supérieur de la structure de dossiers, vous publiez l’application avec `https://ExpenseApp` en tant que l’URL interne.
- Toutefois, à l’intérieur de votre réseau d’entreprise, un utilisateur accède à `https://ExpenseApp/login/login.aspx` pour se connecter et accéder à votre application.
- L’URL externe par défaut est `https://ExpenseApp-contoso.msappproxy.net`, qui ne prend pas un utilisateur externe à la page de connexion.
- Vous souhaitez définir `https://ExpenseApp-contoso.msappproxy.net/login/login.aspx` comme URL de la page d’accueil externe au lieu de cela, par conséquent, un utilisateur externe voit la page de connexion tout d’abord.

>[!NOTE]
>Si vous octroyez aux utilisateurs un accès aux applications publiées, celles-ci sont affichées dans le [volet d’accès Azure AD](../user-help/my-apps-portal-end-user-access.md) et le [lanceur d’applications Office 365](https://www.microsoft.com/microsoft-365/blog/2016/09/27/introducing-the-new-office-365-app-launcher/).

## <a name="before-you-start"></a>Avant de commencer

Avant de définir l’URL de page d’accueil, n’oubliez pas les exigences suivantes :

- Le chemin d’accès que vous spécifiez doit être un chemin d’accès de sous-domaine de l’URL du domaine racine.

  Par exemple, si l’URL de domaine racine est `https://apps.contoso.com/app1/`, l’URL de page d’accueil que vous configurez doit commencer par `https://apps.contoso.com/app1/`.

- Si vous apportez une modification à l’application publiée, ce changement peut réinitialiser la valeur de l’URL de page d’accueil. Ultérieurement, lorsque vous mettez à jour l’application, vous devez revérifier et, si nécessaire, mettre à jour l’URL de page d’accueil.

Vous pouvez modifier la page d’accueil interne ou externe via le portail Azure ou à l’aide de PowerShell.

## <a name="change-the-home-page-in-the-azure-portal"></a>Changer la page d’accueil du portail Azure

Pour modifier les pages d’accueil externes et internes de votre application via le portail Azure AD, procédez comme suit :

1. Se connecter à la [portail Azure Active Directory](https://aad.portal.azure.com/). Le tableau de bord du centre d’administration Azure Active Directory s’affiche.
2. Dans la barre latérale, sélectionnez **Azure Active Directory**. La page de vue d’ensemble d’Azure AD s’affiche.
3. Dans la barre latérale de vue d’ensemble, sélectionnez **inscriptions**. La liste des applications inscrites s’affiche.
4. Choisissez votre application dans la liste. Une page présentant les détails de l’application inscrite s’affiche.
5. Sélectionnez le lien situé sous **URI de redirection**, qui affiche le nombre d’URI de redirection pour le web et les types de client public. La page d’authentification pour l’application inscrite s’affiche.
6. Dans la dernière ligne de la **URI de redirection** table, définissez la **TYPE** colonne à **client Public (mobile et bureau)**, puis, dans le **URI de redirection**colonne, tapez l’URL interne que vous souhaitez utiliser. Une nouvelle ligne vide apparaît au-dessous de la ligne que vous venez de modifier.
7. Dans la nouvelle ligne, définissez la **TYPE** colonne à **Web**, puis, dans le **URI de redirection** colonne, tapez l’URL externe que vous souhaitez utiliser.
8. Si vous souhaitez supprimer des lignes d’URI de redirection existantes, sélectionnez le **supprimer** icône (une poubelle) en regard de chaque ligne indésirable.
9. Sélectionnez **Enregistrer**.

## <a name="change-the-home-page-with-powershell"></a>Changer la page d’accueil à l’aide de PowerShell

Pour configurer la page d’accueil d’une application à l’aide de PowerShell, vous devez :

1. Installez le module Azure AD PowerShell.
2. Recherchez la valeur ObjectId de l’application.
3. Mettre à jour des URL de page d’accueil de l’application à l’aide des commandes PowerShell.

### <a name="install-the-azure-ad-powershell-module"></a>Installer le module Azure AD PowerShell

Avant de définir une URL de page d’accueil personnalisée à l’aide de PowerShell, installez le module Azure AD PowerShell. Vous pouvez télécharger le package à partir de [PowerShell Gallery](https://www.powershellgallery.com/packages/AzureAD/2.0.2.16), qui utilise le point de terminaison de l’API Graph.

Pour installer le package, procédez comme suit :

1. Ouvrez une fenêtre PowerShell standard, puis exécutez la commande suivante :

   ```powershell
   Install-Module -Name AzureAD
   ```

    Si vous l’exécutez en tant que non-administrateur, utilisez l’option `-scope currentuser`.

2. Pendant l’installation, sélectionnez **Y** pour installer deux packages depuis Nuget.org. Les deux packages sont requis.

### <a name="find-the-objectid-of-the-app"></a>Rechercher l’ObjectId de l’application

Vous obtenez l’ObjectId de l’application en recherchant l’application par son nom d’affichage ou la page d’accueil.

1. Dans la même fenêtre PowerShell, importez le module Azure AD.

   ```powershell
   Import-Module AzureAD
   ```

2. Connectez-vous au module Azure AD en tant qu’administrateur client.

   ```powershell
   Connect-AzureAD
   ```

3. Recherchez l’application. Cet exemple utilise PowerShell pour rechercher l’ObjectId en effectuant une recherche de l’application avec un nom d’affichage de `SharePoint`.

   ```powershell
   Get-AzureADApplication | Where-Object { $_.DisplayName -eq "SharePoint" } | Format-List DisplayName, Homepage, ObjectId
   ```

   Vous devez obtenir un résultat semblable à celui qui est illustré ici. Copiez le GUID ObjectId à utiliser dans la section suivante.

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

   Ou bien, vous pourriez simplement extraire la liste de toutes les applications, rechercher la liste de l’application avec un nom d’affichage spécifique ou de la page d’accueil et copier ObjectId de l’application une fois que l’application est trouvée.

   ```powershell
   Get-AzureADApplication | Format-List DisplayName, Homepage, ObjectId
   ```

### <a name="update-the-home-page-url"></a>Mettre à jour l’URL de la page d’accueil

Créez l’URL de la page d’accueil et mettre à jour de votre application avec cette valeur. Continuer à utiliser la même fenêtre PowerShell, ou si vous utilisez une nouvelle fenêtre PowerShell, connectez-vous au module Azure AD à l’aide de `Connect-AzureAD`. Exécutez ensuite les opérations qui suivent :

1. Créez une variable pour contenir la valeur d’ID d’objet que vous avez copié dans la section précédente. (Remplacez la valeur de l’ID d’objet utilisée pour dans cet exemple de SharePoint avec la valeur d’ID d’objet de votre application.)

   ```powershell
   $objguid = "8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4"
   ```

2. Vérifiez que vous avez la bonne application en exécutant la commande suivante. La sortie doit être identique à la sortie que vous avez vu dans la section précédente ([rechercher l’ObjectId de l’application](#find-the-objectid-of-the-app)).

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

3. Créez un objet d’application vide pour conserver les modifications que vous souhaitez apporter.

   ```powershell
   $appnew = New-Object "Microsoft.Open.AzureAD.Model.Application"
   ```

4. Définissez l’URL de page d’accueil sur la valeur souhaitée. La valeur doit correspondre à un chemin d’accès de sous-domaine de l’application publiée. Par exemple, si vous changez l’URL de la page d’accueil de `https://sharepoint-iddemo.msappproxy.net/` en `https://sharepoint-iddemo.msappproxy.net/hybrid/`, les utilisateurs de l’application accèdent directement à la page d’accueil personnalisée.

   ```powershell
   $homepage = "https://sharepoint-iddemo.msappproxy.net/hybrid/"
   ```

5. Effectuer la mise à jour de la page d’accueil.

   ```powershell
   Set-AzureADApplication -ObjectId $objguid -Homepage $homepage
   ```

6. Pour confirmer que la modification a réussi, exécutez à nouveau la commande suivante à partir de l’étape 2.

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

   Dans notre exemple, la sortie doit maintenant apparaître comme suit :

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/hybrid/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

7. Redémarrez l’application pour vérifier que la page d’accueil s’affiche en tant que le premier écran, comme prévu.

>[!NOTE]
>Les modifications que vous apportez à l’application peuvent réinitialiser l’URL de page d’accueil. Si l’URL de votre page d’accueil est réinitialisée, répétez les étapes décrites dans cette section pour la rétablir.

## <a name="next-steps"></a>Étapes suivantes

- [Activer l’accès distant pour SharePoint avec le proxy d’application Azure AD](application-proxy-integrate-with-sharepoint-server.md)
- [Tutoriel : Ajouter une application en local pour l’accès à distance via le Proxy d’Application dans Azure Active Directory](application-proxy-add-on-premises-application.md)