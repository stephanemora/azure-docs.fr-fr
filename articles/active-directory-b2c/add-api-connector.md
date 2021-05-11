---
title: Ajouter des connecteurs d’API à des flux d’utilisateurs (version préliminaire)
description: Configurer un connecteur d’API à utiliser dans un workflow utilisateur.
services: active-directory-b2c
ms.service: active-directory
ms.subservice: B2C
ms.topic: how-to
ms.date: 04/28/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 62de5eff098c467f048ae33cd38e7c730af863bc
ms.sourcegitcommit: 516eb79d62b8dbb2c324dff2048d01ea50715aa1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108174611"
---
# <a name="add-an-api-connector-to-a-sign-up-user-flow"></a>Ajouter un connecteur d’API à un workflow d’utilisateur d’inscription

En tant que développeur ou administrateur informatique, vous pouvez utiliser des connecteurs d’API pour intégrer vos flux d’utilisateurs d’inscription à des API REST pour personnaliser l’inscription et l’intégrer à des systèmes externes. À la fin de cette procédure pas à pas, vous pourrez créer un flux utilisateur Azure AD B2C qui interagit avec des [services API REST](api-connectors-overview.md). 

::: zone pivot="b2c-user-flow"

Dans ce scénario, l’API REST valide si le domaine de l’adresse e-mail est fabrikam.com ou fabricam.com. Le poste fourni par l’utilisateur est supérieur à cinq caractères. 

