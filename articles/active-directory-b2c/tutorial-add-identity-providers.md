---
title: Tutoriel - Ajouter des fournisseurs d’identité à vos applications - Azure Active Directory B2C | Microsoft Docs
description: Découvrez comment ajouter des fournisseurs d’identité à vos applications dans Azure Active Directory B2C à l’aide du portail Azure.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 02/01/2019
ms.author: davidmu
ms.openlocfilehash: 2a1843f941c6abc46928b38a66025fa87c4bcea5
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/06/2019
ms.locfileid: "55757329"
---
# <a name="tutorial-add-identity-providers-to-your-applications-in-azure-active-directory-b2c"></a>Tutoriel : Ajouter des fournisseurs d’identité à vos applications dans Azure Active Directory B2C

Dans vos applications, vous souhaiterez peut-être permettre aux utilisateurs de se connecter avec différents fournisseurs d’identité. Un *fournisseur d’identité* crée, entretient et gère les informations d’identité tout en fournissant des services d’authentification pour les applications. Vous pouvez ajouter des fournisseurs d’identité qui sont pris en charge par Azure Active Directory (Azure AD) B2C à votre [flux d’utilisateurs](active-directory-b2c-reference-policies.md) à l’aide du portail Azure.

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Créer des applications de fournisseurs d’identité
> * Ajouter les fournisseurs d’identité dans votre locataire
> * Ajouter les fournisseurs d’identité à votre flux d’utilisateur

Vous utilisez généralement un seul fournisseur d’identité dans vos applications, mais vous pouvez en ajouter d’autres. Ce tutoriel vous montre comment ajouter un fournisseur d’identité Azure AD et un fournisseur d’identité Facebook à votre application. L’ajout de ces deux fournisseurs d’identité à votre application est facultatif. Vous pouvez également ajouter d’autres fournisseurs d’identité, comme [Amazon](active-directory-b2c-setup-amzn-app.md), [Github](active-directory-b2c-setup-github-app.md), [Google](active-directory-b2c-setup-goog-app.md), [LinkedIn](active-directory-b2c-setup-li-app.md), [Microsoft](active-directory-b2c-setup-msa-app.md) ou [Twitter](active-directory-b2c-setup-twitter-app.md). 

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

[Créez un flux d’utilisateur](tutorial-create-user-flows.md) pour permettre aux utilisateurs de s’inscrire et de se connecter à votre application. 

## <a name="create-applications"></a>Créer des applications

Les applications de fournisseurs d’identité offrent l’identificateur et la clé permettant d’activer la communication avec votre locataire Azure AD B2C. Dans cette section du tutoriel, vous créez une application Azure AD et une application Facebook à partir desquelles vous obtenez les identificateurs et les clés nécessaires pour ajouter les fournisseurs d’identité à votre locataire. Si vous n’ajoutez qu’un des fournisseurs d’identité, il vous suffit de créer l’application pour ce fournisseur.

### <a name="create-an-azure-active-directory-application"></a>Créer une application Azure Active Directory

Pour autoriser la connexion des utilisateurs à partir d’Azure AD, vous devez inscrire une application au sein du locataire Azure AD. Le locataire Azure AD n’est pas identique à votre locataire Azure AD B2C.

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Veillez à utiliser l’annuaire contenant votre locataire Azure AD en cliquant sur le **filtre Répertoire et abonnement** dans le menu du haut et en choisissant le répertoire contenant votre locataire Azure AD.
3. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Inscriptions d’applications**.
4. Sélectionnez **Nouvelle inscription d’application**.
5. Entrez un nom pour votre application. Par exemple : `Azure AD B2C App`.
6. Pour le **Type d’application**, sélectionnez `Web app / API`.
7. Pour le champ **URL de connexion**, entrez l’URL suivante en minuscules, où `your-B2C-tenant-name` est remplacé par le nom de votre locataire Azure AD B2C.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```
    
    Par exemple : `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.
    
    Toutes les URL doivent désormais utiliser [b2clogin.com](b2clogin.md).

