---
title: Configurer la connexion pour une organisation Azure AD
titleSuffix: Azure AD B2C
description: Configurez la connexion pour une organisation Azure Active Directory spécifique dans Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: d864561273f89c510633bbc95a2d037a270401f8
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74947672"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Configurer la connexion pour une organisation Azure Active Directory spécifique dans Azure Active Directory B2C

Pour utiliser Azure Active Directory (Azure AD) en tant que [fournisseur d’identité](active-directory-b2c-reference-oauth-code.md) dans Azure AD B2C, vous devez créer une application qui la représente. Cet article explique comment autoriser la connexion d’utilisateurs à partir d’une organisation Azure AD spécifique en utilisant un flux utilisateur dans Azure AD B2C.

## <a name="create-an-azure-ad-app"></a>Création d’une application Azure AD

Pour autoriser la connexion des utilisateurs d’une organisation Azure AD spécifique, vous devez inscrire une application au sein du locataire Azure AD de l’organisation, qui n’est pas le même que votre locataire Azure AD B2C.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Veillez à bien utiliser le répertoire qui contient votre locataire Azure AD. Sélectionnez le filtre **Répertoire et abonnement** dans le menu du haut, puis choisissez le répertoire contenant votre locataire Azure AD. Il ne s’agit pas du même locataire que votre locataire Azure AD B2C.
3. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Inscriptions d’applications**.
4. Sélectionnez **Nouvelle inscription**.
5. Entrez un nom pour votre application. Par exemple : `Azure AD B2C App`.
6. Acceptez la sélection de **Comptes dans cet annuaire organisationnel uniquement** pour cette application.
7. Pour le champ **URI de redirection**, acceptez la valeur **Web**, puis entrez l’URL suivante en minuscules, où `your-B2C-tenant-name` est remplacé par le nom de votre locataire Azure AD B2C : Par exemple, `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`:

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Toutes les URL doivent désormais utiliser [b2clogin.com](b2clogin.md).

8. Cliquez sur **S'inscrire**. Copiez l’**ID d’application (client)**  : cette information vous servira plus tard.
9. Dans le menu de l’application, sélectionnez **Certificats et secrets**, puis **Nouvelle clé secrète client**.
10. Entrez un nom pour la clé secrète client. Par exemple : `Azure AD B2C App Secret`.
11. Sélectionnez la période d’expiration. Pour cette application, acceptez la sélection **Dans 1 an**.
12. Sélectionnez **Ajouter** et copiez la valeur de la nouvelle clé secrète client qui s’affiche : elle vous servira plus tard.

## <a name="configure-azure-ad-as-an-identity-provider"></a>Configurer Azure AD en tant que fournisseur d’identité

1. Veillez à bien utiliser le répertoire qui contient le locataire Azure AD B2C. Sélectionnez le filtre **Annuaire + abonnement** dans le menu du haut, puis choisissez l’annuaire qui contient votre locataire Azure AD B2C.
1. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
1. Sélectionnez **Fournisseurs d’identité**, puis **Nouveau fournisseur OpenID Connect**.
1. Saisissez un **Nom**. Par exemple, entrez *Contoso Azure AD*.
1. Dans **URL des métadonnées**, entrez l’URL suivante en remplaçant `your-AD-tenant-domain` par le nom de domaine de votre locataire Azure AD :

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

    Par exemple : `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`.

    **Ne pas** utiliser le point de terminaison de métadonnées Azure AD v 2.0, par exemple `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`. Cela génère une erreur similaire à `AADB2C: A claim with id 'UserId' was not found, which is required by ClaimsTransformation 'CreateAlternativeSecurityId' with id 'CreateAlternativeSecurityId' in policy 'B2C_1_SignUpOrIn' of tenant 'contoso.onmicrosoft.com'` lors de la tentative de connexion.

1. Dans **ID client**, entrez l’ID d’application que vous avez enregistré précédemment.
1. Dans **Clé secrète client**, entrez la clé secrète client que vous avez enregistrée précédemment.
1. Conservez les valeurs par défaut dans **Étendue**, **Type de réponse** et **Mode de réponse**.
1. (Facultatif) Entrez une valeur pour **Domain_hint**. Par exemple, *ContosoAD*. Il s’agit de la valeur à utiliser quand vous faites référence à ce fournisseur d’identité à l’aide de *domain_hint* dans la requête.
1. Sous **Mappage des revendications du fournisseur d’identité**, entrez les valeurs de mappage des revendications suivantes :

    * **ID d’utilisateur** : *oid*
    * **Nom d’affichage** : *name*
    * **Prénom** : *given_name*
    * **Nom** : *family_name*
    * **E-mail** : *unique_name*

1. Sélectionnez **Enregistrer**.
