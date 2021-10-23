---
title: Autoriser des comptes de développeurs avec Azure Active Directory B2C
titleSuffix: Azure API Management
description: Découvrez comment autoriser les utilisateurs du portail des développeurs dans gestion des API Azure à l’aide d’Azure Active Directory B2C
services: api-management
author: dlepow
ms.service: api-management
ms.topic: how-to
ms.date: 09/28/2021
ms.author: danlep
ms.openlocfilehash: 553f7ce69d63ceebcdaacc3b29b0abc2a01987f7
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129984551"
---
# <a name="how-to-authorize-developer-accounts-by-using-azure-active-directory-b2c-in-azure-api-management"></a>Comment autoriser des comptes de développeurs avec Azure Active Directory B2C dans Gestion des API Azure


Azure Active Directory B2C est une solution de gestion des identités de cloud pour applications web et mobiles grand public. Vous pouvez l’utiliser pour gérer l’accès au portail des développeurs API Management. 

Dans ce tutoriel, vous allez découvrir comment configurer le service Gestion des API pour permettre son intégration à Azure Active Directory B2C. Comme indiqué plus loin dans cet article, si vous utilisez l’ancien portail des développeurs, qui est déprécié, certaines étapes diffèrent.

Pour plus d’informations sur l’activation de l’accès au portail des développeurs à l’aide d’Active Directory Azure classique, consultez [Comment autoriser des comptes de développeurs avec Azure Active Directory](api-management-howto-aad.md).

## <a name="prerequisites"></a>Configuration requise

* Un locataire Azure Active Directory B2C dans lequel créer une application. Pour plus d’informations, consultez la [Vue d’ensemble d’Azure Active Directory B2C](../active-directory-b2c/overview.md).
* Une instance APIM. Si vous n’en avez pas, consultez les informations permettant de [créer une instance du service Gestion des API Azure](get-started-create-service-instance.md).

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="configure-sign-up-and-sign-in-user-flow"></a>Configurer le flux d’utilisateur Inscription et connexion

Dans cette section, vous allez créer un flux d’utilisateurs au sein de votre locataire Azure Active Directory B2C, qui contient à la fois les stratégies d’inscription et de connexion. Pour obtenir des instructions détaillées, consultez [Créer des flux d’utilisateurs et des stratégies personnalisées dans Azure Active Directory B2C](../active-directory-b2c/tutorial-create-user-flows.md?pivots=b2c-us).

