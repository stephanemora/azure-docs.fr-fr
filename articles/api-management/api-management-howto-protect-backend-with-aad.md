---
title: Protéger un back-end d’API dans Gestion des API à l’aide d’OAuth 2.0 et d’Azure Active Directory
titleSuffix: Azure API Management
description: Apprenez à sécuriser l’accès utilisateur à un back-end d’API web dans Gestion des API Azure et le portail des développeurs avec l’autorisation utilisateur OAuth 2.0 et Azure Active Directory.
services: api-management
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 09/17/2021
ms.author: danlep
ms.custom: contperf-fy21q1
ms.openlocfilehash: 7739411364b187e437e9dda4c72dff85de26d741
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129669565"
---
# <a name="protect-a-web-api-backend-in-azure-api-management-using-oauth-20-authorization-with-azure-active-directory"></a>Protéger un back-end d’API web dans Gestion des API Azure en utilisant l’autorisation OAuth 2.0 avec Azure Active Directory 

Dans cet article, vous allez apprendre à configurer une instance de [Gestion des API Azure](api-management-key-concepts.md) pour protéger une API en utilisant le [protocole OAuth 2.0 avec Azure Active Directory (Azure AD)](../active-directory/develop/active-directory-v2-protocols.md). 

> [!NOTE]
> Cette fonctionnalité est disponible dans les niveaux **Développeur**, **De base** et **Standard** et **Premium** de gestion des API.  
> 
> Vous pouvez suivre chaque étape ci-dessous dans le niveau **Consommation**, à l’exception de l’appel de l’API à partir du portail des développeurs.

## <a name="prerequisites"></a>Prérequis

Avant de suivre les étapes décrites dans cet article, vous devez avoir :

- Une instance Gestion des API
- Une API publiée avec l’instance Gestion des API
- un locataire Azure AD ;

## <a name="overview"></a>Vue d’ensemble

:::image type="content" source="media/api-management-howto-protect-backend-with-aad/overview-graphic-2021.png" alt-text="Graphique de vue d’ensemble pour conceptualiser visuellement le flux suivant.":::

1. Inscrire une application (backend-app) dans Azure AD pour représenter l’API.

1. Inscrire une autre application (client-app) dans Azure AD pour représenter une application cliente qui doit appeler l’API.

1. Dans Azure AD, accorder des autorisations pour permettre à client-app d’appeler backend-app.

1. Configurez la console des développeurs dans le portail des développeurs pour appeler l’API à l’aide de l’autorisation utilisateur OAuth 2.0.

1. Ajouter une stratégie **validate-jwt** afin de valider le jeton OAuth pour chaque requête entrante.

## <a name="1-register-an-application-in-azure-ad-to-represent-the-api"></a>1. Inscrire une application dans Azure AD pour représenter l’API

À l’aide du portail Azure, protégez une API avec Azure AD en inscrivant une application qui représente l’API dans Azure AD. 

Pour plus d'informations sur l'inscription d'une application, consultez [Démarrage rapide : Configurer une application pour exposer une API web](../active-directory/develop/quickstart-configure-app-expose-web-apis.md).