> [!IMPORTANT]
> Les connecteurs d’API pour l’inscription sont une fonctionnalité en préversion publique d’Azure AD B2C. Pour plus d’informations sur les préversions, consultez [Conditions d’utilisation supplémentaires pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

::: zone-end

::: zone pivot="b2c-custom-policy"

Dans ce scénario, nous allons ajouter la possibilité pour les utilisateurs d’entrer un numéro de fidélité dans la page d’inscription Azure AD B2C. L’API REST valide si la combinaison de l’adresse e-mail et du numéro de fidélité est mappée à un code promotionnel. Si l’API REST trouve un code promotionnel pour cet utilisateur, celui-ci est retourné à Azure AD B2C. Enfin, le code promotionnel sera inséré dans les revendications de jeton que l’application utilisera.

Vous pouvez aussi concevoir l’interaction comme une étape d’orchestration. Cela convient lorsque l’API REST ne valide pas les données à l’écran et qu’elle renvoie toujours des revendications. Pour plus d’informations, consultez [Procédure pas à pas : Intégrer les échanges de revendications de l’API REST dans votre parcours utilisateur Azure AD B2C comme étape d’orchestration](custom-policy-rest-api-claims-exchange.md).

::: zone-end

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="create-an-api-connector"></a>Créer un connecteur d'API

Pour utiliser un [connecteur d’API](api-connectors-overview.md), vous devez d’abord créer le connecteur d’API, puis l’activer dans un workflow utilisateur.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Sous **Services Azure**, sélectionnez **Azure AD B2C**.
4. Sélectionnez **Connecteurs d’API (version préliminaire)** , puis sélectionnez **Nouveau connecteur d’API**.

   ![Ajoutez un nouveau connecteur d'API](./media/add-api-connector/api-connector-new.png)

5. Indiquez un nom d’affichage pour l’appel. Par exemple, **Valider les informations utilisateur**.
6. Indiquez l’**URL du point de terminaison** pour l’appel d’API.
7. Choisissez le **Type d'authentification** et configurez les informations d'authentification pour appeler votre API. Consultez la section ci-dessous pour connaître les options de sécurisation de votre API.

    ![Configurer un connecteur d'API](./media/add-api-connector/api-connector-config.png)

8. Sélectionnez **Enregistrer**.

## <a name="securing-the-api-endpoint"></a>Sécuriser le point de terminaison d'API
Vous pouvez protéger votre point de terminaison d'API à l'aide de l'authentification de base HTTP ou de l'authentification par certificat client HTTPS (préversion). Dans les deux cas, vous fournissez les informations d'identification qu'Azure AD B2C utilisera lors de l'appel de votre point de terminaison d'API. Votre point de terminaison d'API vérifie ensuite les informations d'identification et prend les décisions relatives aux autorisations.

### <a name="http-basic-authentication"></a>Authentification HTTP de base
L’authentification de base HTTP est définie dans le document [RFC 2617](https://tools.ietf.org/html/rfc2617). Azure AD B2C envoie une requête HTTP avec les informations d'identification du client (`username` et `password`) dans l'en-tête `Authorization`. Les informations d'identification sont mises en forme en tant que chaîne `username:password` codée en base64. Votre API vérifie ensuite ces valeurs pour déterminer si elle doit rejeter ou non un appel d'API.

### <a name="https-client-certificate-authentication-preview"></a>Authentification par certificat client HTTPS (préversion)

> [!IMPORTANT]
> Cette fonctionnalité actuellement disponible en préversion est fournie sans contrat de niveau de service. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

L'authentification par certificat client est une méthode d'authentification mutuelle basée sur un certificat, dans laquelle le client fournit un certificat client au serveur pour prouver son identité. Dans ce cas, Azure AD B2C utilisera le certificat que vous chargez dans le cadre de la configuration du connecteur d'API. Cela se produit dans le cadre de l’établissement d'une liaison TLS/SSL. Votre service API peut alors limiter l'accès aux seuls services qui disposent des certificats appropriés. Le certificat client est un certificat numérique PKCS12 (PFX) X.509. Dans les environnements de production, il doit être signé par une autorité de certification. 

Pour créer un certificat, vous pouvez utiliser [Azure Key Vault](../key-vault/certificates/create-certificate.md), qui propose des options pour les certificats auto-signés et des intégrations auprès de fournisseurs d'émetteurs de certificats pour les certificats signés. Paramètres recommandés :
- **Objet** : `CN=<yourapiname>.<tenantname>.onmicrosoft.com`
- **Type de contenu** : `PKCS #12`
- **Type d'action de la durée de vie** : `Email all contacts at a given percentage lifetime` ou `Email all contacts a given number of days before expiry`
- **Type de clé** : `RSA`
- **Taille de la clé** : `2048`
- **Clé privée exportable** : `Yes` (afin de pouvoir exporter un fichier pfx)

Vous pouvez ensuite [exporter le certificat](../key-vault/certificates/how-to-export-certificate.md). Vous pouvez également utiliser la [cmdlet New-SelfSignedCertificate](../active-directory-b2c/secure-rest-api.md#prepare-a-self-signed-certificate-optional) de PowerShell pour générer un certificat auto-signé.

Une fois que vous disposez d'un certificat, vous pouvez le charger dans le cadre de la configuration du connecteur d'API. Notez que le mot de passe n’est nécessaire que pour les fichiers de certificat protégés par un mot de passe.

Votre API doit implémenter l'autorisation basée sur les certificats clients envoyés afin de protéger les points de terminaison d'API. Pour Azure App Service et Azure Functions, consultez [Configurer l'authentification mutuelle TLS](../app-service/app-service-web-configure-tls-mutual-auth.md) afin de savoir comment activer et *valider le certificat à partir de votre code d'API*.  Vous pouvez également utiliser le service Gestion des API Azure pour [vérifier les propriétés des certificats clients](
../api-management/api-management-howto-mutual-certificates-for-clients.md) et les comparer avec les valeurs souhaitées à l'aide d'expressions de stratégie.

Nous vous recommandons de définir des alertes de rappel avant la date d'expiration de votre certificat. Vous devrez générer un nouveau certificat et répéter les étapes ci-dessus. Votre service API peut temporairement continuer à accepter les anciens et les nouveaux certificats pendant le déploiement du nouveau certificat. Pour charger un nouveau certificat sur un connecteur d'API existant, sélectionnez le connecteur d'API sous **Connecteurs d'API**, puis cliquez sur **Charger un nouveau certificat**. Le dernier certificat chargé qui n’a pas expiré, et dont la date de début est dépassée, sera automatiquement utilisé par Azure Active Directory.

### <a name="api-key"></a>Clé d’API

Certains services utilisent un mécanisme de « clé API » pour obfusquer l'accès à vos points de terminaison HTTP pendant le développement. Pour [Azure Functions](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys), vous pouvez effectuer cette opération en incluant le `code` comme paramètre de requête dans l'**URL du point de terminaison**. Par exemple, `https://contoso.azurewebsites.net/api/endpoint`<b>`?code=0123456789`</b>). 

Ce mécanisme ne doit pas être utilisé seul en production. Par conséquent, la configuration de l'authentification de base ou par certificat est toujours requise. Si vous ne souhaitez implémenter aucune méthode d'authentification (non recommandé) à des fins de développement, vous pouvez choisir l'authentification de base et utiliser des valeurs temporaires pour `username` et `password`, que votre API peut ignorer pendant l'implémentation de l'autorisation dans votre API.

## <a name="the-request-sent-to-your-api"></a>Demande envoyée à votre API
Un connecteur d’API est matérialisé en tant que requête **HTTP POST**, en envoyant les attributs utilisateur (« revendications ») en tant que paires clé-valeur dans un corps JSON. Les attributs sont sérialisés de la même façon que les propriétés utilisateur [Microsoft Graph](/graph/api/resources/user#properties). 

**Exemple de requête**
```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "surname":"Smith",
 "jobTitle":"Supplier",
 "streetAddress":"1000 Microsoft Way",
 "city":"Seattle",
 "postalCode": "12345",
 "state":"Washington",
 "country":"United States",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "ui_locales":"en-US"
}
```

Seules les propriétés utilisateur et les attributs personnalisés répertoriés dans l’expérience **Azure AD B2C** > **Attributs utilisateur** peuvent être envoyés dans la demande.

Les attributs personnalisés existent au format **extension_\<extensions-app-id>_CustomAttribute** dans le répertoire. Votre API doit s’attendre à recevoir des revendications dans ce même format sérialisé. Pour plus d’informations sur les attributs personnalisés, consultez [Définir des attributs personnalisés dans Azure AD B2C](user-flow-custom-attributes.md).

De plus, la revendication **Paramètres régionaux de l’interface utilisateur (« ui_locales »)** est envoyée par défaut dans toutes les demandes. Elle fournit les paramètres régionaux d’un utilisateur tels qu’ils sont configurés sur son appareil et ils peuvent être utilisés par l’API pour renvoyer des réponses internationalisées.

> [!IMPORTANT]
> Si une revendication n’a pas de valeur au moment où le point de terminaison de l’API est appelé, la revendication n’est pas envoyée à l’API. Votre API doit être conçue pour vérifier et gérer explicitement le cas où une revendication ne figure pas dans la requête.

> [!TIP] 
> Les revendications [**identités (« identities »)**](/graph/api/resources/objectidentity) et **Adresse e-mail (« email »)** peuvent être utilisées par votre API pour identifier un utilisateur avant qu’il n’ait un compte dans votre locataire. 

## <a name="enable-the-api-connector-in-a-user-flow"></a>Activer le connecteur d’API dans un workflow utilisateur

Procédez comme suit pour ajouter un connecteur d’API à un workflow d’utilisateur d’inscription.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Sous **Services Azure**, sélectionnez **Azure AD B2C**.
4. Sélectionnez **Flux d’utilisateurs**, puis sélectionnez le flux utilisateur auquel vous souhaitez ajouter le connecteur d’API.
5. Sélectionnez **Connecteurs d’API**, puis sélectionnez les points de terminaison d’API que vous souhaitez appeler aux étapes suivantes dans le workflow d’utilisateur :

   - **Après la connexion avec un fournisseur d’identité**
   - **Avant de créer l’utilisateur**

   ![Ajouter des API au flux de l’utilisateur](./media/add-api-connector/api-connectors-user-flow-select.png)

6. Sélectionnez **Enregistrer**.

## <a name="after-signing-in-with-an-identity-provider"></a>après la connexion avec un fournisseur d’identité

Un connecteur d’API à cette étape du processus d’inscription est appelé immédiatement après que l’utilisateur s’est authentifié auprès d’un fournisseur d’identité (comme Google, Facebook et Azure AD). Cette étape précède la ***page de collection d’attributs***, qui est le formulaire présenté à l’utilisateur pour collecter des attributs utilisateur. Cette étape n’est pas appelée si un utilisateur s’inscrit auprès d’un compte local.

### <a name="example-request-sent-to-the-api-at-this-step"></a>Exemple de demande envoyée à l’API à cette étape
```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ 
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "lastName":"Smith",
 "ui_locales":"en-US"
}
```

Les revendications exactes envoyées à l’API dépendent des informations fournies par le fournisseur d’identité. « email » est toujours envoyé.

### <a name="expected-response-types-from-the-web-api-at-this-step"></a>Types de réponses attendus de l’API Web à cette étape

Lorsque l’API Web reçoit une requête HTTP de Azure AD pendant un workflow utilisateur, elle peut renvoyer les réponses suivantes :

- Réponse de continuation
- Réponse de blocage

#### <a name="continuation-response"></a>Réponse de continuation

Une réponse de continuation indique que le flux de l’utilisateur doit passer à l’étape suivante : la page de collecte d’attribut.

Dans une réponse de continuation, l’API peut renvoyer des revendications. Si une revendication est retournée par l’API, la revendication effectue les opérations suivantes :

- Renseigne le champ d’entrée dans la page de collections d’attributs.

Consultez un exemple de [réponse de continuation](#example-of-a-continuation-response).

#### <a name="blocking-response"></a>Réponse de blocage

Une réponse de blocage quitte le workflow de l’utilisateur. Il peut être intentionnellement émis par l’API pour arrêter la continuation du workflow de l’utilisateur en affichant une page de blocage à l’utilisateur. La page de blocage affiche les `userMessage` fournies par l’API.

Consultez un exemple de [réponse de blocage](#example-of-a-blocking-response).

## <a name="before-creating-the-user"></a>avant la création de l’utilisateur

Un connecteur d’API à cette étape du processus d’inscription est appelé après la page de collection d’attributs, si elle est inclue. Cette étape est toujours appelée avant qu’un compte d’utilisateur ne soit créé.

### <a name="example-request-sent-to-the-api-at-this-step"></a>Exemple de demande envoyée à l’API à cette étape

```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "surname":"Smith",
 "jobTitle":"Supplier",
 "streetAddress":"1000 Microsoft Way",
 "city":"Seattle",
 "postalCode": "12345",
 "state":"Washington",
 "country":"United States",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "ui_locales":"en-US"
}
```

Les revendications qui sont envoyées à l’API dépendent des informations collectées par l’utilisateur ou fournies par le fournisseur d’identité.

### <a name="expected-response-types-from-the-web-api-at-this-step"></a>Types de réponses attendus de l’API Web à cette étape

Lorsque l’API Web reçoit une requête HTTP de Azure AD pendant un workflow utilisateur, elle peut renvoyer les réponses suivantes :

- Réponse de continuation
- Réponse de blocage
- Réponse de validation

#### <a name="continuation-response"></a>Réponse de continuation

Une réponse de continuation indique que le flux de l’utilisateur doit passer à l’étape suivante : créer l’utilisateur dans le répertoire.

Dans une réponse de continuation, l’API peut renvoyer des revendications. Si une revendication est retournée par l’API, la revendication effectue les opérations suivantes :

- Remplace toute valeur qui a déjà été attribuée à la revendication à partir de la page de collection d’attributs.

Consultez un exemple de [réponse de continuation](#example-of-a-continuation-response).

#### <a name="blocking-response"></a>Réponse de blocage
Une réponse de blocage quitte le workflow de l’utilisateur. Il peut être intentionnellement émis par l’API pour arrêter la continuation du workflow de l’utilisateur en affichant une page de blocage à l’utilisateur. La page de blocage affiche les `userMessage` fournies par l’API.

Consultez un exemple de [réponse de blocage](#example-of-a-blocking-response).

### <a name="validation-error-response"></a>Validation-réponse d’erreur
 Lorsque l’API répond avec une réponse d’erreur de validation, le workflow de l’utilisateur reste sur la page de collection d’attributs et un `userMessage` est présenté à l’utilisateur. L’utilisateur peut ensuite modifier et renvoyer le formulaire. Ce type de réponse peut être utilisé pour la validation d’entrée.

Consultez un exemple de [réponse d’erreur de validation](#example-of-a-validation-error-response).

## <a name="example-responses"></a>Exemples de réponses

### <a name="example-of-a-continuation-response"></a>Exemple de réponse de continuation

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "Continue",
    "postalCode": "12349", // return claim
    "extension_<extensions-app-id>_CustomAttribute": "value" // return claim
}
```

| Paramètre                                          | Type              | Obligatoire | Description                                                                                                                                                                                                                                                                            |
| -------------------------------------------------- | ----------------- | -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| action                                             | String            | Oui      | La valeur doit être `Continue`.                                                                                                                                                                                                                                                              |
| \<builtInUserAttribute>                            | \<attribute-type> | Non       | Les valeurs retournées peuvent remplacer des valeurs collectées à partir d’un utilisateur. Elles peuvent également être retournées dans le jeton si elles sont sélectionnées en tant que **Revendication d’application**.                                              |
| \<extension\_{extensions-app-id}\_CustomAttribute> | \<attribute-type> | Non       | La revendication n’a pas besoin de contenir `_<extensions-app-id>_`. Les valeurs retournées peuvent remplacer des valeurs collectées à partir d’un utilisateur. Elles peuvent également être retournées dans le jeton si elles sont sélectionnées en tant que **Revendication d’application**.  |

### <a name="example-of-a-blocking-response"></a>Exemple de réponse de blocage

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "There was a problem with your request. You are not able to sign up at this time.",
}

```

| Paramètre   | Type   | Obligatoire | Description                                                                |
| ----------- | ------ | -------- | -------------------------------------------------------------------------- |
| version     | String | Oui      | Version de l’API.                                                    |
| action      | String | Oui      | La valeur doit être `ShowBlockPage`                                              |
| userMessage | String | Oui      | Message à afficher à l’utilisateur.                                            |

**Expérience de l’utilisateur final avec une réponse de blocage**

![Exemple de page de blocage](./media/add-api-connector/blocking-page-response.png)

### <a name="example-of-a-validation-error-response"></a>Exemple de réponse d’erreur de validation

```http
HTTP/1.1 400 Bad Request
Content-type: application/json

{
    "version": "1.0.0",
    "status": 400,
    "action": "ValidationError",
    "userMessage": "Please enter a valid Postal Code."
}
```

| Paramètre   | Type    | Obligatoire | Description                                                                |
| ----------- | ------- | -------- | -------------------------------------------------------------------------- |
| version     | String  | Oui      | Version de votre API.                                                    |
| action      | String  | Oui      | La valeur doit être `ValidationError`.                                           |
| status      | Entier / Chaîne | Oui      | La valeur doit être `400`, ou `"400"` pour une réponse ValidationError.  |
| userMessage | String  | Oui      | Message à afficher à l’utilisateur.                                            |

> [!NOTE]
> Le code d’état HTTP doit être « 400 » en plus de la valeur « Status » dans le corps de la réponse.

**Expérience de l’utilisateur final avec une réponse d’erreur de validation**

![Exemple de page de validation](./media/add-api-connector/validation-error-postal-code.png)


::: zone-end

::: zone pivot="b2c-custom-policy"


## <a name="prepare-a-rest-api-endpoint"></a>Préparer un point de terminaison d’API REST

Pour cette procédure pas à pas, vous devez disposer d’une API REST qui vérifie si une adresse e-mail est inscrite dans votre système principal avec un ID de fidélité. Si elle est inscrite, l’API REST doit retourner un code promotionnel d’inscription que le client peut utiliser pour acheter des marchandises sur votre application. Dans le cas contraire, l’API REST doit renvoyer un message d’erreur HTTP 409 : « L’ID de fidélité "{loyalty ID}" n’est pas associé à l’adresse e-mail "{email}". »

Le code JSON suivant illustre les données qu’Azure AD B2C enverra à votre point de terminaison d’API REST. 

```json
{
    "email": "User email address",
    "language": "Current UI language",
    "loyaltyId": "User loyalty ID"
}
```

Une fois que votre API REST valide les données, elle doit retourner un message HTTP 200 (OK), avec les données JSON suivantes :

```json
{
    "promoCode": "24534"
}
```

En cas d’échec de la validation, l’API REST doit retourner un message d’erreur HTTP 409 (Conflit) avec l’élément JSON `userMessage`. Identity Experience Framework attend la revendication `userMessage` retournée par l’API REST. Cette revendication est présentée sous forme de chaîne à l’utilisateur en cas d’échec de la validation.

```json
{
    "version": "1.0.1",
    "status": 409,
    "userMessage": "LoyaltyId ID '1234' is not associated with 'david@contoso.com' email address."
}
```

Cet article ne traite pas de la configuration du point de terminaison d’API REST. Vous avez créé un exemple [Azure Functions](../azure-functions/functions-reference.md). Vous pouvez accéder au code complet de la fonction Azure sur le site de [GitHub](https://github.com/azure-ad-b2c/rest-api/tree/master/source-code/azure-function).

## <a name="define-claims"></a>Définir des revendications

Une revendication fournit un stockage temporaire de données lors d’une exécution de stratégie Azure AD B2C. Vous pouvez déclarer des revendications dans la section [Schéma de revendications](claimsschema.md). 

1. Ouvrez le fichier d’extensions de votre stratégie. Par exemple <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>.
1. Recherchez l’élément [BuildingBlocks](buildingblocks.md). Si l’élément n’existe pas, ajoutez-le.
1. Localisez l’élément [ClaimsSchema](claimsschema.md). Si l’élément n’existe pas, ajoutez-le.
1. Ajoutez les revendications suivantes à l’élément **ClaimsSchema**.  

```xml
<ClaimType Id="loyaltyId">
  <DisplayName>Your loyalty ID</DisplayName>
  <DataType>string</DataType>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
<ClaimType Id="promoCode">
  <DisplayName>Your promo code</DisplayName>
  <DataType>string</DataType>
  <UserInputType>Paragraph</UserInputType>
</ClaimType>
  <ClaimType Id="userLanguage">
  <DisplayName>User UI language (used by REST API to return localized error messages)</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

## <a name="add-the-restful-api-technical-profile"></a>Ajouter le profil technique de l’API RESTful 

Un [profil technique RESTful](restful-technical-profile.md) prend en charge la création d’une interface avec votre propre service RESTful. Azure Active Directory B2C envoie des données au service RESTful dans une collection `InputClaims` et reçoit des données en retour dans une collection `OutputClaims`. Recherchez l’élément **ClaimsProviders** et ajoutez un fournisseur de revendications comme suit :

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-ValidateProfile">
      <DisplayName>Check loyaltyId Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- Set the ServiceUrl with your own REST API endpoint -->
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/ValidateProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <!-- Claims sent to your REST API -->
        <InputClaim ClaimTypeReferenceId="loyaltyId" />
        <InputClaim ClaimTypeReferenceId="email" />
        <InputClaim ClaimTypeReferenceId="userLanguage" PartnerClaimType="lang" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
      </InputClaims>
      <OutputClaims>
        <!-- Claims parsed from your REST API -->
        <OutputClaim ClaimTypeReferenceId="promoCode" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

Dans cet exemple, le `userLanguage` sera envoyé au service REST en tant que `lang` au sein de la charge utile JSON. La valeur de la revendication `userLanguage` contient l’ID de langue de l’utilisateur actuel. Pour plus d’informations, consultez [Programmes de résolution de revendication](claim-resolver-overview.md).

### <a name="configure-the-restful-api-technical-profile"></a>Configurer le profil technique de l’API RESTful 

Après avoir déployé votre API REST, définissez les métadonnées du profil technique `REST-ValidateProfile` pour refléter votre propre API REST, notamment :

- **ServiceUrl**. Définissez l’URL du point de terminaison de l’API REST.
- **SendClaimsIn**. Spécifiez la façon dont les revendications d’entrée sont envoyées au fournisseur de revendications RESTful.
- **AuthenticationType**. Définissez le type de l’authentification effectuée par le fournisseur de revendications RESTful. 
- **AllowInsecureAuthInProduction**. Dans un environnement de production, veillez à définir ces métadonnées sur `true`
    
Pour plus d’informations sur les configurations, consultez [Métadonnées du profil technique RESTful](restful-technical-profile.md#metadata).

Les commentaires ci -dessus `AuthenticationType` et `AllowInsecureAuthInProduction` indiquent les modifications que vous devez effectuer lorsque vous passez à un environnement de production. Pour savoir comment sécuriser vos API RESTful pour la production, consultez [Sécuriser une API RESTful](secure-rest-api.md).

## <a name="validate-the-user-input"></a>Valider l’entrée de l’utilisateur

Pour obtenir le numéro de fidélité de l’utilisateur lors de l’inscription, vous devez permettre à l’utilisateur d’entrer ces données à l’écran. Ajoutez la revendication de sortie **loyaltyId** à la page d’inscription en l’ajoutant à l’élément `OutputClaims` de la section du profil technique d’inscription existant. Spécifiez la liste complète des revendications de sortie pour contrôler l’ordre dans lequel les revendications sont présentées à l’écran.  

Ajoutez la référence du profil technique de validation au profil technique d’inscription, qui appelle le `REST-ValidateProfile`. Le nouveau profil technique de validation sera ajouté au début de la collection `<ValidationTechnicalProfiles>` définie dans la stratégie de base. Ce comportement signifie qu’une fois la validation réussie, Azure AD B2C poursuit le processus en créant le compte dans le répertoire.   

1. Recherchez l’élément **ClaimsProviders**. Ajoutez un fournisseur de revendications comme suit :

    ```xml
    <ClaimsProvider>
      <DisplayName>Local Account</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="newPassword" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="displayName"/>
            <OutputClaim ClaimTypeReferenceId="givenName"/>
            <OutputClaim ClaimTypeReferenceId="surName"/>
            <!-- Required to present the text box to collect the data from the user -->
            <OutputClaim ClaimTypeReferenceId="loyaltyId"/>
            <!-- Required to pass the promoCode returned from "REST-ValidateProfile" 
            to subsequent orchestration steps and token issuance-->
            <OutputClaim ClaimTypeReferenceId="promoCode" />
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="REST-ValidateProfile" />
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    <ClaimsProvider>
      <DisplayName>Self Asserted</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SelfAsserted-Social">
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="email" />
          </InputClaims>
            <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="email" />
            <OutputClaim ClaimTypeReferenceId="displayName"/>
            <OutputClaim ClaimTypeReferenceId="givenName"/>
            <OutputClaim ClaimTypeReferenceId="surname"/>
            <!-- Required to present the text box to collect the data from the user -->
            <OutputClaim ClaimTypeReferenceId="loyaltyId"/>
            <!-- Required to pass the promoCode returned from "REST-ValidateProfile" 
            to subsequent orchestration steps and token issuance-->
            <OutputClaim ClaimTypeReferenceId="promoCode" />
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="REST-ValidateProfile"/>
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

## <a name="include-a-claim-in-the-token"></a>Inclure une revendication dans le jeton 

Pour retourner la revendication de code promotionnel à l’application par partie de confiance, ajoutez une revendication de sortie au fichier <em>`SocialAndLocalAccounts/`**`SignUpOrSignIn.xml`**</em>. La revendication de sortie permettra l’ajout de la revendication au jeton après un parcours utilisateur réussi et l’enverra à l’application. Modifiez l’élément de profil technique dans la section de partie de confiance pour ajouter `promoCode` en tant que revendication de sortie.
 
```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
      <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      <OutputClaim ClaimTypeReferenceId="promoCode" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

## <a name="test-the-custom-policy"></a>Tester la stratégie personnalisée

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Veillez à utiliser l’annuaire qui contient votre locataire Azure AD en sélectionnant le filtre **Annuaire + abonnement** dans le menu du haut et en choisissant l’annuaire qui contient votre locataire Azure AD.
1. Choisissez **Tous les services** dans le coin supérieur gauche du portail Azure, puis recherchez et sélectionnez **Inscriptions d’applications**.
1. Sélectionnez **Infrastructure d’expérience d’identité**.
1. Sélectionnez **Charger une stratégie personnalisée**, puis chargez les fichiers de stratégie que vous avez modifiés : *TrustFrameworkExtensions.xml* et *SignUpOrSignin.xml*. 
1. Sélectionnez la stratégie d’inscription et de connexion que vous avez chargée, puis cliquez sur le bouton **Exécuter maintenant**.
1. Vous devriez pouvoir vous inscrire à l’aide d’une adresse de messagerie.
1. Cliquez sur le lien **S’inscrire maintenant**.
1. Dans le champ **Votre ID de fidélité**, saisissez 1234, puis cliquez sur **Continuer**. À ce stade, vous devez obtenir un message d’erreur de validation.
1. Remplacez par une autre valeur et cliquez sur **Continuer**.
1. Le jeton envoyé à votre application inclut la revendication `promoCode`.

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1584295703,
  "nbf": 1584292103,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1584292103,
  "auth_time": 1584292103,
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "promoCode": "84362"
  ...
}
```

::: zone-end

## <a name="best-practices-and-how-to-troubleshoot"></a>Meilleures pratiques et résolution des problèmes

### <a name="using-serverless-cloud-functions"></a>Utilisation des fonctions cloud serverless

Les fonctions serverless, comme les déclencheurs HTTP dans Azure Functions, fournissent une méthode permettant de créer des points de terminaison d’API à utiliser avec le connecteur d’API. Vous pouvez utiliser la fonction cloud serverless pour, [par exemple](code-samples.md#api-connectors), effectuer une logique de validation et limiter les inscriptions à des domaines de courrier spécifiques. La fonction cloud serverless peut également appeler et invoquer d’autres API web, magasins d’utilisateurs et autres services cloud dans le cade de scénarios plus complexes.

### <a name="best-practices"></a>Meilleures pratiques
Assurez-vous que :
* Votre API suit les contrats de demande et de réponse d’API comme indiqué ci-dessus. 
* L’**URL du point de terminaison** du connecteur d’API pointe vers le point de terminaison d’API approprié.
* Votre API recherche explicitement les valeurs null des revendications reçues.
* Votre API répond aussi rapidement que possible pour garantir une expérience utilisateur fluide.
    * Si vous utilisez une fonction serverless ou un service web scalable, utilisez un plan d’hébergement qui conserve l’API dans un état « de veille » ou « dynamique ». en production. Pour Azure Functions, il est recommandé d’utiliser le [plan Premium](../azure-functions/functions-scale.md).
 
### <a name="use-logging"></a>Utiliser la journalisation

En général, il est judicieux d’utiliser les outils de journalisation activés par votre service API Web, comme [Application Insights](../azure-functions/functions-monitoring.md), pour surveiller votre API en cas de codes d’erreur inattendus, d’exceptions et de performances médiocres.
* Analysez les codes d’état HTTP autres que HTTP 200 ou 400.
* Un code d’état HTTP 401 ou 403 indique généralement un problème avec votre authentification. Vérifiez la couche d’authentification de votre API et la configuration correspondante dans le connecteur d’API.
* Si nécessaire, utilisez des niveaux de journalisation plus agressifs (par exemple, « trace » ou « debug ») lors du développement.
* Surveillez votre API en cas de temps de réponse longs.

## <a name="next-steps"></a>Étapes suivantes

::: zone pivot="b2c-user-flow"

- Bien commencer avec nos [exemples](code-samples.md#api-connectors).
- [Sécuriser votre connecteur d’API](secure-rest-api.md)

::: zone-end

::: zone pivot="b2c-custom-policy"

- [Procédure pas à pas : Intégrer les échanges de revendications de l’API REST dans votre parcours utilisateur Azure AD B2C comme étape d’orchestration](custom-policy-rest-api-claims-exchange.md)
- [Sécuriser votre connecteur d’API](secure-rest-api.md)
- [Reference : Profil technique RESTful](restful-technical-profile.md)

::: zone-end