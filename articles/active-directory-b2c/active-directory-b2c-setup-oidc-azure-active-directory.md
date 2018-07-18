---
title: Ajouter un fournisseur Azure AD à l’aide de stratégies prédéfinies dans Azure Active Directory B2C | Microsoft Docs
description: Découvrez comment ajouter un fournisseur d’identité Open ID Connect (Azure AD).
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/27/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 70d3a19b715052fe658102929a1c29cf3db2d595
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/03/2018
ms.locfileid: "37443716"
---
# <a name="azure-active-directory-b2c-sign-in-using-azure-ad-accounts-through-a-built-in-policy"></a>Azure Active Directory B2C : Se connecter à l’aide de comptes Azure AD par le biais d’une stratégie prédéfinie

>[!NOTE]
> Cette fonctionnalité est en version préliminaire publique. Ne l’utilisez pas dans des environnements de production.

Cet article explique comment autoriser la connexion d’utilisateurs à partir des stratégies prédéfinies d’une organisation Azure Active Directory (Azure AD) spécifique.

## <a name="create-an-azure-ad-app"></a>Création d’une application Azure AD

Pour autoriser la connexion des utilisateurs d’une organisation Azure AD spécifique, vous devez inscrire une application au sein du locataire Azure AD de l’organisation.

>[!NOTE]
> Nous utilisons « contoso.com » pour l’organisation (locataire) Azure AD et « fabrikamb2c.onmicrosoft.com » comme locataire Azure AD B2C dans les instructions suivantes.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
1. Dans le menu supérieur, sélectionnez votre compte. Dans la liste **Répertoire**, choisissez l’organisation (locataire) Azure AD auprès de laquelle vous voulez inscrire votre application (contoso.com).
1. Sélectionnez **Tous les services** dans le volet gauche, puis recherchez « Inscriptions d’applications ».
1. Sélectionnez **Nouvelle inscription d’application**.
1. Entrez un nom pour votre application (par exemple, `Azure AD B2C App`).
1. Pour le type d’application, sélectionnez **Application web/API**.
1. Pour **URL de connexion**, entrez l’URL suivante où `yourtenant` est remplacé par le nom de votre locataire Azure AD B2C (`fabrikamb2c.onmicrosoft.com`) :

    >[!NOTE]
    >La valeur de « yourtenant » doit être en minuscules dans **l’URL de connexion**.

    ```Console
    https://login.microsoftonline.com/te/yourtenant.onmicrosoft.com/oauth2/authresp
    ```

1. Enregistrez l’ID d’application, que vous allez utiliser dans la section suivante en tant qu’ID client.
1. Dans le panneau **Paramètres**, sélectionnez **Clés**.
1. Entrez la **description de la clé** sous la section **Mots de passe** et définissez la **durée** sur « N’expire jamais ». 
1. Cliquez sur **Enregistrer**, puis notez la **valeur** de clé obtenue, que vous allez utiliser dans la section suivante en tant que clé secrète client.

## <a name="configure-azure-ad-as-an-identity-provider-in-your-tenant"></a>Configurer Azure AD en tant que fournisseur d’identité dans votre locataire

1. Dans le menu supérieur, sélectionnez votre compte. À partir de la liste **Répertoire**, choisissez le locataire Azure AD B2C (fabrikamb2c.onmicrosoft.com).
1. [Accédez au menu des paramètres Azure AD B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) dans le portail Azure.
1. Dans le menu des paramètres Azure AD B2C, cliquez sur **Fournisseurs d’identité**.
1. Cliquez sur **+Ajouter** en haut du volet.
1. Fournissez un **Nom** convivial pour la configuration de fournisseur d’identité. Par exemple, entrez « Contoso Azure AD ».
1. Cliquez sur **Type de fournisseur d’identité**, sélectionnez **Open ID Connect**, puis cliquez sur **OK**.
1. Cliquez sur **Configurer ce fournisseur d’identité**.
1. Pour **URL des métadonnées**, entrez l’URL suivante, où `yourtenant` est remplacé par le nom de votre locataire Azure AD (par exemple, `contoso.com`) :

    ```Console
    https://login.microsoftonline.com/yourtenant/.well-known/openid-configuration
    ```
1. Pour **ID client** et **Clé secrète client**, entrez l’ID d’application et la clé de la section précédente.
1. Conservez la valeur par défaut pour **Étendue**, qui doit être `openid`.
1. Conservez la valeur par défaut pour **Type de réponse**, qui doit être `code`.
1. Conservez la valeur par défaut pour **Mode de réponse**, qui doit être `form_post`.
1. Entrez éventuellement une valeur pour **Domaine** (par exemple, `ContosoAD`). Il s’agit de la valeur à utiliser quand vous faites référence à ce fournisseur d’identité à l’aide de *domain_hint* dans la requête. 
1. Cliquez sur **OK**.
1. Cliquez sur **Mapper les revendications de ce fournisseur d’identité**.
1. Pour **Identifiant utilisateur**, entrez `oid`.
1. Pour **Nom d’affichage**, entrez `name`.
1. Pour **Prénom**, entrez `given_name`.
1. Pour **Nom**, entrez `family_name`.
1. Pour **E-mail**, entrez `unique_name`.
1. Cliquez sur **OK**, puis sur **Créer** pour enregistrer votre configuration.

## <a name="next-steps"></a>Étapes suivantes

Ajoutez le fournisseur d’identité Azure AD nouvellement créé à une stratégie prédéfinie et envoyez des commentaires à [AADB2CPreview@microsoft.com](mailto:AADB2CPreview@microsoft.com).
