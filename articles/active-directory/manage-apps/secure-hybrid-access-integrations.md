---
title: Sécuriser l’accès hybride via des intégrations de partenaires Azure AD
description: Aidez les clients à découvrir les applications SaaS, à les migrer vers Azure AD et à connecter les applications qui utilisent des méthodes d’authentification héritées à Azure AD.
titleSuffix: Azure AD
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 04/20/2021
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c8bffabb1cb2e12fb8144e66e038f500a8e5229
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130253858"
---
# <a name="secure-hybrid-access-with-azure-active-directory-partner-integrations"></a>Accès hybride sécurisé avec les intégrations des partenaires Azure Active Directory

Azure Active Directory (Azure AD) prend en charge des protocoles d’authentification modernes conçus pour sécuriser les applications dans un monde très connecté et basé sur le cloud. Toutefois, de nombreuses applications professionnelles ont été créées pour fonctionner dans un réseau d’entreprise protégé, et certaines d’entre elles utilisent des méthodes d’authentification héritées. À mesure que les entreprises cherchent à mettre en place une stratégie de Confiance Zéro et à prendre en charge des environnements de travail hybrides et cloud-first, elles ont besoin de solutions capables de connecter leurs applications à Azure AD et de fournir des mécanismes d’authentification modernes pour leurs applications héritées.

Azure AD prend en charge des protocoles modernes comme SAML, WS-FED et OIDC en mode natif. Le proxy d’application dans Azure AD prend en charge Kerberos et l’authentification basée sur l’en-tête. D’autres protocoles, tels que SSH, NTLM, LDAP et les cookies, ne sont pas encore pris en charge. Mais les éditeurs de logiciels indépendants peuvent créer des solutions pour connecter ces applications à Azure AD afin d’aider les clients dans leur cheminement vers la confiance zéro.

Les éditeurs de logiciels indépendants ont la possibilité d’aider les clients à découvrir et à migrer des applications SaaS vers Azure AD. Ils peuvent également connecter des applications qui utilisent des méthodes d’authentification héritées avec Azure AD. En regroupant les clients sur une seule plateforme (Azure AD), les ISV peuvent gérer plus facilement leurs applications et les aider à mettre en œuvre les principes de la Confiance Zéro. La prise en charge des applications qui utilisent l’authentification héritée sécurise davantage les utilisateurs. Cette solution peut être une excellente ressource de transition jusqu’à ce que les clients modernisent leurs applications pour prendre en charge les protocoles d’authentification modernes.

## <a name="solution-overview"></a>Vue d’ensemble de la solution

La solution que vous créez peut inclure les composants suivants :

1. **Découverte d’applications**. Souvent, les clients n’ont pas connaissance de toutes les applications qu’ils utilisent. Vous pouvez donc commencer par créer des fonctionnalités de découverte d’applications dans votre solution et exposer celles qui ont été découvertes dans l’interface utilisateur. Le client pourra ainsi classer par ordre de priorité les applications qu’il souhaite intégrer à Azure AD.
2. **Migration d’applications**. Vous pouvez ensuite créer un workflow dans le produit pour permettre au client d’intégrer directement des applications à Azure AD sans avoir à accéder au portail Azure AD. Si vous n’implémentez pas de fonctionnalités de découverte dans votre solution, vous pouvez démarrer votre solution ici en intégrant les applications connues des clients à Azure AD.
3. **Prise en charge de l’authentification héritée**. Vous pouvez connecter les applications utilisant des méthodes d’authentification héritées à Azure AD pour qu’elles bénéficient des avantages de l’authentification unique (SSO) et d’autres fonctionnalités.
4. **Accès conditionnel**. Vous pouvez également permettre aux clients d’appliquer des stratégies Azure AD d’[accès conditionnel](../conditional-access/overview.md) aux applications à partir de votre solution, sans avoir à passer par le portail Azure AD.

Le reste de ce guide explique les considérations techniques et nos recommandations pour implémenter une solution.

## <a name="publishing-your-application-to-azure-marketplace"></a>Publication de votre application sur la Place de marché Azure

Vous pouvez préintégrer votre application à Azure AD pour prendre en charge l’authentification unique et le provisionnement automatisé. Pour cela, suivez le processus de [publication dans la Place de marché Azure](../develop/v2-howto-app-gallery-listing.md). La Place de marché Azure est une source fiable d’applications pour les administrateurs informatiques. Les applications répertoriées ont été validées pour être compatibles avec Azure AD. Elles prennent en charge l’authentification unique, automatisent l’approvisionnement des utilisateurs et peuvent facilement s’intégrer aux locataires clients avec l’inscription automatique des applications.