1. Dans le [Portail Azure](https://portal.azure.com), accédez à votre abonné Azure Active Directory B2C.
1. Sous **Stratégies**, sélectionnez **Flux d’utilisateurs** >  **+ Nouveau flux d’utilisateurs**.
1. Dans la page **Créer un flux d’utilisateur**, sélectionnez le flux utilisateur **Inscription et connexion**.
1. Fournissez les informations suivantes :
    1. Entrez un nom unique pour le flux d’utilisateurs.
    1. Dans **Fournisseurs d’identité**, sélectionnez **Inscription par e-mail**.
    1. Dans **Attributs utilisateur et revendications de jeton**, sélectionnez les attributs et les revendications nécessaires pour le portail des développeurs API Management (inutile pour le portail des développeurs hérités).
         ![Revendications de l’application](./media/api-management-howto-aad-b2c/api-management-application-claims.png)
        * **Attributs** : nom donné, nom de famille
        * **Revendications** : adresses e-mail, nom donné, nom de famille, ID objet de l’utilisateur
1. Sélectionnez **Create** (Créer).

## <a name="configure-identity-provider-for-developer-portal"></a>Configurer le fournisseur d’identité pour le portail des développeurs

1. Dans un onglet [Portail Azure](https://portal.azure.com) séparé, accédez à votre instance API Management.
1. Sous **Portail des développeurs**, sélectionnez **Identités** >  **+ Ajouter**.
1. Dans la page **Ajouter le fournisseur d’identité**, sélectionnez **Azure Active Directory B2C**.
1. Dans la fenêtre **Ajouter le fournisseur d’identité**, copiez l’**URL de redirection**.

    :::image type="content" source="media/api-management-howto-aad-b2c/b2c-identity-provider-redirect-url.png" alt-text="Copier l’URL de redirection":::    

1. Revenez à l’onglet du navigateur pour votre abonné Azure Active Directory B2C dans le Portail Azure. Sélectionnez **Inscriptions d'applications** >   **+ Nouvelle inscription**.
1. Dans la page **Inscrire une application**, entrez les informations d’inscription de votre application.
    * Dans la section **Nom**, entrez un nom d’application de votre choix.
    * Dans la section **Types de compte pris en charge**, choisissez le type de comptes appropriés pour votre scénario. Pour cibler un grand nombre de clients, sélectionnez **Comptes dans un fournisseur d’identité ou un répertoire organisationnel (pour authentifier les utilisateurs avec des flux d’utilisateurs)** . Pour plus d’informations, consultez [Inscrire une application](../active-directory/develop/quickstart-register-app.md#register-an-application).
    * Dans **URI de redirection**, entrez l’URL de redirection copiée de votre instance API Management.
    * Dans **Autorisations**, sélectionnez **Accorder le consentement administrateur aux autorisations openid et offline_access.**
    * Sélectionnez **Inscrire** pour créer l’application.

    :::image type="content" source="media/api-management-howto-aad-b2c/b2c-app-registration.png" alt-text="Inscrire une nouvelle application":::

1. Dans la page de **Présentation** de l’application, recherchez la valeur **ID d’application (client)** et copiez la valeur dans le presse-papiers.

    :::image type="content" source="media/api-management-howto-aad-b2c/b2c-app-id.png" alt-text="ID de l’application":::
1. Revenez à la page **Ajouter un fournisseur d’identité** dans API Management et collez l’ID dans la zone de texte **ID client**.
1. Revenez à l’inscription de l’application B2C. Sélectionnez **Certificats et secrets** >  **+ Nouveau secret client**. 
    :::image type="content" source="media/api-management-howto-aad-b2c/generate-app-key.png" alt-text="Créer un secret client"::: 
   * Dans la page **Ajouter un secret client**, entrez une **Description** et sélectionnez **Ajouter**.
   * Enregistrez la clé dans un endroit sûr. Cette valeur secrète ne sera plus jamais affichée lorsque vous aurez quitté cette page.
1. Revenez à la page **Ajouter un fournisseur d’identité** dans API Management et collez la clé dans la zone de texte **Secret client**.
1. Revenez à l’inscription de l’application B2C. Dans le menu de gauche, sous **Gérer**, sélectionnez **Authentification**.
    * Sous **Octroi implicite et flux hybrides**, cochez les cases **Jetons d’accès** et **Jetons d’ID**.
    * Sélectionnez **Enregistrer**.
1. Revenez à la page **Ajouter un fournisseur d’identité** API Management.
    * Dans **Abonné de connexion**, spécifiez le nom de domaine de l’abonné Azure Active Directory B2C.
    * Le champ **Autorité** vous permet de contrôler l’URL de connexion Azure Active Directory B2C à utiliser. Définissez la valeur sur **< votre nom_de_locataire_b2c>.b2clogin.com**.
    * Spécifiez la **stratégie d’inscription** et la **stratégie de connexion** à partir des stratégies de l’abonné B2C.
    * Si vous le souhaitez, fournissez la **Stratégie de modification du profil** et la **Stratégie de réinitialisation du mot de passe**.

         :::image type="content" source="media/api-management-howto-aad-b2c/add-identity-provider.png" alt-text="Configuration du fournisseur d’identité Active Directory B2C":::
1. Après avoir spécifié la configuration souhaitée, sélectionnez **Ajouter**.

Après avoir enregistré les modifications, les développeurs pourront créer de nouveaux comptes et se connecter au portail des développeurs à l’aide d’Azure Active Directory B2C.

## <a name="developer-portal---add-azure-active-directory-b2c-account-authentication"></a>Portail des développeurs - Ajouter l’authentification de compte Azure Active Directory B2C

> [!IMPORTANT]
> Vous devez [republier le portail des développeurs](api-management-howto-developer-portal-customize.md#publish) lorsque vous créez ou mettez à jour des paramètres de configuration Azure Active Directory B2C pour que les modifications prennent effet.

Dans le portail des développeurs, il est possible de se connecter via Azure Active Directory B2C à l’aide du widget **Bouton de connexion : OAuth**. Le widget est déjà inclus dans la page de connexion du contenu du portail des développeurs par défaut.

1. Pour s’inscrire en utilisant Azure Active Directory B2C, ouvrez une nouvelle fenêtre de navigateur et accédez au portail des développeurs. Sélectionnez **Connexion**.

1. Dans la page **Se connecter**, sélectionnez **Azure Active Directory B2C**.

    :::image type="content" source="media/api-management-howto-aad-b2c/developer-portal-sign-in.png" alt-text="Se connecter au portail des développeurs":::
1. Vous êtes redirigé vers la stratégie d’inscription que vous avez configurée dans la section précédente. Choisissez de vous inscrire à l’aide de votre adresse de messagerie dans l’abonné Active Directory B2C

Une fois l’inscription terminée, vous êtes redirigé vers le portail des développeurs. Vous êtes maintenant inscrit sur le portail des développeurs pour votre instance de service Gestion des API.

:::image type="content" source="media/api-management-howto-aad-b2c/developer-portal-home.png" alt-text="Se connecter au portail des développeurs complet":::

Bien qu’un compte soit automatiquement créé chaque fois qu’un nouvel utilisateur se connecte avec Azure Active Directory B2C, vous pouvez ajouter le même widget à la page d’inscription.

Le widget **Formulaire d’inscription : OAuth** représente un formulaire utilisé pour s’inscrire à l’aide d’OAuth.

## <a name="legacy-developer-portal---how-to-sign-up-with-azure-active-directory-b2c"></a>Ancien portail des développeurs - Comment s’inscrire auprès d’Azure Active Directory B2C

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

> [!NOTE]
> Pour intégrer correctement B2C à l’ancien portail des développeurs, utilisez les flux d’utilisateurs **standard v1** avec la [réinitialisation de mot de passe](/azure/active-directory-b2c/user-flow-self-service-password-reset) avant l’inscription/la connexion à un compte de développeur via Azure Active Directory B2C. 

1. Ouvrez une nouvelle fenêtre de navigateur et accédez à l’ancien portail des développeurs. Cliquez sur le bouton **S’inscrire**.

    :::image type="content" source="media/api-management-howto-aad-b2c/b2c-dev-portal.png" alt-text="S’inscrire dans le portail des développeurs hérité":::

1. Choisissez de vous inscrire avec **Azure Active Directory B2C**.

    :::image type="content" source="media/api-management-howto-aad-b2c/b2c-dev-portal-b2c-button.png" alt-text="S'inscrire avec Azure Active Directory B2C":::

1. Vous êtes redirigé vers la stratégie d’inscription que vous avez configurée dans la section précédente. Choisissez de vous inscrire avec votre adresse de messagerie ou l’un de vos comptes sociaux existants.

   > [!NOTE]
   > Si Azure Active Directory B2C est la seule option sous l’onglet **Identités** du portail des éditeurs, vous êtes redirigé directement vers la stratégie d’inscription.

   :::image type="content" source="media/api-management-howto-aad-b2c/b2c-dev-portal-b2c-options.png" alt-text="S’inscrire aux options dans le portail des développeurs hérité":::

   Une fois l’inscription terminée, vous êtes redirigé vers le portail des développeurs. Vous êtes maintenant inscrit sur le portail des développeurs pour votre instance de service Gestion des API.



## <a name="next-steps"></a>Étapes suivantes

*  [Vue d’ensemble d’Azure Active Directory B2C]
*  [Azure Active Directory B2C : infrastructure de stratégie extensible]
*  [Utiliser un compte Microsoft comme fournisseur d’identité dans Azure Active Directory B2C]
*  [Utiliser un compte Google comme fournisseur d’identité dans Azure Active Directory B2C]
*  [Utiliser un compte LinkedIn comme fournisseur d’identité dans Azure Active Directory B2C]
*  [Utiliser un compte Facebook comme fournisseur d’identité dans Azure Active Directory B2C]


[How to add operations to an API]: ./mock-api-responses.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: ./api-management-policies.md
[Caching policies]: ./api-management-policies.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[https://oauth.net/2/]: https://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Vue d’ensemble d’Azure Active Directory B2C]: ../active-directory-b2c/overview.md
[How to authorize developer accounts using Azure Active Directory]: ./api-management-howto-aad.md
[Azure Active Directory B2C : infrastructure de stratégie extensible]: ../active-directory-b2c/user-flow-overview.md
[Utiliser un compte Microsoft comme fournisseur d’identité dans Azure Active Directory B2C]: ../active-directory-b2c/identity-provider-microsoft-account.md
[Utiliser un compte Google comme fournisseur d’identité dans Azure Active Directory B2C]: ../active-directory-b2c/identity-provider-google.md
[Utiliser un compte Facebook comme fournisseur d’identité dans Azure Active Directory B2C]: ../active-directory-b2c/identity-provider-facebook.md
[Utiliser un compte LinkedIn comme fournisseur d’identité dans Azure Active Directory B2C]: ../active-directory-b2c/identity-provider-linkedin.md

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Log in to the Developer portal using an Azure Active Directory account]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account
