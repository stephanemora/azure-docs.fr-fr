---
title: Configurer la connexion pour une organisation Azure Active Directory - Azure Active Directory B2C | Microsoft Docs
description: Configurez la connexion pour une organisation Azure Active Directory spécifique dans Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 28dbf0382ac151857e72d4bb59e207f07c8ad3f3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66508422"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Configurer la connexion pour une organisation Azure Active Directory spécifique dans Azure Active Directory B2C

>[!NOTE]
> Cette fonctionnalité est en version préliminaire publique. Ne l’utilisez pas dans des environnements de production.

Pour utiliser Azure Active Directory (Azure AD) en tant que [fournisseur d’identité](active-directory-b2c-reference-oauth-code.md) dans Azure AD B2C, vous devez créer une application qui la représente. Cet article explique comment autoriser la connexion d’utilisateurs à partir d’une organisation Azure AD spécifique en utilisant un flux utilisateur dans Azure AD B2C.

## <a name="create-an-azure-ad-app"></a>Création d’une application Azure AD

Pour autoriser la connexion des utilisateurs d’une organisation Azure AD spécifique, vous devez inscrire une application au sein du locataire Azure AD de l’organisation, qui n’est pas le même que votre locataire Azure AD B2C.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Veillez à bien utiliser le répertoire qui contient votre locataire Azure AD. Choisissez le **filtre Répertoire et abonnement** dans le menu supérieur et choisissez l’annuaire qui contient votre locataire Azure AD. Il ne s’agit pas du même locataire que votre locataire Azure AD B2C.
3. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Inscriptions d’applications**.
4. Sélectionnez **Nouvelle inscription**.
5. Entrez un nom pour votre application. Par exemple : `Azure AD B2C App`.
6. Acceptez la sélection de **Comptes dans cet annuaire organisationnel uniquement** pour cette application.
7. Pour le champ **URI de redirection**, acceptez la valeur **Web**, puis entrez l’URL suivante en minuscules, où `your-B2C-tenant-name` est remplacé par le nom de votre locataire Azure AD B2C : Par exemple, `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`:

    ```
    https://your--B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Toutes les URL doivent désormais utiliser [b2clogin.com](b2clogin.md).

8. Cliquez sur **S'inscrire**. Copiez l’**ID d’application (client)**  : cette information vous servira plus tard.
9. Dans le menu de l’application, sélectionnez **Certificats et secrets**, puis **Nouvelle clé secrète client**.
10. Entrez un nom pour la clé secrète client. Par exemple : `Azure AD B2C App Secret`.
11. Sélectionnez la période d’expiration. Pour cette application, acceptez la sélection **Dans 1 an**.
12. Sélectionnez **Ajouter** et copiez la valeur de la nouvelle clé secrète client qui s’affiche : elle vous servira plus tard.

## <a name="configure-azure-ad-as-an-identity-provider"></a>Configurer Azure AD en tant que fournisseur d’identité

1. Veillez à bien utiliser le répertoire qui contient le locataire Azure AD B2C. Sélectionnez le filtre **Répertoire et abonnement** dans le menu supérieur, puis choisissez l’annuaire qui contient votre locataire Azure AD B2C.
2. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
3. Cliquez sur **Fournisseurs d’identité**, puis sélectionnez **Ajouter**.
4. Saisissez un **Nom**. Par exemple, entrez : `Contoso Azure AD`.
5. Sélectionnez **Type de fournisseur d’identité**, sélectionnez **Open ID Connect (préversion)** , puis cliquez sur **OK**.
6. Sélectionnez **Configurer ce fournisseur d’identité**
7. Pour **URL des métadonnées**, entrez l’URL suivante qui remplace `your-AD-tenant-domain` par le nom de domaine de votre locataire Azure AD. Exemple `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration` :

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

8. Pour **ID client**, entrez l’ID d’application que vous avez enregistré précédemment et pour **Clé secrète client**, entrez la valeur de clé secrète que vous avez enregistrée précédemment.
9. Entrez éventuellement une valeur pour **Domain_hint**. Par exemple : `ContosoAD`. Il s’agit de la valeur à utiliser quand vous faites référence à ce fournisseur d’identité à l’aide de *domain_hint* dans la requête. 
10. Cliquez sur **OK**.
11. Sélectionnez **Mapper les revendications de ce fournisseur d’identité** et définissez les revendications suivantes :
    
    - Pour **Identifiant utilisateur**, entrez `oid`.
    - Pour **Nom d’affichage**, entrez `name`.
    - Pour **Prénom**, entrez `given_name`.
    - Pour **Nom**, entrez `family_name`.
    - Pour **E-mail**, entrez `unique_name`.

12. Cliquez sur **OK**, puis sur **Créer** pour enregistrer votre configuration.