Nous vous recommandons par ailleurs de devenir un [éditeur vérifié](../develop/publisher-verification-overview.md) pour que les clients sachent que vous êtes l’éditeur approuvé de l’application.

## <a name="enabling-single-sign-on-for-it-admins"></a>Activation de l’authentification unique pour les administrateurs informatiques

[Choisissez OIDC ou SAML](/azure/active-directory/manage-apps/sso-options#choosing-a-single-sign-on-method/) pour activer l’authentification unique pour les administrateurs informatiques de votre solution. La meilleure option consiste à utiliser OIDC. 

Microsoft Graph utilise [OIDC/OAuth](../develop/v2-protocols-oidc.md). Si votre solution utilise OIDC avec Azure AD pour l’authentification unique des administrateurs informatiques, vos clients bénéficieront d’une expérience transparente de bout en bout. Ils utilisent OIDC pour se connecter à votre solution, et le même jeton web JSON (JWT) émis par Azure AD peut ensuite être utilisé pour interagir avec Microsoft Graph.

Si votre solution utilise [SAML](/azure/active-directory/manage-apps/configure-saml-single-sign-on/) pour l’authentification unique de l’administrateur informatique, le jeton SAML ne permet pas à votre solution d’interagir avec Microsoft Graph. Vous pouvez toujours utiliser SAML pour l’authentification unique des administrateurs informatiques, mais votre solution doit prendre en charge l’intégration OIDC avec Azure AD afin de pouvoir obtenir un JWT d’Azure AD pour interagir correctement avec Microsoft Graph. Vous pouvez utiliser l’une des approches suivantes :

- **Approche SAML recommandée** : Créez une nouvelle inscription dans la Place de marché Azure, qui est [une application OIDC](../saas-apps/openidoauth-tutorial.md). Vos clients bénéficieront ainsi de l’expérience la plus fluide. Les deux protocoles SAML et OIDC sont ajoutés à leur locataire. Si votre application ne figure pas dans la galerie Azure AD aujourd’hui, vous pouvez commencer avec une [application multilocataire](../develop/howto-convert-app-to-be-multi-tenant.md) hors galerie.

- **Autre approche SAML** : Votre client peut [créer manuellement une inscription d’application OIDC](../saas-apps/openidoauth-tutorial.md) dans son locataire Azure AD et définir les URI, points de terminaison et autorisations appropriés spécifiés plus loin dans cet article.

Vous pouvez utiliser le [type d’autorisation client_credentials](../develop/v2-oauth2-client-creds-grant-flow.md#get-a-token). Il faudra que votre solution permette à chaque client de saisir un identifiant et un secret dans votre interface utilisateur, et que vous stockiez ces informations. Obtenez un jeton JWT à partir d’Azure AD, puis utilisez-le pour interagir avec Microsoft Graph.

Si vous choisissez cette approche, vous devez proposer à vos clients une documentation prête à l’emploi leur expliquant comment créer cette inscription d’application dans leur locataire Azure AD. Ces informations incluent les points de terminaison, les URI et les autorisations requises.

> [!NOTE]
> Pour qu’une application puisse être utilisée pour l’authentification unique de l’administrateur informatique ou des utilisateurs, l’administrateur informatique du client doit [consentir à l’accès à l’application dans son locataire](./grant-admin-consent.md).

## <a name="authentication-flows"></a>Flux d’authentification

La solution comprend trois flux d’authentification clés qui prennent en charge les scénarios suivants :

- L’administrateur informatique du client se connecte avec l’authentification unique (SSO) pour administrer votre solution.

- L’administrateur informatique du client utilise votre solution pour intégrer des applications à Azure AD par l’intermédiaire de Microsoft Graph.

- Les utilisateurs se connectent aux applications héritées sécurisées par votre solution et Azure AD.

### <a name="your-customers-it-administrator-does-single-sign-on-to-your-solution"></a>L’administrateur informatique de votre client se connecte à votre solution avec l’authentification unique

Votre solution peut utiliser SAML ou OIDC pour l’authentification unique quand l’administrateur informatique du client se connecte. Dans les deux cas, il est recommandé que l’administrateur informatique puisse se connecter à votre solution à l’aide de ses informations d’identification Azure AD. Cela permet de bénéficier d’une expérience transparente et d’utiliser les contrôles de sécurité existants déjà en place. Votre solution doit être intégrée à Azure AD pour l’authentification unique via SAML ou OIDC.

Voici un diagramme et un résumé de ce flux d’authentification des utilisateurs :

![Diagramme montrant un administrateur informatique redirigé par la solution vers Azure AD pour se connecter, puis redirigé par Azure AD vers la solution dans un workflow d’authentification utilisateur.](./media/secure-hybrid-access-integrations/admin-flow.png)

1. L’administrateur informatique souhaite se connecter à votre solution avec ses informations d’identification Azure AD.

2. Votre solution redirige l’administrateur informatique pour Azure AD à l’aide d’une demande de connexion SAML ou OIDC.

3. Azure AD authentifie l’administrateur informatique et le renvoie à votre solution avec un jeton SAML ou JWT à autoriser dans votre solution.

### <a name="the-it-administrator-integrates-applications-with-azure-ad-by-using-your-solution"></a>L’administrateur informatique intègre des applications à Azure AD avec votre solution

La deuxième étape du parcours de l’administrateur informatique consiste à intégrer des applications à Azure AD à l’aide de votre solution. Pour y parvenir, votre solution utilise Microsoft Graph afin de créer des inscriptions d’applications et des stratégies d’accès conditionnel Azure AD.

Voici un diagramme et un résumé de ce flux d’authentification des utilisateurs :

![Diagramme des redirections et autres interactions entre l’administrateur informatique, Azure Active Directory, votre solution et Microsoft Graph dans un workflow d’authentification utilisateur.](./media/secure-hybrid-access-integrations/registration-flow.png)


1. L’administrateur informatique souhaite se connecter à votre solution avec ses informations d’identification Azure AD.

2. Votre solution redirige l’administrateur informatique pour Azure AD à l’aide d’une demande de connexion SAML ou OIDC.

3. Azure AD authentifie l’administrateur informatique et le renvoie à votre solution avec un jeton SAML ou JWT pour l’autorisation dans votre solution.

4. Quand l’administrateur souhaite intégrer l’une de ses applications à Azure AD, il n’est pas obligé de passer par le portail Azure AD. Au lieu de cela, votre solution appelle Microsoft Graph avec le jeton JWT existant pour inscrire ces applications ou leur appliquer des stratégies d’accès conditionnel Azure AD.

### <a name="users-sign-in-to-the-applications-secured-by-your-solution-and-azure-ad"></a>Les utilisateurs se connectent aux applications sécurisées par votre solution et Azure AD

Quand les utilisateurs doivent se connecter à des applications individuelles sécurisées avec votre solution et Azure AD, ils utilisent OIDC ou SAML. Si les applications doivent interagir avec Microsoft Graph ou toute API protégée par Azure AD, nous vous recommandons de les configurer pour utiliser OICD. Avec cette configuration, le jeton JWT obtenu auprès d’Azure AD pour l’authentification vis-à-vis des applications peut également être utilisé pour interagir avec Microsoft Graph. S’il n’est pas nécessaire que les applications individuelles interagissent avec Microsoft Graph ou toute API protégée par Azure AD, SAML suffit.

Voici un diagramme et un résumé de ce flux d’authentification des utilisateurs :

![Diagramme des redirections et autres interactions entre l’utilisateur, Azure Active Directory, votre solution et l’application dans un workflow d’authentification utilisateur.](./media/secure-hybrid-access-integrations/end-user-flow.png)

1. L’utilisateur souhaite se connecter à une application sécurisée par votre solution et Azure AD.
2. Votre solution redirige l’utilisateur vers Azure AD à l’aide d’une demande de connexion SAML ou OIDC.
3. Azure AD authentifie l’utilisateur et le renvoie à votre solution avec un jeton SAML ou JWT pour l’autorisation dans votre solution.
4. Après l’autorisation, votre solution permet à la requête originale de l’application de passer en utilisant le protocole préféré de l’application.

## <a name="summary-of-microsoft-graph-apis"></a>Récapitulatif des API Microsoft Graph

Votre solution doit utiliser les API suivantes. Azure AD vous permet de configurer les autorisations déléguées ou les autorisations de l’application. Pour cette solution, vous n’avez besoin que d’autorisations déléguées.

- [API des modèles d’application](/graph/application-saml-sso-configure-api#retrieve-the-gallery-application-template-identifier/) : Si vous souhaitez effectuer des recherches dans la Place de marché Azure, vous pouvez utiliser cette API pour trouver un modèle d’application correspondant. **Autorisation obligatoire** : Application.Read.All.

- [API d’inscription d’application](/graph/api/application-post-applications) : Utilisez cette API pour créer des inscriptions d’applications OIDC ou SAML et permettre aux utilisateurs finaux de se connecter aux applications sécurisées avec votre solution par les clients. De cette façon, ces applications peuvent également être sécurisées avec Azure AD. **Autorisations obligatoires** : Application.Read.All, Application.ReadWrite.All.

- [API du principal du service](/graph/api/serviceprincipal-update) : après avoir inscrit l’application, vous devez mettre à jour l’objet principal du service pour définir des propriétés SSO. **Autorisations obligatoires** : Application.ReadWrite.All, Directory.AccessAsUser.All, AppRoleAssignment.ReadWrite.All (pour affectation).

- [API d’accès conditionnel](/graph/api/resources/conditionalaccesspolicy) : Si vous souhaitez également appliquer des stratégies d’accès conditionnel Azure AD à ces applications pour utilisateur final, vous pouvez utiliser cette API. **Autorisations obligatoires** : Policy.Read.All, Policy.ReadWrite.ConditionalAccess, Application.Read.All.

## <a name="example-graph-api-scenarios"></a>Exemples de scénarios d’API Graph

Cette section fournit des exemples de référence qui illustrent l’utilisation des API Microsoft Graph pour implémenter des inscriptions d’applications, connecter des applications héritées et activer des stratégies d’accès conditionnel par le biais de votre solution. Cette section fournit également des conseils sur l’automatisation du consentement de l’administrateur, l’obtention du certificat de signature de jetons et l’attribution d’utilisateurs et de groupes. Cette fonctionnalité peut être utile dans votre solution.

### <a name="use-the-graph-api-to-register-apps-with-azure-ad"></a>Utiliser l’API Graph pour inscrire des applications auprès d’Azure AD

#### <a name="add-apps-that-are-in-azure-marketplace"></a>Ajouter des applications qui se trouvent dans la Place de marché Azure

Certaines des applications que votre client utilise seront déjà disponibles dans la [Place de marché Azure](https://azuremarketplace.microsoft.com/marketplace/apps). Vous pouvez créer une solution qui ajoute programmatiquement ces applications au locataire du client. Le code suivant montre comment utiliser l’API Microsoft Graph pour rechercher un modèle correspondant dans la Place de marché Azure, puis comment inscrire l’application dans le locataire Azure AD du client.

Recherchez une application correspondante sur la Place de marché Azure. Si vous utilisez l’API des modèles d’application, le nom complet est sensible à la casse.

```http
Authorization: Required with a valid Bearer token
Method: Get

https://graph.microsoft.com/v1.0/applicationTemplates?$filter=displayname eq "Salesforce.com"
```

Si une correspondance est trouvée à partir de l’appel d’API précédent, capturez l’ID, puis effectuez l’appel d’API suivant tout en fournissant un nom complet convivial pour l’application dans le corps JSON :

```https
Authorization: Required with a valid Bearer token
Method: POST
Content-type: application/json

https://graph.microsoft.com/v1.0/applicationTemplates/cd3ed3de-93ee-400b-8b19-b61ef44a0f29/instantiate
{
    "displayname": "Salesforce.com"
}
```

Quand vous effectuez l’appel d’API précédent, vous générez également un objet de principal de service, ce qui peut prendre quelques secondes. Veillez à capturer l’ID d’application et l’ID du principal de service. Vous les utiliserez dans les appels d’API suivants.

Ensuite, appliquez un correctif à l’objet de principal de service avec le protocole SAML et l’URL de connexion appropriée :

```https
Authorization: Required with a valid Bearer token
Method: PATCH
Content-type: servicePrincipal/json

https://graph.microsoft.com/v1.0/servicePrincipals/3161ab85-8f57-4ae0-82d3-7a1f71680b27
{
    "preferredSingleSignOnMode":"saml",
    "loginURL": "https://www.salesforce.com"
}
```

Enfin, corrigez l’objet d’application avec les URI de redirection appropriés et les URI d’identificateur :

```https
Authorization: Required with a valid Bearer token
Method: PATCH
Content-type: application/json

https://graph.microsoft.com/v1.0/applications/54c4806b-b260-4a12-873c-967116983792
{
    "web": {
    "redirectUris":["https://www.salesforce.com"]},
    "identifierUris":["https://www.salesforce.com"]
}
```

#### <a name="add-apps-that-are-not-in-azure-marketplace"></a>Ajouter des applications qui ne se trouvent pas dans la Place de marché Azure

Si vous ne trouvez pas de résultats dans la Place de marché Azure ou si vous souhaitez simplement intégrer une application personnalisée, vous pouvez inscrire une application personnalisée dans Azure AD à l’aide de l’ID de modèle suivant : **8adf8e6e-67b2-4cf2-a259-e3dc5476c621**. Effectuez ensuite l’appel d’API suivant en fournissant un nom complet convivial de l’application dans le corps JSON :

```https
Authorization: Required with a valid Bearer token
Method: POST
Content-type: application/json

https://graph.microsoft.com/v1.0/applicationTemplates/8adf8e6e-67b2-4cf2-a259-e3dc5476c621/instantiate
{
    "displayname": "Custom SAML App"
}
```

Quand vous effectuez l’appel d’API précédent, vous générez également un objet de principal de service, ce qui peut prendre quelques secondes. Veillez à capturer l’ID d’application et l’ID du principal de service. Vous les utiliserez dans les appels d’API suivants.

Ensuite, appliquez un correctif à l’objet de principal de service avec le protocole SAML et l’URL de connexion appropriée :

```https
Authorization: Required with a valid Bearer token
Method: PATCH
Content-type: servicePrincipal/json

https://graph.microsoft.com/v1.0/servicePrincipals/3161ab85-8f57-4ae0-82d3-7a1f71680b27
{
    "preferredSingleSignOnMode":"saml",
    "loginURL": "https://www.samlapp.com"
}
```

Enfin, corrigez l’objet d’application avec les URI de redirection appropriés et les URI d’identificateur :

```https
Authorization: Required with a valid Bearer token
Method: PATCH
Content-type: application/json

https://graph.microsoft.com/v1.0/applications/54c4806b-b260-4a12-873c-967116983792
{
    "web": {
    "redirectUris":["https://www.samlapp.com"]},
    "identifierUris":["https://www.samlapp.com"]
}
```

#### <a name="cut-over-to-azure-ad-single-sign-on"></a>Basculer vers l’authentification unique Azure AD

Une fois que vous avez inscrit ces applications SaaS dans Azure AD, vous devez les basculer pour qu’elles commencent à utiliser Azure AD comme fournisseur d’identité. Il existe deux façons d'effectuer cette opération :

- Si les applications prennent en charge l’authentification unique en un clic, Azure AD peut basculer l’application pour le client. Le client doit simplement accéder au portail Azure AD et effectuer l’authentification unique en un clic avec les informations d’identification d’administration pour les applications SaaS prises en charge. Pour plus d’informations, consultez [Configuration en un clic de l’authentification unique pour une application](./one-click-sso-tutorial.md).
- Si les applications ne prennent pas en charge l’authentification unique en un clic, le client doit basculer manuellement les applications pour commencer à utiliser Azure AD. Pour plus d’informations, consultez les [tutoriels pour intégrer les applications SaaS à Azure Active Directory](../saas-apps/tutorial-list.md).

### <a name="connect-apps-by-using-legacy-authentication-methods-to-azure-ad"></a>Connectez les applications utilisant des méthodes d’authentification héritées à Azure AD

Ici, votre solution peut se trouver entre Azure AD et l’application et permettre au client de bénéficier des avantages de l’authentification unique et d’autres fonctionnalités Azure Active Directory, même si cette application n’est pas prise en charge. Pour cela, votre application appelle Azure AD pour authentifier l’utilisateur et appliquer des stratégies d’accès conditionnel Azure AD avant qu’il puisse accéder à ces applications avec des protocoles hérités.

Vous pouvez permettre aux clients d’effectuer cette intégration directement à partir de votre console pour offrir une expérience de découverte et d’intégration fluide de bout en bout. Il est nécessaire que votre plateforme crée une inscription d’application SAML ou OIDC entre votre plateforme et Azure AD.

#### <a name="create-a-saml-application-registration"></a>Créer une inscription d’application SAML

Pour créer une inscription d’application SAML, utilisez cet ID de modèle d’application personnalisée pour une application personnalisée : **8adf8e6e-67b2-4cf2-a259-e3dc5476c621**. Effectuez ensuite l’appel d’API suivant en fournissant un nom complet convivial dans le corps JSON :

```https
Authorization: Required with a valid Bearer token
Method: POST
Content-type: application/json

https://graph.microsoft.com/v1.0/applicationTemplates/8adf8e6e-67b2-4cf2-a259-e3dc5476c621/instantiate
{
    "displayname": "Custom SAML App"
}
```

Quand vous effectuez l’appel d’API précédent, vous générez également un objet de principal de service, ce qui peut prendre quelques secondes. Veillez à capturer l’ID d’application et l’ID du principal de service. Vous les utiliserez dans les appels d’API suivants.

Ensuite, appliquez un correctif à l’objet de principal de service avec le protocole SAML et l’URL de connexion appropriée :

```https
Authorization: Required with a valid Bearer token
Method: PATCH
Content-type: servicePrincipal/json

https://graph.microsoft.com/v1.0/servicePrincipals/3161ab85-8f57-4ae0-82d3-7a1f71680b27
{
    "preferredSingleSignOnMode":"saml",
    "loginURL": "https://www.samlapp.com"
}
```

Enfin, corrigez l’objet d’application avec les URI de redirection appropriés et les URI d’identificateur :

```https
Authorization: Required with a valid Bearer token
Method: PATCH
Content-type: application/json

https://graph.microsoft.com/v1.0/applications/54c4806b-b260-4a12-873c-967116983792
{
    "web": {
    "redirectUris":["https://www.samlapp.com"]},
    "identifierUris":["https://www.samlapp.com"]
}
```

#### <a name="create-an-oidc-application-registration"></a>Créer une inscription d’application OIDC

Pour créer une inscription d’application OIDC, utilisez cet ID de modèle pour une application personnalisée : **8adf8e6e-67b2-4cf2-a259-e3dc5476c621**. Effectuez ensuite l’appel d’API suivant en fournissant un nom complet convivial dans le corps JSON :

```https
Authorization: Required with a valid Bearer token
Method: POST
Content-type: application/json

https://graph.microsoft.com/v1.0/applicationTemplates/8adf8e6e-67b2-4cf2-a259-e3dc5476c621/instantiate
{
    "displayname": "Custom OIDC App"
}
```

À partir de l’appel d’API, capturez l’ID de l’application et l’ID du principal du service. Vous les utiliserez dans les appels d’API suivants.

```https
Authorization: Required with a valid Bearer token
Method: PATCH
Content-type: application/json

https://graph.microsoft.com/v1.0/applications/{Application Object ID}
{
    "web": {
    "redirectUris":["https://www.samlapp.com"]},
    "identifierUris":["[https://www.samlapp.com"],
    "requiredResourceAccess": [
    {
        "resourceAppId": "00000003-0000-0000-c000-000000000000",
        "resourceAccess": [
        {
            "id": "7427e0e9-2fba-42fe-b0c0-848c9e6a8182",
            "type": "Scope"
        },
        {
            "id": "e1fe6dd8-ba31-4d61-89e7-88639da4683d",
            "type": "Scope"
        },
        {
            "id": "37f7f235-527c-4136-accd-4a02d197296e",
            "type": "Scope"
        }]
    }]
}
```

> [!NOTE]
> Les autorisations d’API répertoriées dans le nœud `resourceAccess` accorderont à l’application les autorisations *openid*, *User.Read* et *offline_access*, ce qui devrait suffire pour que l’utilisateur se connecte à votre solution. Pour plus d’informations sur les autorisations, consultez les [Informations de référence sur les autorisations Microsoft Graph](/graph/permissions-reference/).

### <a name="apply-conditional-access-policies"></a>Appliquer des stratégies d’accès conditionnel

Les clients et partenaires peuvent également utiliser l’API Microsoft Graph pour créer ou appliquer des stratégies d’accès conditionnel aux applications du client. Pour les partenaires, le fait que le client puisse appliquer ces stratégies directement à partir de votre solution sans avoir à accéder au portail Azure AD peut être une valeur ajoutée. 

Deux options s’offrent à vous quand vous appliquez des stratégies d’accès conditionnel Azure AD :

- Affectez l’application à une stratégie d’accès conditionnel existante.
- Créez une stratégie d’accès conditionnel et affectez l’application à cette nouvelle stratégie.

#### <a name="use-an-existing-conditional-access-policy"></a>Utiliser une stratégie d’accès conditionnel existante

Tout d’abord, exécutez la requête suivante pour obtenir la liste de toutes les stratégies d’accès conditionnel. Récupérez l’ID d’objet de la stratégie que vous souhaitez modifier.

```https
Authorization: Required with a valid Bearer token
Method:GET

https://graph.microsoft.com/v1.0/identity/conditionalAccess/policies
```

Ensuite, corrigez la stratégie en incluant l’ID de l’objet d’application à inclure pour être dans la portée de `includeApplications` dans le corps JSON :

```https
Authorization: Required with a valid Bearer token
Method: PATCH

https://graph.microsoft.com/v1.0/identity/conditionalAccess/policies/{policyid}
{
    "displayName":"Existing CA Policy",
    "state":"enabled",
    "conditions": 
    {
        "applications": 
        {
            "includeApplications":[
                "00000003-0000-0ff1-ce00-000000000000", 
                "{Application Object ID}"
            ]
        },
        "users": {
            "includeUsers":[
                "All"
            ] 
        }
    },
    "grantControls": 
    {
        "operator":"OR",
        "builtInControls":[
            "mfa"
        ]
    }
}
```

#### <a name="create-a-new-conditional-access-policy"></a>Créer une nouvelle stratégie d’accès conditionnel

Ajoutez l’ID d’objet d’application à la portée de `includeApplications` dans le corps JSON :

```https
Authorization: Required with a valid Bearer token
Method: POST

https://graph.microsoft.com/v1.0/identity/conditionalAccess/policies/
{
    "displayName":"New CA Policy",
    "state":"enabled",
    "conditions": 
    {
        "applications": {
            "includeApplications":[
                "{Application Object ID}"
            ]
        },
        "users": {
            "includeUsers":[
                "All"
            ]
        }
    },
    "grantControls": {
        "operator":"OR",
        "builtInControls":[
            "mfa"
        ]
    }
}
```

Si vous souhaitez créer des stratégies d’accès conditionnel Azure AD, voici quelques modèles supplémentaires qui pourront vous aider à commencer à utiliser l’[API d’accès conditionnel](../conditional-access/howto-conditional-access-apis.md) :

```https
#Policy Template for Requiring Compliant Device

{
    "displayName":"Enforce Compliant Device",
    "state":"enabled",
    "conditions": {
        "applications": {
            "includeApplications":[
                "{Application Object ID}"
            ]
        },
        "users": {
            "includeUsers":[
                "All"
            ]
        }
    },
    "grantControls": {
        "operator":"OR",
        "builtInControls":[
            "compliantDevice",
            "domainJoinedDevice"
        ]
    }
}

#Policy Template for Block

{
    "displayName":"Block",
    "state":"enabled",
    "conditions": {
        "applications": {
            "includeApplications":[
                "{Application Object ID}"
            ]
        },
        "users": {
            "includeUsers":[
                "All"
            ] 
        }
    },
    "grantControls": {
        "operator":"OR",
        "builtInControls":[
            "block"
        ]
    }
}
```

### <a name="automate-admin-consent"></a>Automatiser le consentement administrateur

Si le client intègre un grand nombre d’applications à partir de votre plateforme à Azure AD, vous souhaiterez probablement automatiser le consentement administrateur pour qu’ils n’aient pas à accepter manuellement un grand nombre d’applications. Vous pouvez également effectuer cette automatisation via Microsoft Graph. Vous aurez besoin à la fois de l’ID d’objet du principal de service de l’application que vous avez créée dans les appels d’API précédents et de l’ID d’objet du principal de service de Microsoft Graph issu du locataire du client.

Récupérez l’ID d’objet du principal de service de Microsoft Graph en effectuant cet appel d’API :

```https
Authorization: Required with a valid Bearer token
Method:GET

https://graph.microsoft.com/v1.0/serviceprincipals/?$filter=appid eq '00000003-0000-0000-c000-000000000000'&$select=id,appDisplayName
```

Quand vous êtes prêt à automatiser le consentement administrateur, effectuez cet appel d’API :

```https
Authorization: Required with a valid Bearer token
Method: POST
Content-type: application/json

https://graph.microsoft.com/v1.0/oauth2PermissionGrants
{
    "clientId":"{Service Principal Object ID of Application}",
    "consentType":"AllPrincipals",
    "principalId":null,
    "resourceId":"{Service Principal Object ID Of Microsoft Graph}",
    "scope":"openid user.read offline_access}"
}
```

### <a name="get-the-token-signing-certificate"></a>Obtenir le certificat de signature de jetons

Pour obtenir la partie publique du certificat de signature de jetons pour toutes ces applications, utilisez `GET` à partir du point de terminaison de métadonnées Azure AD pour l’application :

```https
Method:GET

https://login.microsoftonline.com/{Tenant_ID}/federationmetadata/2007-06/federationmetadata.xml?appid={Application_ID}
```

### <a name="assign-users-and-groups"></a>Affecter des utilisateurs et des groupes

Une fois les applications publiées dans Azure AD, vous pouvez les affecter à des utilisateurs et à des groupes pour vérifier qu’elles apparaissent bien dans le portail [MyApplications](/azure/active-directory/user-help/my-applications-portal-workspaces/). Cette attribution est stockée sur l’objet de principal de service généré lors de la création de l’application.

Tout d’abord, récupérez toutes les instances `AppRole` que l’application peut avoir associées. Il est fréquent qu’une application SaaS soit associée à plusieurs instances `AppRole`. Pour les applications personnalisées, seule l’instance `AppRole` par défaut est généralement associée. Récupérez l’ID de l’instance `AppRole` que vous souhaitez affecter :

```https
Authorization: Required with a valid Bearer token
Method:GET

https://graph.microsoft.com/v1.0/servicePrincipals/3161ab85-8f57-4ae0-82d3-7a1f71680b27
```

Ensuite, obtenez l’ID d’objet de l’utilisateur ou du groupe que vous souhaitez affecter à l’application auprès d’Azure AD. Prenez également l’ID de rôle d’application de l’appel d’API précédent et envoyez-le dans le cadre du corps patch sur le principal de service :

```https
Authorization: Required with a valid Bearer token
Method: PATCH
Content-type: servicePrincipal/json

https://graph.microsoft.com/v1.0/servicePrincipals/3161ab85-8f57-4ae0-82d3-7a1f71680b27
{
    "principalId":"{Principal Object ID of User -or- Group}",
    "resourceId":"{Service Principal Object ID}",
    "appRoleId":"{App Role ID}"
}
```

## <a name="partnerships"></a>Partenariats

Microsoft a conclu des partenariats avec ces fournisseurs de contrôleurs de livraison d’applications (ADC) pour aider à protéger les applications existantes tout en utilisant les réseaux et les contrôleurs de livraison existants.

| **Fournisseur ADC** | **Lien** |
| --- | --- |
| Akamai Enterprise Application Access | [https://docs.microsoft.com/azure/active-directory/saas-apps/akamai-tutorial](../saas-apps/akamai-tutorial.md) |
| Citrix ADC | [https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial](../saas-apps/citrix-netscaler-tutorial.md) |
| F5 Big-IP Access Policy Manager | [https://docs.microsoft.com/azure/active-directory/manage-apps/f5-aad-integration](./f5-aad-integration.md) |
| Kemp LoadMaster | [https://docs.microsoft.com/azure/active-directory/saas-apps/kemp-tutorial](../saas-apps/kemp-tutorial.md) |
| Pulse Secure Virtual Traffic Manager | [https://docs.microsoft.com/azure/active-directory/saas-apps/pulse-secure-virtual-traffic-manager-tutorial](../saas-apps/pulse-secure-virtual-traffic-manager-tutorial.md) |

Les fournisseurs de solutions VPN suivants se connectent à Azure AD pour proposer des méthodes d’authentification et d’autorisation modernes, telles que l’authentification unique et l’authentification multifacteur.

| **Fournisseur VPN** | **Lien** |
| --- | --- |
| Cisco AnyConnect | [https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-anyconnect](../saas-apps/cisco-anyconnect.md) |
| Fortinet FortiGate | [https://docs.microsoft.com/azure/active-directory/saas-apps/fortigate-ssl-vpn-tutorial](../saas-apps/fortigate-ssl-vpn-tutorial.md) |
| F5 Big-IP Access Policy Manager | [https://docs.microsoft.com/azure/active-directory/manage-apps/f5-aad-password-less-vpn](./f5-aad-password-less-vpn.md) |
| Palo Alto Networks GlobalProtect | [https://docs.microsoft.com/azure/active-directory/saas-apps/paloaltoadmin-tutorial](../saas-apps/paloaltoadmin-tutorial.md) |
| Pulse Connect Secure | [https://docs.microsoft.com/azure/active-directory/saas-apps/pulse-secure-pcs-tutorial](../saas-apps/pulse-secure-pcs-tutorial.md) |

Les fournisseurs suivants de solutions de périmètre défini par logiciel (SDP) se connectent à Azure AD pour proposer des méthodes d’authentification et d’autorisation modernes, telles que l’authentification unique et l’authentification multifacteur.

| **Fournisseur SDP** | **Lien** |
| --- | --- |
| Répartiteur d’accès DatawizaDatawiza Access Broker | [https://docs.microsoft.com/en-us/azure/active-directory/manage-apps/datawiza-with-azure-ad](./datawiza-with-azure-ad.md) |
| Perimeter 81 | [https://docs.microsoft.com/azure/active-directory/saas-apps/perimeter-81-tutorial](../saas-apps/perimeter-81-tutorial.md) |
| Plateforme d’authentification Silverfort | [https://docs.microsoft.com/en-us/azure/active-directory/manage-apps/silverfort-azure-ad-integration](./silverfort-azure-ad-integration.md) |
| Maverics Identity Orchestrator de Strata | [https://docs.microsoft.com/azure/active-directory/saas-apps/maverics-identity-orchestrator-saml-connector-tutorial](../saas-apps/maverics-identity-orchestrator-saml-connector-tutorial.md) |
| Zscaler Private Access | [https://docs.microsoft.com/azure/active-directory/saas-apps/zscalerprivateaccess-tutorial](../saas-apps/zscalerprivateaccess-tutorial.md) |