1. Dans le [portail Azure](https://portal.azure.com), recherchez et sélectionnez **Inscriptions d’applications**.

1. Sélectionnez **Nouvelle inscription**. 

1. Lorsque la page **Inscrire une application** s’affiche, saisissez les informations d’inscription de votre application :

   - Dans la section **Nom**, saisissez un nom d’application cohérent qui s’affichera pour les utilisateurs de l’application, par exemple *backend-app*. 
   - Dans la section **Types de comptes pris en charge**, sélectionnez une option adaptée à votre scénario. 

1. Laissez la section [**URI de redirection**](../active-directory/develop/reply-url.md) vide pour l’instant.

1. Sélectionnez **Inscrire** pour créer l’application. 

1. Dans la page **Vue d’ensemble** de l’application, recherchez la valeur **ID d’application (client)** et notez-la.

1. Sous la section **Gérer** du menu latéral, sélectionnez **Exposer une API** et définissez l’**URI de l’ID d’application** avec la valeur par défaut. Enregistrez cette valeur pour une utilisation ultérieure.

1. Sélectionnez le bouton **Ajouter une étendue** pour afficher la page **Ajouter une étendue** :
    1. Entrez un nouveau **Nom d’étendue**, le **Nom d’affichage du consentement administrateur** et la **Description du consentement administrateur**.
    1. Assurez-vous que l’état d’étendue **Activée** est sélectionné.

1. Sélectionnez le bouton **Ajouter une étendue** pour créer l’étendue. 

1. Répétez les étapes 8 et 9 pour ajouter toutes les étendues prises en charge par votre API.

1. Lorsque les étendues sont créées, prenez-en note afin de les utiliser à une étape ultérieure. 

## <a name="2-register-another-application-in-azure-ad-to-represent-a-client-application"></a>2. Inscrire une autre application dans Azure AD pour représenter une application cliente

Enregistrez chaque application client qui appelle l’API comme une application dans Azure AD. Dans cet exemple, l’application cliente est la **console de développeur** dans le portail des développeurs de gestion des API. 

Pour inscrire une autre application dans Azure AD afin de représenter la console de développeur :

1. Dans le [portail Azure](https://portal.azure.com), recherchez et sélectionnez **Inscriptions d’applications**.

1. Sélectionnez **Nouvelle inscription**.

1. Lorsque la page **Inscrire une application** s’affiche, saisissez les informations d’inscription de votre application :

   - Dans la section **Nom**, saisissez un nom d’application cohérent qui s’affichera pour les utilisateurs de l’application, par exemple *client-app*. 
   - Dans la section **Types de comptes pris en charge**, sélectionnez **Comptes dans un répertoire d’organisation (tout répertoire Azure AD – Multilocataire)** . 

1. Dans la section **URI de redirection**, sélectionnez `Web` et laissez le champ URL vide pour le moment.

1. Sélectionnez **Inscrire** pour créer l’application. 

1. Dans la page **Vue d’ensemble** de l’application, recherchez la valeur **ID d’application (client)** et notez-la.

1. Créez un secret client pour cette application afin de l’utiliser à une étape ultérieure.

   1. Sous la section **Gérer** du menu latéral, sélectionnez **Certificats et secrets**.
   1. Sous **Secrets client**, sélectionnez **Nouveau secret client**.
   1. Sous **Ajouter une clé secrète client**, fournissez une **Description** et choisissez le moment où la clé doit expirer.
   1. Sélectionnez **Ajouter**.

Lorsque le secret est créé, notez la valeur de clé afin de l’utiliser à une étape ultérieure. 

## <a name="3-grant-permissions-in-azure-ad"></a>3. Accorder des autorisations dans Azure AD

Maintenant que vous avez inscrit deux applications pour représenter l’API et la console de développeur, accordez des autorisations pour permettre à client-app d’appeler backend-app.  

1. Dans le [portail Azure](https://portal.azure.com), recherchez et sélectionnez **Inscriptions d’applications**.

1. Choisissez votre application cliente. Ensuite, dans la liste des pages de l’application, sélectionnez **Autorisations de l’API**.

1. Sélectionnez **Ajouter une autorisation**.

1. Sous **Sélectionnez une API**, sélectionnez **Mes API**, puis recherchez et sélectionnez votre backend-app.

1. Sélectionnez **Autorisations déléguées**, puis les autorisations appropriées à votre backend-app.

1. Sélectionnez **Ajouter des autorisations**.

Éventuellement :
1. Accédez à la page **Autorisations des API** de votre application cliente.

1. Sélectionnez **Accorder un consentement administrateur pour \<your-tenant-name>** afin de donner le consentement au nom de tous les utilisateurs de ce répertoire. 

## <a name="4-enable-oauth-20-user-authorization-in-the-developer-console"></a>4. Activer l’autorisation utilisateur OAuth 2.0 dans la console des développeurs

À ce stade, vous avez créé vos applications dans Azure AD et vous avez accordé les autorisations appropriées pour que client-app puisse appeler backend-app. 

Dans cet exemple, vous activez l’autorisation utilisateur OAuth 2.0 dans la console de développement (l’application cliente).

1. Dans le portail Azure, recherchez l’**URL du point de terminaison d’autorisation** et l’**URL du point de terminaison de jeton**, puis enregistrez-les pour plus tard. 
    1. Ouvrez la page **Inscriptions d’applications**. 
    1. Sélectionnez **Points de terminaison**.
    1. Copiez le **Point de terminaison d’autorisation OAuth 2.0** et le **Point de terminaison de jeton OAuth 2.0**. 

1. Accédez à votre instance Gestion des API.

1. Sous la section **Portail des développeurs** dans le menu latéral, sélectionnez **OAuth 2.0 + OpenID Connect**. 

1. Sous l’onglet **OAuth 2.0**, sélectionnez **Ajouter**.

1. Spécifiez un **Nom d’affichage** et une **Description**.

1. Pour **l’URL de la page d’inscription client**, entrez une valeur d’espace réservé telle que `http://localhost`. 
    * L’**URL de la page d’inscription client** pointe vers la page que les utilisateurs peuvent utiliser pour créer et configurer leurs propres comptes pour les fournisseurs OAuth 2.0 qui prennent en charge cette fonctionnalité. 
    * Nous utilisons un caractère générique car, dans cet exemple, les utilisateurs ne créent pas et ne configurent pas leurs propres comptes.

1. Sélectionnez **Code d’autorisation** comme **Types d’octroi d’autorisation**.

1. Spécifiez l’**URL de point de terminaison d’autorisation** et l’**URL de point de terminaison de jeton** que vous avez enregistrées plus tôt : 
    1. Copiez et collez la valeur de **Point de terminaison d’autorisation OAuth 2.0** et dans la zone de texte **URL de point de terminaison d’autorisation**. 
    1. Sélectionnez **PUBLICATION** sous Méthode de demande d’autorisation.
    1. Entrez la valeur de **Point de terminaison de jeton OAuth 2.0** et collez-la dans la zone de texte **URL de point de terminaison de jeton**. 
        * Si vous utilisez le point de terminaison **v1** :
          * Ajoutez un paramètre de corps nommé **Ressource**.
          * Entrez l’**ID d’application** de l’application back-end pour la valeur.
        * Si vous utilisez le point de terminaison **v2** :
          * Utilisez l’étendue d’application back-end que vous avez créée dans le champ **Étendue par défaut**.
          * Veillez à définir la valeur de la propriété [`accessTokenAcceptedVersion`](../active-directory/develop/reference-app-manifest.md#accesstokenacceptedversion-attribute) sur `2` dans le [manifeste de votre application](../active-directory/develop/reference-app-manifest.md).
          

   >[!IMPORTANT]
   > Si vous pouvez utiliser des points de terminaison **v1** ou **v2**, nous vous recommandons d’utiliser des points de terminaison v2. 

1. Spécifiez les informations d’identification du client :
    * Pour **ID client**, utilisez l’**ID d’application** de client-app.
    * Pour **Secret client**, utilisez la clé que vous avez créée pour client-app. 

1. Prenez note de l’**URI de redirection** pour le type d’octroi de code d’autorisation.

1. Sélectionnez **Create** (Créer).

1. Revenez à l’inscription de votre application cliente. 
 
1. Sous **Gérer**, sélectionnez **Authentification**.

1. Sous **Configurations de plateforme** :
    * Cliquez sur **Ajouter une plateforme**.
    * Sélectionnez le type **Web**. 
    * Collez l’URI de redirection que vous avez enregistré précédemment sous **URI de redirection**.
    * Cliquez sur le bouton **Configurer** pour enregistrer.

   Maintenant que la console des développeurs peut obtenir des jetons d’accès à partir de Azure AD via votre serveur d’autorisation OAuth 2.0, activez l’autorisation utilisateur OAuth 2.0 pour votre API. Cela permet à la console de développeur de savoir qu’elle doit obtenir un jeton d’accès pour l’utilisateur avant d’effectuer des appels vers votre API.

15. Accédez à votre instance Gestion des API,puis accédez à **API**.

1. Sélectionnez l’API que vous souhaitez protéger. Par exemple : `Echo API`.

1. Accédez à **Settings**.

1. Sous **Sécurité** :
    1. Choisissez **OAuth 2.0**.
    1. Sélectionnez le serveur OAuth 2.0 que vous avez configuré précédemment. 

1. Sélectionnez **Enregistrer**.

## <a name="5-successfully-call-the-api-from-the-developer-portal"></a>5. Appel de l’API à partir du portail des développeurs

> [!NOTE]
> Cette section ne s’applique pas au niveau **consommation**, qui ne prend pas en charge le portail des développeurs.

Maintenant que l’autorisation utilisateur OAuth 2.0 est activée sur votre API, la console de développeur obtient un jeton d’accès pour le compte de l’utilisateur avant d’appeler l’API.

1. Accédez à l’opération de votre choix sous l’API dans le portail des développeurs. 
1. Sélectionnez **Essayer** pour vous connecter à la console des développeurs.

1. Observez le nouvel élément dans la section **Autorisation** correspondant au serveur d’autorisation que vous venez d’ajouter.

1. Sélectionnez **Code d’autorisation** dans la liste déroulante Autorisation. 
1. Vous êtes invité à vous connecter au locataire Azure AD. 
    * Si vous êtes déjà connecté avec ce compte, il est possible qu’aucune invite ne s’affiche.

1. Une fois la connexion établie, un en-tête `Authorization` est ajouté à la requête avec un jeton d’accès d’Azure AD. Voici un exemple de jeton (encodage Base64) :

   ```
   Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCIsImtpZCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCJ9.eyJhdWQiOiIxYzg2ZWVmNC1jMjZkLTRiNGUtODEzNy0wYjBiZTEyM2NhMGMiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC80NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgvIiwiaWF0IjoxNTIxMTUyNjMzLCJuYmYiOjE1MjExNTI2MzMsImV4cCI6MTUyMTE1NjUzMywiYWNyIjoiMSIsImFpbyI6IkFWUUFxLzhHQUFBQUptVzkzTFd6dVArcGF4ZzJPeGE1cGp2V1NXV1ZSVnd1ZXZ5QU5yMlNkc0tkQmFWNnNjcHZsbUpmT1dDOThscUJJMDhXdlB6cDdlenpJdzJLai9MdWdXWWdydHhkM1lmaDlYSGpXeFVaWk9JPSIsImFtciI6WyJyc2EiXSwiYXBwaWQiOiJhYTY5ODM1OC0yMWEzLTRhYTQtYjI3OC1mMzI2NTMzMDUzZTkiLCJhcHBpZGFjciI6IjEiLCJlbWFpbCI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiSmlhbmciLCJnaXZlbl9uYW1lIjoiTWlhbyIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpcGFkZHIiOiIxMzEuMTA3LjE3NC4xNDAiLCJuYW1lIjoiTWlhbyBKaWFuZyIsIm9pZCI6IjhiMTU4ZDEwLWVmZGItNDUxMS1iOTQzLTczOWZkYjMxNzAyZSIsInNjcCI6InVzZXJfaW1wZXJzb25hdGlvbiIsInN1YiI6IkFGaWtvWFk1TEV1LTNkbk1pa3Z3MUJzQUx4SGIybV9IaVJjaHVfSEM1aGciLCJ0aWQiOiI0NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgiLCJ1bmlxdWVfbmFtZSI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsInV0aSI6ImFQaTJxOVZ6ODBXdHNsYjRBMzBCQUEiLCJ2ZXIiOiIxLjAifQ.agGfaegYRnGj6DM_-N_eYulnQdXHhrsus45QDuApirETDR2P2aMRxRioOCR2YVwn8pmpQ1LoAhddcYMWisrw_qhaQr0AYsDPWRtJ6x0hDk5teUgbix3gazb7F-TVcC1gXpc9y7j77Ujxcq9z0r5lF65Y9bpNSefn9Te6GZYG7BgKEixqC4W6LqjtcjuOuW-ouy6LSSox71Fj4Ni3zkGfxX1T_jiOvQTd6BBltSrShDm0bTMefoyX8oqfMEA2ziKjwvBFrOjO0uK4rJLgLYH4qvkR0bdF9etdstqKMo5gecarWHNzWi_tghQu9aE3Z3EZdYNI_ZGM-Bbe3pkCfvEOyA
   ```

1. Sélectionnez **Send** pour appeler l’API avec succès.

## <a name="6-configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>6. Configuration d’une stratégie de validation JWT pour autoriser des demandes

Jusqu’à présent :
* Vous avez essayé d’effectuer un appel à partir de la console des développeurs.
* Vous êtes invité à vous connecter au locataire Azure AD. 
* La console des développeurs obtient un jeton d’accès pour votre compte et comprend le jeton dans la requête adressée à l’API.

Toutefois, que se passe-t-il si quelqu’un appelle votre API sans jeton ou avec un jeton non valide ? Par exemple, si vous appelez l’API sans l’en-tête `Authorization`, l’appel sera toujours transmis, car la gestion des API ne valide pas le jeton d’accès. Elle transfert simplement l’en-tête `Authorization` à l’API principale.

Utilisez la stratégie [Validate JWT](./api-management-access-restriction-policies.md#ValidateJWT) pour pré-autoriser les requêtes dans Gestion des API avec les jetons d’accès de chaque requête entrante. Si une requête n’a pas de jeton valide, Gestion des API la bloque. 

L’exemple de stratégie suivant, lorsqu’il est ajouté à la section de stratégie `<inbound>`, vérifie la valeur de la revendication d’audience dans un jeton d’accès obtenu à partir d’Azure AD et retourne un message d’erreur si le jeton n’est pas valide. 


```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/{aad-tenant}/v2.0/.well-known/openid-configuration" />
    <required-claims>
        <claim name="aud">
            <value>insert aud claim value expected in the token</value>
        </claim>
    </required-claims>
</validate-jwt>
```

> [!NOTE]
> L’URL `openid-config` ci-dessus correspond au point de terminaison v2. Pour le point de terminaison `openid-config` v1, utilisez `https://login.microsoftonline.com/{aad-tenant}/.well-known/openid-configuration`.

> [!TIP] 
> Recherchez la valeur **{aad-tenant}** comme ID de locataire Azure AD dans le portail Azure, soit sur :
> * La page Vue d’ensemble de votre ressource Azure AD, soit sur
> * La page **Gérer > Propriétés** de votre ressource Azure AD.

Pour plus d’informations sur la façon de configurer des stratégies, consultez [Définir ou modifier des stratégies](./set-edit-policies.md).

## <a name="build-an-application-to-call-the-api"></a>Générer une application pour appeler l’API

Dans ce guide, vous avez utilisé la console de développeur dans Gestion des API comme exemple d’application cliente pour appeler le `Echo API` protégé par OAuth 2.0. Pour en savoir plus sur la façon de générer une application et d’implémenter OAuth 2.0, consultez [exemples de code Azure AD](../active-directory/develop/sample-v2-code.md).

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [Azure AD et OAuth2.0](../active-directory/develop/authentication-vs-authorization.md).
- Découvrez plus de [vidéos](https://azure.microsoft.com/documentation/videos/index/?services=api-management) sur Gestion des API.
- Pour les autres méthodes permettant de sécuriser votre service principal, consultez [Authentification mutuelle des certificats](./api-management-howto-mutual-certificates.md).
- [Création d’une instance du service Gestion des API](./get-started-create-service-instance.md).
- [Gérer votre première API](./import-and-publish.md).
