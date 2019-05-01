---
title: Configurer la connexion pour une organisation Azure Active Directory - Azure Active Directory B2C | Microsoft Docs
description: Configurez la connexion pour une organisation Azure Active Directory spécifique dans Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 76585f91358ad4744dd5ae1f426afda0650d9a8f
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64704014"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Configurer la connexion pour une organisation Azure Active Directory spécifique dans Azure Active Directory B2C

>[!NOTE]
> Cette fonctionnalité est en version préliminaire publique. Ne l’utilisez pas dans des environnements de production.

Pour utiliser Azure Active Directory (Azure AD) en tant que [fournisseur d’identité](active-directory-b2c-reference-oauth-code.md) dans Azure AD B2C, vous devez créer une application qui la représente. Cet article explique comment autoriser la connexion d’utilisateurs à partir d’une organisation Azure AD spécifique en utilisant un flux utilisateur dans Azure AD B2C.

## <a name="create-an-azure-ad-app"></a>Création d’une application Azure AD

Pour autoriser la connexion des utilisateurs d’une organisation Azure AD spécifique, vous devez inscrire une application au sein du locataire Azure AD de l’organisation, qui n’est pas le même que votre locataire Azure AD B2C.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Veillez à utiliser l’annuaire contenant votre locataire Azure AD en cliquant sur le filtre Répertoire et abonnement dans le menu du haut et en choisissant le répertoire contenant votre locataire Azure AD.
3. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Inscriptions d’applications**.
4. Sélectionnez **Nouvelle inscription d’application**.
5. Entrez un nom pour votre application. Par exemple : `Azure AD B2C App`.
6. Pour le **Type d’application**, sélectionnez `Web app / API`.
7. Pour le champ **URL de connexion**, entrez l’URL suivante en minuscules, où `your-B2C-tenant-name` est remplacé par le nom de votre locataire Azure AD B2C. Par exemple, `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`:

    ```
    https://your-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Toutes les URL doivent désormais utiliser [b2clogin.com](b2clogin.md).

8. Cliquez sur **Créer**. Copiez **l’ID d’application** pour une utilisation ultérieure.
9. Sélectionnez l’application, puis **Paramètres**.
10. Sélectionnez **Clés**, entrez la description de la clé, sélectionnez une durée, puis cliquez sur **Enregistrer**. Copiez la valeur de la clé qui est affichée pour une utilisation ultérieure.

## <a name="configure-azure-ad-as-an-identity-provider"></a>Configurer Azure AD en tant que fournisseur d’identité

1. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en cliquant sur le **filtre annuaire et abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire Azure AD B2C.
2. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
3. Cliquez sur **Fournisseurs d’identité**, puis sélectionnez **Ajouter**.
4. Saisissez un **Nom**. Par exemple, entrez « Contoso Azure AD ».
5. Sélectionnez **Type de fournisseur d’identité**, sélectionnez **Open ID Connect (préversion)**, puis cliquez sur **OK**.
6. Cliquez sur **Configurer ce fournisseur d’identité**.
7. Pour **URL des métadonnées**, entrez l’URL suivante qui remplace `your-AD-tenant-domain` par le nom de domaine de votre locataire Azure AD. Exemple `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration` :

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

8. Pour **ID client**, entrez l’ID d’application que vous avez enregistré précédemment et pour la **clé secrète client**, entrez la valeur de clé que vous avez enregistrée précédemment.
9. Entrez éventuellement une valeur pour **Domain_hint**. Par exemple : `ContosoAD`. Il s’agit de la valeur à utiliser quand vous faites référence à ce fournisseur d’identité à l’aide de *domain_hint* dans la requête. 
10. Cliquez sur **OK**.
11. Sélectionnez **Mapper les revendications de ce fournisseur d’identité** et définissez les revendications suivantes :
    
    - Pour **Identifiant utilisateur**, entrez `oid`.
    - Pour **Nom d’affichage**, entrez `name`.
    - Pour **Prénom**, entrez `given_name`.
    - Pour **Nom**, entrez `family_name`.
    - Pour **E-mail**, entrez `unique_name`.

12. Cliquez sur **OK**, puis sur **Créer** pour enregistrer votre configuration.
