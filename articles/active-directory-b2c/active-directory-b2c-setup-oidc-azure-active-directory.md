---
title: Configurer une stratégie intégrée pour des comptes de connexion Azure Active Directory dans Azure Active Directory B2C | Microsoft Docs
description: Configurer une stratégie intégrée pour des comptes de connexion Azure Active Directory dans Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 5f51fbff11412324ad167d49202f7215cefb5ac2
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/10/2018
ms.locfileid: "49076916"
---
# <a name="set-up-sign-in-azure-active-directory-accounts-a-built-in-policy-in-azure-active-directory-b2c"></a>Configurer une stratégie intégrée pour des comptes de connexion Azure Active Directory dans Azure Active Directory B2C

>[!NOTE]
> Cette fonctionnalité est en version préliminaire publique. Ne l’utilisez pas dans des environnements de production.

Cet article explique comment autoriser la connexion d’utilisateurs à partir d’une organisation Azure Active Directory (Azure AD) spécifique en utilisant une stratégie intégrée dans Azure Active Directory(Azure AD) B2C.

## <a name="create-an-azure-ad-app"></a>Création d’une application Azure AD

Pour autoriser la connexion des utilisateurs d’une organisation Azure AD spécifique, vous devez inscrire une application au sein du locataire Azure AD de l’organisation.

>[!NOTE]
>`Contoso.com` est utilisé pour le locataire Azure AD de l’organisation et `fabrikamb2c.onmicrosoft.com` est utilisé comme le locataire Azure AD B2C dans les instructions suivantes.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Veillez à utiliser le répertoire contenant votre locataire Azure AD (contoso.com) en cliquant sur le filtre Répertoire et abonnement dans le menu du haut et en choisissant le répertoire contenant votre locataire Azure AD.
3. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **inscriptions d’applications**.
4. Sélectionnez **Nouvelle inscription d’application**.
5. Entrez un nom pour votre application. Par exemple : `Azure AD B2C App`.
6. Pour le **Type d’application**, sélectionnez `Web app / API`.
7. Pour la **Connexion URL**, entrez l’URL suivante tout en minuscules, à l’endroit où `your-tenant` est remplacé par le nom de votre locataire Azure AD B2C (fabrikamb2c.onmicrosoft.com) :

    ```
    https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/oauth2/authresp
    ```

    Toutes les URL doivent désormais utiliser [b2clogin.com](b2clogin.md).

8. Cliquez sur **Créer**. Copiez l’**ID d’application** pour une utilisation ultérieure.
9. Sélectionnez l’application, puis **Paramètres**.
10. Sélectionnez **Clés**, entrez la description de la clé, sélectionnez une durée, puis cliquez sur **Enregistrer**. Copiez la valeur de la clé qui est affichée pour une utilisation ultérieure.

## <a name="configure-azure-ad-as-an-identity-provider-in-your-tenant"></a>Configurer Azure AD en tant que fournisseur d’identité dans votre locataire

1. Veillez à utiliser l’annuaire qui contient le locataire Azure AD B2C (fabrikamb2c.onmicrosoft.com) en cliquant sur le **filtre Répertoire et abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire Azure AD B2C.
2. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
3. Cliquez sur **Fournisseurs d’identité**, puis sélectionnez **Ajouter**.
4. Saisissez un **Nom**. Par exemple, entrez « Contoso Azure AD ».
5. Sélectionnez **Type de fournisseur d’identité**, sélectionnez **Open ID Connect (préversion)**, puis cliquez sur **OK**.
6. Cliquez sur **Configurer ce fournisseur d’identité**.
7. Pour **URL des métadonnées**, entrez l’URL suivante qui remplace `your-tenant` par le nom de votre locataire Azure AD :

    ```
    https://login.microsoftonline.com/your-tenant/.well-known/openid-configuration
    ```
8. Pour **ID client**, entrez l’ID d’application que vous avez enregistré précédemment et pour la **clé secrète client**, entrez la valeur de clé que vous avez enregistrée précédemment.
9. Entrez éventuellement une valeur pour **domain_hint** (par exemple, `ContosoAD`). Il s’agit de la valeur à utiliser quand vous faites référence à ce fournisseur d’identité à l’aide de *domain_hint* dans la requête. 
10. Cliquez sur **OK**.
11. Sélectionnez **Mapper les revendications de ce fournisseur d’identité** et définissez les revendications suivantes :
    
    - Pour **Identifiant utilisateur**, entrez `oid`.
    - Pour **Nom d’affichage**, entrez `name`.
    - Pour **Prénom**, entrez `given_name`.
    - Pour **Nom**, entrez `family_name`.
    - Pour **E-mail**, entrez `unique_name`.

12. Cliquez sur **OK**, puis sur **Créer** pour enregistrer votre configuration.