8. Cliquez sur **Créer**. Copiez **l’ID d’application** pour une utilisation ultérieure.
9. Sélectionnez l’application, puis **Paramètres**.
10. Sélectionnez **Clés**, entrez la description de la clé, sélectionnez une durée, puis cliquez sur **Enregistrer**. Copiez la valeur de la clé afin de l’utiliser ultérieurement dans ce tutoriel.

### <a name="create-a-facebook-application"></a>Création d’une application Facebook

Pour utiliser un compte Facebook en tant que fournisseur d’identité dans Azure AD B2C, vous devez créer une application dans Facebook. Si vous n’avez pas encore de compte Facebook, vous pouvez en obtenir un à l’adresse [https://www.facebook.com/](https://www.facebook.com/).

1. Connectez-vous à [Facebook pour les développeurs](https://developers.facebook.com/) avec les informations d’identification de votre compte Facebook.
2. Si ce n’est déjà fait, vous devez vous inscrire en tant que développeur Facebook. Pour cela, sélectionnez **S’inscrire** dans le coin supérieur droit de la page, acceptez les politiques de Facebook et suivez les étapes de l’inscription.
3. Sélectionnez **Mes applications**, puis cliquez sur **Ajouter une nouvelle application**. 
4. Entrez un **nom d’affichage** et une **adresse e-mail de contact** valide.
5. Cliquez sur **Créer l’ID d’application**. Il vous sera peut-être demander d’accepter les politiques de la plateforme Facebook et d’effectuer une vérification de sécurité en ligne.
6. Sélectionnez **Paramètres** > **Base**.
7. Choisissez une **Catégorie**, par exemple `Business and Pages`. Cette valeur est requise par Facebook, mais elle n’est pas utilisée pour Azure AD B2C.
8. Au bas de la page, sélectionnez **Ajouter une plateforme**, puis sélectionnez **Site web**.
9. Dans **URL du site**, entrez `https://your-tenant-name.b2clogin.com/`, en remplaçant `your-tenant-name` par le nom de votre locataire. Entrez une URL pour l’**URL de stratégie de confidentialité**, par exemple `http://www.contoso.com`. L’URL de stratégie est une page que vous tenez à jour pour fournir des informations de confidentialité pour votre application.
10. Sélectionnez **Enregistrer les modifications**.
11. En haut de la page, copiez la valeur de l’**ID de l’application**. 
12. Cliquez sur **Afficher**, puis copiez la valeur **Clé secrète de l’application**. Vous avez besoin de ces deux valeurs pour configurer Facebook en tant que fournisseur d’identité dans votre client. **App Secret** est une information d’identification de sécurité importante.
13. Sélectionnez **Produits**, puis sélectionnez **Configurer** sous **Connexion Facebook**.
14. Sélectionnez **Paramètres** sous **Connexion Facebook**.
15. Dans **URI de redirection OAuth valides**, entrez `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Remplacez `your-tenant-name` par le nom de votre locataire. Cliquez sur **Save Changes** en bas de la page.
16. Pour rendre votre application Facebook disponible dans Azure AD B2C, cliquez sur le sélecteur **d’état** dans la partie supérieure droite de la page et **activez-le**. Cliquez sur **Confirmer**. À ce stade, l’état doit passer de **Développement** à **Production**.

## <a name="add-the-identity-providers"></a>Ajouter les fournisseurs d’identité

Après avoir créé l’application pour le fournisseur d’identité que vous souhaitez ajouter, vous ajoutez le fournisseur d’identité à votre locataire.

### <a name="add-the-azure-active-directory-identity-provider"></a>Ajouter le fournisseur d’identité Azure Active Directory

1. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD B2C en cliquant sur le **filtre annuaire et abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire Azure AD B2C.
2. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Azure AD B2C**.
3. Cliquez sur **Fournisseurs d’identité**, puis sélectionnez **Ajouter**.
4. Saisissez un **Nom**. Par exemple, entrez *Contoso Azure AD*.
5. Sélectionnez **Type de fournisseur d’identité**, sélectionnez **Open ID Connect (préversion)**, puis cliquez sur **OK**.
6. Cliquez sur **Configurer ce fournisseur d’identité**.
7. Pour **URL des métadonnées**, entrez l’URL suivante qui remplace `your-AD-tenant-domain` par le nom de domaine de votre locataire Azure AD.

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

    Par exemple : `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`.

8. Pour **ID client**, entrez l’ID d’application que vous avez enregistré précédemment et pour le **secret client**, entrez la valeur de clé que vous avez enregistrée précédemment.
9. Entrez éventuellement une valeur pour **Domain_hint**. Par exemple : `ContosoAD`. 
10. Cliquez sur **OK**.
11. Sélectionnez **Mapper les revendications de ce fournisseur d’identité** et définissez les revendications suivantes :
    
    - Pour **Identifiant utilisateur**, entrez `oid`.
    - Pour **Nom d’affichage**, entrez `name`.
    - Pour **Prénom**, entrez `given_name`.
    - Pour **Nom**, entrez `family_name`.
    - Pour **E-mail**, entrez `unique_name`.

12. Cliquez sur **OK**, puis sur **Créer** pour enregistrer votre configuration.

### <a name="add-the-facebook-identity-provider"></a>Ajouter le fournisseur d’identité Facebook

1. Cliquez sur **Fournisseurs d’identité**, puis sélectionnez **Ajouter**.
2. Entrez un **nom**. Par exemple, entrez *Facebook*.
3. Sélectionnez **Type de fournisseur d’identité**, sélectionnez **Facebook**, puis cliquez sur **OK**.
4. Sélectionnez **Configurer ce fournisseur d’identité** et entrez l’ID d’application que vous avez enregistré précédemment en tant **qu’ID client**. Entrez le secret d’application que vous avez copié enregistré en tant que **Secret client**.
5. Cliquez sur **OK**, puis sur **Créer** pour enregistrer votre configuration Facebook.

## <a name="update-the-user-flow"></a>Mettre à jour le flux d’utilisateur

Dans le tutoriel que vous avez suivi dans le cadre des prérequis, vous avez créé un flux d’utilisateur pour l’inscription et la connexion nommé *B2C_1_signupsignin1*. Dans cette section, vous ajoutez les fournisseurs d’identité au flux d’utilisateur *B2C_1_signupsignin1*.

1. Sélectionnez **Flux d’utilisateurs (stratégies)**, puis sélectionnez le flux utilisateur *B2C_1_signupsignin1*.
2. Sélectionnez **Fournisseurs d’identité**, sélectionnez les fournisseurs d’identité **Facebook** et **Contoso Azure AD** que vous avez ajoutés.
3. Sélectionnez **Enregistrer**.

### <a name="test-the-user-flow"></a>Tester le flux utilisateur

1. Dans la page Vue d’ensemble du flux d’utilisateur, sélectionnez **Exécuter le flux d’utilisateur**.
2. Pour **Application**, sélectionnez l’application web *webapp1* que vous avez précédemment inscrite. L’**URL de réponse** doit être `https://jwt.ms`.
3. Cliquez sur **Exécuter le flux d’utilisateur**, puis connectez-vous avec un fournisseur d’identité que vous avez précédemment ajouté.
4. Répétez les étapes 1 à 3 pour les autres fournisseurs d’identité que vous avez ajoutés.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer des applications de fournisseurs d’identité
> * Ajouter les fournisseurs d’identité dans votre locataire
> * Ajouter les fournisseurs d’identité à votre flux d’utilisateur

> [!div class="nextstepaction"]
> [Personnaliser l’interface utilisateur de vos applications dans Azure Active Directory B2C](tutorial-customize-ui.md)